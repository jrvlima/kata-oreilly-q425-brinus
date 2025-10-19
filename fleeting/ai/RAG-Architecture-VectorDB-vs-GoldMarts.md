# RAG Architecture: Why Vector DB AND Gold Marts?

## Overview

This document explains why MobilityCorp's RAG (Retrieval-Augmented Generation) architecture requires **both** Vector DB and Gold Marts. They serve complementary but different purposes in providing grounded AI assistance.

## Vector DB (Qdrant) - Semantic/Unstructured Search

### Purpose
Find **semantically similar** content using natural language queries

### Data Type
- Unstructured text (incident reports, maintenance logs, ops summaries, playbooks)
- Pre-computed embeddings (384-1536 dimensional vectors)

### Query Pattern
- "Why did Amsterdam have low availability last week?"
- Returns: Text documents with similar **meaning** (not just keyword matches)

### Example
```
Query: "vehicles not charging"
Matches: Documents containing "battery issue", "EV charge problem", "power failure"
→ Semantic similarity, not exact keyword match
```

## Gold Marts (ClickHouse/Postgres) - Structured/Factual Data

### Purpose
Query **structured facts** and current system state using SQL

### Data Type
- Structured tables (KPIs, metrics, aggregates)
- Dimensional models (location, vehicle_type, date dimensions)
- Real-time operational state

### Query Pattern
- "How many eBikes are available in Berlin right now?"
- Returns: Precise numeric answers from structured data

### Example
```sql
SELECT COUNT(*) FROM available_vehicles
WHERE location='Berlin' AND vehicle_type='eBike' AND battery_pct > 50
→ Result: 42
```

## Why RAG Needs BOTH

RAG combines them to answer complex questions that require **context (Vector DB) + facts (Gold Marts)**:

### Example 1: Hybrid Question

**Question**: "Why did Amsterdam have low availability last Tuesday, and what was the utilization rate?"

**RAG Flow**:

1. **Vector DB**: Retrieves incident summaries from last Tuesday
   - Result: "Demand spike during morning commute + 12 vehicles low battery + rebalancing delayed"

2. **Gold Marts**: Queries actual utilization metrics
   ```sql
   SELECT utilization_pct FROM kpi_summary_daily
   WHERE location='Amsterdam' AND date='2024-10-15'
   ```
   - Result: 92% (vs. 75% average)

3. **LLM Synthesizes**:
   > "Amsterdam had 92% utilization (17% above average). Root cause: morning demand spike combined with 12 vehicles requiring battery swap. Rebalancing was delayed by 45 minutes.
   >
   > **Citations**: daily_ops_summary 2024-10-15, kpi_summary_daily."

**Why both are needed**:
- Vector DB provides the **narrative context** (why it happened)
- Gold Marts provides the **precise numbers** (how much impact)

### Example 2: Grounded Recommendations

**Question**: "Which locations should we expand eBike fleet next quarter?"

**RAG Flow**:

1. **Gold Marts**: Query unmet demand metrics
   ```sql
   SELECT location_id, SUM(lost_bookings) AS unmet_demand
   FROM demand_features_hourly
   WHERE vehicle_type='eBike' AND date >= '2024-07-01'
   GROUP BY location_id
   ORDER BY unmet_demand DESC
   LIMIT 5
   ```
   - Result: Berlin (350 lost bookings/week), Paris (280), Amsterdam (240)

2. **Vector DB**: Search for expansion playbooks
   - Query: "How to evaluate new location capacity?"
   - Result: "Expansion_Playbook_v3: Consider unmet demand, competitor presence, charging infrastructure..."

3. **LLM Synthesizes**:
   > "Top expansion candidates:
   > 1. Berlin (350 lost bookings/week)
   > 2. Paris (280)
   > 3. Amsterdam (240)
   >
   > Recommend adding 20 eBikes to Berlin based on unmet demand analysis.
   >
   > **Citations**: demand_features_hourly, expansion_playbook_v3."

**Why both are needed**:
- Gold Marts provides **quantitative evidence** (data-driven ranking)
- Vector DB provides **qualitative guidance** (how to make the decision)

## Key Differences Summary

| Aspect | Vector DB | Gold Marts |
|--------|-----------|------------|
| **Data Type** | Unstructured text (docs, logs) | Structured tables (metrics, facts) |
| **Query Type** | Semantic similarity | SQL queries |
| **Answers** | "What happened?" "How was it resolved?" | "How many?" "What's the value?" |
| **Strength** | Context, narrative, tribal knowledge | Precision, aggregation, current state |
| **Search Method** | Cosine similarity on embeddings | Indexes on columns |
| **Example** | "Similar battery drain incidents" | "42 eBikes available in Berlin" |
| **Use Cases** | Troubleshooting, playbooks, historical patterns | Real-time metrics, KPIs, dashboards |

## Why Not Just One?

### If only Vector DB:
- ❌ Can't answer "Exactly how many vehicles?" (embeddings aren't precise)
- ❌ Can't compute aggregations (SUM, AVG, percentiles)
- ❌ No real-time operational state (Read Model DB writes to Gold, not Vector DB)
- ❌ Poor at exact numeric queries ("vehicles with battery between 40-50%")

### If only Gold Marts:
- ❌ Can't search unstructured text (incident reports, playbooks)
- ❌ No semantic search ("battery issue" ≠ "vehicles not charging" in SQL)
- ❌ Can't retrieve historical context/narratives
- ❌ Requires exact column names (user can't ask natural language questions)

## Data Flow Architecture

```
┌─────────────────┐
│   Gold Marts    │ ← dbt transforms structured data
│  (ClickHouse)   │
└────────┬────────┘
         │
         │ SQL queries (precise numbers)
         │
         ├──────────────────────┐
         │                      │
         ▼                      ▼
    ┌─────────┐           ┌─────────────┐
    │ RAG API │◄──────────│  Vector DB  │
    │(LiteLLM)│           │  (Qdrant)   │
    └─────────┘           └──────┬──────┘
         │                       ▲
         │                       │
         │                       │ Embeddings (semantic search)
         │                       │
         ▼                       │
    ┌──────────┐          ┌─────┴──────────┐
    │   User   │          │ Embedding      │
    │(Ops/Staff)│         │ Pipeline       │
    └──────────┘          └────────────────┘
                                 ▲
                                 │
                          Daily ops summaries,
                          incident reports,
                          playbooks
```

## Architecture Benefits for O'Reilly Katas

This hybrid RAG design demonstrates:

✅ **AI Best Practices**: Grounding LLMs with retrieval (prevents hallucination)

✅ **Architectural Characteristics Match**: Combines real-time (Gold Marts) + semantic search (Vector DB)

✅ **Validation & Verification**: Citations enable humans to verify AI responses
   - Example: "Source: kpi_summary_daily.utilization_pct, 2024-10-15"

✅ **Uncertainty Handling**: LLM can say "No data found in Gold Marts for Berlin on that date" rather than inventing numbers

✅ **Multi-Modal Retrieval**:
   - Semantic (Vector DB): "Find similar past incidents"
   - Structured (Gold Marts): "Get exact metrics for comparison"
   - Hybrid (Both): "Explain anomaly with supporting data"

## Use Case Matrix

| Question Type | Vector DB | Gold Marts | RAG Combines |
|---------------|-----------|------------|--------------|
| "Why did X happen?" | ✅ Primary | ❌ | Context from vectors |
| "How many X?" | ❌ | ✅ Primary | Precise count from SQL |
| "What's the trend?" | ❌ | ✅ Primary | Time-series aggregation |
| "How do I fix Y?" | ✅ Primary | ❌ | Retrieve playbook steps |
| "Why X and what's the impact?" | ✅ Needed | ✅ Needed | **Both required** |
| "Similar incidents to Z?" | ✅ Primary | ⚠️ Optional | Vector similarity + optional metric comparison |

## Implementation Notes

### Vector DB Configuration
- **Collection**: `operational_knowledge`
- **Vector Size**: 384 (all-MiniLM-L6-v2) or 1536 (text-embedding-ada-002)
- **Metadata Fields**: date, location_id, tags[], source_table, incident_type
- **Hybrid Search**: Vector similarity + metadata filters (e.g., `date >= '2024-10-01'`)

### Gold Marts Tables (Examples)
- `kpi_summary_daily`: Aggregated KPIs per location/date
- `fleet_health_per_vehicle_daily`: Per-vehicle metrics
- `demand_features_hourly`: Demand patterns for forecasting
- `available_vehicles`: Current real-time availability (from Read Model DB)

### RAG API Query Flow
```python
def answer_question(user_query: str) -> Response:
    # 1. Classify query intent
    intent = classify(user_query)  # "factual", "contextual", "hybrid"

    # 2. Retrieve from appropriate sources
    if intent in ["contextual", "hybrid"]:
        vector_results = vector_db.search(user_query, top_k=5)

    if intent in ["factual", "hybrid"]:
        sql_query = llm.generate_sql(user_query, schema=gold_marts_schema)
        sql_results = gold_marts.execute(sql_query)

    # 3. Combine context for LLM
    prompt = f"""
    Question: {user_query}

    Context from past incidents:
    {vector_results}

    Current metrics:
    {sql_results}

    Provide answer with citations.
    """

    # 4. Generate grounded response
    response = llm.generate(prompt)
    return response
```

## Trade-offs and Alternatives Considered

### Alternative 1: Vector DB Only (with SQL data embedded as text)
**Rejected because**:
- ❌ Embeddings lose numeric precision (42.7% becomes "around 43%")
- ❌ Can't perform aggregations on embedded text
- ❌ Storage inefficiency (storing "42" as 384-dim vector)

### Alternative 2: Gold Marts Only (with full-text search)
**Rejected because**:
- ❌ Keyword search misses semantic similarity
- ❌ Can't search unstructured incident narratives effectively
- ❌ No multilingual support (German/Dutch/French ops docs)

### Alternative 3: Separate RAG endpoints (one for vectors, one for SQL)
**Rejected because**:
- ❌ User needs to know which endpoint to call
- ❌ Can't answer hybrid questions in single query
- ❌ No intelligent routing based on question type

### Chosen: Unified RAG API with Hybrid Retrieval ✅
**Advantages**:
- ✅ Single natural language interface
- ✅ Automatic routing to appropriate data source(s)
- ✅ Citations from both structured and unstructured sources
- ✅ Handles simple and complex questions equally well

## References

- [Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks (Lewis et al., 2020)](https://arxiv.org/abs/2005.11401)
- [LlamaIndex Documentation - Hybrid Search](https://docs.llamaindex.ai/)
- [Qdrant Hybrid Search Guide](https://qdrant.tech/documentation/concepts/hybrid-queries/)
- O'Reilly Book: "AI Engineering" - Chapter on RAG architectures
- O'Reilly Book: "Generative AI Design Patterns" - RAG pattern variations

---

**Document Version**: 1.0
**Last Updated**: 2025-10-19
**Author**: BRINUS Team (O'Reilly Q4 2025 Architectural Katas)
