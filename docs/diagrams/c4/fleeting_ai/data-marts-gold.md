# Data Marts (Gold) - Curated dimensional models for AI, business intelligence, and strategic planning

## Context

MobilityCorp stakeholders need trusted, business-friendly datasets for decision-making and AI training. Requirements include:

- Executive dashboards: KPIs (revenue growth, customer acquisition cost, LTV) with drill-down by region/vehicle type
- AI/ML features: Engineered datasets for demand forecasting, dynamic pricing, battery optimization models
- Strategic planning: Historical trends, seasonal patterns, location performance, fleet ROI analysis
- RAG knowledge base: Semantic embeddings derived from operational summaries, incident reports, SLA metrics
- Cross-functional access: Non-technical users (product, operations, finance) need self-service analytics

## Problem

Silver layer contains cleaned facts but lacks business context and semantic meaning required for human + AI consumption:

1. **Business Logic Scattered**: Metric definitions (e.g., "active user" = 3+ bookings/month) duplicated across notebooks, BI tools, and code
2. **Join Complexity**: Analysts repeatedly join same 5-10 tables; each rebuild introduces inconsistencies
3. **Schema Instability**: Silver schema changes break downstream reports and ML pipelines
4. **Performance Bottlenecks**: Complex ad-hoc queries on Silver layer overwhelm OLAP database during peak usage
5. **AI Training Inefficiency**: Data scientists spend 70% of time on feature engineering vs. model development

## Solution Justification

Gold layer provides curated, stable, semantically-rich datasets built with software engineering rigor:

- **Dimensional Modeling**: Star schemas (fact tables + dimensions) optimized for BI tool performance (Tableau, Superset)
- **Metric Centralization**: Single source of truth for KPIs via dbt metrics layer; business logic as code with version control
- **Incremental Builds**: dbt incrementally processes only new Silver data, reducing pipeline runtime from hours to minutes
- **Data Quality Gates**: dbt tests enforce referential integrity, uniqueness, and range checks before publishing to Gold
- **Feature Store Pattern**: ML-ready datasets with point-in-time-correct joins prevent data leakage in training

## Use Cases

### 1. Executive KPI Dashboard
- **Dataset**: `kpi_summary_monthly` dimensional model
- **Dimensions**: date_month, location_dim, vehicle_type_dim
- **Measures**: total_revenue, booking_count, active_users, customer_ltv, fleet_utilization_pct
- **Pipeline**: dbt model joins Silver `revenue_facts` + `user_cohorts` + `vehicle_utilization`; refreshed daily
- **Benefit**: Superset dashboard loads in <2s; execs drill down by city without writing SQL

### 2. Demand Forecasting ML Features
- **Dataset**: `demand_features_hourly` feature table
- **Columns**: location_id, hour, day_of_week, is_holiday, weather_temp, historical_bookings_7d_avg, vehicle_availability
- **Pipeline**: dbt model aggregates Silver telemetry + bookings + external weather API; includes time-travel joins to prevent leakage
- **Benefit**: ML engineers import ready-to-use features; model training time cut from 3 days to 4 hours

### 3. Fleet Performance Scorecard
- **Dataset**: `fleet_health_per_vehicle_daily` fact table
- **Columns**: vehicle_id, date, total_rental_hours, revenue_generated, maintenance_incidents, avg_battery_health, co2_saved_kg
- **Pipeline**: dbt model unions data from Silver `vehicle_utilization`, `maintenance_logs`, `charging_sessions`
- **Benefit**: Operations team identifies underperforming vehicles for rebalancing; CSV export for board reporting

### 4. RAG Embedding Source - Operational Summaries
- **Dataset**: `daily_ops_summary_text` table
- **Columns**: date, location_id, summary_text (natural language), metadata_json
- **Example Row**: "2024-10-15, Berlin, 'High demand for eBikes during morning commute (8-9am). 3 vehicles required battery swap. Weather: rainy, 12°C.'"
- **Pipeline**: dbt model aggregates Silver metrics + anomalies into human-readable summaries; Embedding Pipeline generates vectors
- **Benefit**: RAG API retrieves contextually relevant past incidents when answering "Why did Berlin have low availability last week?"

### 5. SLA Compliance Report
- **Dataset**: `sla_metrics_by_service_daily` dimensional model
- **Dimensions**: date, service_name, environment (prod/staging)
- **Measures**: p95_response_time_ms, error_rate_pct, uptime_pct, sla_target_met (boolean)
- **Pipeline**: dbt model aggregates Silver observability metrics; applies business rules (e.g., p95 < 200ms = pass)
- **Benefit**: Auto-generated PDF report for compliance audits; Slack alerts when SLA breached

## Technology Choice

**Selected**: dbt (data build tool) on ClickHouse or PostgreSQL

**Rationale**:
- **dbt**: Industry-standard for analytics engineering; SQL-based transformations with software engineering best practices
  - Version control (Git) for all data transformations
  - Automated testing (unique, not_null, relationships, custom tests)
  - Lineage graphs (DAG visualization) show upstream/downstream dependencies
  - Incremental models reduce pipeline costs (process only new data)
  - Metrics layer (dbt Semantic Layer) centralizes KPI definitions
  - Documentation generation (data catalog with column descriptions)
- **ClickHouse Backend**: Ideal for large-scale Gold tables (billions of rows)
  - Materialized views for sub-second dashboard queries
  - Aggregating MergeTree for automatic rollups
- **PostgreSQL Backend**: Simpler for smaller deployments (<100M rows)
  - Native support for arrays, JSONB for semi-structured features
  - Foreign keys enforce referential integrity
- **EU Compliance**: dbt is open-source (orchestration layer only); all data stays in self-hosted ClickHouse/Postgres
