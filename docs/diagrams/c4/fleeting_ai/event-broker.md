# Event Broker - High-throughput, low-latency, durable pub/sub for event streaming and integration

## Context

MobilityCorps platform operates in a distributed, event-driven environment where multiple services, IoT devices, and analytical systems need to exchange data in real-time. The system must handle:

- Business domain events from microservices (bookings, payments, returns)
- Cross-location operations spanning multiple EU cities with multi-language/multi-currency support
- IoT device events from vehicle bay sensors
    - High-frequency telemetry from thousands of vehicles (GPS, battery status, operational metrics)
    - Real-time alerts for battery management, geofencing, and fault detection

## Problem

The platform faces several integration and data flow challenges:

1. **Temporal Decoupling**: Services need to communicate asynchronously without tight coupling or synchronous dependencies
2. **Event Replay & Audit**: Business and operational events must be retained for compliance, debugging, and ML training
3. **Stream Processing**: Real-time transformations, enrichment, and aggregations are required for inventory updates and alerting
4. **Scalability**: The system must handle spiky loads (e.g., morning commute bookings, evening returns) without data loss
5. **Multi-Consumer Patterns**: Same events need to be consumed by operational services, analytics pipelines, and AI/ML systems

## Solution Justification

An event broker with durable, partitioned logs (Kafka-style architecture) addresses these needs by:

- **Durability**: Events are persisted to disk and replicated across nodes, ensuring zero data loss
- **Scalability**: Horizontal scaling via topic partitions allows independent throughput scaling per event type
- **Replay Capability**: Consumer groups can reprocess historical events for recovery, backfills, or new projections
- **Event Sourcing Foundation**: Immutable event log serves as the source of truth for CQRS read models
- **Vendor Neutrality**: Standard protocols (CloudEvents) and schema evolution (via Schema Registry) prevent vendor lock-in

## Use Cases

### 1. Vehicle Telemetry Ingestion
- **Producers**: Edge Ingest Gateway (from Vehicle Bay Devices and vehicle GPS units)
- **Topics**: `veh.events.raw`
- **Consumers**: Stream Processor (Flink), Object Store (Bronze layer for ML)
- **Volume**: ~10k events/sec during peak hours

### 2. Domain Event Distribution
- **Producers**: Booking Service, Payment Service, Return Service
- **Topics**: `domain.events`
- **Consumers**: All microservices (event choreography), Read Model DB updaters
- **Examples**: `booking_created`, `payment_authorized`, `return_completed`

### 3. Inventory & Availability Updates
- **Producers**: Stream Processor (derived from raw telemetry + bookings)
- **Topics**: `veh.inventory.updates`
- **Consumers**: Booking Service (for availability checks), Customer App (real-time updates)

### 4. Real-time Alerting
- **Producers**: Stream Processor (anomaly detection), Edge Ingest Gateway
- **Topics**: `alerts`
- **Consumers**: Fleet Management Service, Staff App notifications, On-call alerting systems
- **Examples**: `low_battery_alert`, `geofence_violation`, `maintenance_required`

### 5. Analytics & ML Pipelines
- **Producers**: All services
- **Topics**: All topics (consumed holistically)
- **Consumers**: Batch Orchestrator (Airflow), Embedding Pipeline, Feature Store ETL
- **Purpose**: Build Gold-layer data marts, train ML models, generate semantic embeddings for RAG

## Technology Choice

**Selected**: Apache Kafka

**Rationale**:
- Battle-tested at scale (used by Uber, Netflix, LinkedIn for similar use cases)
- Native support for stream processing via Kafka Streams/ksqlDB/Flink
- Strong ecosystem (Connect, Schema Registry, monitoring tools)
- CloudEvents compatibility via Avro/Protobuf serialization
- Multi-region replication for EU data residency compliance
