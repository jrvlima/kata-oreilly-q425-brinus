# Constraints & Assumptions

## Overview

This document defines hard constraints (non-negotiable limitations) and key assumptions that shape the architectural design of the MobilityCorp platform.

---

## 1. Business Constraints

### BC-01: Regulatory Compliance

**Type**: Hard Constraint

**Description**: Must comply with all applicable EU regulations.

**Specific Requirements**:

- **GDPR**: Full compliance with EU data protection regulation
- **PSD2**: Payment Services Directive compliance for transactions
- **eIDAS**: Electronic identification and trust services
- **Local Regulations**: City-specific parking, speed limits, vehicle operation rules
- **Insurance**: Per-country vehicle insurance and liability coverage
- **Accessibility**: EN 301 549 compliance for digital services

**Impact on Architecture**:

- Data residency constraints (EU-only storage for EU customers)
- User consent management system
- Right-to-deletion and data export capabilities
- Audit logging and reporting
- Multi-tenancy per country/city for regulatory variations

---

### BC-02: Multi-Location Operations

**Type**: Hard Constraint

**Description**: Platform must support operations across multiple EU cities and countries simultaneously.

**Specific Requirements**:

- **Multi-Language**: Minimum 7 EU languages (EN, DE, FR, ES, IT, NL, PT)
- **Multi-Currency**: EUR primary + local currencies
- **Multi-Timezone**: CET, WET, EET time zones
- **Location-Specific Config**: Pricing, vehicle types, parking rules per city

**Impact on Architecture**:

- Localization service for language/currency
- Configuration management per location
- Geographic data partitioning
- Multi-region deployment for latency

---

### BC-03: Operating Budget

**Type**: Soft Constraint

**Description**: Limited budget for technology infrastructure and AI services.

**Specific Requirements**:

- Target infrastructure cost: <€0.50 per vehicle per month
- Prefer open-source over proprietary when possible
- Auto-scaling to optimize costs
- Minimize vendor lock-in for pricing flexibility

**Impact on Architecture**:

- Cloud cost optimization (spot instances, auto-scaling)
- Serverless for infrequent workloads
- Open-source ML frameworks preferred
- Cost monitoring and budget alerts

---

### BC-04: Time to Market

**Type**: Soft Constraint

**Description**: Competitive pressure requires rapid delivery of AI-driven features.

**Specific Requirements**:

- Phase 1 (MVP): 6 months - Core booking and operations
- Phase 2 (AI Basic): 9 months - Demand prediction and battery forecasting
- Phase 3 (AI Advanced): 12 months - Personalization and optimization

**Impact on Architecture**:

- Start with managed AI services (faster) vs. custom models (slower)
- Iterative delivery with continuous improvement
- Feature flags for gradual rollout
- Modular architecture for parallel development

---

## 2. Technical Constraints

### TC-01: Vehicle Hardware

**Type**: Hard Constraint

**Description**: All vehicles must be equipped with specific hardware capabilities.

**Specific Requirements**:

- **GPS**: All vehicles GPS-enabled (10m accuracy minimum)
- **Connectivity**: 4G/5G cellular for real-time communication
- **NFC**: NFC reader for smartphone-based unlocking
- **Battery Sensors**: Real-time battery level reporting
- **IoT Protocol**: MQTT over TLS for vehicle-to-cloud communication

**Impact on Architecture**:

- Vehicle telemetry ingestion platform (MQTT broker)
- GPS data processing and storage (time-series DB)
- NFC authentication service
- Offline capability for poor connectivity scenarios

---

### TC-02: Customer Smartphones

**Type**: Hard Constraint

**Description**: Customers must use NFC-capable smartphones.

**Specific Requirements**:

- **NFC**: Required for vehicle unlock
- **GPS**: For vehicle search and navigation
- **Camera**: For photo verification of vehicle return
- **OS**: iOS 14+ or Android 8+ (90% coverage)
- **Connectivity**: 4G/5G for real-time app functionality

**Impact on Architecture**:

- Native mobile apps (iOS Swift, Android Kotlin)
- NFC integration for unlock
- Offline mode for poor connectivity
- Photo upload and processing service

---

### TC-03: Designated Parking Spots

**Type**: Hard Constraint

**Description**: Vehicles must be picked up and returned to designated parking spots.

**Specific Requirements**:

- Photo proof of parking required
- GPS verification within 50 meters of parking spot
- Parking spot inventory management
- Penalties for non-compliance

**Impact on Architecture**:

- Parking spot database with GPS coordinates
- Photo verification service
- Geo-fencing and distance calculations
- Violation detection and penalty system

---

### TC-04: Charging Infrastructure

**Type**: Hard Constraint

**Description**: Electric vehicles must be charged upon return.

**Specific Requirements**:

- Customers must plug in vehicles at designated charging stations
- Photo proof of connection required
- Charging session tracking (start, duration, energy)
- Battery swap capability for staff

**Impact on Architecture**:

- Charging station management system
- Charging session tracking and billing
- IoT integration for charger status
- Battery swap workflow for field staff

---

### TC-05: Staff Field Equipment

**Type**: Hard Constraint

**Description**: Operations staff use vans equipped for redistribution and battery swaps.

**Specific Requirements**:

- Vans with spare battery inventory
- Staff smartphones with field operations app
- Tools for basic vehicle maintenance
- NFC access to any vehicle (staff privilege)

**Impact on Architecture**:

- Field operations mobile app
- Task assignment and routing system
- Inventory management for spare batteries
- Staff authentication and authorization (elevated privileges)

---

## 3. AI & ML Constraints

### AIC-01: AI Provider Uncertainty

**Type**: Hard Constraint

**Description**: AI technology landscape is rapidly changing with provider risks.

**Specific Requirements**:

- Avoid vendor lock-in to single AI provider
- Plan for price changes or service shutdowns
- Model portability (use standard frameworks)
- Abstract AI provider APIs (adapter pattern)

**Impact on Architecture**:

- Multi-provider AI strategy (OpenAI, Anthropic, AWS Bedrock)
- Model-agnostic feature store
- Abstraction layer for AI services
- Hybrid approach: Managed services + self-hosted models

---

### AIC-02: Non-Deterministic Outputs

**Type**: Hard Constraint

**Description**: GenAI outputs are non-deterministic and require validation.

**Specific Requirements**:

- Cannot rely solely on AI for critical decisions
- Human-in-the-loop for high-stakes operations
- Confidence scores and uncertainty quantification
- Fallback to rule-based systems if AI fails
- Continuous monitoring for model drift

**Impact on Architecture**:

- Hybrid AI + rules approach
- Human approval workflows for critical recommendations
- Model monitoring and observability platform
- A/B testing framework for model validation
- Automated rollback on performance degradation

---

### AIC-03: Cold Start Problem

**Type**: Hard Constraint

**Description**: AI models require historical data; new locations lack training data.

**Specific Requirements**:

- Bootstrap models with generic patterns
- Transfer learning from existing locations
- Gradual transition from rules to AI as data accumulates
- Minimum 3 months data before full AI deployment

**Impact on Architecture**:

- Rule-based fallback for new locations
- Transfer learning capability
- Progressive AI feature rollout per location
- Data collection prioritization for new cities

---

### AIC-04: Model Training Compute

**Type**: Soft Constraint

**Description**: Limited budget for expensive model training compute.

**Specific Requirements**:

- Prefer lightweight models (XGBoost, linear models) over deep learning where possible
- Use managed ML services (AWS SageMaker) to reduce ops overhead
- Spot instances for training jobs (cost savings)
- Schedule training during off-peak hours

**Impact on Architecture**:

- Hybrid model strategy (simple + complex models)
- Cloud-native ML platform (managed services)
- Automated training pipeline with cost optimization
- Model efficiency as key metric

---

## 4. Data Constraints

### DC-01: Data Retention

**Type**: Hard Constraint

**Description**: Legal and compliance requirements for data retention.

**Specific Requirements**:

- **Financial Data**: 7 years (tax compliance)
- **User Data**: 3 years inactive, then delete (GDPR)
- **Operational Logs**: 2 years
- **Vehicle Telemetry**: 1 year hot storage, archive beyond
- **ML Training Data**: Indefinite (anonymized)

**Impact on Architecture**:

- Automated data lifecycle policies
- Archival to cold storage (S3 Glacier)
- Anonymization pipeline for long-term analytics
- Scheduled data deletion jobs

---

### DC-02: Data Privacy

**Type**: Hard Constraint

**Description**: GDPR and privacy regulations restrict data usage.

**Specific Requirements**:

- No PII in logs or analytics (masking required)
- User consent for data collection and AI usage
- Right-to-deletion within 30 days
- Data minimization (collect only necessary data)
- No cross-border data transfer outside EU

**Impact on Architecture**:

- PII detection and masking in logs
- Consent management service
- Anonymization/pseudonymization for analytics
- EU-only data residency
- Data deletion workflow

---

### DC-03: Data Quality

**Type**: Soft Constraint

**Description**: AI models require high-quality training data.

**Specific Requirements**:

- GPS accuracy validation (discard <50m accuracy)
- Battery level outlier detection
- Data completeness checks
- Schema validation for events
- Handling of missing or corrupted data

**Impact on Architecture**:

- Data quality monitoring and alerting
- Data validation pipeline
- Outlier detection and handling
- Schema evolution management

---

## 5. Operational Constraints

### OC-01: 24/7 Operations

**Type**: Hard Constraint

**Description**: Platform operates 24/7 with rentals possible at any time.

**Specific Requirements**:

- No planned downtime windows (or minimal, off-peak)
- On-call engineering support
- Automated incident response
- Self-healing capabilities where possible

**Impact on Architecture**:

- Zero-downtime deployments (blue-green, canary)
- Auto-scaling for demand fluctuations
- Health checks and automatic restarts
- PagerDuty integration for critical alerts

---

### OC-02: Manual Operations

**Type**: Soft Constraint (AI improves this over time)

**Description**: Current reliance on manual staff operations.

**Specific Requirements**:

- Staff manually redistribute vehicles
- Manual battery swaps
- Customer support handles issues
- No autonomous vehicle movement (for now)

**Impact on Architecture**:

- AI recommendations with human execution
- Task assignment and tracking for staff
- Gradual automation as AI matures
- Support tools and dashboards for staff

---

## 6. Security Constraints

### SC-01: No Sensitive Data in Logs

**Type**: Hard Constraint

**Description**: PII and payment data must never appear in logs.

**Specific Requirements**:

- Automated PII detection and masking
- Tokenized payment data (no card numbers)
- Sanitized error messages
- Audit trail for data access (not in logs)

**Impact on Architecture**:

- Log masking library/middleware
- Structured logging with redaction rules
- Separate audit log service
- Testing for PII leakage

---

### SC-02: Encrypted Communication

**Type**: Hard Constraint

**Description**: All communication must be encrypted.

**Specific Requirements**:

- TLS 1.3 for client-server
- MQTT over TLS for vehicles
- Database connections encrypted
- No plaintext credentials

**Impact on Architecture**:

- TLS termination at load balancer
- Certificate management (Let's Encrypt, ACM)
- Encrypted database connections
- Secret management (Vault, Secrets Manager)

---

## 7. Assumptions

### A-01: Customer Assumptions

#### A-01.1: Smartphone Access

**Assumption**: All customers have smartphones with NFC, GPS, and camera.

**Validation**: 95%+ smartphone penetration in EU target demographic (18-55 years).

**Risk if Invalid**: Cannot serve customers without NFC phones.

**Mitigation**: Offer alternative unlock methods (keypad codes) in future if needed.

---

#### A-01.2: Internet Connectivity

**Assumption**: Customers have reliable 4G/5G connectivity for app usage.

**Validation**: 4G coverage >95% in target EU cities.

**Risk if Invalid**: App functionality degraded in poor connectivity areas.

**Mitigation**: Offline mode for core features (reservation cache, GPS tracking).

---

#### A-01.3: Digital Payment Methods

**Assumption**: Customers use credit/debit cards or digital wallets.

**Validation**: 90%+ card/digital payment adoption in EU.

**Risk if Invalid**: Exclude cash-only customers.

**Mitigation**: Partner with local payment methods (SEPA, iDEAL) for broader coverage.

---

### A-02: Vehicle Assumptions

#### A-02.1: Vehicle Hardware Reliability

**Assumption**: Vehicle telematics hardware is reliable and maintains connectivity.

**Validation**: Enterprise-grade IoT hardware with >99% uptime.

**Risk if Invalid**: Lost GPS tracking, inability to unlock vehicles remotely.

**Mitigation**: Local fallback (NFC unlock always works), offline buffering for telemetry.

---

#### A-02.2: Battery Reporting Accuracy

**Assumption**: Vehicle battery sensors report accurate charge levels.

**Validation**: Hardware manufacturer specifications, field testing.

**Risk if Invalid**: Inaccurate battery predictions, customer mid-trip failures.

**Mitigation**: Historical calibration, battery drain modeling, conservative estimates.

---

### A-03: Operations Assumptions

#### A-03.1: Staff Availability

**Assumption**: Sufficient field staff available for redistribution and battery swaps.

**Validation**: Current staffing levels adequate, can hire more as fleet grows.

**Risk if Invalid**: Cannot execute AI recommendations, poor fleet availability.

**Mitigation**: Optimize routes to maximize staff efficiency, hire temp staff for peak seasons.

---

#### A-03.2: Staff Smartphone Access

**Assumption**: All field staff have company smartphones with field operations app.

**Validation**: Company provides smartphones to all field staff.

**Risk if Invalid**: Cannot receive tasks or execute operations.

**Mitigation**: Mandatory equipment provision for field staff.

---

### A-04: Data Assumptions

#### A-04.1: Sufficient Historical Data

**Assumption**: Sufficient historical data exists for ML model training.

**Validation**: 6+ months operational data in existing locations.

**Risk if Invalid**: Poor model accuracy, no AI deployment possible.

**Mitigation**: Use rule-based approach until data sufficient, transfer learning from similar locations.

---

#### A-04.2: Data Quality

**Assumption**: Historical data is accurate and representative.

**Validation**: Data quality audits, outlier detection.

**Risk if Invalid**: Models trained on bad data, poor predictions.

**Mitigation**: Data cleaning pipeline, validation checks, ongoing monitoring.

---

### A-05: AI Assumptions

#### A-05.1: Demand Predictability

**Assumption**: Customer demand follows predictable patterns based on time, location, weather, events.

**Validation**: Historical usage patterns show temporal and spatial correlations.

**Risk if Invalid**: AI predictions inaccurate, no improvement over random placement.

**Mitigation**: Continuous model validation, fallback to historical averages.

---

#### A-05.2: AI Provider Availability

**Assumption**: Third-party AI services (OpenAI, AWS Bedrock) remain available and affordable.

**Validation**: Enterprise SLAs from providers, multiple provider options.

**Risk if Invalid**: Service outages, cost increases, provider shutdowns.

**Mitigation**: Multi-provider strategy, self-hosted model fallback, cost monitoring.

---

#### A-05.3: Model Explainability Acceptable

**Assumption**: Dispatcher acceptance of AI recommendations even without full explainability.

**Validation**: User research, pilot testing with operations staff.

**Risk if Invalid**: Staff ignore AI recommendations, no operational improvement.

**Mitigation**: Provide confidence scores, show historical accuracy, allow overrides, gradual trust building.

---

### A-06: External Assumptions

#### A-06.1: Event Data Availability

**Assumption**: External event data (concerts, sports, transit disruptions) is available via APIs.

**Validation**: Public event calendars, ticketing platforms, transit APIs exist.

**Risk if Invalid**: Cannot predict event-based demand surges.

**Mitigation**: Manual event entry by operations staff, community-sourced data.

---

#### A-06.2: Weather Data

**Assumption**: Weather forecast data is available and accurate.

**Validation**: Weather APIs (OpenWeatherMap, NOAA) provide forecasts.

**Risk if Invalid**: Cannot factor weather impact on demand.

**Mitigation**: Historical weather patterns as fallback, use actual weather post-hoc.

---

#### A-06.3: Map & Routing Services

**Assumption**: Google Maps or similar services remain available and affordable.

**Validation**: Enterprise agreements in place.

**Risk if Invalid**: Cannot provide navigation, route optimization for staff.

**Mitigation**: Alternative providers (Mapbox, OpenStreetMap), offline maps for critical features.

---

## Constraint & Assumption Summary

| Category | Constraints | Assumptions | Impact on Architecture |
|----------|------------|-------------|------------------------|
| **Business** | 4 | 0 | Compliance, localization, cost optimization |
| **Technical** | 5 | 0 | Hardware integration, parking/charging workflows |
| **AI/ML** | 4 | 3 | Multi-provider, validation, monitoring |
| **Data** | 3 | 2 | Retention, privacy, quality pipelines |
| **Operational** | 2 | 2 | 24/7 uptime, manual operations support |
| **Security** | 2 | 0 | Encryption, PII masking |
| **Customer** | 0 | 3 | Smartphone capabilities, payment methods |
| **External** | 0 | 3 | Event data, weather, maps |
| **TOTAL** | **20** | **13** | **33 factors shaping design** |

---

## Risk Matrix

| Constraint/Assumption | Likelihood of Violation | Impact if Violated | Mitigation Priority |
|----------------------|------------------------|-------------------|-------------------|
| **AIC-01** (AI Provider Risk) | Medium | High | Critical |
| **AIC-02** (Non-Deterministic) | High | High | Critical |
| **DC-02** (Data Privacy) | Low | Critical | Critical |
| **A-04.1** (Historical Data) | Medium | High | High |
| **A-05.1** (Demand Predictability) | Medium | Medium | High |
| **A-05.2** (AI Provider Availability) | Low | High | Medium |

---

## Document Control

- **Version**: 1.0
- **Last Updated**: 2025-10-20
- **Owner**: BRINUS Team (O'Reilly Q4 2025 Kata)
- **Status**: Draft
- **References**:
  - [02_problem_statement.md](../02_problem_statement.md)
  - [05_functional_requirements.md](../05_functional_requirements.md)
  - [07_non_functional_requirements.md](../07_non_functional_requirements.md)
