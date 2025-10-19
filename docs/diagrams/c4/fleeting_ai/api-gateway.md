# API Gateway - Unified entry point for client APIs with security, routing, and observability

## Context

MobilityCorp's customer and staff applications need secure, performant API access to backend microservices. Requirements:

- Authentication/authorization for customers and staff (JWT tokens, OAuth2)
- Rate limiting to prevent abuse (per-user, per-IP throttling)
- Multi-region routing (EU locations with latency-optimized paths)
- API versioning (v1, v2) without breaking existing mobile apps
- Protocol translation (REST → gRPC for internal services)

## Problem

Direct client-to-microservice communication creates operational and security challenges:

1. **Security Gaps**: Each microservice must implement auth, leading to inconsistencies and vulnerabilities
2. **CORS Complexity**: Web apps need CORS policies; managing across 10+ services is error-prone
3. **Rate Limit Sprawl**: Without centralized throttling, malicious users overwhelm individual services
4. **Network Chattiness**: Mobile apps making 5 sequential API calls add 500ms latency (5 × 100ms RTT)
5. **Deployment Coupling**: Backend service IP changes break mobile apps; need stable DNS/endpoints

## Solution Justification

API Gateway decouples clients from backend topology with centralized cross-cutting concerns:

- **Single Entry Point**: Clients call `api.mobilitycorp.com/*`; gateway routes to appropriate microservice
- **Authentication/Authorization**: Verify JWT tokens once at gateway; propagate user_id to downstream services
- **Rate Limiting**: Redis-backed distributed rate limiter (100 req/min per user)
- **Request Aggregation**: GraphQL gateway combines multiple REST calls into single round-trip
- **TLS Termination**: Offload SSL/TLS at gateway; internal services use mutual TLS

## Use Cases

### 1. Customer App - Vehicle Search
- **Request**: `GET /v1/vehicles/available?location=Berlin&type=eBike`
- **Gateway Flow**:
  1. Verify JWT token (customer_id extracted)
  2. Check rate limit (customer_id → 10 requests in last minute, within 100/min limit)
  3. Route to Booking Service (load-balanced across 3 instances)
  4. Add tracing headers (trace_id, span_id for observability)
  5. Return response with CORS headers
- **Benefit**: Customer app doesn't know Booking Service topology; gateway handles service discovery

### 2. Staff App - Fleet Status Dashboard
- **Request**: `GET /v1/fleet/status?location=Amsterdam`
- **Gateway Flow**:
  1. Verify staff JWT (role: operations_manager)
  2. Authorization check (staff role has fleet_read permission)
  3. Route to Fleet Management Service
  4. Cache response (Redis, 30sec TTL) for subsequent requests
- **Benefit**: Role-based access control enforced centrally; 30s cache reduces backend load by 90%

### 3. Mobile App - Multi-Service Aggregation
- **Request** (GraphQL): `{ user { bookings, paymentMethods }, availableVehicles(location: "Berlin") }`
- **Gateway Flow**:
  1. Parse GraphQL query
  2. Parallel requests to Booking Service (bookings), Payment Service (methods), Booking Service (vehicles)
  3. Aggregate responses into single JSON
- **Benefit**: 1 round-trip vs. 3; mobile app latency reduced from 300ms to 120ms

### 4. API Versioning - Backward Compatibility
- **Scenario**: Booking Service adds required `insurance_tier` field; old mobile apps don't send it
- **Solution**: Gateway maintains v1 and v2 routes
  - `/v1/bookings` → Gateway adds default `insurance_tier: 'basic'` before forwarding
  - `/v2/bookings` → Pass through unchanged
- **Benefit**: Zero mobile app downtime; gradual migration to v2

### 5. Rate Limiting & DDoS Protection
- **Scenario**: Bot attempts 10k requests/min to vehicle search API
- **Gateway Action**:
  1. Detect IP exceeds 100 req/min threshold
  2. Return `429 Too Many Requests` without hitting backend
  3. Temporary IP ban (15 min) after 3 violations
- **Benefit**: Backend services protected; legitimate users unaffected

## Technology Choice

**Selected**: Kong Gateway (with Envoy as alternative)

**Rationale**:
- **Kong Gateway**: Open-source API gateway with plugin ecosystem
  - Authentication plugins (JWT, OAuth2, API keys)
  - Rate limiting with Redis backend (distributed, scalable)
  - Request/response transformations (add headers, rewrite paths)
  - Declarative config (YAML) with GitOps support
  - Admin API for dynamic routing updates (no restart)
  - Observability: OpenTelemetry integration, Prometheus metrics
- **Envoy Proxy**: Alternative for service mesh use cases
  - Native gRPC support; ideal if migrating REST → gRPC
  - xDS API for dynamic configuration (control plane)
  - Used by Istio, AWS App Mesh
- **EU Compliance**: Kong self-hosted; all traffic stays in EU infrastructure
