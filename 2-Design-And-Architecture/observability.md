# Observability for Backend Engineers

> Logs, Metrics, Traces — xây dựng hệ thống có thể quan sát và debug trong production

## Tại sao Observability quan trọng
- **Testing ≠ Observability:** không thể test hết mọi production scenario
- **Senior interview expectation:** "Start with observability" — thiết kế system design không có monitoring là red flag
- **On-call reality:** observability quyết định MTTR (Mean Time To Recovery)

---

# 1. Three Pillars of Observability

## Logs
- Discrete events có timestamp — human-readable records
- **Structured logging:** JSON format, không plain text — fields: timestamp, level, service, traceId, userId, message, error
- **Correlation ID / Trace ID:** truyền qua tất cả services → trace 1 request qua nhiều logs
- **Log levels:** ERROR (alert!), WARN (investigate), INFO (business events), DEBUG (dev only)
- **Tools:** ELK Stack (Elasticsearch + Logstash + Kibana), Loki + Grafana, AWS CloudWatch Logs

## Metrics
- Numerical measurements over time — aggregatable, alert-able
- **Types:**
  - **Counter:** chỉ tăng (total requests, errors) → rate query
  - **Gauge:** tăng/giảm (memory usage, active connections, queue depth)
  - **Histogram:** distribution (request latency → P50/P95/P99)
  - **Summary:** pre-calculated percentiles (less flexible than Histogram)
- **Golden Signals (Google SRE):**
  - **Latency:** P99 response time (not avg — avg hides outliers)
  - **Traffic:** requests/second
  - **Errors:** error rate (5xx / total)
  - **Saturation:** CPU%, memory%, connection pool usage
- **Tools:** Prometheus + Grafana, AWS CloudWatch, Datadog

## Traces
- End-to-end journey của 1 request qua nhiều services
- **Span:** một operation (DB query, HTTP call, function) với start/end time
- **Trace:** tree of spans cho 1 request
- **Context propagation:** traceId/spanId truyền qua HTTP headers, Kafka headers (W3C Trace Context)
- **Tools:** Jaeger, Zipkin, AWS X-Ray, Tempo

## Logs vs Metrics vs Traces — When to Use
| Situation | Use |
|-----------|-----|
| "What happened at 10:30?" | Logs |
| "Is error rate increasing?" | Metrics |
| "Why is this request slow?" | Traces |
| "Which service caused the problem?" | Traces → Logs |

---

# 2. OpenTelemetry (OTel) — The Standard

## Tại sao OTel quan trọng
- **Vendor-neutral:** instrument once, export to any backend (Jaeger, Zipkin, Datadog, New Relic)
- **Industry standard:** replaces vendor-specific SDKs
- **Unified:** logs + metrics + traces in one framework

## OTel Architecture

App (OTel SDK) → OTLP protocol → OTel Collector (agent/gateway) → Jaeger (traces) + Prometheus (metrics) + Loki (logs) → Grafana (unified dashboards)

## Spring Boot Integration
- Dependencies: `micrometer-tracing-bridge-otel` + `opentelemetry-exporter-otlp`
- Config: `management.tracing.sampling.probability: 1.0` (dev), 0.1–0.2 for production
- OTLP endpoint: `management.otlp.tracing.endpoint: http://otel-collector:4318/v1/traces`
- Auto-instruments: HTTP requests, Spring MVC, JDBC queries, Kafka messages
- TraceId/SpanId auto-propagated to MDC → appear in log output

---

# 3. Prometheus + Grafana

## Prometheus Architecture
- Pull-based: Prometheus scrapes metrics from `/actuator/prometheus` endpoint
- Storage: local time-series DB (retention configurable, typically 15–30 days)
- Alerting: Prometheus Alertmanager → PagerDuty / Slack / OpsGenie
- **Spring Boot Actuator:** expose `/actuator/prometheus` endpoint (enable in config)

## PromQL Key Patterns
- Rate of requests: `rate(http_server_requests_total[5m])`
- Error rate: `sum(rate(requests{status=~"5.."}[1m])) / sum(rate(requests[1m]))`
- P99 latency: `histogram_quantile(0.99, sum(rate(http_request_duration_seconds_bucket[5m])) by (le))`

## Grafana Dashboard Strategy
- **Service Dashboard:** golden signals per service (latency, error rate, throughput, saturation)
- **Infrastructure Dashboard:** CPU, memory, disk, network per node
- **Business Dashboard:** orders/sec, payment success rate, active users
- **SLO Dashboard:** error budget burn rate

---

# 4. Distributed Tracing Deep-Dive

## Context Propagation
- **HTTP:** W3C Trace Context headers — `traceparent: 00-{traceId}-{parentSpanId}-{flags}`
- **Kafka:** trace context in message headers — Spring Kafka + OTel auto-propagates
- **Baggage:** carry business context (userId, tenantId) alongside trace — available to all services

## Sampling Strategies
- **100% (dev/test):** capture every request — expensive for production
- **Head-based (10–20%):** decide at request start — simple, predictable cost
- **Tail-based:** sample based on outcome (always sample errors/slow requests) — complex but more valuable

---

# 5. SLO / SLA / Error Budget

## Định nghĩa
- **SLI (Service Level Indicator):** metric đo lường — ví dụ: % requests < 500ms
- **SLO (Service Level Objective):** internal target — ví dụ: 99.9% requests < 500ms
- **SLA (Service Level Agreement):** contractual commitment with customers — usually less strict than SLO
- **Error Budget:** 100% - SLO = time/requests you're allowed to fail per period

## Error Budget Example
- SLO: 99.9% availability → Error Budget = 0.1% × 30 days = 43.2 minutes/month allowed downtime
- Budget remaining → can deploy aggressively
- Budget nearly exhausted → freeze releases, focus on reliability

## Error Budget Burn Rate Alerting (better than thresholds)
- Alert if burn rate > 14.4x in last 1 hour (will exhaust budget in 5 days)
- Alert if burn rate > 6x in last 6 hours (similar projection)
- Avoids false positives from momentary spikes

---

# 6. Logging Best Practices

## Structured Logging
- Always log as JSON in production — easily indexed by ELK/Loki
- MDC (Mapped Diagnostic Context): inject contextual fields (userId, requestId, traceId) for all logs in a request
- Never log PII (name, email, phone, address) or credentials

## Log Aggregation Pattern

App (Logback → JSON stdout) → Container runtime logs → Fluentd/Fluent Bit (DaemonSet in K8s) → Elasticsearch / Loki / CloudWatch → Kibana / Grafana / CloudWatch Insights

## What NOT to Log
- PII: names, emails, phone numbers, addresses
- Credentials, tokens, passwords, card numbers
- Sensitive business data unless required for compliance

---

# 7. Alerting Strategy

## Alert Hierarchy
- INFO logs → no alert
- WARN logs → track, review daily
- ERROR logs → alert if rate spikes above baseline
- CRITICAL → page on-call immediately

## Good Alert Characteristics (giảm alert fatigue)
- **Actionable:** alert only when action needed immediately — not for informational events
- **Specific:** identify which service/instance, which metric
- **Prioritized:** P1 (page immediately) vs P2 (notify during business hours)
- **Avoid flapping:** hysteresis — alert when crosses threshold, resolve when well below
- **Anomaly detection:** better than fixed thresholds for variable traffic (Datadog, CloudWatch Anomaly Detection)

---

# 8. Observability-First Design

> Design systems with observability in mind, not as an afterthought

## Checklist for Every Service
- [ ] Structured logging with correlation ID
- [ ] Health check endpoint (`/actuator/health`)
- [ ] Metrics exposed (golden signals at minimum)
- [ ] Distributed tracing integrated (OTel)
- [ ] Alerts on golden signals
- [ ] Runbook linked in alerts

## Observability in System Design Interviews
- "What monitoring would you add?" → golden signals + distributed tracing + error budget
- Mention OTel as standard, Prometheus + Grafana for infra, ELK/Loki for logs
- Always link observability to incident response (MTTR reduction)

---

# 9. Interview Topics

## Câu hỏi hay gặp
- "Làm sao debug slow request qua nhiều microservices?" → Distributed tracing, trace waterfall diagram
- "Logs vs Metrics vs Traces — khi nào dùng cái nào?" → Logs: debugging; Metrics: trends/alerts; Traces: request flow
- "Làm sao setup alerting không bị alert fatigue?" → Error budget burn rate, không threshold mọi thứ
- "OpenTelemetry là gì, tại sao dùng thay Jaeger SDK?" → Vendor neutral, single instrumentation
- "Làm sao trace request qua Kafka?" → Context propagation trong Kafka headers
- "P50 vs P95 vs P99 — tại sao không dùng average?" → Average ẩn outliers; P99 worst-case user experience
- "SLO vs SLA?" → SLO internal target stricter than SLA; error budget drives reliability decisions
