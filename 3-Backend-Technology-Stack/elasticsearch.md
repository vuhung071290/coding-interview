# Elasticsearch for Backend Engineers

> Search engine và analytics platform — khi database full-text search không đủ

## 1. Core Concepts

### Index, Document, Shard
- **Index:** tương đương "table" trong SQL — collection of documents
- **Document:** JSON object — đơn vị lưu trữ (tương đương "row")
- **Shard:** partition của index — đơn vị parallelism và distribution
  - **Primary shard:** lưu data, số cố định sau khi tạo index
  - **Replica shard:** copy của primary — availability + read scaling
- **Node:** 1 instance ES, có thể master/data/ingest roles
- **Cluster:** tập hợp nodes

Ví dụ: Index "products" với 5 primary shards × 1 replica = 10 shards tổng. Primary và replica của cùng shard phải trên khác node.

### Inverted Index (tại sao ES search nhanh)
Thay vì scan từng document, ES xây dựng inverted index:
- "brown" → Doc 1, Doc 2, Doc 3
- "fox" → Doc 1, Doc 3
- "quick" → Doc 1, Doc 3 (lowercase, analyzed)

Khi query "brown fox" → tìm trong inverted index → O(1) lookup thay vì full scan

**Analysis pipeline:** tokenize → lowercase → remove stopwords → stemming → index terms

---

## 2. Mapping & Analysis

### Field Types
| Type | Description | Use When |
|------|-------------|----------|
| text | Analyzed (tokenized, lowercased) | Full-text search |
| keyword | Not analyzed | Exact match, sorting, aggregations |
| integer / double / long | Numeric | Range queries, math |
| date | ISO8601 / epoch | Date ranges |
| boolean | true/false | Filter |
| nested | Array of objects with own fields | Nested queries |

### text vs keyword — Key Distinction
- **text:** `"Quick Brown Fox"` → indexed as `["quick", "brown", "fox"]` → fuzzy, partial match works
- **keyword:** `"Quick Brown Fox"` → indexed as-is → only exact match, good for sorting/facets
- Common pattern: dual field — `name` (text) + `name.keyword` (keyword) for both use cases

### Analyzers
- **Standard:** default — tokenize on whitespace/punctuation, lowercase
- **Vietnamese:** cần ICU plugin hoặc custom analyzer
- Custom analyzer: define tokenizer + token filters (lowercase, stop words, stemming)
- Stemming: "running" / "runs" / "ran" → "run" (Porter stemmer)

---

## 3. Query DSL

### Full-Text Queries (analyzed)
- **match:** main full-text query — tokenizes input, finds matching terms (OR logic by default)
  - `operator: "AND"` — all terms must match
- **match_phrase:** exact phrase in order — "quick brown" won't match "brown quick"
- **multi_match:** search across multiple fields; field boosting with `^` (e.g., `name^3`)
  - Types: `best_fields`, `most_fields`, `cross_fields`, `phrase`

### Term-Level Queries (not analyzed — exact match)
- **term:** exact value match — use on keyword fields
- **terms:** multiple values (equivalent to SQL IN)
- **range:** numeric/date range — `gte`, `lte`, `gt`, `lt`
- **exists:** check if field has a value
- **ids:** find by document IDs

### Boolean Query (combine queries)
- **must:** AND logic — contributes to relevance score
- **filter:** AND logic — NO score contribution, cacheable (use for filters!)
- **should:** OR logic — boosts score if matches
- **must_not:** NOT logic — excludes from results

**Key insight:** Use `filter` context for structured filters (price range, category, in-stock), `must` for text relevance. Filter context is faster (cached).

### Fuzzy Search
- `fuzziness: "AUTO"` — 0 for 1-2 chars, 1 for 3-5 chars, 2 for 6+
- Handles typos: "laptpo" matches "laptop"
- Higher fuzziness = more matches but lower precision

---

## 4. Aggregations

### Bucket Aggregations (nhóm documents)
- **terms:** group by field value — equivalent to GROUP BY
- **range:** bucket documents into price/date ranges
- **date_histogram:** group by time interval (day, month)
- **filter:** single-bucket for a filter condition

### Metric Aggregations (tính toán trên buckets)
- **avg, min, max, sum** — standard stats
- **stats** — min, max, avg, sum, count in one
- **percentiles** — P50, P95, P99 — good for latency analysis
- **cardinality** — approximate unique count (HyperLogLog)

### Nested Aggregations (faceted search)
Bucket aggs can have sub-aggregations: "by category" → "avg price per category"
This powers e-commerce faceted search (category counts, price ranges, brand filters)

---

## 5. Relevance Scoring

### TF-IDF (traditional)
- **TF (Term Frequency):** term xuất hiện nhiều trong document → score cao
- **IDF (Inverse Document Frequency):** term hiếm trong toàn index → score cao
- "the" xuất hiện mọi nơi → low IDF; "elasticsearch" hiếm → high IDF

### BM25 (default từ ES 5.0+)
- Improvement over TF-IDF
- Diminishing returns for TF (stops rewarding very high frequency)
- Field length normalization: shorter field + same terms = higher score
- Tunable: `k1` (TF saturation), `b` (length normalization)

### Boosting Strategies
- **Field boost in query:** `name^3` — name field 3x more important than description
- **function_score:** custom scoring with functions (recency, popularity, featured flag)
- **Script score:** arbitrary Painless script for complex scoring logic

---

## 6. Performance & Operations

### Index Design
- **Shard sizing:** 20–50GB per shard (rule of thumb)
- Disable fields not needed: `"enabled": false`
- `"index": false` for fields only needed for display, not search
- **Avoid** `dynamic: true` on untrusted data → mapping explosion

### Search Performance
- **Filter first:** filter context cached → put structure filters in `filter`, not `must`
- **search_after pagination:** instead of offset-based (deep pagination slow)
- **Source filtering:** `_source: ["name", "price"]` — don't return full document if unneeded
- **Routing:** custom routing key sends related docs to same shard → fewer shard requests

### Write Performance
- **Bulk API:** batch multiple writes — single HTTP round-trip
- **refresh_interval:** increase to `30s` or `-1` during bulk indexing
- **Replicas:** temporarily set to `0` during initial bulk load, restore after

### Common Operational Issues
- **Split brain:** use ≥ 3 master-eligible nodes; `minimum_master_nodes = n/2 + 1`
- **Hot shard:** uneven traffic → custom routing or alias rollover strategy
- **Mapping explosion:** too many dynamic fields → `dynamic: strict`

---

## 7. Elasticsearch vs Database Full-Text Search

| | Elasticsearch | PostgreSQL FTS |
|---|---|---|
| Relevance ranking | ✅ BM25, custom scoring | ⚠️ Limited |
| Fuzzy/typo tolerance | ✅ Built-in fuzziness | ❌ |
| Aggregations/facets | ✅ Rich bucket/metric aggs | ⚠️ Limited |
| Consistency | ⚠️ Near real-time (1s delay) | ✅ Strong ACID |
| Setup complexity | ❌ High (cluster, ops) | ✅ Low (native) |
| Best for | Product search, log analytics, full-text at scale | Simple search on small datasets |

**Decision:** PostgreSQL FTS for < 1M docs and simple search. Elasticsearch when: relevance matters, faceted search needed, large datasets, typo tolerance required.

---

## 8. Data Sync Patterns

**Keeping ES in sync with primary DB:**
- **Dual-write:** App writes to DB then ES — risk of inconsistency on failure
- **Kafka Connect + Debezium (CDC):** capture DB changes from WAL → Kafka → ES connector — most reliable
- **Application events:** domain events → Kafka → ES consumer
- **Scheduled sync:** periodic full/incremental sync — simple but lag

---

## 9. Interview Topics

### Câu hỏi hay gặp
- "Elasticsearch hoạt động như thế nào?" → Inverted index, sharding, BM25 relevance scoring
- "Làm sao implement search với typo tolerance?" → `fuzziness: AUTO`
- "text vs keyword field?" → Analyzed vs not analyzed; dual field pattern
- "Làm sao sync data từ DB vào ES?" → CDC với Kafka Connect + Debezium, hoặc dual-write
- "Tại sao dùng filter thay vì query cho filter conditions?" → Cache, no score computation
- "Elasticsearch có ACID không?" → Không. Near real-time, eventually consistent; không dùng như primary DB
- "Làm sao implement faceted search?" → Bucket aggregations (terms, range) + sub-aggregations
- "Deep pagination problem?" → `search_after` instead of `from+size` for deep pages

### System Design Applications
- **E-commerce search:** product search với facets, relevance, category filters
- **ELK Stack:** centralized logging (Logstash/Fluentd → Elasticsearch → Kibana)
- **Autocomplete:** `prefix` query hoặc `completion` suggester
- **Social media feed search:** full-text post search với recency boost
