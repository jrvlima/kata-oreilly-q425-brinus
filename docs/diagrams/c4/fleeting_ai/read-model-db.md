# Read Model DB - Optimized query database for CQRS read-side projections

## Context

MobilityCorp's microservices follow event-sourcing and CQRS patterns to separate write-side commands from read-side queries. The platform must serve:

- Sub-100ms API responses for customer app queries (available vehicles, booking history)
- Complex joins across multiple aggregate boundaries (vehicle + booking + user + location)
- Multi-language/multi-currency localized data for EU operations
- Real-time availability updates as vehicles are booked/returned
- Staff dashboard queries aggregating fleet status across locations

## Problem

Event-sourced write models (stored as event logs in Kafka) are optimized for consistency and auditability but are unsuitable for query performance:

1. **Reconstruction Overhead**: Deriving current state from event logs requires replaying hundreds of events per aggregate
2. **Query Complexity**: Cross-aggregate queries (e.g., "available cars near me with >50% battery") are expensive to compute from events
3. **Latency Requirements**: Mobile apps demand <100ms response times, impossible with event replay
4. **Denormalization Needs**: UI views require pre-joined data (vehicle + location + pricing) not available in single event streams
5. **Scalability**: Read traffic (browse, search) far exceeds write traffic (bookings); separate scaling needed

## Solution Justification

Dedicated read model database with materialized projections solves query performance at scale:

- **Pre-Computed Views**: Event consumers build denormalized tables optimized for specific query patterns
- **Eventual Consistency**: Accepts brief lag (typically <500ms) between write and read availability for massive scalability gains
- **Index Optimization**: Postgres indexes tailored for API access patterns (geospatial, text search, time-range)
- **Connection Pooling**: Pgbouncer enables thousands of concurrent API connections without DB overload
- **Independent Scaling**: Read replicas scale horizontally for query load without impacting write-side services

## Use Cases

### 1. Vehicle Availability Lookup
- **Query**: "Find available eBikes within 500m of GPS coordinates with >30% battery"
- **Projection**: `available_vehicles` table (vehicle_id, type, location_gis, battery_pct, pricing)
- **Updater**: Stream Processor listens to `veh.inventory.updates` and `booking_created`/`return_completed` events
- **Performance**: <50ms with PostGIS spatial index

### 2. User Booking History
- **Query**: "Show customer's last 20 rentals with vehicle type, duration, and cost"
- **Projection**: `user_booking_history` table (booking_id, user_id, vehicle_type, start_time, end_time, total_cost)
- **Updater**: Payment Service publishes `booking_completed` events; consumer updates read model
- **Performance**: <20ms with user_id index

### 3. Fleet Status Dashboard
- **Query**: "Show all vehicles by location with status (rented/available/charging/maintenance)"
- **Projection**: `fleet_status_by_location` table (location_id, vehicle_counts_by_status, avg_battery)
- **Updater**: Aggregates `veh.events.raw` telemetry + `domain.events` bookings every 30 seconds
- **Performance**: <100ms for 50+ locations

### 4. Localized Pricing Display
- **Query**: "Show per-minute rates for cars in Berlin (EUR) vs Amsterdam (EUR)"
- **Projection**: `pricing_by_location` table (location_id, vehicle_type, currency, rate_per_minute)
- **Updater**: Pricing Service publishes `price_updated` events; read model stays in sync
- **Performance**: <10ms (cached at API Gateway)

### 5. RAG Context Retrieval
- **Query**: AI assistant needs current vehicle state to answer "Is vehicle XYZ available now?"
- **Projection**: Same `available_vehicles` table queried by RAG API
- **Updater**: Shared with Use Case #1
- **Performance**: <30ms; RAG combines with vector DB results for grounded answers

## Technology Choice

**Selected**: PostgreSQL 16+

**Rationale**:
- Proven scalability to 100k+ QPS with read replicas
- PostGIS extension for geospatial queries (critical for "vehicles near me")
- Full-text search with trigram indexes (vehicle search, location lookup)
- JSONB columns for flexible schema evolution (user preferences, vehicle metadata)
- Streaming replication for HA and read scaling
- Strong ecosystem (Pgbouncer, TimescaleDB compatibility for time-series)
- EU hosting options (AWS RDS, managed Postgres on EU regions)
