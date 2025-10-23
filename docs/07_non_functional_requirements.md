# Non-Functional Requirements (FURPS+)

## Overview

This document defines quality attributes and non-functional requirements for the MobilityCorp platform using the FURPS+ framework:

- **F**unctionality (security, compliance)
- **U**sability
- **R**eliability
- **P**erformance
- **S**upportability
- **+** Design constraints, implementation requirements, interface requirements, physical requirements

---

## 1. Functionality (F)

### NFR-F-01: Security

#### NFR-F-01.1: Authentication & Authorization

**Description**: All system access must be authenticated and authorized with role-based access control.

**Requirements**:

- Multi-factor authentication (MFA) for staff accounts
- OAuth 2.0 / OpenID Connect for customer authentication
- Role-based access control (RBAC) with principle of least privilege
- Session timeout: 15 minutes for staff, 7 days for customers (with refresh)
- Account lockout after 5 failed login attempts

**Measurement**: 100% of endpoints protected, zero unauthorized access incidents

**Priority**: Critical

---

#### NFR-F-01.2: Data Encryption

**Description**: All sensitive data must be encrypted in transit and at rest.

**Requirements**:

- TLS 1.3 for all client-server communication
- AES-256 encryption for data at rest
- Encrypted database fields for PII (name, email, phone, payment methods)
- End-to-end encryption for customer-staff messaging (future)

**Measurement**: Security audit compliance, zero plaintext PII breaches

**Priority**: Critical

---

#### NFR-F-01.3: Secret Management

**Description**: Credentials, API keys, and secrets must never be hardcoded or exposed.

**Requirements**:

- Centralized secret management (HashiCorp Vault, AWS Secrets Manager)
- Automatic secret rotation every 90 days
- No secrets in code repositories or logs
- Audit trail for all secret access

**Measurement**: Zero hardcoded secrets in codebase, automated secret scanning

**Priority**: Critical

---

### NFR-F-02: Compliance

#### NFR-F-02.1: GDPR Compliance

**Description**: Full compliance with EU General Data Protection Regulation.

**Requirements**:

- User consent management for data collection
- Right to access: Export user data within 30 days
- Right to deletion: Permanently delete user data within 30 days
- Right to rectification: Update incorrect data
- Data minimization: Collect only necessary data
- Data retention policies: Auto-delete inactive accounts after 3 years
- Data residency: EU customer data stored in EU regions

**Measurement**: GDPR audit compliance, zero privacy violations

**Priority**: Critical

---

#### NFR-F-02.2: PCI-DSS Compliance

**Description**: Payment processing must comply with Payment Card Industry Data Security Standard.

**Requirements**:

- No storage of full card numbers or CVV
- Tokenization via payment gateway (Stripe, Adyen)
- PCI-DSS certified hosting environment
- Annual security audits
- Cardholder data encryption

**Measurement**: PCI-DSS Level 1 certification

**Priority**: Critical

---

#### NFR-F-02.3: PSD2 Compliance

**Description**: Payment services must comply with EU Payment Services Directive 2.

**Requirements**:

- Strong Customer Authentication (SCA) for payments >€30
- 3D Secure 2.0 support
- Transaction monitoring and reporting

**Measurement**: PSD2 audit compliance

**Priority**: High

---

### NFR-F-03: Audit & Compliance Logging

**Description**: All sensitive operations must be audited with immutable logs.

**Requirements**:

- Audit logging for: authentication, authorization failures, payment transactions, data access, configuration changes
- Immutable audit logs (append-only, tamper-proof)
- Log retention: 7 years for financial transactions, 2 years for operational logs
- Structured logging (JSON) with correlation IDs

**Measurement**: 100% coverage of sensitive operations, zero log tampering incidents

**Priority**: High

---

## 2. Usability (U)

### NFR-U-01: User Experience

#### NFR-U-01.1: Mobile App Responsiveness

**Description**: Mobile apps must be responsive and intuitive for customer and staff use.

**Requirements**:

- Native iOS and Android apps (Swift, Kotlin)
- <2 second app launch time
- Offline capability for core features (vehicle reservation cache, GPS tracking)
- Touch-friendly UI (minimum 44x44pt touch targets)
- Accessibility: WCAG 2.1 AA compliance

**Measurement**: App Store rating >4.0/5.0, <5% uninstall rate within 7 days

**Priority**: High

---

#### NFR-U-01.2: Multi-Language Support

**Description**: Customer-facing interfaces must support multiple EU languages.

**Requirements**:

- Minimum 7 languages: EN, DE, FR, ES, IT, NL, PT
- User selectable language in settings
- Localized content (dates, times, currency formats)
- Right-to-left (RTL) support (future for Arabic)
- Translation coverage >95% for core flows

**Measurement**: <5% user complaints about language issues

**Priority**: High

---

#### NFR-U-01.3: Accessibility

**Description**: Platform must be accessible to users with disabilities.

**Requirements**:

- WCAG 2.1 Level AA compliance
- Screen reader support (iOS VoiceOver, Android TalkBack)
- High contrast mode
- Keyboard navigation for web interfaces
- Text size adjustability

**Measurement**: Accessibility audit score >90%, zero ADA complaints

**Priority**: Medium

---

### NFR-U-02: Onboarding & Learning

#### NFR-U-02.1: Quick Onboarding

**Description**: New users must be able to complete their first rental quickly.

**Requirements**:

- Account creation <3 minutes
- First rental <10 minutes from app download (for returning customers)
- Interactive tutorial for first-time users
- Contextual help and tooltips

**Measurement**: >80% first-session conversion rate, <20% tutorial abandonment

**Priority**: High

---

### NFR-U-03: Error Handling & Feedback

**Description**: System must provide clear, actionable feedback for errors and issues.

**Requirements**:

- User-friendly error messages (no technical jargon)
- Suggested actions for resolution
- Multi-language error messages
- Escalation path to customer support
- Inline validation for forms

**Measurement**: <5% customer support contacts due to unclear errors

**Priority**: Medium

---

## 3. Reliability (R)

### NFR-R-01: Availability

#### NFR-R-01.1: Service Uptime

**Description**: Platform must maintain high availability for customer-facing services.

**Requirements**:

- 99.95% uptime for mobile apps and APIs (21.9 minutes downtime/month)
- 99.9% uptime for internal operations tools
- Zero unplanned downtime during peak hours (7-9am, 5-7pm)
- Planned maintenance in off-peak hours with advance notice

**Measurement**: Uptime monitoring, SLA compliance reports

**Priority**: Critical

---

#### NFR-R-01.2: Fault Tolerance

**Description**: System must gracefully handle component failures without total outage.

**Requirements**:

- No single point of failure (redundant infrastructure)
- Automatic failover for critical services (<30 seconds)
- Circuit breakers for cascading failure prevention
- Graceful degradation (read-only mode if write services fail)

**Measurement**: Zero total outages, <1 partial outage per quarter

**Priority**: Critical

---

### NFR-R-02: Data Integrity

#### NFR-R-02.1: No Data Loss

**Description**: System must not lose customer, booking, or payment data under any circumstances.

**Requirements**:

- Exactly-once delivery semantics for critical events (bookings, payments)
- Database replication with automatic failover
- Point-in-time recovery capability (1-hour granularity)
- Automated backups every 4 hours, retained for 30 days
- Cross-region backup replication

**Measurement**: Zero data loss incidents, successful quarterly DR tests

**Priority**: Critical

---

#### NFR-R-02.2: Consistency

**Description**: System must maintain data consistency across distributed components.

**Requirements**:

- Strong consistency for bookings, payments, vehicle locks
- Eventual consistency acceptable for analytics, dashboards (max 5-minute lag)
- Distributed transaction support (Saga pattern) for multi-step operations
- Idempotency for all write operations (retry-safe)

**Measurement**: Zero double-booking incidents, zero payment inconsistencies

**Priority**: Critical

---

### NFR-R-03: Disaster Recovery

**Description**: System must recover from catastrophic failures with minimal data loss.

**Requirements**:

- **RTO (Recovery Time Objective)**: <1 hour for critical services
- **RPO (Recovery Point Objective)**: <5 minutes data loss acceptable
- Multi-region deployment (active-active or active-passive)
- Automated failover to backup region
- Quarterly disaster recovery drills

**Measurement**: Successful DR tests, RTO/RPO compliance

**Priority**: High

---

## 4. Performance (P)

### NFR-P-01: Response Time

#### NFR-P-01.1: API Latency

**Description**: APIs must respond quickly to provide good user experience.

**Requirements**:

- Vehicle search API: <100ms (p95), <200ms (p99)
- Booking creation: <500ms (p95), <1s (p99)
- Vehicle unlock: <2s (p95), <5s (p99)
- Profile updates: <200ms (p95), <500ms (p99)
- Dashboard queries: <1s (p95), <3s (p99)

**Measurement**: Application Performance Monitoring (APM), SLI/SLO tracking

**Priority**: High

---

#### NFR-P-01.2: End-to-End Latency

**Description**: Real-time events must flow through the system with low latency.

**Requirements**:

- GPS position update to dashboard: <5 seconds (p95)
- Booking event to operations dashboard: <2 seconds (p95)
- Battery alert to field tech notification: <30 seconds (p95)
- ML prediction request: <100ms (p95) for real-time, <5 min for batch

**Measurement**: Distributed tracing, end-to-end latency dashboards

**Priority**: High

---

### NFR-P-02: Throughput

#### NFR-P-02.1: Event Ingestion

**Description**: System must handle high volume of telemetry and domain events.

**Requirements**:

- Vehicle telemetry: 10,000+ events/second sustained
- Peak load (commute hours): 20,000+ events/second
- Booking events: 500+ bookings/minute
- Search queries: 1,000+ searches/second

**Measurement**: Load testing, production throughput monitoring

**Priority**: High

---

#### NFR-P-02.2: Concurrent Users

**Description**: System must support thousands of concurrent users.

**Requirements**:

- 50,000+ concurrent mobile app users
- 10,000+ active rentals simultaneously
- 100+ concurrent operations staff
- Load testing to 2x expected peak load

**Measurement**: Load testing reports, production concurrency metrics

**Priority**: High

---

### NFR-P-03: Resource Efficiency

#### NFR-P-03.1: Infrastructure Costs

**Description**: System must be cost-efficient to operate at scale.

**Requirements**:

- Auto-scaling to handle peak loads, scale down during off-peak
- Serverless for infrequent workloads (batch jobs, reports)
- Spot instances for non-critical workloads
- Cost monitoring and budget alerts
- Target: <€0.50 infrastructure cost per vehicle per month

**Measurement**: Monthly infrastructure costs, cost-per-transaction

**Priority**: Medium

---

#### NFR-P-03.2: Battery Optimization

**Description**: Mobile apps must be battery-efficient for user devices.

**Requirements**:

- GPS tracking only during active rentals
- Background tasks minimized
- Efficient network usage (batching, compression)
- Target: <5% battery drain per hour during active rental

**Measurement**: Battery usage analytics, user feedback

**Priority**: Medium

---

## 5. Supportability (S)

### NFR-S-01: Observability

#### NFR-S-01.1: Monitoring & Alerting

**Description**: System must be observable with comprehensive metrics and alerting.

**Requirements**:

- **Metrics**: Prometheus for time-series metrics (RED: Rate, Errors, Duration)
- **Logs**: Centralized structured logging (ELK, CloudWatch)
- **Traces**: Distributed tracing (OpenTelemetry, Jaeger)
- **Dashboards**: Grafana for real-time visualization
- **Alerting**: PagerDuty for on-call escalation
- Alert response time: Critical <5 min, High <15 min

**Measurement**: MTTD (Mean Time to Detect) <5 minutes, MTTR (Mean Time to Resolve) <30 min

**Priority**: Critical

---

#### NFR-S-01.2: Health Checks

**Description**: All services must expose health check endpoints.

**Requirements**:

- Liveness probe: Is service running?
- Readiness probe: Is service ready to serve traffic?
- Dependency checks: Database, cache, external APIs
- Health dashboard with service dependency map

**Measurement**: 100% service health check coverage

**Priority**: High

---

### NFR-S-02: Maintainability

#### NFR-S-02.1: Code Quality

**Description**: Codebase must be maintainable with high quality standards.

**Requirements**:

- Code coverage: >80% for critical paths, >60% overall
- Static analysis: Zero critical issues (SonarQube)
- Code review: 100% of changes peer-reviewed
- Documentation: API docs (OpenAPI/Swagger), architecture docs (C4 model)
- Coding standards: Language-specific style guides enforced

**Measurement**: Code quality metrics, technical debt ratio <5%

**Priority**: High

---

#### NFR-S-02.2: Dependency Management

**Description**: Third-party dependencies must be managed and kept up-to-date.

**Requirements**:

- Automated dependency scanning (Dependabot, Snyk)
- Security vulnerability alerts
- Quarterly dependency updates
- License compliance checks

**Measurement**: Zero critical security vulnerabilities, <10 high-severity issues

**Priority**: High

---

### NFR-S-03: Operability

#### NFR-S-03.1: Deployment

**Description**: Deployments must be automated, safe, and fast.

**Requirements**:

- CI/CD pipeline for all services
- Automated testing (unit, integration, E2E)
- Blue-green or canary deployments (zero downtime)
- Automated rollback on failure
- Deployment time: <10 minutes for microservices

**Measurement**: Deployment frequency (target: multiple per day), deployment failure rate <5%

**Priority**: High

---

#### NFR-S-03.2: Scalability

**Description**: System must scale horizontally to handle growth.

**Requirements**:

- Stateless services (scale out easily)
- Auto-scaling based on CPU, memory, request rate
- Database read replicas for read-heavy workloads
- Caching (Redis) for frequently accessed data
- CDN for static assets

**Measurement**: Auto-scaling within 2 minutes, handle 10x traffic with scaling

**Priority**: High

---

### NFR-S-04: Documentation

**Description**: System must be well-documented for developers, operations, and users.

**Requirements**:

- **Architecture**: C4 diagrams, ADRs, data flow diagrams
- **API**: OpenAPI/Swagger specs, code examples
- **Operations**: Runbooks, incident response procedures
- **User**: In-app help, FAQ, knowledge base
- Documentation freshness: Updated within 1 sprint of changes

**Measurement**: Documentation coverage >90%, <5% stale docs

**Priority**: Medium

---

## 6. Design Constraints (+)

### NFR-DC-01: Technology Stack

**Description**: Specific technologies mandated or preferred for the platform.

**Requirements**:

- **Cloud Provider**: AWS, GCP, or Azure (cloud-agnostic where possible)
- **Container Orchestration**: Kubernetes
- **Event Streaming**: Apache Kafka or AWS Kinesis
- **Databases**: PostgreSQL (relational), Redis (cache), InfluxDB (time-series)
- **ML Platform**: AWS SageMaker, Azure ML, or open-source (Kubeflow)
- **Mobile**: Native iOS (Swift), Android (Kotlin)

**Priority**: High

---

### NFR-DC-02: Data Residency

**Description**: Customer data must be stored in EU regions for GDPR compliance.

**Requirements**:

- EU customers: Data stored in EU regions (eu-west-1, eu-central-1)
- Cross-region replication only within EU
- Data transfer outside EU requires explicit consent

**Priority**: Critical

---

### NFR-DC-03: Vendor Neutrality

**Description**: Minimize vendor lock-in for critical components, especially AI.

**Requirements**:

- Use open standards (CloudEvents, OpenTelemetry)
- Abstract vendor-specific APIs (adapter pattern)
- Support multiple AI providers (OpenAI, Anthropic, AWS Bedrock)
- Infrastructure as Code for portability (Terraform)

**Priority**: High

---

## 7. Implementation Requirements (+)

### NFR-IR-01: Development Standards

**Description**: Software development must follow best practices.

**Requirements**:

- **Version Control**: Git with trunk-based development or Gitflow
- **Branching**: Feature branches, main protected
- **Code Review**: Minimum 2 approvals for critical services
- **Testing**: Unit tests required, integration tests for APIs, E2E for critical paths
- **CI/CD**: Automated build, test, deploy pipeline

**Priority**: High

---

### NFR-IR-02: AI Model Governance

**Description**: AI models must be developed and deployed with governance controls.

**Requirements**:

- Model versioning and registry
- A/B testing for model comparison
- Gradual rollout (canary) for new models
- Rollback capability within 5 minutes
- Explainability for predictions (SHAP, LIME)
- Bias detection and mitigation

**Priority**: High

---

## 8. Interface Requirements (+)

### NFR-IF-01: API Standards

**Description**: All APIs must follow consistent standards.

**Requirements**:

- RESTful design (HTTP verbs, status codes, pagination)
- API versioning (URL path: `/v1/vehicles`)
- Rate limiting (per-user, per-IP)
- Standard error format (RFC 7807 Problem Details)
- CORS support for web clients
- OpenAPI 3.0 specification

**Priority**: High

---

### NFR-IF-02: Event Standards

**Description**: All events must follow CloudEvents standard.

**Requirements**:

- CloudEvents v1.0 envelope
- Standardized event types (domain.entity.action)
- Schema validation via Schema Registry
- Backward-compatible schema evolution

**Priority**: High

---

## 9. Physical Requirements (+)

### NFR-PR-01: Infrastructure

**Description**: Physical and cloud infrastructure requirements.

**Requirements**:

- Multi-AZ deployment for high availability
- Multi-region for disaster recovery
- CDN for global content delivery
- Edge locations for low-latency APIs (future)

**Priority**: High

---

### NFR-PR-02: Vehicle Hardware

**Description**: Vehicle hardware requirements for platform integration.

**Requirements**:

- GPS module with 10m accuracy
- 4G/5G cellular connectivity
- NFC reader for customer smartphones
- IoT platform integration (MQTT over TLS)
- Battery telemetry sensors

**Priority**: Critical

---

## Non-Functional Requirements Summary

| Category | Sub-Category | Count | Critical | High | Medium |
|----------|-------------|-------|----------|------|--------|
| **Functionality** | Security, Compliance, Audit | 6 | 5 | 2 | 0 |
| **Usability** | UX, Onboarding, Errors | 5 | 0 | 3 | 2 |
| **Reliability** | Availability, Data Integrity, DR | 5 | 5 | 1 | 0 |
| **Performance** | Latency, Throughput, Efficiency | 6 | 0 | 4 | 2 |
| **Supportability** | Observability, Maintainability, Docs | 8 | 1 | 6 | 1 |
| **Design Constraints** | Tech Stack, Data Residency, Vendor | 3 | 1 | 2 | 0 |
| **Implementation** | Dev Standards, AI Governance | 2 | 0 | 2 | 0 |
| **Interface** | API Standards, Event Standards | 2 | 0 | 2 | 0 |
| **Physical** | Infrastructure, Hardware | 2 | 1 | 1 | 0 |
| **TOTAL** | | **39** | **13** | **23** | **5** |

---

## Architecture Characteristics Mapping

Based on "Fundamentals of Software Architecture" by Richards & Ford:

| Architectural Characteristic | NFR Reference | Priority | Trade-offs |
|------------------------------|---------------|----------|------------|
| **Availability** | NFR-R-01 | Critical | Cost (redundancy) |
| **Reliability** | NFR-R-02 | Critical | Complexity |
| **Performance** | NFR-P-01, NFR-P-02 | High | Cost, complexity |
| **Scalability** | NFR-S-03.2 | High | Cost, complexity |
| **Security** | NFR-F-01 | Critical | Performance, usability |
| **Observability** | NFR-S-01 | Critical | Storage cost |
| **Deployability** | NFR-S-03.1 | High | Tooling investment |
| **Testability** | NFR-S-02.1 | High | Development time |
| **Maintainability** | NFR-S-02 | High | Development time |
| **Compliance** | NFR-F-02 | Critical | Complexity, cost |

---

## Document Control

- **Version**: 1.0
- **Last Updated**: 2025-10-20
- **Owner**: BRINUS Team (O'Reilly Q4 2025 Kata)
- **Status**: Draft
- **References**:
  - [05_functional_requirements.md](05_functional_requirements.md)
  - [06_technical_requirements.md](06_technical_requirements.md)
  - FURPS+ Model
  - "Fundamentals of Software Architecture" (Richards & Ford)
