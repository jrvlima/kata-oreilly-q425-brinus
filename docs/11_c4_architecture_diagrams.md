# MobilityCorp C4 Architecture Diagrams

This document provides a comprehensive view of the MobilityCorp platform architecture using the C4 model.

## Table of Contents

- [Overview Diagrams](#overview-diagrams)
- [System Architecture](#system-architecture)
- [Computer Vision System](#computer-vision-system)
- [AI Systems](#ai-systems)
- [Platform Components](#platform-components)
- [Data Architecture](#data-architecture)
- [Applications](#applications)

---

## Overview Diagrams

### System Context
![System Context](diagrams/c4/png/index.png)

High-level view of the MobilityCorp platform showing major systems and external actors.

### Full System Architecture
![Full System Architecture](diagrams/c4/png/full.png)

Complete system architecture showing all components and their relationships.

---

## System Architecture

### Platform Overview
![Platform Overview](diagrams/c4/png/platform.png)

Core platform architecture showing the main infrastructure and service layers.

### Microservices Architecture
![Microservices](diagrams/c4/png/microservices.png)

Detailed view of the microservices architecture, showing individual business capability services.

### Message Broker
![Broker](diagrams/c4/png/broker.png)

Event streaming and messaging infrastructure using durable logs, queues, and stream processors.

### Stream Processing
![Stream Processor](diagrams/c4/png/stream_processor.png)

Real-time event processing architecture for stateful transformations and enrichment.

---

## Computer Vision System

### System Context
![CV System Context](diagrams/c4/png/cv_system_context.png)

High-level view of the Computer Vision system and its integration points.

### Container View
![CV Containers](diagrams/c4/png/cv_containers.png)

Container-level architecture of the Computer Vision system components.

### Component View
![CV Components](diagrams/c4/png/cv_components.png)

Detailed component architecture of the CV system internals.

### Docking Flow
![CV Docking Flow](diagrams/c4/png/cv_docking_flow.png)

Sequence diagram showing the vehicle docking verification process.

### Damage Detection Flow
![CV Damage Flow](diagrams/c4/png/cv_damage_flow.png)

Flow diagram for vehicle damage detection and assessment.

### Provider Failover
![CV Provider Failover](diagrams/c4/png/cv_provider_failover.png)

Architecture for handling AI provider failover and redundancy.

### Human Review Process
![CV Human Review](diagrams/c4/png/cv_human_review.png)

Workflow for human-in-the-loop review of CV system decisions.

---

## AI Systems

### AI Systems Overview
![AI Systems](diagrams/c4/png/ai_systems.png)

Overview of all AI-powered systems for operational verification, safety, and compliance.

### AI Analytics
![AI Analytics](diagrams/c4/png/ai_analytics.png)

Analytics and machine learning systems for predictive insights and optimization.

---

## Platform Components

### Telemetry System
![Telemetry](diagrams/c4/png/telemetry.png)

Real-time telemetry collection and processing from vehicles (GPS, battery, status).

### Batch Orchestrator
![Batch Orchestrator](diagrams/c4/png/stream_processor.png)

Workflow automation for data pipelines, analytics reports, and scheduled tasks.

---

## Data Architecture

### Data Layers
![Data Layers](diagrams/c4/png/data_layers.png)

Multi-tier data architecture for raw events, analytical facts, and curated data marts.

### Fresh Availability
![Fresh Availability](diagrams/c4/png/fresh_availability.png)

Architecture pattern for maintaining real-time data availability.

### Burst Tolerance
![Burst Tolerance](diagrams/c4/png/burst_tolerance.png)

System design for handling traffic spikes and burst loads.

### Exactly-Once Projection
![Exactly-Once Projection](diagrams/c4/png/exactly_once_projection.png)

Data consistency pattern ensuring exactly-once processing semantics.

---

## Applications

### Customer Application
![Customer App](diagrams/c4/png/customer_app.png)

Mobile application architecture for end-user vehicle rental and management.

### Staff Application
![Staff App](diagrams/c4/png/staff_app.png)

Internal staff application for operations, maintenance, and support.

---

## Diagram Legend

- **Blue boxes**: Core platform services and data layers
- **Green boxes**: Business capability microservices
- **Purple boxes**: AI-powered systems
- **Cyan boxes**: Infrastructure components (stream processors, orchestrators)
- **Brown boxes**: Messaging infrastructure (broker, queues, logs)

---

## Related Documentation

- [Architecture Decision Records](../adrs/)
- [Requirements Documentation](../fleeting/Requirements/)
- [Data Model](../fleeting/DataModel/)
- [Competition Overview](../qata-description.md)

---

**Generated for**: O'Reilly Q4 2025 Architectural Katas Challenge
**Team**: BRINUS (Rafael, Prashant, Khaled, João Vinagre)
**Last Updated**: October 23, 2025
