# Stream Processor - Real-time event processing with stateful transformations and enrichment

## Context

MobilityCorp needs real-time data transformations that batch processing (hourly/daily) cannot support:

- Inventory updates: Compute vehicle availability within seconds of booking/return (not hours)
- Anomaly detection: Identify battery drain patterns, geofence violations, demand spikes in real-time
- Event enrichment: Join telemetry with location metadata, weather data, booking history
- Aggregations: Calculate per-location metrics (utilization %, avg battery) with 1-minute windows
- Deduplication: Filter duplicate IoT messages from unreliable cellular networks

## Problem

Kafka provides durable event logs but no computation; microservices handle request/response, not complex event processing:

1. **Stateful Joins**: Correlating vehicle telemetry with booking events requires maintaining state across millions of events
2. **Windowed Aggregations**: "Bookings per hour per location" needs time-based grouping that microservices can't efficiently handle
3. **Out-of-Order Events**: IoT devices send delayed messages; batch jobs fail to handle late arrivals correctly
4. **Scalability**: Processing 120k events/hour requires parallelism that single-instance services can't provide
5. **Exactly-Once Semantics**: Duplicate processing (battery alert sent 3 times) creates operational chaos

## Solution Justification

Stream processing framework provides distributed, stateful computation over event streams:

- **Stateful Operations**: Maintain keyed state (vehicle_id → last_known_location) with RocksDB embedded storage
- **Event-Time Processing**: Handle out-of-order events with watermarks (accept events up to 5 min late)
- **Windowing**: Tumbling/sliding/session windows for time-based aggregations
- **Exactly-Once Guarantees**: Kafka transactions + checkpointing ensure no duplicate output
- **Horizontal Scaling**: Partition by vehicle_id; scale to 100+ parallel tasks

## Use Cases

### 1. Vehicle Inventory Updates
- **Input Streams**:
  - `veh.events.raw`: Vehicle telemetry (GPS, battery, operational_status)
  - `domain.events`: Booking events (booking_created, return_completed)
- **Processing**:
  ```sql
  -- Flink SQL or ksqlDB
  SELECT vehicle_id, location, battery_pct,
         CASE WHEN booking_id IS NULL THEN 'available' ELSE 'rented' END AS status
  FROM telemetry_stream
  LEFT JOIN bookings_stream ON telemetry.vehicle_id = bookings.vehicle_id
  WHERE telemetry.timestamp > bookings.start_time
    AND telemetry.timestamp < bookings.end_time
  ```
- **Output**: Publish to `veh.inventory.updates` topic (consumed by Read Model DB updaters)
- **Benefit**: Customer app shows real-time availability (within 2 seconds of return)

### 2. Low-Battery Alert Detection
- **Input**: `veh.events.raw` telemetry stream
- **Processing**:
  - Filter events where `battery_pct < 20`
  - Deduplicate within 5-minute window (avoid alert spam)
  - Enrich with vehicle metadata (type, location, last_service_date)
- **Output**: Publish to `alerts` topic
- **Benefit**: Fleet ops receives alert within 10 seconds; can dispatch battery swap before customer impact

### 3. Demand Spike Detection
- **Input**: `domain.events` (booking_created events)
- **Processing**:
  ```sql
  -- Tumbling 15-minute windows
  SELECT location_id, COUNT(*) AS booking_count,
         AVG(booking_count) OVER (PARTITION BY location_id ROWS BETWEEN 4 PRECEDING AND CURRENT ROW) AS avg_last_hour
  FROM bookings_stream
  GROUP BY TUMBLE(event_time, INTERVAL '15' MINUTE), location_id
  HAVING booking_count > 1.5 * avg_last_hour  -- 50% spike
  ```
- **Output**: Publish spike alert to `alerts` topic
- **Benefit**: Dynamic pricing model adjusts rates during demand spikes (surge pricing)

### 4. Read Model Projection - Available Vehicles
- **Input**: `veh.inventory.updates` (derived from Use Case #1)
- **Processing**:
  - Maintain stateful table of vehicle availability (keyed by vehicle_id)
  - Update PostgreSQL Read Model DB via Flink JDBC sink
- **Output**: Upsert to `available_vehicles` table in Read Model DB
- **Benefit**: Booking Service queries <50ms; always shows current availability

### 5. Time-Series Aggregation - Utilization Metrics
- **Input**: `veh.events.raw` + `domain.events`
- **Processing**:
  ```sql
  -- Hourly rollup per location/vehicle_type
  SELECT location_id, vehicle_type,
         SUM(CASE WHEN status='rented' THEN 1 ELSE 0 END) AS rented_count,
         COUNT(*) AS total_count,
         CAST(rented_count AS DOUBLE) / total_count AS utilization_pct
  FROM enriched_telemetry
  GROUP BY HOP(event_time, INTERVAL '1' HOUR, INTERVAL '1' HOUR), location_id, vehicle_type
  ```
- **Output**: Insert to ClickHouse Silver layer (`vehicle_utilization_hourly` table)
- **Benefit**: Operations dashboard shows real-time utilization trends without batch job delays

## Technology Choice

**Selected**: Apache Flink (with ksqlDB as alternative for simpler use cases)

**Rationale**:
- **Apache Flink**: Industry-standard for stateful stream processing
  - True event-time processing with watermarks (handles late data correctly)
  - Exactly-once semantics (Kafka transactions + distributed snapshots)
  - Rich windowing (tumbling, sliding, session, custom)
  - DataStream API (Java/Python) + SQL API (Flink SQL)
  - Connectors: Kafka, JDBC (Postgres), ClickHouse, Elasticsearch
  - State backends: RocksDB (embedded), S3 (checkpoints)
  - Scales to 1000s of parallel tasks
- **ksqlDB**: Simpler alternative for SQL-only use cases
  - Built on Kafka Streams; tight Kafka integration
  - Pull/push queries for real-time dashboards
  - Easier ops (no separate cluster; runs as Kafka app)
  - Limited vs. Flink (no custom code, fewer connectors)
- **Kafka Streams**: Library (not framework) for Java/Scala apps
  - Embedded in microservices (no separate deployment)
  - Good for single-team use cases
- **EU Compliance**: Flink self-hosted; all state stored in RocksDB (local) + S3 checkpoints (EU regions)
