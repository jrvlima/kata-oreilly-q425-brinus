# RAG API - Retrieval-Augmented Generation for grounded AI assistance with citations

## Context

MobilityCorp staff (operations, support, planning) need AI assistant that can:

- Answer complex questions using real operational data (not hallucinated)
- Provide citations to source data for trustworthiness and verification
- Combine historical context (Vector DB) with real-time state (Read Model DB)
- Reason across structured (SQL tables) and unstructured (incident reports) data
- Support multi-turn conversations with session context

## Problem

Pure LLM approaches fail for enterprise operational AI assistants:

1. **Hallucination Risk**: GPT-4 invents vehicle IDs, battery levels, booking counts without grounding in actual data
2. **Staleness**: LLM training data ends in 2023; can't answer "What's fleet status right now?"
3. **No Citations**: Users can't verify AI responses; "trust but can't verify" blocks adoption
4. **Context Limits**: LLM 128k context can't fit entire Gold database; must retrieve relevant subset
5. **No Reasoning Over Data**: LLM can't execute SQL to compute "What's average utilization by location last 7 days?"

## Solution Justification

RAG architecture combines retrieval (search) + generation (LLM) for grounded, verifiable responses:

- **Hybrid Retrieval**: Vector search (semantic) + SQL queries (structured) retrieve relevant context
- **Prompt Augmentation**: Retrieved docs/rows injected into LLM prompt: "Given this data... answer question"
- **Citation Extraction**: LLM response includes source references (doc IDs, SQL table.column)
- **Tool Use**: LLM can invoke SQL queries, API calls (vehicle status), or vector searches as needed
- **Provider Abstraction**: LiteLLM routes to OpenAI, Anthropic, AWS Bedrock, or self-hosted models

## Use Cases

### 1. Operations Q&A - Real-Time Fleet Status
- **Question**: "How many vehicles in Berlin are available with >50% battery right now?"
- **RAG Flow**:
  1. RAG API receives question
  2. LLM generates SQL: `SELECT COUNT(*) FROM available_vehicles WHERE location='Berlin' AND battery_pct > 50`
  3. Execute query against Read Model DB → Result: 42 vehicles
  4. LLM formats response: "There are 42 vehicles in Berlin with >50% battery. Source: available_vehicles table."
- **Benefit**: Grounded answer with citation; operations trusts data for decision-making

### 2. Historical Context - Incident Troubleshooting
- **Question**: "Why did Amsterdam have low availability last Tuesday?"
- **RAG Flow**:
  1. Embed question → Search Vector DB for similar incidents (filter: location=Amsterdam, date=last Tuesday)
  2. Retrieve: "2024-10-15: Demand spike during commute + 12 vehicles low battery + rebalancing delayed"
  3. Query Gold `kpi_summary_daily` for utilization % and demand metrics
  4. LLM synthesizes: "Amsterdam had 92% utilization (vs. 75% average). 12 vehicles needed charging. Rebalancing was delayed by 45 minutes. Citation: daily_ops_summary 2024-10-15, kpi_summary_daily."
- **Benefit**: AI combines unstructured (summaries) + structured (metrics) for root cause analysis

### 3. Planning Support - Demand Forecasting Insights
- **Question**: "Which locations should we add more eBikes to next quarter?"
- **RAG Flow**:
  1. Query Gold `demand_features_hourly` for top 5 locations by unmet demand (bookings with no available vehicles)
  2. Vector search for expansion playbooks: "How to evaluate new location capacity?"
  3. LLM response: "Top locations with unmet demand: 1) Berlin (350 lost bookings/week) 2) Paris (280) 3) Amsterdam (240). Recommend adding 20 eBikes to Berlin. Citation: demand_features_hourly, expansion_playbook_v3."
- **Benefit**: Data-driven expansion planning with provenance for exec buy-in

### 4. Multi-Turn Conversation - Customer Support
- **Turn 1**: "Show me bookings for customer ID 12345"
- **RAG**: Queries Read Model → "Customer 12345 has 3 active bookings: eBike in Berlin, Car in Amsterdam, Scooter in Paris"
- **Turn 2**: "Which one has lowest battery?"
- **RAG**: Maintains session context → Queries vehicle battery levels → "eBike in Berlin (22% battery). Citation: vehicle_id VEH-789, last_updated 2 mins ago."
- **Benefit**: Conversational interface replaces 5-screen support dashboard; faster resolution

### 5. Compliance Assistant - GDPR Data Access Request
- **Question**: "What data do we have for user ID 67890?"
- **RAG Flow**:
  1. LLM generates queries:
     - `SELECT * FROM bookings WHERE user_id=67890`
     - `SELECT * FROM payments WHERE user_id=67890`
     - Vector search for user in incident logs
  2. Execute queries; aggregate results
  3. LLM formats: "User 67890 data: 15 bookings, 12 payments (€450 total), 2 support tickets. Citations: bookings table, payments table, incident_log."
- **Benefit**: GDPR compliance team responds to data requests in 5 minutes vs. 2 hours

## Technology Choice

**Selected**: FastAPI + LlamaIndex + LiteLLM

**Rationale**:
- **FastAPI**: High-performance Python web framework; async support for concurrent RAG requests
- **LlamaIndex**: RAG framework with prebuilt components
  - Query engines (SQL, vector, hybrid)
  - Citation extraction and response synthesis
  - Multi-step reasoning (ReAct, chain-of-thought)
  - Supports custom retrievers (Qdrant, Postgres)
- **LangChain**: Alternative with richer tool ecosystem (but more complex API)
- **LiteLLM**: Provider abstraction layer
  - Single API for OpenAI, Anthropic Claude, AWS Bedrock, Azure OpenAI, self-hosted (Ollama, vLLM)
  - Cost tracking per model/user
  - Fallback routing (if OpenAI down → use Anthropic)
  - EU compliance: Route to Bedrock eu-central-1 or self-hosted models
- **AWS Bedrock**: Managed LLM service in EU regions (Claude 3.5, Llama 3)
  - No data leaves AWS; GDPR-compliant
  - Fine-tuning for MobilityCorp domain terms
