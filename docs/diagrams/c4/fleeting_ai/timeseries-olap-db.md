# Time-series/OLAP DB (Silver Layer) - Cleaned analytical facts for dashboards and operational intelligence

## Context

MobilityCorp operations teams need real-time visibility into fleet performance, and business analysts require fast ad-hoc queries across millions of events. Key reporting needs:

- Operations dashboards: Vehicle uptime, battery health trends, utilization rates (per hour, per location)
- Business analytics: Revenue per vehicle type, booking conversion rates, customer retention cohorts
- Anomaly detection: Sudden drops in vehicle availability, charging station failures, demand spikes
- SLA monitoring: Response times, system health metrics, event processing lag
- Multi-dimensional analysis: Slice metrics by location, vehicle type, time period, weather conditions

## Problem

Raw Bronze events are too verbose for fast analytics, and Read Model DB is optimized for transactional queries, not analytical workloads:

1. **Query Performance**: Aggregating millions of raw events (e.g., "hourly bookings by city last 3 months") takes minutes on S3, needs sub-second responses
2. **Data Cleansing**: Bronze contains duplicates, out-of-order events, and malformed records that must be filtered before analysis
3. **Time-Series Optimization**: Most queries filter/group by time; row-oriented databases scan unnecessary columns
4. **Compression Efficiency**: Event data is highly compressible (repetitive fields like vehicle_type, location_id) but S3 stores uncompressed
5. **Dashboard Latency**: Grafana/Superset dashboards require <3s query response; batch processing from Bronze is too slow

## Solution Justification

A columnar time-series database as the Silver layer bridges raw events and curated analytics:

- **Columnar Storage**: ClickHouse columnar format compresses time-series data 10-50x; queries scan only needed columns
- **Time-Partitioning**: Native date/time partitioning enables efficient time-range pruning (90% of queries filter on timestamp)
- **Streaming Ingestion**: Stream Processor (Flink) continuously cleans and loads data from Kafka with exactly-once semantics
- **SQL Interface**: Analysts use standard SQL; no need to learn Spark or write MapReduce jobs
- **Pre-Aggregation**: Materialized views maintain hourly/daily rollups, accelerating dashboard queries 100x

## Use Cases

### 1. Fleet Utilization Dashboard
- **Query**: "Show vehicle utilization % (rented hours / total hours) per location for last 30 days"
- **Data**: `vehicle_utilization_hourly` table (pre-aggregated by Stream Processor)
- **Columns**: location_id, vehicle_type, hour, rented_count, available_count
- **Performance**: <500ms for 30-day query across 50 locations; ClickHouse time partitioning scans only relevant hours

### 2. Battery Health Trend Analysis
- **Query**: "Plot average battery degradation per vehicle over 6 months (daily granularity)"
- **Data**: `battery_metrics_daily` materialized view (aggregated from `veh.events.raw`)
- **Columns**: vehicle_id, date, avg_charge_cycles, avg_capacity_pct, max_temp
- **Performance**: <1s for 180 days × 5000 vehicles; columnar compression reduces scan from TB to GB

### 3. Anomaly Detection - Booking Drops
- **Query**: "Alert if bookings drop >20% compared to same hour last week (by location)"
- **Data**: `bookings_per_hour` table (real-time from `booking_created` events)
- **Processing**: Flink CEP (Complex Event Processing) queries Silver layer for historical baseline
- **Performance**: <100ms lookback query; enables real-time alerting vs. batch-only approaches

### 4. Revenue Attribution Report
- **Query**: "Break down revenue by vehicle type, pricing tier, and customer segment (monthly, last 12 months)"
- **Data**: `revenue_facts` table (joined from `payment.events` + `booking.events` + `user.dimension`)
- **Columns**: month, location_id, vehicle_type, pricing_tier, customer_segment, total_revenue, booking_count
- **Performance**: <2s for 12 months × 50 locations × 4 vehicle types; pre-computed aggregates eliminate joins at query time

### 5. Operational SLA Monitoring
- **Query**: "Show p95 API response time and event processing lag per microservice (last 24 hours)"
- **Data**: `observability_metrics` table (ingested from OpenTelemetry collector)
- **Columns**: service_name, timestamp, p50_latency, p95_latency, p99_latency, event_lag_seconds
- **Performance**: <300ms; ClickHouse quantile functions optimize percentile calculations on time-series data

## Technology Choice

**Selected**: ClickHouse (with TimescaleDB as alternative for PostgreSQL-centric teams)

**Rationale**:
- **ClickHouse**: Best-in-class columnar OLAP performance; queries 100-1000x faster than Postgres on analytical workloads
  - Handles billions of rows with sub-second queries
  - Native Kafka Engine table for direct streaming ingestion
  - Materialized views with automatic rollup (hourly → daily → monthly)
  - Distributed mode for horizontal scaling (sharding + replication)
  - SQL-compatible with strong aggregation functions (quantiles, HyperLogLog, time-series gaps)
- **TimescaleDB**: PostgreSQL extension for teams wanting unified stack
  - Automatic time-based partitioning (hypertables)
  - Continuous aggregates (similar to ClickHouse materialized views)
  - PostGIS integration for geospatial + time-series queries
  - Easier operational model if already running Postgres for Read Model DB
- **EU Compliance**: Both support self-hosted deployment in EU regions or managed options (ClickHouse Cloud, Timescale Cloud on AWS EU)
