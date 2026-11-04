# AI & LLM Integration for Backend Engineers

> LLM integration, RAG patterns, vector databases — backend engineer cần biết gì trong AI era

## Tại sao Backend Engineer cần biết AI
- **Market reality 2025–2026:** hầu hết công ty đang tích hợp AI features — chatbot, search, recommendation, automation
- **Competitive advantage:** BE engineer biết AI integration được tuyển dụng ưu tiên
- **New architecture patterns:** RAG, agent workflows, MCP đang trở thành standard
- **roadmap.sh** đã thêm AI tools & knowledge vào backend roadmap

---

# 1. LLM Fundamentals cho Backend Engineers

## Các khái niệm cần biết

### Token
- LLM xử lý text thành tokens (không phải words)
- 1 token ≈ 4 chars English, nhiều hơn với tiếng Việt
- **Context window:** số tokens tối đa input + output (ví dụ: Claude 200K, GPT-4o 128K)
- **Cost:** tính theo tokens — input tokens rẻ hơn output tokens

### Model Families (2025)
| Provider | Model | Dùng cho |
|---------|-------|---------|
| Anthropic | Claude Opus/Sonnet/Haiku | Reasoning, long context |
| OpenAI | GPT-4o, o1, o3 | General purpose |
| Google | Gemini 2.0 | Multimodal, long context |
| Meta | Llama 3.x | Self-hosted, open source |
| Mistral | Mistral Large | European alternative |

### Temperature
- **0.0:** deterministic, consistent → code gen, extraction, classification
- **0.7:** balanced → content generation
- **1.0+:** creative, variable → brainstorming

---

# 2. LLM API Integration

## Basic API Integration
- HTTP POST to provider API (Anthropic, OpenAI, etc.)
- Params: model, max_tokens, messages (system + user), temperature
- Response: content text, stop_reason, token usage

## Spring AI — Java/Spring Integration
- Abstraction layer over multiple LLM providers
- `spring-ai-openai-spring-boot-starter` / `spring-ai-anthropic-spring-boot-starter`
- `ChatClient` — simple chat interface
- `EmbeddingModel` — text to vector
- `VectorStore` — semantic search over documents
- `ChatClient.advisor()` — built-in RAG with `QuestionAnswerAdvisor`

## Langchain4j — Alternative Java LLM Library
- Lower-level control than Spring AI
- Good for agents, tools, structured output

## Prompt Engineering Principles
- **System prompt:** define role, constraints, output format
- **Structured output:** ask for specific JSON format, then parse
- **Few-shot examples:** show examples of expected input/output in prompt
- **Chain of thought:** "Think step by step" → better reasoning for complex tasks
- **Temperature 0** for deterministic tasks (extraction, classification)

## Error Handling
- **Rate limit (429):** exponential backoff, retry
- **Server error (500/503):** retry with backoff
- **Content filter:** handle gracefully with fallback response
- **Timeout:** LLM calls can take 1–30s — async processing, timeouts

---

# 3. RAG (Retrieval-Augmented Generation)

## Vấn đề LLM giải quyết
- LLM có knowledge cutoff → không biết thông tin mới
- LLM "hallucinate" thông tin không có
- LLM không biết company-specific knowledge

## RAG Pattern

User Query → Embed query to vector → Search vector DB → top-K relevant chunks → Build prompt: [system] + [retrieved context] + [user query] → LLM generates answer grounded in context → Response (optionally with citations)

## RAG Implementation Steps
1. **Indexing phase:** load documents → chunk (500–1000 tokens) → embed each chunk → store in vector DB
2. **Query phase:** embed user query → similarity search (top-K, threshold) → retrieve relevant chunks → inject into prompt → call LLM

## Chunking Strategies
- **Fixed size:** simple, may cut mid-sentence
- **Sentence/paragraph:** respects semantic boundaries
- **Recursive:** split on `\n\n`, then `\n`, then space — good for most docs
- **Semantic:** cluster by meaning — complex but best quality

## RAG Quality Metrics
- **Precision:** retrieved chunks relevant to query?
- **Recall:** all relevant chunks retrieved?
- **Faithfulness:** LLM answer grounded in retrieved context (no hallucination)?
- **Answer relevance:** answer actually addresses the question?

---

# 4. Vector Databases & Embeddings

## Embeddings
- Chuyển text/image thành numerical vector (thường 768–3072 dimensions)
- Similar content → similar vectors (high cosine similarity)
- **Embedding Models:** OpenAI text-embedding-3-small, Sentence-BERT, all-MiniLM

## Similarity Metrics
- **Cosine similarity:** angle between vectors — most common for text
- **Euclidean distance:** geometric distance — use for normalized vectors
- **Dot product:** fast, works well with normalized vectors

## Vector Database Options
| Database | Type | Best for |
|---------|------|---------|
| **pgvector** | PostgreSQL extension | Existing PostgreSQL, small-medium scale |
| **Pinecone** | Managed cloud | Production, easy scaling, no ops |
| **Weaviate** | Self-hosted/managed | Hybrid search (vector + keyword) |
| **Chroma** | Embedded/server | Development, local testing |
| **Milvus** | Self-hosted | High scale, complex queries |
| **Qdrant** | Self-hosted/managed | Rust-based, high performance |

## pgvector (simplest cho Java/Spring teams)
- PostgreSQL extension — no separate DB needed
- Create `vector(1536)` column, build IVFFlat or HNSW index
- Query with `<=>` (cosine distance) operator
- Scales to millions of vectors; for billions need dedicated vector DB
- Supports hybrid search: combine vector similarity + SQL filters

---

# 5. AI System Design Patterns

## Pattern 1: Semantic Search (thay text search)
User types natural language → embed query → cosine similarity vs product embeddings → return semantically similar results (không cần exact keyword match)

## Pattern 2: Document Q&A System
Upload PDF/docs → chunk → embed → store in vector DB → User asks → RAG → LLM answers based on docs
Use cases: internal knowledge base, customer support, legal doc analysis

## Pattern 3: AI-Powered Classification
Customer complaint text → LLM classify(DELIVERY_ISSUE | PAYMENT | PRODUCT | OTHER) → route to correct support team. No manual labeling needed.

## Pattern 4: Content Generation Pipeline
Product data (name, specs) → LLM generate SEO description + meta tags → quality check → store if passes threshold

## Pattern 5: AI Agents / Agentic Workflows
LLM + Tools (function calling) → User: "Create order and send confirmation" → Agent: search products → check inventory → create order → send email → respond with result

## AI Agent Architecture
- **Tool definition:** describe functions LLM can call (name, params, description)
- **Tool execution loop:** LLM decides to call tool → app executes → result back to LLM → LLM continues or calls more tools
- **Stopping condition:** LLM says "final answer" or max iterations reached
- **State management:** conversation history, intermediate results

---

# 6. MCP (Model Context Protocol)

## Khái niệm
- Open protocol của Anthropic (2024) — LLM kết nối với external tools/data
- **Standardized:** thay thế custom function calling per-vendor
- **Components:** MCP Client (LLM app) ↔ MCP Server (your tools/data)

## MCP Server Types
- **Resources:** expose data (DB records, files, APIs) — LLM có thể read
- **Tools:** functions LLM có thể call (create order, search products)
- **Prompts:** reusable prompt templates

## Spring AI MCP Support
- `@McpTool` annotation to expose Java methods as MCP tools
- Auto-generates tool descriptions from method/param annotations
- Handles marshaling/unmarshaling of requests and responses

---

# 7. Production Considerations

## Cost Management
- **Cache frequent queries:** LLM calls expensive — cache embeddings và common responses (Redis)
- **Model selection:** cheaper/faster models for simple tasks; flagship only when needed
- **Token optimization:** truncate long contexts, summarize conversation history
- **Batch processing:** embed documents in batches, không từng cái

## Reliability
- **Fallback strategy:** nếu LLM unavailable → rule-based fallback hoặc simpler model
- **Timeout:** LLM calls chậm (1–30s) — async processing, appropriate timeout config
- **Rate limits:** API có limits → queue requests, exponential backoff

## Security
- **Prompt injection:** never embed raw user input directly in system prompt; validate/sanitize input
- **Data leakage:** không log full prompts có sensitive data
- **PII trong prompts:** anonymize trước khi gửi LLM (name → USER_123, email → EMAIL_1)
- **Output validation:** parse + validate LLM output trước khi dùng (never trust raw LLM output for critical operations)

## Observability cho AI
- Log: model, prompt hash (not full prompt), tokens used, latency, cost
- Track: success rate, latency P95/P99, cost per request
- Eval: human feedback loop, automatic quality metrics (RAGAS for RAG)

---

# 8. Interview Topics

## Câu hỏi hay gặp
- "Làm sao build chatbot cho internal knowledge base?" → RAG pattern, embed docs, pgvector
- "Hallucination là gì, giải quyết thế nào?" → RAG (ground in real data), output validation, structured prompts
- "LLM API integration production challenges?" → Cost, latency, rate limits, non-determinism, security
- "Vector DB là gì, dùng khi nào?" → Semantic similarity search, RAG, recommendation
- "Embedding là gì?" → Numerical representation của text; similar content → similar vectors
- "AI agent là gì?" → LLM + tools (function calling) → autonomous multi-step task execution

## Nên biết cho 2025–2026 Interviews
- RAG pattern — hiểu được implement
- Vector DB concepts (không cần deep knowledge, hiểu use case)
- LLM API integration cơ bản (Spring AI hoặc Langchain4j)
- Prompt engineering principles
- Security considerations (prompt injection, PII)
