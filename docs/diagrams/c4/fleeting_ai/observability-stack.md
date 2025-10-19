# Observability Stack - Unified monitoring, logging, tracing, and alerting for distributed systems

## Context

MobilityCorp's microservices architecture spans 10+ services, IoT gateways, data pipelines, and ML models across EU regions. Operations teams need:

- Real-time system health visibility (service uptime, latency, error rates)
- Distributed tracing for request flows (API → Booking Service → Payment Service → Kafka)
- Log aggregation for debugging (correlate errors across microservices by request_id)
- Event pipeline monitoring (Kafka lag, dead-letter queues, stream processing backpressure)
- SLA enforcement (P95 latency < 200ms, 99.9% uptime per service)

## Problem

Without centralized observability, troubleshooting distributed failures is reactive and time-consuming:

1. **Blind Spots**: Logs scattered across 50+ containers; no way to correlate "payment failed" with upstream booking service error
2. **Alert Fatigue**: Disparate monitoring tools (CloudWatch, app logs, Kafka metrics) create noise; oncall gets 20 alerts for single root cause
3. **Debugging Latency**: Customer reports "slow booking" but no visibility into which service (API Gateway, Booking, Payment, Kafka) added delay
4. **Data Pipeline Failures**: Stream processing lag goes unnoticed for hours; read models fall behind, showing stale vehicle availability
5. **Compliance Gaps**: GDPR requires audit logs for data access; no centralized way to prove "who accessed vehicle XYZ telemetry?"

## Solution Justification

OpenTelemetry-based observability stack provides unified instrumentation and vendor-neutral telemetry:

- **Three Pillars**: Metrics (Prometheus), Logs (Loki), Traces (Tempo) correlated by trace_id and span_id
- **Auto-Instrumentation**: OTel SDKs inject tracing into HTTP, gRPC, Kafka clients without code changes
- **Vendor Neutrality**: Standard OTLP protocol prevents lock-in; swap backends (Grafana → Datadog) without app redeployment
- **Context Propagation**: trace_id flows across service boundaries (API → microservices → Kafka → consumers)
- **Unified Dashboards**: Grafana combines metrics + logs + traces in single pane of glass

## Use Cases

### 1. End-to-End Request Tracing
- **Scenario**: Customer reports "Booking failed with error 500"
- **Trace Flow**: API Gateway (50ms) → Booking Service (120ms) → Payment Service (timeout 5s) → Kafka (never reached)
- **Root Cause**: Payment Service trace shows "DB connection pool exhausted" → Operations scales DB connections
- **Benefit**: Reduced MTTR from 2 hours (log diving) to 5 minutes (trace analysis)

### 2. Kafka Lag Monitoring & Alerting
- **Metrics**: Prometheus scrapes Kafka Exporter for consumer lag per topic/partition
- **Dashboard**: Grafana panel shows `inventory_updates` consumer lagging by 10k messages (5 minutes behind)
- **Alert**: PagerDuty fires when lag > 1000 for >2 minutes
- **Benefit**: Proactive scaling of stream processors before customers see stale availability

### 3. Distributed Logging for Incident Response
- **Scenario**: Edge Ingest Gateway rejects 30% of vehicle telemetry at 8am spike
- **LogQL Query**: `{service="edge-gateway"} |= "schema_validation_failed" | json | status_code = 400`
- **Result**: 500 logs show vehicles sending old CloudEvents v0.3 format (firmware bug)
- **Benefit**: Correlate error spike with recent firmware rollout; rollback within 10 minutes

### 4. SLA Compliance Dashboard
- **Metrics**: Prometheus histogram tracks P50/P95/P99 latency per service endpoint
- **Dashboard**: Grafana shows Booking Service P95 = 180ms (within SLA), Payment Service P95 = 250ms (breach)
- **Alerting**: Slack notification when SLA breached; auto-creates Jira ticket
- **Benefit**: Automated SLA reports for compliance audits; proactive optimization before customer complaints

### 5. Data Pipeline Health Monitoring
- **Metrics**: Flink exposes checkpointing duration, backpressure, records-in/out per operator
- **Dashboard**: Grafana shows `VehicleTelemetryEnrichment` operator with 80% backpressure (bottleneck)
- **Action**: Ops increases parallelism from 4 to 8; backpressure drops to 10%
- **Benefit**: Prevent pipeline stalls before they impact downstream Gold tables and ML models

## Technology Choice

**Selected**: OpenTelemetry + Prometheus + Grafana + Loki + Tempo

**Rationale**:
- **OpenTelemetry**: CNCF standard for instrumentation; future-proof vendor-neutral observability
  - SDKs for all languages (Java, Python, Go, TypeScript)
  - Auto-instrumentation for frameworks (Spring Boot, FastAPI, Express)
  - Collector acts as telemetry gateway (receive OTLP, export to multiple backends)
- **Prometheus**: Industry-standard for metrics; pull-based scraping with PromQL
  - Service discovery (Kubernetes, Consul) auto-detects targets
  - Alertmanager for deduplication, grouping, routing
- **Grafana**: Unified visualization for metrics, logs, traces
  - Templated dashboards (service health, Kafka lag, SLA compliance)
  - Alerting with PagerDuty/Slack/email integrations
- **Loki**: Log aggregation optimized for Kubernetes (label-based indexing, cost-effective)
- **Tempo**: Distributed tracing backend with Grafana-native integration
- **EU Compliance**: All components self-hosted; logs/traces never leave EU infrastructure
