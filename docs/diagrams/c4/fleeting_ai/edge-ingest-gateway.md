# Edge Ingest Gateway - Secure, scalable IoT telemetry ingestion with protocol translation

## Context

MobilityCorp operates thousands of IoT devices transmitting real-time telemetry:

- Vehicle GPS trackers (location, speed, heading) every 30 seconds
- Battery management systems (charge level, voltage, temperature) every 5 minutes
- Vehicle Bay Devices (occupancy sensors, charging status) every 1 minute
- Scooter/eBike telematics (lock status, tilt sensor, diagnostics) on-demand
- Constrained devices (low power, intermittent connectivity, 2G/3G/4G networks)

## Problem

IoT devices have unique constraints incompatible with standard web APIs:

1. **Protocol Diversity**: Vehicles use MQTT (pub/sub), Bay Devices use CoAP (REST-like), GPS trackers use proprietary binary protocols
2. **Bandwidth Limits**: Cellular data costs require minimal payload sizes; JSON wastes bytes vs. binary formats
3. **Unreliable Networks**: Devices roam between cells; need automatic reconnect, message buffering, QoS guarantees
4. **Security**: Devices can't handle full TLS handshakes; need lightweight auth (PSK, certificate-based)
5. **Data Quality**: Malformed/malicious data from compromised devices can corrupt analytics pipelines

## Solution Justification

Dedicated edge gateway handles protocol translation and validation at the network perimeter:

- **Protocol Adapters**: MQTT broker (EMQX), CoAP proxy (Envoy), HTTP/2 (for newer devices)
- **CloudEvents Normalization**: Convert heterogeneous payloads to standard CloudEvents format
- **Schema Validation**: Reject malformed events before they reach Kafka (consult Schema Registry)
- **TLS Termination**: Offload encryption at edge; backend uses mutual TLS
- **Buffering & Retry**: MQTT QoS 1/2 guarantees at-least-once delivery; Redis queue for burst absorption

## Use Cases

### 1. Vehicle GPS Telemetry Ingestion
- **Device**: GPS tracker publishes MQTT message every 30 seconds
- **Payload** (MQTT): `{vehicle_id: "VEH-123", lat: 52.52, lon: 13.405, battery_pct: 67, timestamp: "2024-10-15T08:30:00Z"}`
- **Gateway Flow**:
  1. EMQX broker receives MQTT message (QoS 1 for guaranteed delivery)
  2. Gateway wraps in CloudEvents envelope (adds event_id, source, type)
  3. Validates against `veh.telemetry.v1` schema (Schema Registry)
  4. Publishes to Kafka `veh.events.raw` topic (Avro-encoded)
- **Benefit**: 1000 vehicles × 120 msgs/hour = 120k events/hour ingested with <50ms latency

### 2. Low-Battery Alert Generation
- **Device**: Vehicle publishes battery_pct: 15 (below 20% threshold)
- **Gateway Flow**:
  1. Gateway detects low battery condition (rule engine: battery_pct < 20)
  2. Generates alert event: `{alert_type: "low_battery", vehicle_id: "VEH-123", severity: "high"}`
  3. Publishes to Kafka `alerts` topic (high-priority partition)
  4. Stream Processor consumes alert → notifies Fleet Management Service
- **Benefit**: Edge-level alerting reduces latency from 5 minutes (batch job) to <10 seconds

### 3. Vehicle Bay Occupancy Sensor
- **Device**: Bay sensor sends CoAP POST every 1 minute
- **Payload** (CoAP): `bay_id=BAY-42&occupied=true&vehicle_id=VEH-789&charging=true`
- **Gateway Flow**:
  1. Envoy proxy terminates CoAP request (converts to HTTP internally)
  2. Parse query params → JSON → CloudEvents
  3. Validate schema: `bay.occupancy.v1`
  4. Publish to Kafka `veh.events.raw`
- **Benefit**: CoAP uses 50% less bandwidth than HTTP for constrained devices; gateway handles protocol complexity

### 4. Malformed Data Rejection
- **Device**: Compromised vehicle sends corrupted telemetry (missing vehicle_id field)
- **Gateway Flow**:
  1. CloudEvents wrapper adds metadata
  2. Schema validation fails (vehicle_id is required field)
  3. Gateway rejects event; logs to observability stack
  4. Publishes to `dead_letter_queue` topic for forensic analysis
- **Benefit**: Data pipeline protected from corruption; bad data never reaches analytics

### 5. Firmware Update Command
- **Scenario**: Fleet Management Service needs to update vehicle firmware
- **Gateway Flow** (bidirectional):
  1. Service publishes command to Kafka `vehicle.commands` topic
  2. Gateway consumes command; routes to specific vehicle via MQTT (topic: `vehicles/VEH-123/commands`)
  3. Vehicle acknowledges firmware download started
  4. Gateway publishes ack to Kafka `veh.events.raw`
- **Benefit**: Gateway maintains persistent MQTT connections; services don't need device awareness

## Technology Choice

**Selected**: EMQX (MQTT broker) + Envoy Proxy (CoAP/HTTP) + CloudEvents SDK

**Rationale**:
- **EMQX**: Scalable MQTT 3.1.1/5.0 broker for IoT
  - 10M+ concurrent connections per cluster
  - QoS 0/1/2 for delivery guarantees
  - Rule engine for edge filtering/alerting
  - Kafka bridge (native integration)
  - Authentication: X.509 certificates, PSK, JWT
- **Envoy Proxy**: Service proxy for CoAP and HTTP/2 devices
  - HTTP/2 server push for low-latency commands
  - Rate limiting per device_id
  - Health checks and circuit breaking
- **CloudEvents**: CNCF standard for event metadata
  - Vendor-neutral envelope format
  - Supports Avro, Protobuf, JSON content types
  - Tracing context propagatxion (distributed tracing)
- **EU Compliance**: EMQX + Envoy self-hosted; all telemetry stays in EU regions
