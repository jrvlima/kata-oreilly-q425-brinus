# Vector DB - Semantic search and embeddings storage for AI-powered knowledge retrieval

## Context

MobilityCorp operations generate vast unstructured knowledge (incident reports, maintenance logs, ops summaries, troubleshooting playbooks). Teams need AI to:

- Answer natural language questions ("Why did Berlin have low scooter availability last Tuesday?")
- Retrieve relevant past incidents for troubleshooting ("Similar battery drain patterns in Amsterdam 3 months ago")
- Recommend solutions based on historical resolutions ("Last time charging station failed, team swapped controller")
- Onboard new staff with searchable operational wisdom (tribal knowledge → documented patterns)
- Enable semantic search across multi-language documentation (English, German, French, Dutch)

## Problem

Traditional keyword search fails to capture semantic meaning and contextual relationships:

1. **Vocabulary Mismatch**: User asks "vehicles not charging" but docs say "EV battery issue" → no match
2. **Contextual Blindness**: Search for "low availability" returns 1000 results; can't filter by root cause (demand spike vs. maintenance vs. battery)
3. **Cross-Lingual Gaps**: Berlin ops write incident reports in German; Amsterdam in Dutch; search fails across locations
4. **Temporal Relevance**: Old incidents from 2022 rank equally with recent patterns from 2024
5. **No Reasoning**: Can't answer "What's the usual fix when...?" requires human to read 50 docs and synthesize

## Solution Justification

Vector database with semantic embeddings enables AI-powered knowledge retrieval:

- **Semantic Search**: Embeddings capture meaning; "vehicles won't charge" matches "battery charging failure" (95% similarity)
- **Hybrid Search**: Combine vector similarity + keyword filters (e.g., date range, location) for precision
- **Multilingual Support**: Sentence transformers map German/Dutch/French to same embedding space
- **Metadata Filtering**: Vector search with predicate pushdown (e.g., "incidents in Berlin + battery tag + last 30 days")
- **RAG Foundation**: Retrieves top-k relevant documents to ground LLM responses with citations

## Use Cases

### 1. Operations Assistant - Incident Troubleshooting
- **Query**: "Why are scooters in Amsterdam not available this morning?"
- **Retrieval**:
  1. Embed query to 384-dim vector
  2. Search vector DB for top 5 similar incident summaries (filter: location=Amsterdam, last 7 days)
  3. Results: "2024-10-15: Morning demand spike, 20% vehicles low battery, rebalancing delayed"
- **RAG Response**: LLM combines retrieval + real-time data to answer "Likely demand spike + battery management issue. Check rebalancing schedule."
- **Benefit**: Support agent resolves issue in 2 min vs. 20 min manual log search

### 2. Maintenance Playbook Lookup
- **Query**: "How to fix charging station offline error?"
- **Retrieval**:
  1. Embed query; search vector DB for maintenance playbooks (filter: tag=charging_station)
  2. Top result: "Charging Station Offline → Step 1: Check breaker. Step 2: Restart controller. Step 3: Call vendor."
- **RAG Response**: LLM returns step-by-step instructions with citations to original playbook
- **Benefit**: Technician resolves issue without calling senior engineer; MTTR reduced 40%

### 3. Historical Pattern Analysis
- **Query**: "Show all battery drain incidents similar to vehicle VEH-12345 last month"
- **Retrieval**:
  1. Fetch incident embedding for VEH-12345 from vector DB
  2. Similarity search for k=10 nearest neighbors (cosine similarity > 0.85)
  3. Results: 8 incidents with similar GPS routes + weather conditions + battery age
- **Benefit**: Ops identifies faulty battery batch proactively before more failures

### 4. Multilingual Knowledge Base
- **Query** (in English): "How to handle customer complaints about vehicle cleanliness?"
- **Retrieval**:
  1. Embed English query
  2. Vector search returns matches from German, Dutch, French docs (multilingual embeddings)
  3. Top result (in German): "Sauberkeitsbeschwerde → Fahrzeug aus Flotte nehmen, Reinigungsteam benachrichtigen"
- **RAG Response**: LLM translates to English: "Remove vehicle from fleet, notify cleaning team"
- **Benefit**: Cross-location knowledge sharing without manual translation

### 5. Ops Summary Semantic Search
- **Query**: "What were the main issues in Q3 2024?"
- **Retrieval**:
  1. Embed query; search `daily_ops_summary_text` table (filter: date between Q3 dates)
  2. Cluster top 50 results by embedding similarity (k-means)
  3. Extract centroid summaries: "Battery management", "Demand forecasting errors", "Weather impact"
- **Benefit**: Exec gets AI-generated quarterly summary in 10 seconds vs. 2 days analyst work

## Technology Choice

**Selected**: Qdrant (with alternatives: Weaviate, Milvus)

**Rationale**:
- **Qdrant**: Rust-based vector database optimized for performance and filtering
  - Native support for hybrid search (vector + keyword + metadata filters)
  - Payload filtering with rich query language (geo, datetime, tags)
  - Quantization (scalar, product) reduces memory footprint by 16x
  - Distributed mode with sharding and replication
  - gRPC + REST APIs; Python/TypeScript clients
- **Weaviate**: Strong for multi-tenancy and GraphQL queries
- **Milvus**: Best for billion-scale vectors (overkill for MobilityCorp's 10M embeddings)
- **EU Compliance**: Qdrant self-hosted; no data leaves infrastructure
