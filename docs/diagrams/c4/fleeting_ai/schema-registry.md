# Schema Registry - Centralized schema management for event-driven architecture

## Context

MobilityCorp's event-driven platform involves dozens of microservices, IoT devices, and data pipelines exchanging events through the message broker. As the system evolves:

- Services are developed and deployed independently by different teams
- Event schemas need to evolve without breaking existing consumers
- Multiple serialization formats (Avro, Protobuf, JSON) are used across the platform
- CloudEvents standard wraps domain events with metadata
- Producers and consumers may be written in different languages (Python, Go, Java, TypeScript)

## Problem

Without centralized schema governance, the platform faces critical integration challenges:

1. **Schema Drift**: Producers may change event structure without consumer awareness, causing runtime deserialization failures
2. **Incompatible Changes**: Breaking changes (removing fields, changing types) can cascade failures across dependent services
3. **Version Sprawl**: Multiple event versions coexist without clear compatibility contracts
4. **Discovery Gap**: Developers lack a central catalog to explore available events and their structures
5. **Validation Blind Spots**: Invalid events may be published, corrupting downstream data pipelines and analytics

## Solution Justification

A Schema Registry provides contract-first event design and runtime enforcement:

- **Compatibility Checking**: Enforces forward/backward/full compatibility rules before schema registration
- **Versioning**: Maintains complete history of schema evolution with clear version lineage
- **Validation**: Edge Ingest Gateway and service producers validate events against registered schemas before publishing
- **Code Generation**: Auto-generates serialization code (Avro/Proto classes) ensuring type safety
- **Documentation**: Serves as living API documentation for event contracts across teams

## Use Cases

### 1. IoT Device Event Validation
- **Scenario**: Vehicle Bay Device publishes telemetry via Edge Ingest Gateway
- **Flow**: Gateway validates CloudEvents payload against `veh.telemetry.v1` schema before publishing to Kafka
- **Benefit**: Reject malformed/malicious device data at the edge, preventing data corruption

### 2. Microservice Event Evolution
- **Scenario**: Booking Service needs to add `promo_code` field to `booking_created` event
- **Flow**: Developer attempts to register `booking.events.v2` schema; Registry validates backward compatibility
- **Benefit**: Existing consumers (Payment Service, Read Model updaters) continue working; new consumers can leverage field

### 3. Stream Processing Schema References
- **Scenario**: Flink job joins `veh.events.raw` with `domain.events` to enrich inventory updates
- **Flow**: Flink fetches schemas from Registry to deserialize events correctly
- **Benefit**: Type-safe stream processing; automatic handling of schema evolution

### 4. Analytics Pipeline Schema Discovery
- **Scenario**: Data engineer building new Gold-layer aggregate needs to understand available event types
- **Flow**: Browse Registry UI/API to discover `return_completed` event structure and field semantics
- **Benefit**: Self-service data access; reduced cross-team coordination overhead

### 5. Multi-Language Interoperability
- **Scenario**: Python-based ML pipeline consumes events produced by Go-based Fleet Management Service
- **Flow**: Both sides use Registry-generated serialization code from same Avro schema
- **Benefit**: Zero serialization mismatches; guaranteed wire-format compatibility

## Technology Choice

**Selected**: Apicurio Registry (with Confluent Schema Registry API compatibility)

**Rationale**:
- Open-source with Apache 2.0 license (no vendor lock-in)
- Supports Avro, Protobuf, JSON Schema, and CloudEvents
- REST + gRPC APIs for high-performance validation
- Native Kafka storage backend (fits existing infrastructure)
- Compatible with Confluent ecosystem tooling (Kafka Connect, ksqlDB)
- EU data residency compliant (self-hosted)
