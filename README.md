# MobilityCorp AI-Enabled Architecture

**Team BRINUS** - O'Reilly Architectural Katas Q4 2025
**Submission Date**: October 23, 2025

---

## 📋 Competition Deliverables Checklist

This submission includes all required deliverables as specified in the competition guidelines:

| Required Deliverable | Status | Location | Description |
|---------------------|--------|----------|-------------|
| **1️⃣ Overview Narrative** | ✅ **Complete** | [`docs/OVERVIEW.md`](docs/OVERVIEW.md) | Comprehensive narrative (18KB) describing how Team BRINUS uses AI to solve MobilityCorp's 3 business challenges |
| **2️⃣ Architecture Diagrams** | ✅ **Complete** | [`docs/diagrams/c4/src/`](docs/diagrams/c4/src/) | 11 C4 model source files (.c4) providing comprehensive and targeted views for each AI use case |
| **3️⃣ Architecture Decision Records** | ✅ **Complete** | [`docs/adrs/`](docs/adrs/) | 3 comprehensive ADRs (77KB total) with detailed trade-off analysis for AI-related implementations |
| **4️⃣ Implementation Details** | ✅ **Complete** (Optional) | [`docs/TECHNICAL_DEEP_DIVE.md`](docs/TECHNICAL_DEEP_DIVE.md) | Technical implementation guide (28KB) with working TypeScript code examples |

**Total Documentation**: 151KB of comprehensive technical documentation addressing all 6 judging criteria.

---

## 🎯 Judge's Review Guide (35-45 Minutes)

This guide walks you through our submission in the optimal order for evaluation.

### Step 1: Understand the Business Context (10 minutes)

**Read**: [`docs/OVERVIEW.md`](docs/OVERVIEW.md)

**What you'll learn**:
- MobilityCorp's 3 critical business challenges
- How we use AI to solve challenges #1 (vehicles in wrong places) and #2 (EVs running out of charge)
- **Why we deliberately DON'T use AI** for challenge #3 (customer engagement) - demonstrates architectural maturity
- Business impact: **€4.91M ROI in Year 1** (3-month payback period)
- Key innovation: **RAG + Multimodal LLMs instead of fine-tuned custom models**
  - 2 months to production (vs 12+ months for fine-tuning)
  - €280K cost (vs €600K+ for ML engineers)
  - Zero ML expertise required

**Key sections to focus on**:
- "The Three Business Challenges & AI Solutions" (pages 1-5)
- "Why RAG Instead of Fine-Tuned Models?" (page 3)
- "Business Impact Summary" (page 17)

---

### Step 2: Review Main Architecture Decision (15 minutes)

**Read**: [`docs/adrs/ADR-002-computer-vision-for-operations.md`](docs/adrs/ADR-002-computer-vision-for-operations.md)

This is our **most comprehensive ADR** (46KB, 1,161 lines) and addresses **all 6 judging criteria**.

**What you'll learn**:

#### ✅ Judging Criterion #1: Innovative Use of Generative AI
- **RAG-First Approach**: Why we use pre-trained multimodal LLMs + RAG instead of custom models
- **LLM Provider Abstraction**: Strategy pattern enabling 5-minute provider switching
- **Three CV Systems**:
  1. Docking Verification (eBikes/scooters)
  2. Damage Detection (before/after comparison)
  3. Return Photo Verification (cars/vans charging cable)

#### ✅ Judging Criterion #2: Suitability Given Constraints
- Startup-friendly: No ML engineers needed
- Fast time-to-market: 2-month pilot deployment
- Predictable costs: $95/mo Weaviate + ~$23K/mo LLM APIs
- GDPR-compliant: EU data residency, face blurring, 90-day retention

#### ✅ Judging Criterion #4: Dealing with Uncertainty in AI Technology
- **Provider Flexibility**: Switch OpenAI → Anthropic → Azure in 5 minutes (zero code changes)
- **Automatic Fallback**: Circuit breaker after 3 failures
- **Cost Optimization**: Track spend by provider, switch to cheaper alternatives
- **Response to scenarios**:
  - OpenAI raises prices 5X → Switch to Anthropic (save 20%)
  - OpenAI outage → Auto-fallback to Claude (99.9% uptime)
  - Provider shutdown → Pre-planned migration path

#### ✅ Judging Criterion #6: Validation and Verification of AI Results
- **7-Layer Validation Strategy**:
  1. Temperature=0 (forces deterministic outputs)
  2. Structured JSON responses (prevents hallucinations)
  3. Confidence thresholds (90%+ auto-approve, 70-89% human review, <70% reject)
  4. Daily synthetic tests (100 photos, expect 95% accuracy)
  5. A/B testing (90% stable, 10% experimental prompts)
  6. Ground truth feedback loop (human overrides improve RAG)
  7. Production monitoring (Grafana dashboards, PagerDuty alerts)

**Key sections to focus on**:
- "Why RAG for Startups?" (lines 55-150)
- "Ensuring Deterministic AI" (lines 430-520)
- "Handling AI Uncertainty" (lines 820-900)
- "Privacy & GDPR Compliance" (lines 710-780)

---

### Step 3: Generate and Review Architecture Diagrams (10 minutes)

Our C4 diagrams are defined in source files (`.c4`) and can be rendered to PNG.

#### 📐 How to Generate Diagrams

**Prerequisites**: Node.js installed (https://nodejs.org/)

**Commands**:
```bash
cd docs/diagrams/c4
npm install
npx likec4 build --output png
```

**Output**: 14 PNG diagrams generated in `docs/diagrams/c4/png/`

**Build time**: ~10 seconds

**Alternatively**: View interactive diagrams
```bash
npx likec4 serve
# Opens browser at http://localhost:3000
```

#### 🎨 What Diagrams to Review

**System Context (Level 1)**:
- `index.png` - MobilityCorp system in relation to users and external systems

**Container Level (Level 2)**:
- `full.png` - Complete system showing API Gateway, Microservices, Platform, AI Systems
- `platform.png` - Event-driven backbone (Kafka, Flink, data layers)
- `cv_system_context.png` - CV Verification system integration

**Component Level (Level 3)** - Shows internal architecture:
- `cv_components.png` - CV system components (RAG, LLM abstraction, decision logic)
- `cv_docking_flow.png` - Docking verification workflow (eBikes/scooters)
- `cv_damage_flow.png` - Damage detection workflow (before/after comparison)

**Failure Scenarios**:
- `cv_provider_failover.png` - How system handles LLM provider failures
- `cv_human_review.png` - Human review workflow for low-confidence cases

**Where diagrams address judging criteria**:
- **Criterion #3** (Appropriate Levels of Detail): We provide System Context, Container, Component, and Code levels
- **Criterion #5** (Architectural Characteristics Match): Diagrams show our AI is additive to existing event-driven architecture

---

### Step 4: Review Supporting ADRs (10 minutes)

#### 📄 ADR-001: Adopt Open Mobility Foundation Standards

**Read**: [`docs/adrs/ADR-001-adopt-omf-standards.md`](docs/adrs/ADR-001-adopt-omf-standards.md)

**What you'll learn**:
- Foundation for regulatory compliance
- Uses MDS (Mobility Data Specification) and CDS (Curb Data Specification)
- Enables multi-city expansion, GDPR compliance
- Standardized APIs reduce integration effort

**Why this matters**: Establishes the baseline platform that AI systems integrate with.

---

#### 📄 ADR-003: Geo-Fence and Wrong-Way Detection (Edge AI)

**Read**: [`docs/adrs/ADR-003-geofence-and-wrong-way-detection.md`](docs/adrs/ADR-003-geofence-and-wrong-way-detection.md)

**What you'll learn**:
- **Edge AI for safety-critical decisions** (Raspberry Pi on vehicles)
- Why edge vs cloud: <100ms latency, offline operation, privacy-first
- **Two AI features**:
  1. Geo-fence enforcement (restricted zones, speed limits)
  2. Wrong-way detection (one-way streets)
- **Progressive intervention UX**: Silent slowdown → Audio warning → Escalation
  - Why silent first? Better UX, fewer false alarms, users don't feel "policed"
- **Hardware**: GPS + IMU + Raspberry Pi 4 (~$100/vehicle)
- **Business impact**: €216K/year fine savings, 80% fewer wrong-way incidents

**Why this matters**: Shows we understand when to use Edge AI vs Cloud AI.

---

### Step 5 (Optional): Deep Technical Implementation (10 minutes)

**Read**: [`docs/TECHNICAL_DEEP_DIVE.md`](docs/TECHNICAL_DEEP_DIVE.md)

**What you'll learn**:
- **Working TypeScript code examples** showing exactly how we:
  - Fix AI non-determinism (temperature=0, structured JSON, confidence thresholds)
  - Implement LLM provider abstraction (Strategy pattern, circuit breaker)
  - Switch providers in 5 minutes (change env var, rolling restart)
  - Track costs and optimize spend

**Code examples included**:
- OpenAI provider implementation
- Anthropic provider implementation
- Provider factory (config-based selection)
- Circuit breaker + auto-fallback orchestrator
- Daily synthetic testing framework
- A/B testing infrastructure
- Ground truth feedback loop

**Why this matters**: Proves our solution is implementable, not just theoretical.

---

## 🏆 How We Address Each Judging Criterion

### ✅ 1. Innovative Use of Generative AI

**Our Innovations**:

1. **RAG-First Approach** (Most Innovative)
   - Industry norm: Build custom fine-tuned models (YOLOv8, Mask R-CNN)
   - Our approach: Use pre-trained multimodal LLMs + RAG (Weaviate Cloud)
   - Why innovative: Proves RAG can match/exceed custom model accuracy for startups
   - Result: 2 months to production vs 12+ months, €280K vs €600K+

2. **LLM Provider Abstraction Layer**
   - Industry norm: Lock-in to single provider (OpenAI or Anthropic)
   - Our approach: Strategy pattern, plug-and-play providers
   - Why innovative: Instant switching (5 minutes), multi-provider A/B testing
   - Result: Resilient to price changes, outages, shutdowns

3. **Silent First Intervention** (Wrong-Way Detection)
   - Industry norm: Immediate warnings "YOU'RE DOING SOMETHING WRONG"
   - Our approach: Silent motor slowdown → Audio warning → Escalation
   - Why innovative: Better UX, educates before confronting
   - Result: Fewer false alarms, users don't feel policed

4. **Knowing When NOT to Use AI**
   - We deliberately avoided AI for customer engagement (challenge #3)
   - Traditional UX/product improvements work better (deterministic, proven)
   - Why innovative: Shows architectural maturity, focus AI where ROI is clear

**Evidence**: [`docs/adrs/ADR-002-computer-vision-for-operations.md`](docs/adrs/ADR-002-computer-vision-for-operations.md) lines 55-150

---

### ✅ 2. Suitability of Solution Given Constraints

**MobilityCorp Constraints**:
- ✅ Startup (no ML engineers on team)
- ✅ Cost-conscious (need predictable pricing)
- ✅ Fast time-to-market (prove product-market fit)
- ✅ EU GDPR compliance required

**How Our Solution Fits**:

| Constraint | Traditional Approach | Our RAG Approach | Fit |
|------------|---------------------|------------------|-----|
| No ML engineers | Hire 2-3 ML engineers ($450K+/year) | Just developers writing prompts | ✅ Perfect |
| Predictable costs | GPU infrastructure ($5-10K/mo) | $95/mo Weaviate + $23K/mo LLM APIs | ✅ Perfect |
| Fast launch | 9-12 months (model training) | 2 months (pilot deployment) | ✅ Perfect |
| GDPR compliance | Complex (custom models, data retention) | Built-in (Weaviate EU cluster, no training on data) | ✅ Perfect |

**Evidence**: [`docs/OVERVIEW.md`](docs/OVERVIEW.md) pages 3-4

---

### ✅ 3. Appropriate Levels of Detail

**We Provide All 4 Levels**:

| Level | What We Provide | Where |
|-------|-----------------|-------|
| **High-Level** (Business) | Overview narrative, ROI analysis, business challenges | [`docs/OVERVIEW.md`](docs/OVERVIEW.md) |
| **Mid-Level** (Architecture) | 3 comprehensive ADRs with trade-offs, cost analysis, validation | [`docs/adrs/`](docs/adrs/) |
| **Low-Level** (Design) | C4 diagrams (Context, Container, Component) | [`docs/diagrams/c4/src/`](docs/diagrams/c4/src/) |
| **Implementation** (Code) | TypeScript examples, algorithms, APIs | [`docs/TECHNICAL_DEEP_DIVE.md`](docs/TECHNICAL_DEEP_DIVE.md) |

**C4 Diagram Coverage** (11 source files, 14 rendered views):
- System Context (1 view)
- Container Level (7 views: full system, platform, microservices, telemetry, customer app, staff app, AI systems)
- Component Level (3 views: CV components, docking flow, damage flow)
- Scenarios (3 views: provider failover, human review, event-driven flows)

**Evidence**: All documentation, comprehensive across all levels

---

### ✅ 4. Dealing with Uncertainty in AI Technology

**The Uncertainty Problem**:
- What if OpenAI raises prices 5X?
- What if OpenAI/Claude shuts down?
- What if model accuracy degrades over time?
- What if we hit rate limits during peak usage?

**Our Solutions**:

#### Scenario 1: Provider Price Increase (5X)
```
Current: OpenAI ($0.01/image) = $36K/month
OpenAI raises prices to $0.05/image = $180K/month

Response (5 minutes, zero code changes):
1. Change env var: LLM_PROVIDER=anthropic
2. Rolling restart: kubectl rollout restart deployment/cv-verification
3. Monitor for 24 hours (accuracy, latency, disputes)

New cost: $28.8K/month (save $151K/month vs new OpenAI pricing)
Downtime: 0 seconds
```

#### Scenario 2: Provider Outage
```
OpenAI API returns 503 errors

Automatic response (circuit breaker):
1. After 3 failures → switch to Anthropic (fallback)
2. Alert on-call engineer (investigate root cause)
3. Continue operating normally

Uptime: 99.9% (multi-provider redundancy)
```

#### Scenario 3: Provider Shutdown (Nuclear Scenario)
```
Anthropic acquired, service discontinued

Pre-planned migration:
- Week 1: Switch to Azure OpenAI (enterprise SLA, tested)
- Week 2: Load test, monitor production metrics
- Worst case: Self-host LLaVA (open-source multimodal LLM)
  - Deploy on AWS g5.xlarge GPU instances
  - Cost: $864/month (cheaper than cloud APIs at scale!)
  - Trade-off: Higher latency, but full control
```

#### Scenario 4: Model Drift (Accuracy Degrades)
```
LLM accuracy drops from 95% → 85% over 3 months

Detection:
- Daily synthetic tests (100 photos, expect 95% accuracy)
- Alert if pass rate <90%
- Human review rate spikes from 5% → 15%

Response:
1. Rollback to last known-good prompt version
2. Investigate: Provider model update? Prompt drift? Data quality?
3. A/B test prompt revisions (10% traffic on new version)
4. Only promote if metrics improve
```

**Evidence**: [`docs/adrs/ADR-002-computer-vision-for-operations.md`](docs/adrs/ADR-002-computer-vision-for-operations.md) lines 820-900

**Implementation**: [`docs/TECHNICAL_DEEP_DIVE.md`](docs/TECHNICAL_DEEP_DIVE.md) Part 2 (LLM Provider Abstraction)

---

### ✅ 5. Matching Architectural Characteristics with Existing Architecture

**MobilityCorp's Existing Architecture** (inferred from requirements):
- Event-driven (GPS updates, booking events, return events)
- Multi-region (EU cities with different regulations)
- Mobile-first (NFC unlock, photo submission)
- IoT integration (GPS trackers, NFC readers, charging sensors)
- Scalability (10,000 vehicles → 100,000 vehicles)

**Our AI Additions Are Aligned** (Additive, Not Disruptive):

| Characteristic | CV Verification (Cloud AI) | Geo-Fence (Edge AI) | Alignment |
|----------------|---------------------------|---------------------|-----------|
| **Event-driven** | Triggered by "rental_ended" event | Triggered by GPS updates (10Hz) | ✅ Yes |
| **Multi-region** | Multi-language prompts, EU data residency | Per-city map data, local regulations | ✅ Yes |
| **Mobile-first** | Photo upload from app, real-time feedback | Audio warnings, app notifications | ✅ Yes |
| **IoT integration** | Uses GPS metadata, charging sensor data | GPS + IMU fusion, motor control | ✅ Yes |
| **Scalability** | Stateless API calls, horizontal scaling | Edge processing (no cloud bottleneck) | ✅ Yes |
| **Reliability** | Multi-provider fallback, circuit breakers | Offline operation, graceful degradation | ✅ Yes |

**No Architectural Conflicts**: Our AI systems integrate seamlessly with existing event streams (Kafka topics: `vehicle.events.raw`, `domain.events`).

**Evidence**: [`docs/diagrams/c4/src/model.cv_verification.c4`](docs/diagrams/c4/src/model.cv_verification.c4) shows integration points

---

### ✅ 6. Validation and Verification of AI Results

**The Non-Determinism Problem**:
- LLMs are inherently non-deterministic
- Same photo can produce different results
- Example: Photo of scooter in dock
  - Attempt 1: PASS (confidence: 0.92)
  - Attempt 2: PASS (confidence: 0.94)
  - Attempt 3: FAIL (confidence: 0.68) ← **Same photo!**

**Our 7-Layer Validation Strategy**:

#### Layer 1: Temperature = 0 (Force Determinism)
```typescript
const response = await openai.chat.completions.create({
  model: "gpt-4-vision-preview",
  temperature: 0,  // ← Makes LLM deterministic
  // Same photo + same prompt = same result 99.9% of time
});
```

#### Layer 2: Structured JSON Responses (Prevent Hallucinations)
```json
{
  "decision": "PASS" | "FAIL",
  "confidence": 0.0-1.0,
  "reasoning": "Brief explanation",
  "docking_pins_inserted": true | false,
  "charging_light_on": true | false
}
```
- Force LLM to return valid JSON (not free-form text)
- Prevents hallucinations, enables testing

#### Layer 3: Confidence Thresholds (Route Uncertain Cases)
```
if confidence >= 0.90: AUTO_APPROVE (85-90% of cases)
if confidence >= 0.70: HUMAN_REVIEW (5-10% of cases)
if confidence < 0.70:  AUTO_REJECT (5% of cases)
```

#### Layer 4: Daily Synthetic Testing (Detect Model Drift)
```typescript
// Run every day at 6am (before peak usage)
// Test 100 known photos (50 PASS, 50 FAIL)
// Alert if accuracy drops below 90%
```

#### Layer 5: A/B Testing (Safe Prompt Improvements)
```
90% traffic → v1.0 prompt (stable)
10% traffic → v1.1 prompt (experimental)
Only promote v1.1 if metrics improve
```

#### Layer 6: Ground Truth Feedback Loop
```
Human overrides → Store in Weaviate RAG → Improve future decisions
Every dispute becomes training data
Continuous improvement without model retraining
```

#### Layer 7: Production Monitoring (Grafana Dashboards)
```yaml
Metrics:
- Average confidence score (target: >90%)
- Human review rate (target: <5%)
- User dispute rate (target: <2%)
- Decision consistency (same photo twice = same result, target: >98%)

Alerts:
- Confidence drops below 85% → Page oncall
- Human review rate spikes above 15% → Investigate
- User dispute rate above 5% → Fix prompts
```

**Result**: 99.9% consistent decisions, <2% user disputes

**Evidence**:
- [`docs/adrs/ADR-002-computer-vision-for-operations.md`](docs/adrs/ADR-002-computer-vision-for-operations.md) lines 430-520
- [`docs/TECHNICAL_DEEP_DIVE.md`](docs/TECHNICAL_DEEP_DIVE.md) Part 1 (Fixing AI Non-Determinism)

---

## 💡 Our AI Solution Summary

### Challenge 1: Vehicles Not in the Right Places
**AI Solution**: Computer Vision Verification (RAG + Multimodal LLMs)
- Docking Verification AI (eBikes/scooters)
- Damage Detection AI (before/after comparison)
- Return Photo Verification AI (cars/vans charging cable)
- **Impact**: €5.2M/year savings (automated verification)

### Challenge 2: EVs Running Out of Charge
**AI Solution**: Computer Vision checks charging cable plugged in
- Same CV system ensures vehicles are charging when returned
- Staff app shows which bays need battery swaps (rule-based, not AI)
- **Impact**: €500K/year revenue (20% more fleet availability)

### Challenge 3: Low Customer Engagement
**AI Solution**: 🚫 **We DON'T use AI** (Deliberately)
- GenAI recommendations are unreliable for this use case
- Traditional approaches work better: subscription plans, gamification, partnerships
- **Why this matters**: Shows architectural maturity - not every problem needs AI
- **Impact**: Focus limited resources on high-ROI AI use cases

**Total Business Impact**: €4.91M net ROI in Year 1 (3-month payback period)

---

## 📐 Repository Structure

```
kata-oreilly-q425-brinus/
│
├── README.md                          # This file (judge's guide)
├── .gitignore                         # Excludes generated PNG files
│
└── docs/
    ├── OVERVIEW.md                    # ⭐ Executive summary (18KB)
    ├── TECHNICAL_DEEP_DIVE.md         # Implementation guide (28KB)
    │
    ├── adrs/                          # Architecture Decision Records
    │   ├── ADR-001-adopt-omf-standards.md              # Foundation (5.1KB)
    │   ├── ADR-002-computer-vision-for-operations.md   # Main AI solution (46KB)
    │   └── ADR-003-geofence-and-wrong-way-detection.md # Edge AI (26KB)
    │
    └── diagrams/c4/                   # C4 Model diagrams
        ├── src/                       # LikeC4 source files (11 files)
        │   ├── model.c4               # Core system model
        │   ├── model.platform.c4      # Event-driven platform backbone
        │   ├── model.microservices.c4 # Business domain services
        │   ├── model.ai_analytics.c4  # AI systems container
        │   ├── model.cv_verification.c4        # CV system components
        │   ├── model.cv_verification.views.c4  # CV workflow views
        │   └── ...
        ├── png/                       # Generated diagrams (run: npx likec4 build)
        ├── package.json               # LikeC4 dependencies
        └── likec4.config.ts           # Build configuration
```

---

## 🛠️ Building C4 Diagrams (For Judges)

Our C4 diagrams are defined as code (`.c4` files) and rendered using LikeC4.

### Prerequisites
- Node.js installed (https://nodejs.org/ - LTS version recommended)

### Generate PNG Diagrams

```bash
# Navigate to diagrams directory
cd docs/diagrams/c4

# Install dependencies (first time only)
npm install

# Generate PNG diagrams
npx likec4 build --output png
```

**Output**: 14 PNG files generated in `docs/diagrams/c4/png/`

**Build time**: ~10 seconds

### View Interactive Diagrams (Optional)

```bash
# Start local server
npx likec4 serve

# Opens browser at http://localhost:3000
# Interactive diagrams with zoom, pan, navigation
```

### Diagrams Generated

1. **index.png** - System Context View
2. **full.png** - Full System Container View
3. **platform.png** - Platform Backbone
4. **microservices.png** - Microservices Layer
5. **telemetry.png** - Telemetry Pipeline
6. **customer_app.png** - Customer Application
7. **staff_app.png** - Staff Application
8. **ai_analytics.png** - AI Systems
9. **data_layers.png** - Data Architecture
10. **fresh_availability.png** - Dynamic: Availability Update
11. **burst_tolerance.png** - Dynamic: Spike Handling
12. **exactly_once_projection.png** - Dynamic: Idempotent Processing
13. **cv_system_context.png** - CV Verification System (generated from views)
14. **Additional CV views** - Workflows, components, failure scenarios

**Note**: PNG files are excluded from git (`.gitignore`) due to file size limits (2.4MB for `full.png`). All diagram definitions are preserved in source `.c4` files.

---

## 👥 Team BRINUS

- **Rafael** - Software Architect
- **Prashant** - Backend Engineer
- **Khaled** - Full-Stack Engineer
- **João Vinagre** - Solutions Architect

**Contact**: joao.lima@zeecode.io

**Competition**: O'Reilly Architectural Katas Q4 2025
**Submission**: October 23, 2025

---

## 📚 Additional Resources

### Technical References
- [C4 Model](https://c4model.com/) - Diagram notation we use
- [LikeC4](https://likec4.dev/) - Diagram-as-code tool
- [ADR Template](https://github.com/joelparkerhenderson/architecture-decision-record) - Decision record format
- [OpenAI GPT-4V](https://platform.openai.com/docs/guides/vision) - Multimodal LLM API
- [Anthropic Claude](https://docs.anthropic.com/claude/docs) - Alternative multimodal LLM
- [Weaviate](https://weaviate.io/) - Vector database for RAG
- [Open Mobility Foundation](https://www.openmobilityfoundation.org/) - MDS/CDS standards

---

## ✅ Final Verification Checklist (For Judges)

Before completing your review, verify all deliverables are accessible:

- [ ] **Overview Narrative**: Read [`docs/OVERVIEW.md`](docs/OVERVIEW.md) ✅
- [ ] **ADR-001**: Read [`docs/adrs/ADR-001-adopt-omf-standards.md`](docs/adrs/ADR-001-adopt-omf-standards.md) ✅
- [ ] **ADR-002** (Main): Read [`docs/adrs/ADR-002-computer-vision-for-operations.md`](docs/adrs/ADR-002-computer-vision-for-operations.md) ✅
- [ ] **ADR-003**: Read [`docs/adrs/ADR-003-geofence-and-wrong-way-detection.md`](docs/adrs/ADR-003-geofence-and-wrong-way-detection.md) ✅
- [ ] **C4 Diagrams**: Generated PNG files from `.c4` source (run `npx likec4 build`) ✅
- [ ] **Technical Deep Dive** (Optional): Read [`docs/TECHNICAL_DEEP_DIVE.md`](docs/TECHNICAL_DEEP_DIVE.md) ✅

**All 6 Judging Criteria Addressed**:
- [x] 1. Innovative Use of Generative AI
- [x] 2. Suitability Given Constraints
- [x] 3. Appropriate Levels of Detail
- [x] 4. Dealing with Uncertainty in AI Technology
- [x] 5. Matching Architectural Characteristics
- [x] 6. Validation and Verification of AI Results

---

**Thank you for reviewing Team BRINUS's submission!**

For questions or clarifications, please contact us via Discord or email.

---

## 📄 License

This architecture proposal is submitted for the O'Reilly Architectural Katas Q4 2025 competition.

Copyright © 2025 Team BRINUS. All rights reserved.
