# MobilityCorp AI-Enabled Architecture

**Team BRINUS** - O'Reilly Architectural Katas Q4 2025
**Submission Date**: October 23, 2025

---

## Quick Navigation for Judges

| Deliverable | Location | Description |
|-------------|----------|-------------|
| **📄 Overview** | [`docs/OVERVIEW.md`](docs/OVERVIEW.md) | Executive summary of how AI solves MobilityCorp's 3 business challenges |
| **📋 ADRs** | [`docs/adrs/`](docs/adrs/) | Architecture Decision Records with trade-off analysis |
| **🎨 C4 Diagrams** | [`docs/diagrams/c4/`](docs/diagrams/c4/) | System architecture at all levels (Context, Container, Component) |
| **📊 Requirements** | [`docs/01_preamble/`](docs/01_preamble/) | Business requirements and technical specifications |

---

## The Problem

**MobilityCorp** operates electric scooters, eBikes, cars, and vans across multiple EU cities. They face three critical challenges:

1. ❌ **Vehicles not in the right places** when customers need them
2. ❌ **EVs running out of charge**, reducing fleet availability
3. ❌ **Low customer engagement** - most usage is ad-hoc, not regular commutes

---

## Our AI Solution

We propose an **AI-first architecture** using:
- **RAG (Retrieval Augmented Generation)** for operational verification
- **Edge AI** for safety-critical real-time decisions
- **LLM Provider Abstraction** for vendor flexibility

### Key Innovation: RAG Instead of Fine-Tuning

| Approach | Timeline | Cost (Year 1) | ML Engineers Needed | Flexibility |
|----------|----------|---------------|---------------------|-------------|
| Fine-tuned CV models | 9-12 months | €600K+ | 2-3 engineers | LOW |
| **RAG + Multimodal LLMs** ✅ | **2 months** | **€280K** | **0 engineers** | **HIGH** |

### Business Impact Summary

| Metric | Value |
|--------|-------|
| **Annual Cost Savings** | €5.46M (manual review elimination + fine reduction) |
| **Annual Revenue Increase** | €700K (fleet availability + dispute reduction) |
| **Total Investment (Year 1)** | €1.25M (hardware + API costs) |
| **Net ROI (Year 1)** | **€4.91M profit** |
| **Payback Period** | **3 months** |

---

## How We Address Each Judging Criterion

### ✅ 1. Innovative Use of Generative AI

**Our Innovations**:
- **RAG-First Approach**: Use pre-trained multimodal LLMs (GPT-4V, Claude 3.5) with Weaviate RAG instead of custom fine-tuned models
  - **Why innovative**: Most teams build custom models (slow, expensive). We prove RAG can match/exceed accuracy for startups
  - **Result**: 2 months to production vs 12+ months for fine-tuning

- **LLM Provider Abstraction Layer**: Plug-and-play architecture to switch providers in 5 minutes
  - **Why innovative**: Industry standard is lock-in to single provider. We enable instant switching (OpenAI → Claude → Azure)
  - **Result**: Resilient to price changes, service outages, provider shutdowns

- **Silent First Intervention** (Wrong-Way Detection): Progressive UX that educates before escalating
  - **Why innovative**: Industry norm is immediate warnings ("YOU'RE DOING SOMETHING WRONG"). We use silent motor slowdown first
  - **Result**: Better UX, fewer false alarms, users don't feel policed

- **Knowing When NOT to Use AI**: Deliberately avoided AI for customer engagement (focus on high-ROI use cases)
  - **Why innovative**: Shows architectural maturity - not every problem needs AI

**Evidence**: See [`docs/adrs/ADR-002-computer-vision-for-operations.md`](docs/adrs/ADR-002-computer-vision-for-operations.md) (section: "Why RAG for Startups?")

---

### ✅ 2. Suitability Given Constraints

**MobilityCorp Constraints**:
- Startup (no ML engineers on team)
- Cost-conscious (need predictable pricing)
- Fast time-to-market (no time for 12-month model training)
- EU GDPR compliance required

**How Our Solution Fits**:
- ✅ **No ML expertise needed**: Just natural language prompts, any developer can maintain
- ✅ **Predictable costs**: $95/mo Weaviate + ~$23K/mo LLM APIs (vs €5.5M manual review costs)
- ✅ **2-month pilot**: Deployable quickly, measurable ROI
- ✅ **GDPR-compliant**: EU data residency (Weaviate EU cluster), face blurring, 90-day retention

**Evidence**: See [`docs/OVERVIEW.md`](docs/OVERVIEW.md) (section: "Why RAG Instead of Fine-Tuned Models?")

---

### ✅ 3. Appropriate Levels of Detail

**We Provide**:
- **High-Level**: Overview narrative showing how AI solves 3 business challenges ([`docs/OVERVIEW.md`](docs/OVERVIEW.md))
- **Mid-Level**: 3 comprehensive ADRs with cost analysis, trade-offs, validation strategies ([`docs/adrs/`](docs/adrs/))
- **Low-Level**: C4 diagrams at 4 levels (Context, Container, Component, Code) ([`docs/diagrams/c4/`](docs/diagrams/c4/))
- **Implementation**: Technical details (algorithms, APIs, data models) embedded in ADRs

**C4 Diagram Coverage**:
- 7 views for Computer Vision system (use cases, deployment, failure scenarios)
- Platform architecture (event-driven backbone, data layers, microservices)
- System context (actors, external systems, integrations)

**Evidence**: Browse [`docs/diagrams/c4/png/`](docs/diagrams/c4/png/) for rendered diagrams

---

### ✅ 4. Dealing with Uncertainty in AI Technology

**The Uncertainty Problem**:
- What if OpenAI raises prices 5X?
- What if OpenAI/Claude shuts down?
- What if model accuracy degrades over time?

**Our Solutions**:

#### Scenario 1: Provider Price Increase
**Response** (automatic, 5 minutes):
```
LLM_PROVIDER=openai → LLM_PROVIDER=anthropic
Cost: $36K/mo → $29K/mo (20% reduction)
Downtime: 0 seconds (hot-swap)
```

#### Scenario 2: Provider Outage
**Response** (automatic):
```
Circuit breaker → Fallback to Claude → Alert engineer
Uptime: 99.9% (multi-provider redundancy)
```

#### Scenario 3: Provider Shutdown
**Response** (pre-planned):
```
Week 1: Switch to Claude (tested regularly)
Week 2: Deploy Azure OpenAI (enterprise SLA)
Worst case: Self-host LLaVA (open-source, $864/mo)
```

#### Scenario 4: Model Drift
**Detection**: Daily synthetic tests (50 good + 50 bad photos, expect 95% accuracy)
**Response**: Rollback to last known-good prompt, investigate root cause, A/B test fixes

**Evidence**: See [`docs/adrs/ADR-002-computer-vision-for-operations.md`](docs/adrs/ADR-002-computer-vision-for-operations.md) (section: "Handling AI Uncertainty")

---

### ✅ 5. Architectural Characteristics Match Existing System

**MobilityCorp's Existing Architecture** (inferred):
- Event-driven (GPS updates, booking events, return events)
- Multi-region (EU cities with different regulations)
- Mobile-first (NFC unlock, photo submission)
- IoT integration (GPS trackers, NFC readers, charging sensors)

**Our AI Additions Are Aligned**:

| Characteristic | CV Verification | Geo-Fence Edge AI | Alignment |
|----------------|-----------------|-------------------|-----------|
| Event-driven | Triggered by "rental ended" event | Triggered by GPS updates (10Hz) | ✅ Yes |
| Multi-region | Multi-language prompts, EU data residency | Per-city map data, local regulations | ✅ Yes |
| Mobile-first | Photo upload from app, real-time feedback | Audio warnings, app notifications | ✅ Yes |
| IoT integration | Uses GPS metadata, charging sensor data | GPS + IMU fusion, motor control | ✅ Yes |
| Scalability | Stateless API calls, horizontal scaling | Edge processing (no cloud bottleneck) | ✅ Yes |

**No Architectural Conflicts**: Our AI is additive, not disruptive. Integrates with existing event streams.

**Evidence**: See [`docs/diagrams/c4/png/`](docs/diagrams/c4/png/) for architecture diagrams showing integration points

---

### ✅ 6. Validation and Verification of AI Results

**The Non-Determinism Problem**: LLMs can give different results for the same photo!

**Our 7-Layer Validation Strategy**:

1. **Temperature=0** (Forces Determinism)
   - LLM parameter that makes outputs deterministic
   - Same photo + same prompt = same result (99.9% of time)

2. **Structured JSON Responses**
   - Force LLM to return `{decision: "PASS", confidence: 0.92, reasoning: "..."}`
   - Prevents hallucinations, easier to test

3. **Confidence Thresholds**
   - 90-100%: Auto-approve (high confidence)
   - 70-89%: Human review (uncertain)
   - <70%: Auto-reject (low confidence)

4. **Daily Synthetic Testing**
   - Run 50 known-good + 50 known-bad photos through system
   - Expect 95% accuracy
   - Alert if pass rate <90% (model degrading)

5. **A/B Testing New Prompts**
   - 90% traffic on v1.0 (stable), 10% on v1.1 (experimental)
   - Compare accuracy, confidence, user satisfaction
   - Only promote v1.1 if metrics improve

6. **Ground Truth Feedback Loop**
   - Every human review override becomes training data
   - User disputes → staff reviews → add to test suite
   - Gradually improve accuracy over time

7. **Continuous Production Monitoring** (Grafana dashboards)
   - Average confidence score (target: >90%)
   - Human review rate (target: <5%)
   - User dispute rate (target: <2%)
   - Decision consistency (same photo twice = same result, target: >98%)

**Evidence**: See [`docs/adrs/ADR-002-computer-vision-for-operations.md`](docs/adrs/ADR-002-computer-vision-for-operations.md) (section: "Ensuring Deterministic AI")

---

## Repository Structure

```
kata-oreilly-q425-brinus/
│
├── README.md                          # This file (judge navigation)
│
├── docs/
│   ├── OVERVIEW.md                    # ⭐ Executive summary (start here!)
│   │
│   ├── adrs/                          # Architecture Decision Records
│   │   ├── ADR-001-adopt-omf-standards.md              # Foundation (MDS/CDS for compliance)
│   │   ├── ADR-002-computer-vision-for-operations.md   # CV with RAG (main AI solution)
│   │   └── ADR-003-geofence-and-wrong-way-detection.md # Edge AI (safety-critical)
│   │
│   ├── diagrams/c4/                   # C4 Model diagrams
│   │   ├── src/                       # LikeC4 source files (DSL)
│   │   │   ├── model.c4               # Core system model
│   │   │   ├── model.platform.c4      # Platform backbone
│   │   │   ├── model.microservices.c4 # Microservices layer
│   │   │   ├── model.ai_analytics.c4  # AI systems integration
│   │   │   ├── model.cv_verification.c4        # Computer Vision system
│   │   │   └── model.cv_verification.views.c4  # CV views (7 diagrams)
│   │   └── png/                       # Rendered diagrams (for judges)
│   │
│   └── 01_preamble/                   # Business requirements
│       ├── preamble.md                # Business context & personas
│       ├── software_requirements.md   # Functional requirements
│       └── technical_requirements.md  # Technical translation
│
├── Q4Qata2025.md                      # Competition problem statement
└── qata-description.md                # Competition overview
```

---

## Key Architecture Decision Records (ADRs)

### 📄 ADR-001: Adopt Open Mobility Foundation Standards
**Decision**: Use MDS (Mobility Data Specification) and CDS (Curb Data Specification) for city compliance
**Why**: Standardized APIs reduce integration effort, GDPR-compliant, enables multi-city expansion
**Impact**: Faster expansion to new cities, regulatory compliance out-of-the-box

👉 **[Read Full ADR](docs/adrs/ADR-001-adopt-omf-standards.md)**

---

### 📄 ADR-002: Computer Vision AI Using RAG (Most Important)
**Decision**: Use RAG + Multimodal LLMs (GPT-4V/Claude 3.5) instead of fine-tuned custom models
**Why**: No ML engineers needed, 2 months to production (vs 12+), vendor flexibility, €5.2M/year savings
**Key Features**:
- Docking Verification AI (eBikes/scooters)
- Damage Detection AI (before/after comparison)
- Return Photo Verification AI (cars/vans charging cable)

**Validation Strategy**:
- Temperature=0 for determinism
- Daily synthetic tests
- A/B testing for prompt improvements
- Ground truth feedback loop

**Uncertainty Handling**:
- LLM provider abstraction (switch OpenAI → Claude in 5 mins)
- Multi-provider fallback (OpenAI → Claude → Azure → Self-hosted)
- Cost tracking and optimization

👉 **[Read Full ADR](docs/adrs/ADR-002-computer-vision-for-operations.md)** (comprehensive, addresses all judging criteria)

---

### 📄 ADR-003: Geo-Fence and Wrong-Way Detection (Edge AI)
**Decision**: Use Edge AI (Raspberry Pi on vehicles) for safety-critical real-time decisions
**Why**: <100ms latency required, offline operation, privacy-first, 90% reduction in city fines
**Key Features**:
- Geo-fence enforcement (restricted zones, speed limits)
- Wrong-way detection (one-way streets with progressive intervention)
- Silent first intervention (better UX, less confrontational)

**Hardware**: GPS + IMU + Raspberry Pi 4 (~$100/vehicle)
**Impact**: €216K/year fine savings, 80% fewer wrong-way incidents

👉 **[Read Full ADR](docs/adrs/ADR-003-geofence-and-wrong-way-detection.md)**

---

## C4 Diagrams (Architecture Views)

We use the **C4 Model** for clear, multi-level architecture visualization.

### System Context Level (L1)
Shows MobilityCorp system in relation to users and external systems.

**View**: [`docs/diagrams/c4/png/index.png`](docs/diagrams/c4/png/index.png)

---

### Container Level (L2)
Shows major containers: API Gateway, Microservices, Platform Backbone, AI Systems.

**Key Views**:
- Full system: [`docs/diagrams/c4/png/full.png`](docs/diagrams/c4/png/full.png)
- Platform backbone: [`docs/diagrams/c4/png/platform.png`](docs/diagrams/c4/png/platform.png)
- CV verification: [`docs/diagrams/c4/png/cv_system_context.png`](docs/diagrams/c4/png/cv_system_context.png)

---

### Component Level (L3)
Shows internal components of Computer Vision system, LLM abstraction, RAG pipeline.

**Key Views**:
- CV components: [`docs/diagrams/c4/png/cv_components.png`](docs/diagrams/c4/png/cv_components.png)
- Docking flow: [`docs/diagrams/c4/png/cv_docking_flow.png`](docs/diagrams/c4/png/cv_docking_flow.png)
- Damage detection: [`docs/diagrams/c4/png/cv_damage_flow.png`](docs/diagrams/c4/png/cv_damage_flow.png)

---

### Deployment & Scenarios
Shows how system handles failures, human review, provider failover.

**Key Views**:
- Provider failover: [`docs/diagrams/c4/png/cv_provider_failover.png`](docs/diagrams/c4/png/cv_provider_failover.png)
- Human review: [`docs/diagrams/c4/png/cv_human_review.png`](docs/diagrams/c4/png/cv_human_review.png)

**All diagrams**: Browse [`docs/diagrams/c4/png/`](docs/diagrams/c4/png/) directory

---

## Team BRINUS

- **Rafael** - Software Architect
- **Prashant** - Backend Engineer
- **Khaled** - Full-Stack Engineer
- **João Vinagre** - Solutions Architect

**Contact**: joao.lima@zeecode.io

**Competition**: O'Reilly Architectural Katas Q4 2025
**Submission**: October 23, 2025

---

## Quick Start for Judges

1. **Read the Overview** (10 minutes): [`docs/OVERVIEW.md`](docs/OVERVIEW.md)
   - Understand the 3 business challenges
   - See how AI addresses each one
   - Review business impact (€4.91M ROI Year 1)

2. **Review Main ADR** (15 minutes): [`docs/adrs/ADR-002-computer-vision-for-operations.md`](docs/adrs/ADR-002-computer-vision-for-operations.md)
   - Why RAG instead of fine-tuning
   - LLM provider abstraction (handles uncertainty)
   - Validation strategy (addresses non-determinism)
   - Privacy & GDPR compliance

3. **Browse Diagrams** (10 minutes): [`docs/diagrams/c4/png/`](docs/diagrams/c4/png/)
   - System context (how everything connects)
   - CV verification workflows
   - Failure handling & human review

4. **Optional Deep Dive**: Other ADRs and requirement docs in [`docs/`](docs/)

**Total Review Time**: ~35 minutes for comprehensive understanding

---

## How This Addresses MobilityCorp's Challenges

| Challenge | Traditional Solution | Our AI Solution | Business Impact |
|-----------|---------------------|-----------------|-----------------|
| **Vehicles not in right places** | Manual redistribution | CV verification ensures proper returns | €5.2M/year savings (automated verification) |
| **EVs running out of charge** | Manual battery swaps | CV checks charging cable plugged in | €500K/year revenue (20% more availability) |
| **Low customer engagement** | AI recommendation engine | 🚫 **We don't use AI** (traditional UX/product better) | Focus AI where ROI is clear |

**Key Insight**: Not every problem needs AI. We focus on high-ROI use cases (CV verification) and use traditional solutions for customer engagement.

---

## License

This architecture proposal is submitted for the O'Reilly Architectural Katas Q4 2025 competition.

Copyright © 2025 Team BRINUS. All rights reserved.

---

## Additional Resources

**Competition Materials**:
- Problem statement: [`Q4Qata2025.md`](Q4Qata2025.md)
- Competition description: [`qata-description.md`](qata-description.md)

**Technical References**:
- [C4 Model](https://c4model.com/) - Diagram notation
- [ADR Template](https://github.com/joelparkerhenderson/architecture-decision-record) - Decision record format
- [OpenAI GPT-4V](https://platform.openai.com/docs/guides/vision) - Multimodal LLM API
- [Weaviate](https://weaviate.io/) - Vector database for RAG
- [Open Mobility Foundation](https://www.openmobilityfoundation.org/) - MDS/CDS standards

---

**Thank you for reviewing our submission!**

For questions or clarifications, please contact Team BRINUS via our Discord channel or email.
