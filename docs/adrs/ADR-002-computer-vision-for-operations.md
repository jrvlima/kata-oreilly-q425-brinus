# ADR-002: Computer Vision AI for Operational Verification Using RAG

## Status
Accepted

## Context

MobilityCorp faces significant operational challenges that result in:
- **Revenue Loss**: Vehicles not properly docked continue charging customers for extended periods
- **Dispute Resolution**: "He said/she said" arguments about vehicle damage cost time and money
- **Charging Compliance**: Cars/vans not plugged in properly lead to unavailable vehicles
- **Manual Verification**: Staff manually reviewing return photos is slow and error-prone
- **Customer Frustration**: Users uncertain if they completed return correctly

These problems require automated, real-time verification at scale across thousands of daily rentals in multiple EU countries.

### Business Impact
- **Current State**: ~15% of returns require manual review, averaging 8 minutes each
- **Scale**: With 10,000 vehicles × 3 rentals/day × 15% = 4,500 manual reviews daily
- **Cost**: 4,500 × 8 mins = 600 staff-hours/day across EU operations
- **Disputes**: ~5% of rentals result in damage disputes, requiring documentation and resolution

### Requirements from Session-1 Q&A
- Users must submit photos of returned vehicles
- Cars/vans must be plugged into EV chargers at designated spots
- All vehicles must be returned to designated parking bays
- GPS trackers verify location, but not docking/charging/damage status

### Startup Constraints
- **No ML Engineers**: Team focused on business, not model training/fine-tuning
- **Cost-Conscious**: Need affordable, scalable solution with predictable pricing
- **Avoid Vendor Lock-In**: Must be able to switch AI providers if pricing changes or service shuts down
- **Fast Time-to-Market**: No time for custom model training, need production-ready solution

## Decision

We will implement **three Computer Vision AI systems** using **RAG (Retrieval Augmented Generation)** with multimodal LLMs instead of fine-tuned custom models.

### Architecture Approach: RAG + Multimodal LLMs

**Why RAG Instead of Fine-Tuning?**

| Approach | Pros | Cons | Verdict |
|----------|------|------|---------|
| **Fine-tuned CV Models** (YOLOv8, Mask R-CNN) | High accuracy for specific tasks | Requires ML engineers, long training time, expensive GPUs, hard to update | ❌ Not suitable for startup |
| **RAG + Multimodal LLMs** (GPT-4V, Claude 3.5) | No ML expertise needed, easy updates, provider-agnostic, explains decisions | Slightly higher API costs per query | ✅ **Our Choice** |

**Core Architecture**:
```
User Photo → Multimodal LLM (GPT-4V/Claude 3.5) + RAG (Weaviate) → Decision (PASS/FAIL) + Explanation
```

---

## Why RAG for Startups? (Strategic Rationale)

### The Startup Dilemma

MobilityCorp is a **startup** focused on disrupting the mobility rental market. Like most startups, we face critical constraints:

1. **Limited Technical Resources**: No dedicated ML/AI team, no data scientists
2. **Time Pressure**: Need to launch quickly, prove product-market fit
3. **Budget Constraints**: Cannot afford expensive GPU infrastructure or ML engineers
4. **Uncertainty**: Business model may pivot, requirements will change frequently
5. **Need to Focus on Core Business**: Vehicle operations, customer experience, revenue growth

### Why Traditional CV Approaches Don't Work for Us

**Option 1: Fine-Tuned Custom Models (YOLOv8, Mask R-CNN)**
```
Requirements:
- Hire 2-3 ML engineers ($150K-200K/year each) = $450K+/year
- Collect & label 50K+ training images (6+ months)
- Rent GPU infrastructure for training ($5K-10K/month)
- Continuous retraining as vehicles/docks change
- Deep ML expertise for debugging model failures

Timeline: 9-12 months to production
Total Cost (Year 1): $600K+ in salaries + infrastructure
Flexibility: LOW (hard to change, requires retraining)
```
❌ **Verdict**: Too expensive, too slow, too risky for a startup

**Option 2: Pre-Trained CV Models (no customization)**
```
Use off-the-shelf models like AWS Rekognition, Google Vision API
- Good for generic object detection (cars, bikes, people)
- BAD for specific tasks (is docking pin inserted? charging cable connected?)
- Cannot understand business logic (20-25% damage tolerance)
- No explainability (why did it fail?)

Accuracy: ~60-70% for our use cases (not good enough)
```
❌ **Verdict**: Insufficient accuracy, no business context

### Why RAG + Multimodal LLMs is Perfect for Startups

**RAG (Retrieval Augmented Generation) Approach**:
```
1. Use pre-trained multimodal LLMs (GPT-4V, Claude 3.5)
   - Already trained on millions of images
   - Understand natural language instructions
   - Can reason about images contextually

2. Store historical photos + decisions in vector DB (Weaviate)
   - As users submit photos, system learns patterns
   - Retrieve similar past cases to inform current decision
   - No retraining needed, just more examples

3. Combine LLM intelligence + RAG context
   - LLM analyzes photo WITH 5 similar past cases
   - Gets smarter over time WITHOUT retraining
   - Explains decisions in natural language

Timeline: 2 months to production
Cost (Year 1): $280K (mostly API costs, no salaries)
Flexibility: HIGH (change prompts, switch providers instantly)
```
✅ **Verdict**: Fast, affordable, scalable, startup-friendly

### Specific Benefits of RAG for MobilityCorp

#### 1. **Zero ML Expertise Required**
```
Traditional Approach:
- "We need to tune the learning rate and batch size"
- "Model is overfitting, need more regularization"
- "Loss curve plateaued, try different architecture"
→ Requires PhD-level ML knowledge

RAG Approach:
- "Show me 5 similar docking photos from the past"
- "Is this bike properly docked? Here are examples"
- "Update the prompt to check for charging light"
→ Just natural language, any developer can do this
```

#### 2. **Continuous Improvement Without Retraining**
```
Traditional ML:
Week 1: Train model on 10K photos → 85% accuracy
Week 10: Accuracy drops to 78% (model drift)
→ Need to retrain: collect new data, label, train (2 weeks of work)

RAG:
Week 1: 10K photos in Weaviate → 85% accuracy
Week 10: 50K photos in Weaviate → 92% accuracy (more examples!)
→ No retraining, just automatic improvement as more data arrives
```

#### 3. **Handles Edge Cases Naturally**
```
Edge Case: "Bike docking in snow" (never seen during training)

Traditional ML:
- Model has never seen snow → fails catastrophically
- Need to collect snow photos, retrain model
- Takes weeks/months

RAG:
- LLM has general knowledge of snow (from pre-training)
- Retrieves similar "difficult weather" cases from past
- Makes reasonable decision even on first snow case
- Staff review → becomes example for future snow cases
```

#### 4. **Explainability & Trust**
```
Traditional ML Output:
"FAIL - Confidence: 0.87"
→ No explanation, user frustrated

RAG Output:
"FAIL - Docking pins not inserted (similar to Case #12345 from 2025-10-15).
 See reference photo showing correct docking."
→ User understands WHY, more likely to accept decision
```

#### 5. **Business Logic Integration**
```
Business Requirement: "Tolerate 20-25% damage margin of error"

Traditional ML:
- Hard to encode this in neural network
- Need custom loss function, complex training
- ML engineers required

RAG:
- Just tell the LLM in natural language:
  "If damage increases by less than 5%, mark as ACCEPTABLE.
   Between 5-25%, charge user €20-50.
   Above 25%, flag for insurance."
→ Changes in minutes, no retraining
```

#### 6. **Provider Flexibility (Critical for Startups)**
```
Startup Risk: "What if OpenAI raises prices 5X?"

Traditional ML (with custom models):
- Locked into our trained model
- Cannot switch easily
- Forced to pay or rebuild from scratch

RAG:
- Switch from OpenAI to Anthropic in minutes
- Same prompts work across providers
- Test multiple providers, use cheapest
→ No vendor lock-in, always best price
```

### Why Weaviate Cloud Specifically?

**Evaluation of RAG/Vector DB Options**:

| Criteria | Weaviate Cloud | Pinecone | Vectara | Supabase+pgvector |
|----------|----------------|----------|---------|-------------------|
| **Cost** | $95/mo (10M vectors) | $70/mo (100K vectors) ❌ | $99/mo (500K queries) ❌ | $25/mo ✅ |
| **Multimodal** | ✅ Native image support | ⚠️ Text only | ✅ Yes | ❌ Manual embeddings |
| **No ML Needed** | ✅ Fully managed | ✅ Yes | ✅ Yes | ⚠️ Some setup |
| **Open Source** | ✅ Can self-host | ❌ Closed | ❌ Closed | ✅ Postgres |
| **Avoid Lock-In** | ✅ Export anytime | ⚠️ Harder | ❌ Proprietary | ✅ Your DB |
| **Scale** | ✅ 10M → 100M easy | ✅ Yes | ⚠️ Limited free | ⚠️ Slower at scale |
| **EU Data Residency** | ✅ Yes | ✅ Yes | ✅ Yes | ✅ Yes |

**Why Weaviate Cloud Wins**:

1. **Best Balance of Cost & Features**:
   - $95/mo handles 3.6M photos/month comfortably
   - Pinecone too small (100K vectors), would need $500+/mo plan
   - Vectara limited queries (500K/mo = ~16K/day, we need 120K/day)
   - Supabase cheapest but requires more technical work

2. **True Multimodal RAG**:
   - Native image embedding support
   - Don't need to convert images to text descriptions
   - Finds visually similar photos (not just text matches)

3. **Open-Source Escape Hatch**:
   - If costs grow too high, can self-host Weaviate
   - Keep same API, just point to our own cluster
   - No rewrite needed (unlike Pinecone, Vectara)

4. **Startup-Friendly Pricing Curve**:
   ```
   Today (pilot): $95/mo for 10M vectors
   Next year (10 countries): $295/mo for 50M vectors
   Year 3 (self-host): $0/mo API fees, $2K/mo infrastructure
   → Grows with us, no cliff pricing
   ```

5. **Community & Support**:
   - Active open-source community (help with issues)
   - Good documentation for developers
   - Startup program (potential discounts)

### RAG Success Metrics for MobilityCorp

**What "Success" Looks Like**:

| Metric | Month 1 | Month 3 | Month 6 | Target |
|--------|---------|---------|---------|--------|
| Auto-approval rate | 75% | 85% | 93% | 95% |
| Human review needed | 20% | 10% | 5% | <5% |
| User dispute rate | 8% | 4% | 2% | <2% |
| Average confidence | 82% | 88% | 92% | >90% |
| API cost per verification | $1.20 | $0.90 | $0.77 | <$1.00 |
| Time to decision | 4s | 3s | 2s | <3s |

**Key Insight**: Accuracy improves naturally over time as RAG accumulates more examples, WITHOUT any engineering effort!

### The Bottom Line for Startups

**RAG + Multimodal LLMs = AI for Teams Without AI Expertise**

- ✅ **Fast**: 2 months to production (vs 12+ for custom models)
- ✅ **Affordable**: $280K/year (vs $600K+ for ML team)
- ✅ **Flexible**: Change prompts in minutes, switch providers instantly
- ✅ **Self-Improving**: Gets smarter with more data, no retraining
- ✅ **Explainable**: Natural language explanations build user trust
- ✅ **Scalable**: Handles 10X growth without proportional cost increase
- ✅ **Low Risk**: No vendor lock-in, multiple fallback options

**This is why RAG is the RIGHT choice for MobilityCorp.**

---

## System Components

### 1. Multimodal LLM Provider (Plug-and-Play Abstraction)

**Problem**: What if OpenAI changes pricing or shuts down? We need vendor flexibility.

**Solution**: **LLM Abstraction Layer** - Switch providers without code changes

**Supported Providers**:
- **OpenAI GPT-4V** (Vision) - $0.01 per image (current primary choice)
- **Anthropic Claude 3.5 Sonnet** - $0.008 per image (cheaper alternative)
- **Azure OpenAI** - Enterprise SLA, data residency control
- **Google Gemini Pro Vision** - Future option
- **Open-source multimodal models** - Fallback if all cloud providers fail (LLaVA, Qwen-VL)

**Provider Abstraction Interface** (Conceptual):
```
┌───────────────────────────────────────────────────────────────┐
│                  LLM Abstraction Layer                         │
│  (Single API, multiple backends)                              │
└───────────┬───────────────────────────────────────────────────┘
            │
    ┌───────┴────────┬────────────┬──────────────┬─────────────┐
    ↓                ↓            ↓              ↓             ↓
┌─────────┐  ┌──────────┐  ┌───────────┐  ┌─────────┐  ┌──────────┐
│ OpenAI  │  │ Anthropic│  │  Azure    │  │ Google  │  │ Open     │
│ GPT-4V  │  │ Claude   │  │  OpenAI   │  │ Gemini  │  │ Source   │
└─────────┘  └──────────┘  └───────────┘  └─────────┘  └──────────┘
```

**How to Switch Providers**:
- Configuration file: `LLM_PROVIDER=openai` → change to `LLM_PROVIDER=anthropic`
- All prompts provider-agnostic (work with GPT-4V, Claude 3.5, etc.)
- Automatic fallback if primary provider down (OpenAI → Claude → Azure)
- Cost tracking per provider to optimize spend

**Provider Selection Criteria**:
- **Default**: OpenAI GPT-4V (best accuracy for vision tasks as of 2025)
- **Cost Optimization**: Claude 3.5 Sonnet (20% cheaper, similar accuracy)
- **Enterprise/GDPR**: Azure OpenAI (data residency in EU, SLA guarantees)
- **Disaster Recovery**: Open-source models self-hosted (if all cloud providers fail)

---

### 2. RAG System: Weaviate Cloud

**Purpose**: Store historical photos + decisions to improve accuracy and provide context to LLMs

**Why Weaviate Cloud?**
- ✅ **Affordable**: $95/mo for 10M vectors (handles 3.6M photos/month comfortably)
- ✅ **No ML Engineers Needed**: Fully managed, automatic embeddings
- ✅ **Multimodal**: Native support for image embeddings
- ✅ **Open-Source**: Can self-host later if costs grow (avoid lock-in)
- ✅ **Privacy-Compliant**: Weaviate does NOT train models on customer data (see Data Privacy section below)
- ✅ **Fast**: Sub-100ms vector similarity search
- ✅ **Scalable**: Upgrade to 100M vectors if we expand to more countries

**What Gets Stored in Weaviate**:
```
For each rental return:
- User-submitted photos (4 for bikes/scooters, 2 for cars/vans)
- LLM decision (PASS/FAIL + confidence score)
- Metadata: vehicle_type, location, timestamp, weather_conditions
- Damage assessment: before_score, after_score, delta_percentage
- Human review overrides (if staff corrected AI decision)
```

**RAG Workflow**:
```
User submits photo →
  Weaviate finds 5 most similar past cases →
    LLM analyzes current photo WITH context from past cases →
      Returns decision + explanation referencing similar cases
```

**Example RAG Query**:
```
User submits docking photo →
  Weaviate: "Find 5 most similar docking photos from past 30 days"
  Retrieved: 3 PASS cases (docking pins visible), 2 FAIL cases (pins not visible)

LLM Prompt:
  "Analyze this photo. Here are 5 similar cases:
   - Case 1 (PASS): Docking pins clearly connected, charging light ON
   - Case 2 (PASS): Similar angle, pins inserted, light visible
   - Case 3 (FAIL): Pins not inserted, bike leaning on dock
   ...
   Is the current photo PASS or FAIL? Explain why."

LLM Response:
  "FAIL - Docking pins not fully inserted (similar to Case 3).
   Charging light not visible. Confidence: 92%"
```

---

### 3. Data Privacy & Security

**Critical Question**: Will Weaviate train AI models on our data?

**Answer: NO** ✅

**Weaviate Cloud Privacy Guarantees**:
1. **Zero Training on Customer Data**:
   - Weaviate is a vector database, NOT a model training platform
   - Your data stays in YOUR isolated cluster
   - Weaviate staff cannot access your data without explicit permission
   - Source: [Weaviate Security Whitepaper](https://weaviate.io/developers/weaviate/concepts/security)

2. **Data Residency**:
   - Can deploy Weaviate cluster in EU region (GDPR compliance)
   - Data never leaves EU borders
   - Complies with GDPR Article 44-50 (data transfer restrictions)

3. **Encryption**:
   - Data encrypted at rest (AES-256)
   - Encrypted in transit (TLS 1.3)
   - Access control via API keys (rotate every 90 days)

4. **LLM Provider Privacy**:
   - **OpenAI**: Enterprise API has zero data retention (not used for training)
   - **Anthropic Claude**: No data retention by default
   - **Azure OpenAI**: Data stays in your Azure tenant, Microsoft SLA
   - **Self-Hosted**: Full control (LLaVA, Qwen-VL) for maximum privacy

**Privacy Architecture**:
```
User Photo (contains vehicle + potentially bystanders)
  ↓
Pre-processing: Blur faces/license plates (OpenCV)
  ↓
Store in Weaviate (EU region, encrypted)
  ↓
Send to LLM via Enterprise API (zero retention)
  ↓
90-day retention → Archive to S3 (encrypted) → Delete after 7 years (GDPR)
```

**GDPR Compliance**:
- User consent: "I agree to photo verification for rental completion"
- Right to erasure: Users can request photo deletion (automated via API)
- Data minimization: Only store photos for 90 days (active), then archive
- Transparency: Show users which photos are stored and for how long

---

### 4. Ensuring Deterministic AI (Critical Judging Criterion)

**Problem**: LLMs are non-deterministic - same photo might get different results!

**How to Ensure Consistency**:

#### 4.1 Temperature = 0 (Maximum Determinism)
```
LLM API call:
{
  "model": "gpt-4-vision",
  "temperature": 0,  ← Forces deterministic outputs
  "messages": [...],
  "max_tokens": 300
}
```
- Temperature=0 makes LLM choose most probable answer every time
- Same photo + same prompt = same result (99.9% of the time)

#### 4.2 Structured Output Format
```
Force LLM to return JSON (not free-form text):
{
  "decision": "PASS" | "FAIL",
  "confidence": 0.92,
  "reasoning": "Docking pins clearly visible and inserted...",
  "similar_cases": [case_id_1, case_id_2],
  "requires_human_review": false
}
```
- Structured output easier to validate and test
- Catch hallucinations (e.g., confidence=1.0 should never happen)

#### 4.3 Prompt Versioning & Testing
```
Every prompt is versioned:
- v1.0: Initial docking verification prompt (2025-10-21)
- v1.1: Added "check charging light" instruction (2025-11-05)
- v2.0: Rewrote for better Claude 3.5 compatibility (2025-12-10)

Before deploying new prompt:
1. Test on 100 known-good photos → expect 95% PASS
2. Test on 100 known-bad photos → expect 95% FAIL
3. If test suite passes → deploy to production
4. If fails → rollback to previous prompt version
```

#### 4.4 Confidence Thresholds & Human Review
```
LLM Confidence Score → Action:
- 90-100%: Auto-approve (high confidence)
- 70-89%: Flag for human review (uncertain)
- 0-69%: Auto-reject (low confidence)

Track % of human reviews over time:
- Week 1: 25% need review (new system, learning)
- Week 4: 15% need review (RAG improving with more data)
- Week 12: <5% need review (stable, accurate)

Alert if human review rate suddenly spikes (indicates model drift)
```

#### 4.5 Continuous Validation (Production Monitoring)
```
Real-time metrics (Prometheus + Grafana):

1. Decision Consistency:
   - Same photo submitted twice → should get same decision
   - Alert if consistency drops below 95%

2. Confidence Score Distribution:
   - Track average confidence over time
   - Alert if average drops below 85% (model degrading)

3. Human Override Rate:
   - Staff review AI decision → staff disagrees
   - If override rate >10% → retrain/adjust prompts

4. User Dispute Rate:
   - Users complain "AI was wrong"
   - Investigate every dispute, add to test suite

5. A/B Testing New Prompts:
   - 90% traffic on v1.0 prompt, 10% on v1.1
   - Compare accuracy, confidence, user satisfaction
   - Only promote v1.1 if metrics improve
```

#### 4.6 Synthetic Testing (Daily)
```
Automated test suite runs every day:
- 50 known-good photos → expect 95%+ PASS
- 50 known-bad photos → expect 95%+ FAIL
- 20 edge cases (poor lighting, snow, rain) → measure confidence

If test suite pass rate drops below 90%:
- Block new deployments
- Alert on-call engineer
- Investigate: model provider issue? prompt drift? data quality?
```

#### 4.7 Explainable AI (Build Trust)
```
Every decision includes explanation:
"FAIL - Docking pins not inserted (similar to Case #12345 from 2025-10-15)"

User sees:
- Why AI made this decision
- Which past cases it referenced
- How confident it is (92%)
- Option to dispute with one tap

This transparency:
- Builds user trust
- Helps identify systematic errors
- Provides training data for improvement
```

---

## High-Level System Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                        Mobile App (User)                            │
│  - Camera capture (4 photos: front, back, left, right)             │
│  - Real-time preview ("Make sure docking pins are visible")        │
│  - Grace period countdown timer (5 mins)                            │
│  - Decision display (PASS/FAIL + explanation)                       │
└────────────────┬────────────────────────────────────────────────────┘
                 │ HTTPS (photos + metadata)
                 ↓
┌─────────────────────────────────────────────────────────────────────┐
│                    API Gateway + Load Balancer                      │
│  - Rate limiting (prevent abuse: max 10 photos/min per user)       │
│  - Authentication (JWT)                                             │
│  - Route to verification service                                    │
└────────────────┬────────────────────────────────────────────────────┘
                 │
                 ↓
┌─────────────────────────────────────────────────────────────────────┐
│                Photo Pre-Processing Service                         │
│  - Resize images to standard size (1024x1024)                       │
│  - Blur faces/license plates (OpenCV, GDPR compliance)              │
│  - Compress to reduce API costs (JPEG quality 85%)                  │
│  - Extract metadata: GPS, timestamp, vehicle_id                     │
└────────────────┬────────────────────────────────────────────────────┘
                 │
        ┌────────┴────────┐
        ↓                 ↓
┌──────────────┐  ┌──────────────────────────────────────────────────┐
│  Weaviate    │  │      Verification Service (Core Logic)           │
│  Cloud (RAG) │←─┤                                                  │
│              │  │  1. Fetch similar cases from Weaviate (RAG):     │
│  - 10M       │  │     "Find 5 photos similar to this docking photo"│
│    vectors   │  │                                                  │
│  - EU region │  │  2. Build prompt with context:                   │
│  - $95/mo    │  │     - Current photo                              │
│              │  │     - 5 similar past cases (with decisions)      │
│              │  │     - Business rules (tolerance margins)         │
│              │  │                                                  │
│              │  │  3. Send to LLM Abstraction Layer                │
└──────────────┘  └─────────────┬────────────────────────────────────┘
                                │
                                ↓
┌─────────────────────────────────────────────────────────────────────┐
│               LLM Abstraction Layer (Provider Switch)               │
│                                                                      │
│  Config: LLM_PROVIDER=openai                                        │
│          LLM_FALLBACK=anthropic                                     │
│          TEMPERATURE=0                                              │
│                                                                      │
│  Try primary provider (OpenAI) →                                    │
│    If fails/slow → fallback to Anthropic →                          │
│      If both fail → queue for manual review                         │
└────────────────┬────────────────────────────────────────────────────┘
                 │
        ┌────────┴────────┬────────────────────┐
        ↓                 ↓                     ↓
┌───────────────┐  ┌──────────────┐  ┌─────────────────┐
│ OpenAI        │  │ Anthropic    │  │ Azure OpenAI    │
│ GPT-4V        │  │ Claude 3.5   │  │ (Enterprise)    │
│               │  │              │  │                 │
│ $0.01/image   │  │ $0.008/image │  │ SLA + Privacy   │
└───────┬───────┘  └──────┬───────┘  └────────┬────────┘
        └──────────────────┴──────────────────┘
                           │
                           ↓ (JSON response)
┌─────────────────────────────────────────────────────────────────────┐
│                    Response Handler                                 │
│  {                                                                   │
│    "decision": "FAIL",                                              │
│    "confidence": 0.92,                                              │
│    "reasoning": "Docking pins not inserted (similar to Case #1234)",│
│    "requires_human_review": false                                   │
│  }                                                                   │
│                                                                      │
│  If confidence < 0.7 → Queue for human review                       │
│  If confidence >= 0.7 → Return to user immediately                  │
└────────────────┬────────────────────────────────────────────────────┘
                 │
        ┌────────┴────────┐
        ↓                 ↓
┌───────────────┐  ┌──────────────────────────────────────────────────┐
│ Store Results │  │      Notification Service                        │
│               │  │                                                  │
│ - Weaviate    │  │  If FAIL:                                        │
│   (add case   │  │    Push: "⚠️ Not docked! Fix in 5 mins"         │
│    to RAG)    │  │    Show: Similar case photo as example          │
│               │  │                                                  │
│ - PostgreSQL  │  │  If PASS:                                        │
│   (decision   │  │    Push: "✅ Return successful! Payment posted" │
│    audit log) │  │    Email: Receipt with photo proof              │
│               │  │                                                  │
│ - S3 Bronze   │  │  If Human Review:                                │
│   (raw photo) │  │    Internal: Add to staff review queue           │
└───────────────┘  └──────────────────────────────────────────────────┘
```

---

## Use Case Implementations

### 1. Docking Verification AI

**Workflow**:
```
User taps "End Rental" in app
  ↓
IoT sensor checks dock connection
  ↓
If disconnected → App requires photo submission
  ↓
Photo sent to Verification Service:
  1. Weaviate RAG finds 5 similar docking photos
  2. LLM (GPT-4V/Claude) analyzes with prompt:
     "Is this bike properly docked? Check:
      - Docking pins inserted into dock slots
      - Charging light indicator visible (green/blue)
      - Bike upright and stable
      Here are 5 similar cases: [RAG context]"
  3. LLM returns: {decision: FAIL, confidence: 0.92, reasoning: "..."}
  ↓
If FAIL: Push notification "⚠️ Not docked! You have 5 minutes to fix or get charged €0.50/min"
  ↓
User has grace period to correct → resubmit photo
  ↓
If still FAIL after 5 mins: Late return charges apply
If PASS: Rental successfully ended, payment processed
```

**RAG Benefit**:
- As more users submit photos, Weaviate learns what "properly docked" looks like
- Different bike models, dock types, lighting conditions → all captured in RAG
- LLM gets smarter over time WITHOUT retraining (just more RAG examples)

### 2. Damage Detection AI

**Workflow**:
```
START OF RENTAL:
User arrives at vehicle → App prompts "Take 4 photos (front, back, left, right)"
  ↓
LLM analyzes baseline condition:
  Weaviate RAG: "Find damage assessment history for this vehicle"
  LLM Prompt: "Identify any existing damage: scratches, dents, flat tires"
  LLM Response: {damage_score: 20%, items: ["Small scratch on left panel"]}
  ↓
Store damage "fingerprint" with booking record
  ↓
Show user: "⚠️ Existing damage detected: scratch on left panel (see photo)"
  ↓
User confirms condition → Rental begins

END OF RENTAL:
User submits 4 return photos (same angles)
  ↓
LLM compares before/after:
  Weaviate RAG: "Retrieve before photos + damage score for booking #12345"
  LLM Prompt: "Compare before/after photos. Calculate damage delta.
               Before damage: 20% (small scratch)
               After photos: [current images]
               What is new damage? Provide percentage increase."
  LLM Response: {before: 20%, after: 24%, delta: 4%, severity: "MINOR"}
  ↓
If delta <= 5%: Auto-approve (within tolerance margin)
If delta 5-25%: Charge €20-50, notify user with before/after comparison
If delta > 25%: Flag for insurance review, hold deposit
```

**Damage Tolerance Logic**:
- **Minor** (0-5% delta): Acceptable margin of error, no charge
- **Moderate** (5-25% delta): User liable, auto-charge based on severity
- **Major** (>25% delta): Insurance claim, manual review required

**RAG Benefit**:
- Historical damage patterns help LLM understand "normal wear and tear"
- Different vehicle types have different tolerance thresholds (scooters vs cars)
- Seasonal patterns (winter salt damage, summer heat cracks) learned from data

### 3. Return Photo Verification AI (Cars/Vans)

**Workflow**:
```
User ends car/van rental → App requires 2 photos:
  1. Wide shot: vehicle in parking bay + bay number visible
  2. Close-up: charging cable plugged into vehicle port
  ↓
LLM analyzes with RAG context:
  Weaviate RAG: "Find photos of this parking bay from past returns"
  LLM Prompt: "Verify:
               1. Vehicle in correct parking bay (compare bay number)
               2. Charging cable connected to vehicle charging port
               3. Vehicle matches license plate from booking
               Reference photos: [RAG examples of correct returns at this bay]"
  LLM Response: {decision: FAIL, reasoning: "Charging cable not visible"}
  ↓
If FAIL: Push notification "❌ Charging cable not detected. Please plug in."
  ↓
Grace period: 10 minutes to correct (longer than bikes due to charging setup)
  ↓
If fixed: PASS → Rental ended
If not fixed: Late return charges + vehicle marked "not charged"
```

**RAG Benefit**:
- Each parking bay has reference photos (what "correct" looks like)
- Different lighting conditions (day/night) handled by RAG examples
- Multi-language bay signage (German, French, Spanish) learned from RAG

---

## Cost Analysis

### Monthly Operating Costs (10,000 vehicles × 3 rentals/day = 30K rentals/day)

**Weaviate Cloud**:
- Plan: Standard ($95/mo for 10M vectors)
- Storage: 30K rentals/day × 30 days × 4 photos = 3.6M photos/month ✅ (fits)
- Cost: **$95/month**

**LLM API Costs** (Primary: OpenAI GPT-4V):
- Cost per image: $0.01
- Usage: 30K rentals/day × 4 photos = 120K images/day = 3.6M images/month
- **Monthly cost: $36,000/month** 💰

**Cost Optimization Strategies**:

1. **Switch to Claude 3.5 Sonnet** (20% cheaper):
   - Cost per image: $0.008
   - Monthly savings: $7,200/month (3.6M × $0.002)
   - **New cost: $28,800/month**

2. **Tiered Verification** (Reduce API calls):
   ```
   Use cheap/fast check first, expensive check only if needed:

   Step 1: IoT sensor check (FREE)
     - If docked properly → No photo needed, skip LLM
     - Only 20% fail sensor check → need photo verification

   Step 2: Simple rule-based check (FREE)
     - Image too dark? → Ask user to retake
     - Image blurry? → Ask user to retake
     - GPS wrong location? → Instant FAIL, no LLM needed

   Step 3: LLM verification (PAID)
     - Only 80% of photos reach this step
     - New volume: 3.6M × 0.8 = 2.88M images/month
     - New cost: $28,800 × 0.8 = $23,040/month
   ```

3. **Caching** (Avoid redundant LLM calls):
   ```
   If user submits same photo twice → return cached result (FREE)
   Estimated 5% cache hit rate → $1,152/month savings
   ```

**Total Optimized Monthly Cost**:
- Weaviate: $95
- Claude 3.5 API: $23,040 (with tiered verification + caching)
- **Total: ~$23,135/month** for 3.6M image verifications
- **Cost per verification: $0.77** (vs $8 manual review labor cost)

**Annual Savings vs Manual Review**:
- Manual review cost: 600 staff-hours/day × €25/hour × 365 days = **€5.5M/year**
- AI verification cost: $23K/month × 12 = **$277K/year (€255K)**
- **Net savings: €5.2M/year** 💰💰💰

---

## Handling AI Uncertainty (Critical Judging Criterion)

### Problem Scenarios & Solutions

**Scenario 1: OpenAI Changes Pricing**
```
Current: $0.01/image
New price: $0.05/image (5x increase!)

Response (automatic):
1. LLM Abstraction Layer detects cost spike
2. Switch to Claude 3.5 ($0.008/image) within minutes
3. No code changes, just config: LLM_PROVIDER=anthropic
4. Monitor accuracy for 24 hours, rollback if worse
```

**Scenario 2: OpenAI Service Outage**
```
OpenAI API returns 503 errors

Response (automatic):
1. Circuit breaker triggers after 3 failed requests
2. Fallback to Anthropic Claude immediately
3. Alert on-call engineer
4. Continue operating with zero downtime
```

**Scenario 3: OpenAI Shuts Down**
```
Hypothetical: OpenAI acquired, service discontinued

Response (planned):
1. Switch to Anthropic Claude (primary alternative)
2. Deploy Azure OpenAI (enterprise SLA)
3. Worst case: Self-host LLaVA (open-source multimodal LLM)
   - Deploy on AWS GPU instances (g5.xlarge)
   - Slightly higher latency, but full control
   - Cost: ~$1.20/hour = $864/month (cheaper than cloud APIs!)
```

**Scenario 4: All Cloud Providers Become Too Expensive**
```
Response (nuclear option):
1. Self-host open-source models:
   - LLaVA 1.6 (34B parameters, multimodal)
   - Deploy on Kubernetes with GPU nodes
   - Use vLLM for fast inference
2. Fine-tune on MobilityCorp data (now have enough examples from RAG)
3. Trade-off: Higher infrastructure cost, but zero per-query cost
```

### Multi-Provider Cost Tracking
```
Track spend by provider in real-time:

Week 1:
- OpenAI: $18,000 (75% traffic)
- Claude: $6,000 (25% traffic for A/B test)
- Total: $24,000

Week 2 (OpenAI raises prices):
- OpenAI: $30,000 (would be cost at 100%)
- Claude: $20,000 (at 100% traffic)
→ Decision: Switch 100% to Claude, save $10K/month
```

---

## Validation & Verification Strategy

### How to Ensure AI is Working Correctly (Critical Judging Criterion)

**1. Pre-Deployment Validation**
```
Before launching to production:
- Collect 1,000 labeled photos (500 PASS, 500 FAIL)
- Run through LLM with prompt v1.0
- Measure accuracy: Target >95%
- If fails → revise prompt, retry
```

**2. Continuous Production Monitoring**
```
Real-time dashboards (Grafana):

Metric 1: Confidence Score Distribution
- Track average confidence over time
- Alert if drops below 85%
- Chart: [Confidence over 30 days]

Metric 2: Human Review Rate
- Track % of cases needing manual review
- Target: <5% after 3 months
- Alert if exceeds 20% (model degrading)

Metric 3: User Dispute Rate
- Users click "Dispute Decision"
- Track disputes per 1,000 rentals
- Target: <10 disputes per 1,000
- Investigate every dispute

Metric 4: Decision Consistency
- Same photo submitted twice → same decision?
- Measure consistency rate (should be >98%)
- Alert if drops below 95%

Metric 5: Provider Performance
- Track accuracy by provider:
  - OpenAI: 96% accuracy
  - Claude: 94% accuracy
  → Use OpenAI for critical decisions
```

**3. Ground Truth Feedback Loop**
```
Every human review becomes training data:

User submits photo → AI says FAIL → User disputes → Staff reviews → Staff says PASS
  ↓
This is a "false positive" error
  ↓
Add to test suite: "Photo #12345 should be PASS"
  ↓
Test new prompts against this case
  ↓
Gradually improve accuracy
```

**4. Weekly Model Quality Reports**
```
Automated report every Monday:
- Total verifications: 210K (last week)
- Auto-approved: 195K (93%)
- Human reviewed: 15K (7%)
- User disputes: 420 (0.2%)
- Average confidence: 91%
- Provider costs: OpenAI $21K, Claude $0
- Test suite pass rate: 97%

Action items:
- 3 new failure patterns identified
- Update prompt to handle edge case: "bike in snow"
- Deploy prompt v1.2 to 10% traffic for A/B test
```

**5. Quarterly Accuracy Audits**
```
Every quarter:
1. Randomly sample 500 AI decisions
2. Have 3 staff members review each (majority vote)
3. Calculate AI accuracy vs human consensus
4. Target: >95% agreement with humans
5. If below target → investigate root causes
```

---

## Implementation Roadmap

### Phase 1: Pilot (Month 1-2)
**Scope**: Top 50 parking bays, one vehicle type (e-scooters)
- Deploy Weaviate Cloud ($95/mo)
- Implement LLM Abstraction Layer
- Launch docking verification only
- Collect 10K photos for RAG
- **Success Metrics**:
  - 80% auto-approval rate
  - <10% user dispute rate
  - 95% user satisfaction

### Phase 2: Expand (Month 3-4)
**Scope**: All vehicle types, all parking bays
- Add damage detection (before/after comparison)
- Add return photo verification (cars/vans)
- Scale Weaviate to handle 3.6M photos/month
- **Success Metrics**:
  - 90% auto-approval rate
  - <5% human review needed
  - Reduce manual review staff by 50%

### Phase 3: Optimize (Month 5-6)
**Scope**: Cost reduction, accuracy improvement
- A/B test OpenAI vs Claude (find cheaper option)
- Implement tiered verification (reduce API calls)
- Deploy caching layer
- Fine-tune prompts based on 6 months of data
- **Success Metrics**:
  - Reduce API costs by 30%
  - Increase auto-approval to 95%
  - <2% user dispute rate

### Phase 4: Scale (Month 7-12)
**Scope**: Multi-country expansion
- Deploy to 10 EU countries
- Multi-language support (German, French, Spanish, etc.)
- Regional prompt tuning (different parking bay styles)
- Self-hosted fallback (open-source LLM) for disaster recovery
- **Success Metrics**:
  - Handle 100K rentals/day
  - <€20K/month API costs
  - 98% uptime SLA

---

## Consequences

### Positive

**Business Value**:
- **€5.2M/year savings** vs manual review
- **Faster rental completion**: 3 seconds vs 8 minutes
- **Revenue protection**: €2M/year from proper docking enforcement
- **Dispute reduction**: 50% fewer damage disputes

**Technical Advantages**:
- **No ML engineers needed**: Focus on business, not model training
- **Vendor flexibility**: Switch LLM providers in minutes
- **Scales automatically**: Handle 10X growth without hiring staff
- **Continuous improvement**: RAG gets smarter with more data (no retraining)

**User Experience**:
- **Real-time feedback**: Know immediately if return accepted
- **Grace period**: 5-10 minutes to fix issues (reduces anxiety)
- **Transparent**: AI explains WHY it failed (builds trust)
- **Multi-language**: Works in all EU languages

**Privacy & Compliance**:
- **GDPR-compliant**: Data in EU, 90-day retention, face blurring
- **No training on customer data**: Weaviate + Enterprise LLM APIs
- **Audit trail**: Every decision logged for disputes

### Negative

**Costs**:
- **LLM API fees**: $23K/month (still 90% cheaper than manual review)
- **Weaviate**: $95/month (affordable)
- **Infrastructure**: Load balancers, API gateways, monitoring
- **Risk**: API costs could increase (mitigated by provider flexibility)

**Technical Complexity**:
- **LLM Abstraction Layer**: Need to maintain multi-provider support
- **Prompt Engineering**: Requires experimentation to optimize accuracy
- **Monitoring**: Need robust observability (Grafana, alerts, on-call)

**User Friction**:
- **False positives**: Legitimate returns rejected (~5% of cases)
- **Photo quality**: Users frustrated if photos rejected due to blur/darkness
- **Learning curve**: Users need to learn how to take good verification photos

**AI Limitations**:
- **Non-deterministic**: Same photo might get slightly different results
- **Model drift**: Accuracy degrades over time (need continuous monitoring)
- **Edge cases**: Unusual situations (snow, fog, night) harder to handle
- **Explainability**: AI reasoning sometimes hard to understand

### Mitigation Strategies

**Cost Control**:
- Implement tiered verification (reduce API calls by 20%)
- Cache duplicate photos (5% savings)
- Switch to cheaper providers (Claude vs OpenAI)
- Self-host fallback for disaster recovery

**Accuracy**:
- Continuous monitoring (Grafana dashboards, alerts)
- Weekly quality reports
- Quarterly accuracy audits
- Ground truth feedback loop (human corrections)

**User Experience**:
- In-app photo guidance ("Frame like this 📸")
- Instant retry if photo quality poor
- Grace period to fix issues
- Live support chat (GenAI chatbot + human escalation)

**Reliability**:
- Multi-provider fallback (OpenAI → Claude → Azure)
- Circuit breaker pattern (auto-switch on errors)
- Manual review queue (graceful degradation)
- 99.9% uptime SLA with proper monitoring

---

## Related Decisions

- ADR-001: Adopt Open Mobility Foundation Standards (MDS/CDS for location verification)
- ADR-003: LLM Provider Selection and Abstraction Strategy (to be created)
- ADR-004: Privacy and GDPR Compliance for Photo Verification (to be created)
- ADR-005: RAG Architecture and Vector Database Selection (to be created)
- ADR-006: Monitoring and Observability Strategy (to be created)

---

## References

**Multimodal LLMs**:
- [OpenAI GPT-4V Documentation](https://platform.openai.com/docs/guides/vision)
- [Anthropic Claude 3.5 Vision](https://docs.anthropic.com/claude/docs/vision)
- [Azure OpenAI Service](https://azure.microsoft.com/en-us/products/ai-services/openai-service)

**RAG & Vector Databases**:
- [Weaviate Cloud Documentation](https://weaviate.io/developers/weaviate/cloud)
- [Weaviate Security Whitepaper](https://weaviate.io/developers/weaviate/concepts/security)
- [RAG Best Practices](https://www.pinecone.io/learn/retrieval-augmented-generation/)

**Privacy & Compliance**:
- [GDPR Article 44-50 (Data Transfers)](https://gdpr.eu/tag/chapter-5/)
- [OpenAI Enterprise Privacy](https://openai.com/enterprise-privacy/)
- [Anthropic Commercial Terms](https://www.anthropic.com/legal/commercial-terms)

**Cost Optimization**:
- [Multimodal LLM Pricing Comparison 2025](https://artificialanalysis.ai/)
- [Vector Database Cost Analysis](https://vector-database-benchmarks.com/)

---

## Notes

**Date**: October 21, 2025
**Authors**: BRINUS Team (Rafael, Prashant, Khaled, João Vinagre)
**Reviewers**: To be reviewed by competition judges

**Key Innovation for Judges**:
- ✅ **RAG-first approach** (no ML engineers needed, faster time-to-market)
- ✅ **LLM provider abstraction** (addresses "AI uncertainty" judging criterion)
- ✅ **Comprehensive validation strategy** (addresses "verification" judging criterion)
- ✅ **Privacy-first design** (GDPR-compliant, no training on customer data)
- ✅ **Cost-effective** (€5.2M/year savings vs manual review)
- ✅ **Graceful degradation** (multiple fallback layers ensure uptime)

**Estimated Business Impact**:
- **Cost Savings**: €5.2M/year in operational efficiency
- **Revenue Protection**: €2M/year from proper docking enforcement
- **Dispute Reduction**: 50% fewer damage disputes (faster resolution)
- **Scalability**: Support 10X growth without proportional staff increase
- **Time-to-Market**: 2 months to pilot vs 12+ months for custom model training
