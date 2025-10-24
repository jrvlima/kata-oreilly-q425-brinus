# ADR-002: Event-Driven CV Verification with RAG API (Qdrant) for Returns

## Status

Accepted

## Context

MobilityCorp needs scalable, reliable verification of returns and condition photos without blocking users at the bay:

- **Operational risk**: vehicles not docked/plugged, incorrect bay returns.
    
- **Disputes**: damage “before vs after” disagreements.
    
- **Manual workload**: photo reviews consume staff time.
    
- **UX**: users want quick completion feedback, not long waits.
    

We operate across multiple cities, with thousands of daily rentals. We must be **cost-conscious**, **move fast**, and **avoid vendor lock-in**.

## Decision

Adopt an **event-driven**, two-tier verification flow:

1. **Tier-0 (synchronous, sub-second)**  
    Fast deterministic checks at return submission (booking state, geofence/bay match, charger telemetry if available, photo sanity). If OK → **Provisional Accept** (user journey ends with success).
    
2. **Tier-1 (asynchronous CV)**  
    Photos flow through Kafka → **Preprocessing** (resize, blur faces/plates, compress) → **RAG-powered CV Decision** via **RAG API** (multimodal LLM through LiteLLM + **Qdrant** for few-shot retrieval). Emit `CVDecision { PASS | REVIEW | FAIL }` events for Booking/Return services to finalize.
    

We will use **RAG + multimodal LLMs** (no custom model training) for the CV step and **Qdrant** as the vector database for “similar past cases” retrieval. The **RAG API** hosts the **CV Decision Endpoint** (`POST /cv/decision`) and also serves other RAG use-cases.

## Rationale

### Why RAG + Multimodal (vs custom CV training)

- **Speed to market**: prompts + retrieval over pre-trained vision LLMs; no ML team required.
    
- **Maintainability**: improve prompts and retrieval corpus; no retraining cycles.
    
- **Provider flexibility**: LiteLLM proxy routes to OpenAI/Anthropic/Azure/Gemini; switch by config.
    
- **Explainability**: return reasons + reference cases; easier dispute handling.
    

### Why **event-driven** (vs Photo→Booking gRPC)

- **Loose coupling** across bounded contexts (Photo, Booking/Return).
    
- **Backpressure & retries** with Kafka; DLQs and reprocessing are straightforward.
    
- **Auditability**: durable trail from `PhotoStored` → `CVDecision`.
    
- **UX**: provisional accept avoids bay-side waiting; finalization happens in background.
    

### Why **Qdrant** for the vector store

- **Hybrid retrieval** (dense + lexical) and robust filtering for case selection.
    
- **Deployment flexibility** (managed, hybrid, or self-hosted) to meet data-residency and cost needs.
    
- **Simple APIs** (REST/gRPC) and solid client libraries for Python/TypeScript.
    
- **No vendor tie-in**: open-source core provides an escape hatch.


## Architecture (high level)

```
Customer App → API Gateway → Photo Controller (202 Accepted; correlation_id)
                 ↓
             Photo Service (persist metadata + Outbox)
                 ↓
     Kafka topic: cv.photo.stored  ──►  Preprocessing (Flink/ksqlDB/OpenCV/Sharp)
                                           • Blur faces/plates; resize/compress
                                           • Write Silver artifact to Object Store
                                      ──►  Kafka: cv.photo.preprocessed
                                                     ↓
                            CV Orchestrator (Flink/Kafka Streams)
                                       ──►  RAG API: /cv/decision
                                              • Few-shot retrieval (Qdrant)
                                              • Multimodal LLM via LiteLLM (temp=0)
                                       ──►  Kafka: cv.photo.decision
                                                     ↓
                     Booking/Return Services (consume + finalize state)

```


### Core components & responsibilities

- **Photo Service**: persists metadata, emits `PhotoStored` via transactional outbox.
    
- **Preprocessing**: blur/resize/AV-scan → write to **Object Store (Bronze/Silver)**; emit `PhotoPreprocessed`.
    
- **RAG API** (FastAPI):
    
    - **CV Decision Endpoint** (`POST /cv/decision`): given `s3_uri_silver`, booking/bay context, thresholds, returns structured JSON `{decision, confidence, reasons[], prompt_version, provider}`.
        
    - Integrations:
        
        - **Qdrant** for “K similar cases” retrieval (few-shot grounding).
            
        - **LiteLLM Proxy** for provider-abstracted multimodal inference.
            
- **Stream jobs**: CV Orchestrator calls the endpoint and publishes `CVDecision` events.
    
- **Booking/Return Services**: consume `CVDecision`, idempotently advance booking (`ProvisionalAccepted → FinalAccepted/FinalRejected/Review`).
    
- **Airflow**: nightly synthetic evals, drift checks, cost monitors, backfills (re-scoring with new `prompt_version`).
    

## API Contracts (concise)

**POST** `/cv/decision`  
Request:

```json
{
  "photo_id": "ph_123",
  "booking_id": "bk_456",
  "s3_uri_silver": "s3://photos/silver/2025/10/23/ph_123.jpg",
  "bay_id": "A12",
  "vehicle_type": "car|van|bike|scooter",
  "captured_at": "2025-10-23T21:05:14Z",
  "geo": {"lat": -19.92, "lon": -43.94},
  "case_type": "return_proof|docking|damage_delta",
  "thresholds": {"pass": 0.90, "review": 0.70},
  "prompt_version": "v1.3",
  "correlation_id": "corr-abc-789"
}

```
Response:

```json
{
  "photo_id": "ph_123",
  "booking_id": "bk_456",
  "decision": "PASS|FAIL|REVIEW",
  "confidence": 0.93,
  "reasons": ["Vehicle in bay A12", "Charger connected"],
  "provider": "anthropic|openai|azure|gemini",
  "prompt_version": "v1.3",
  "references": [{"type": "fewshot", "id": "case_2025-09-01_bayA12_1"}],
  "latency_ms": 1820
}

```


Headers: `X-Correlation-Id`, `Idempotency-Key: photo_id`.  
Policy: **temperature=0**, JSON-only, timeouts 3–5s with retries/backoff (or DLQ).

## Event Model (summary)

- `cv.photo.stored` (key = booking_id) → metadata + URIs (no bytes).
    
- `cv.photo.preprocessed` → includes `s3_uri_silver`, checksums.
    
- `cv.photo.decision` → `{decision, confidence, reasons[], provider, prompt_version}`.
    
- `cv.photo.human_override` → staff approvals/rejections; feeds evaluation.
    

Partition key: **booking_id** for co-partitioning across topics.

## Data & Privacy

- **Images**: bytes in **Object Store (Bronze/Silver)**; **never** on events.
    
- **PII**: faces/plates blurred at preprocessing; least-privilege signed URLs.
    
- **Retention**: raw (short, e.g., 90 days), processed as policy allows; GDPR erasure via tags.
    
- **Decision history**: OLTP/OLAP tables for audits, analytics, dispute resolution.


| Option                                 | Pros                                       | Cons                                                    | Verdict    |
| -------------------------------------- | ------------------------------------------ | ------------------------------------------------------- | ---------- |
| **Sync CV via gRPC (Photo→Booking)**   | Immediate answer                           | Tight coupling, poor resilience, blocks UX              | Rejected   |
| **Custom CV models (YOLO/Mask R-CNN)** | Task-specific accuracy                     | ML team, data labeling, GPUs, long timeline             | Rejected   |
| **Vector DB: Weaviate Cloud**          | Built-in multimodal modules                | Different pricing/ops model; not required for our stack | Not chosen |
| **Vector DB: Qdrant**                  | Hybrid retrieval, flexible deployment, OSS | Requires embedding pipeline (which we have)             | **Chosen** |


## Consequences

### Positive

- **Fast UX**: provisional accept in ≤1–2s; user leaves the bay immediately.
    
- **Scale & reliability**: Kafka + stream processors, DLQs, replay.
    
- **Explainability**: structured decisions, reasons, few-shot references.
    
- **Flexibility**: swap LLM providers; adjust prompts/thresholds without retraining.
    
- **Auditability**: durable event trail + decision history.
    

### Negative / Risks

- **Asynchrony**: final decision arrives later; requires clear user messaging.
    
- **LLM variability**: mitigated with temp=0, structured JSON, prompts versioned.
    
- **Costs**: per-image LLM costs—mitigate with Tier-0 filters, caching, and provider switching.
    
- **Complexity**: event topics, orchestration, and monitoring require mature ops.
    

## Monitoring & Validation

- **Dashboards**: pass rate, review rate, provider latency, cost/photo, override rate.
    
- **Consistency**: same input → same decision checks; alert on drops.
    
- **Synthetic tests** (daily): curated PASS/FAIL/edge sets; block deploys on regression.
    
- **Backfills**: Airflow re-scores historical photos when `prompt_version` changes; emit vNext decisions.
    

## Implementation Notes

- **Transactional Outbox** in Photo Service.
    
- **Idempotency** across consumers (`photo_id`, `prompt_version`).
    
- **HPA & quotas**: run `rag-api-chat` and `rag-api-cv` as **separate deployments** behind one Service; independent autoscaling and budgets.
    
- **Security**: signed, time-bound URIs; do not log image content; scrub PII from traces.
    

## Related Decisions

- ADR-001: Open Mobility Foundation Standards (MDS/CDS)
    
- ADR-003: LLM Provider Abstraction via LiteLLM
    
- ADR-004: Privacy & GDPR for Photo Verification
    
- ADR-005: Vector DB Selection (Qdrant)
    
- ADR-006: Observability & SLOs
    

## Notes

**Date**: Oct 24, 2025  
**Authors**: BRINUS Team (Rafael, Prashant, Khaled, João Vinagre)