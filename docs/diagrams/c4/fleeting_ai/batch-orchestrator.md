# Batch Orchestrator - Workflow automation for data pipelines, ML training, and scheduled analytics

## Context

MobilityCorp's data platform requires coordinated execution of complex, interdependent workflows:

- Data transformations: Bronze → Silver → Gold ETL pipelines (dbt models with 100+ dependencies)
- ML lifecycle: Feature extraction, model training, evaluation, deployment (daily retraining for demand forecasts)
- Backfill operations: Reprocess historical data after schema changes or bug fixes
- External integrations: Fetch weather data, exchange rates, traffic conditions for enrichment
- Scheduled reporting: Generate daily ops summaries, weekly executive reports, monthly SLA audits

## Problem

Manual execution and scattered cron jobs create operational fragility and limit data platform reliability:

1. **Dependency Hell**: Silver tables depend on Bronze ingestion; Gold depends on Silver; embeddings depend on Gold. One failure cascades
2. **Retry Logic Gap**: Failed jobs (network timeouts, OOM errors) require manual intervention; no automatic retries or alerting
3. **Resource Contention**: Multiple pipelines compete for compute during peak hours; no prioritization or resource quotas
4. **Observability Blind Spots**: Cron logs scattered across servers; no centralized view of pipeline health or SLA tracking
5. **Backfill Complexity**: Reprocessing 6 months of data requires manually running 180 daily jobs in correct order

## Solution Justification

Workflow orchestration platform with DAG-based scheduling provides reliability and operational excellence:

- **Declarative DAGs**: Python code defines task dependencies (e.g., `gold_kpis >> embedding_pipeline`); engine handles execution order
- **Automatic Retries**: Configurable retry policies with exponential backoff; SLA alerts when jobs miss deadlines
- **Resource Management**: Task pools limit concurrency; priority queues ensure critical jobs run first
- **Idempotency**: Tasks designed to be re-runnable; supports backfills with date-range parameters
- **Observability**: Web UI shows real-time task status, logs, duration trends, and failure root causes

## Use Cases

### 1. Bronze → Silver → Gold ETL Pipeline
- **Workflow**:
  1. Check Bronze S3 buckets for new data (sensor task)
  2. Trigger dbt run for Silver models (CleanVehicleTelemetry, AggregateBookings)
  3. Run dbt tests; fail pipeline if data quality checks fail
  4. Trigger dbt run for Gold models (KPI_Summary, Fleet_Health)
  5. Publish metrics to observability stack
- **Schedule**: Hourly (on the hour)
- **Benefit**: Single DAG ensures all layers stay in sync; automatic retry if Silver job OOMs

### 2. ML Model Retraining - Demand Forecasting
- **Workflow**:
  1. Extract features from Gold `demand_features_hourly` (last 90 days)
  2. Train XGBoost model on Spark cluster
  3. Evaluate model on holdout set; compare MAE vs. current production model
  4. If new model improves MAE by >5%, deploy to MLflow Model Registry
  5. Send Slack notification with model performance report
- **Schedule**: Daily at 2am (off-peak)
- **Benefit**: Automated model drift detection; rollback to previous model if performance degrades

### 3. Historical Data Backfill
- **Workflow**:
  - User triggers backfill for `2024-01-01` to `2024-06-30`
  - Airflow creates 181 daily task instances
  - Each task runs dbt model with `--vars '{"run_date": "2024-01-01"}'`
  - Tasks parallelized (10 concurrent) with automatic retry on failure
- **Trigger**: Manual (ad-hoc)
- **Benefit**: Rebuilt 6 months of Gold tables in 8 hours vs. 3 days of manual scripting

### 4. External Data Ingestion - Weather Enrichment
- **Workflow**:
  1. HTTP Sensor polls weather API for new data (every 15 minutes)
  2. PythonOperator fetches forecasts for all MobilityCorp locations (50 cities)
  3. Transform to standard schema (location_id, timestamp, temp, precipitation)
  4. Load to Silver `weather_conditions` table
  5. Trigger downstream Gold models that depend on weather (demand forecasting, fleet rebalancing)
- **Schedule**: Every 15 minutes
- **Benefit**: Real-time weather context enables dynamic pricing during bad weather (surge for sheltered vehicles)

### 5. Scheduled Reporting - Weekly Executive Summary
- **Workflow**:
  1. Query Gold `kpi_summary_monthly` for last 7 days
  2. Generate visualizations (Matplotlib/Plotly charts)
  3. Render Jinja2 template with charts + narrative text
  4. Export to PDF via WeasyPrint
  5. Email to exec distribution list + upload to S3
- **Schedule**: Every Monday at 6am
- **Benefit**: Executives receive consistent reports without analyst manual work; frees team for ad-hoc analysis

## Technology Choice

**Selected**: Apache Airflow (with Prefect as modern alternative)

**Rationale**:
- **Apache Airflow**: Industry-standard workflow orchestrator; proven at scale (Airbnb, Netflix, Spotify)
  - Python-based DAG authoring; integrates with entire Python data stack (dbt, Spark, Pandas)
  - Rich operator ecosystem (S3, Postgres, Kafka, Kubernetes, dbt, MLflow)
  - Scheduler supports cron and interval-based triggers
  - Distributed executor (Celery/Kubernetes) scales to 1000s of concurrent tasks
  - Web UI with RBAC for multi-team access control
- **Prefect**: Modern alternative with better developer experience
  - Dynamic DAGs (generate tasks at runtime based on data)
  - Built-in retries with Fibonacci backoff
  - Hybrid cloud model (orchestrator in cloud, workers on-prem for EU compliance)
  - Negative engineering (no Airflow DAG parsing bottleneck)
- **EU Compliance**: Both support self-hosted deployment; Prefect Cloud has EU regions
