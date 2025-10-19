# Object Store (Bronze Layer) - Immutable raw event archive for replay, audit, and ML training

## Context

MobilityCorp's platform generates millions of events daily from vehicles, bookings, payments, and returns. These raw events are the foundational truth for:

- Historical analysis and trend detection (seasonal demand patterns, usage hotspots)
- Machine learning model training (battery prediction, demand forecasting, fraud detection)
- Compliance and audit trails (GDPR access logs, financial transaction records)
- Event replay for disaster recovery or read model rebuilding
- A/B test analysis and retrospective debugging

## Problem

Kafka's log retention is limited (typically 7-30 days) for operational reasons, but business needs require indefinite event retention:

1. **ML Training Data**: Models need years of historical telemetry to learn seasonal patterns and long-term trends
2. **Regulatory Compliance**: EU regulations (GDPR, ePrivacy) mandate multi-year audit trails for personal data access
3. **Cost Efficiency**: Storing TB-scale data in Kafka is 10-50x more expensive than object storage
4. **Replay Capability**: Rebuilding read models after schema changes requires access to original events (months/years back)
5. **Cross-Team Access**: Data scientists and analysts need batch access to raw events without impacting operational Kafka clusters

## Solution Justification

Object storage as the Bronze layer (data lake foundation) provides durable, cost-effective archival:

- **Immutability**: Write-once semantics guarantee events are never modified, ensuring audit integrity
- **Cost Efficiency**: S3 Standard-IA and Glacier reduce storage costs by 90% vs. Kafka for cold data
- **Scalability**: Petabyte-scale storage with no operational overhead (fully managed)
- **Partition Awareness**: Events stored in Hive/Iceberg-style partitions (year/month/day/hour) for efficient querying
- **Format Standardization**: CloudEvents in Avro/Parquet ensure schema evolution and cross-tool compatibility

## Use Cases

### 1. ML Model Training - Battery Prediction
- **Scenario**: Train gradient boosting model to predict vehicle battery drain rates based on weather, terrain, and usage patterns
- **Data Needs**: 18 months of `veh.events.raw` telemetry (GPS, battery readings, temperature)
- **Access Pattern**: Batch read via Spark job; 500GB filtered down to 50GB training set
- **Benefit**: S3 Select pushes down filters, reducing egress costs by 10x vs. full scan

### 2. Compliance Audit - GDPR Data Access Request
- **Scenario**: Customer requests all data collected about them over 3 years
- **Data Needs**: All events containing user_id across `domain.events`, `veh.events.raw`, `payment.events`
- **Access Pattern**: Athena/Trino SQL query with user_id predicate; results exported to encrypted JSON
- **Benefit**: Immutable archive proves data provenance; partitioning limits scan to relevant time ranges

### 3. Disaster Recovery - Read Model Rebuild
- **Scenario**: Read Model DB corruption requires full rebuild from event history
- **Data Needs**: 90 days of `domain.events` (booking, payment, return events)
- **Access Pattern**: Kafka Connect S3 Source replays events back to Kafka; consumers rebuild projections
- **Benefit**: Kafka logs only retained 7 days; Bronze layer enables recovery beyond retention window

### 4. Retrospective Analytics - A/B Test Analysis
- **Scenario**: Product team wants to analyze impact of pricing change 6 months ago on repeat usage
- **Data Needs**: Historical `booking_created` events with promo codes and customer segments
- **Access Pattern**: dbt model joins Bronze events with Gold user cohorts; results in Superset dashboard
- **Benefit**: Enables retrospective analysis without pre-defining metrics; raw events answer unanticipated questions

### 5. Feature Engineering - Demand Forecasting
- **Scenario**: Batch Orchestrator extracts time-series features (bookings per hour per location) for demand prediction model
- **Data Needs**: 2 years of `booking_created`, `return_completed` events joined with weather data
- **Access Pattern**: Airflow DAG runs Spark job; writes aggregated features to Gold Marts
- **Benefit**: Separates expensive batch processing from real-time operational Kafka; S3 bandwidth scales independently

## Technology Choice

**Selected**: AWS S3 (or MinIO for self-hosted)

**Rationale**:
- Industry-standard object storage with 99.999999999% durability (11 nines)
- Storage classes (Standard, IA, Glacier) optimize cost per access pattern
- S3 Select / Athena / Trino enable SQL-on-object-store without data movement
- AWS Glue Data Catalog for schema discovery (Hive-compatible metastore)
- Lifecycle policies automate transition to Glacier for compliance archives
- MinIO alternative provides S3-compatible API for on-prem/hybrid deployments
- EU region hosting (eu-central-1, eu-west-1) for data residency compliance
