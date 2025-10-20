# Glossary

This glossary defines key technologies, tools, frameworks, platforms, and concepts used across the MobilityCorp architecture and AI-enabled data platform.

---

## Kafka

**Type:** Event Streaming Platform  
**Description:** Distributed publish/subscribe system for high-throughput, low-latency data pipelines.  
**Usage:** Backbone for real-time event delivery between microservices, IoT devices, and analytical systems.  

---

## Kafka Connect

**Type:** Integration Framework  
**Description:** Plugin-based framework for moving data between Kafka topics and external systems.  
**Usage:** Writes domain and telemetry events from Kafka to S3/MinIO (Bronze layer) via S3 Sink Connector.  

---

## CloudEvents

CloudEvents is an open specification for describing event data in a consistent way across services, platforms, and message brokers.

**Type:** Open Standard (CNCF)  
**Description:** Specification for describing event metadata in a common way, promoting interoperability between producers and consumers.  
**Usage:** Used for normalized event payloads across microservices and IoT devices.

### Core Idea

CloudEvents defines a standard schema for event metadata—things like the event’s source, type, timestamp, and unique ID—so that different systems (Kafka, AWS EventBridge, Google Pub/Sub, Azure Event Grid, etc.) can interoperate seamlessly.

Think of it as “JSON for events” — a portable format that makes events self-describing and transport-agnostic.

---

## Flink

**Type:** Stream Processing Framework  
**Description:** Open-source system for stateful computations over unbounded and bounded data streams.  
**Usage:** Performs joins, window aggregations, and late-event handling for telemetry and domain events.  

---

## ksqlDB

**Type:** Stream Processing Platform  
**Description:** SQL-based layer on top of Kafka Streams enabling declarative stream processing.  
**Usage:** Used for simpler, SQL-only transformations and materialized views from Kafka topics.  

---

## Kafka Streams

**Type:** Stream Processing Library  
**Description:** Lightweight Java library for building applications that process data in Kafka topics.  
**Usage:** Enables microservices to perform local aggregations or joins without Flink overhead.  

---

## ClickHouse

**Type:** OLAP Database  
**Description:** Column-oriented analytical database optimized for time-series and aggregate queries.  
**Usage:** Silver layer storage for analytical facts and dashboards.  

---

## TimescaleDB

**Type:** Time-Series Database (PostgreSQL extension)  
**Description:** Extends PostgreSQL with efficient time-series data storage and aggregation capabilities.  
**Usage:** Alternative Silver-layer backend for analytical facts.  

---

## S3 / MinIO

**Type:** Object Storage  
**Description:** Highly durable, scalable storage for binary and structured data.  
**Usage:** Bronze layer archive for raw events and telemetry; source for ML training and replay.  

---

## dbt (Data Build Tool)

**Type:** Transformation Framework  
**Description:** Enables modular, versioned SQL transformations using a software engineering workflow.  
**Usage:** Converts Silver analytical tables into Gold curated marts for BI and AI consumption.  

---

## Airflow

**Type:** Workflow Orchestrator  
**Description:** Platform for authoring, scheduling, and monitoring data workflows.  
**Usage:** Runs scheduled DAGs for dbt transformations and embedding pipelines.  

---

## Prefect

**Type:** Workflow Orchestration Platform  
**Description:** Python-based orchestration alternative to Airflow.  
**Usage:** Coordinates batch jobs and embedding generation tasks.  

---

## PostgreSQL

**Type:** Relational Database  
**Description:** ACID-compliant open-source database with broad SQL support.  
**Usage:** Serves as the Read Model DB in CQRS, optimized for operational projections.  

---

## Qdrant

**Type:** Vector Database  
**Description:** Open-source similarity search engine for embeddings.  
**Usage:** Stores AI embeddings and enables hybrid (dense + sparse) retrieval for RAG pipelines.  

---

## Bedrock Titan Embeddings

**Type:** Embedding Model (AWS Bedrock)  
**Description:** Amazon’s managed text-embedding foundation model.  
**Usage:** Generates semantic vectors from curated (Gold) data for retrieval-augmented generation.  

---

## LlamaIndex

**Type:** AI Data Framework  
**Description:** Python framework for building RAG pipelines and knowledge ingestion flows.  
**Usage:** Parses dbt-curated outputs, chunks text, and generates embeddings for the Vector DB.  

---

## LangChain

**Type:** AI Orchestration Framework  
**Description:** Modular framework to compose LLMs, retrieval, and tools into reasoning pipelines.  
**Usage:** Powering the RAG API orchestration layer to query Gold data and the Vector DB.  

---

## FastAPI

**Type:** Web Framework (Python)  
**Description:** Modern asynchronous framework for building APIs with automatic OpenAPI docs.  
**Usage:** Backend for RAG API and inference endpoints.  

---

## CQRS (Command Query Responsibility Segregation)

**Type:** Architectural Pattern  
**Description:** Separates read and write models for scalability and flexibility.  
**Usage:** Used in MobilityCorp microservices; Read Model DB provides fast query access for assistants.  

---

## Bronze Layer

**Type:** Data Architecture Tier  
**Description:** Immutable raw data archive for replay, audits, and machine learning training.  
**Usage:** Receives event streams from Kafka via connectors.  

---

## Silver Layer

**Type:** Data Architecture Tier  
**Description:** Cleaned, normalized, and enriched analytical facts.  
**Usage:** Output of Flink/ksqlDB processing; foundation for curated Gold marts.  

---

## Gold Layer

**Type:** Data Architecture Tier  
**Description:** Curated dimensional models optimized for BI and AI.  
**Usage:** Source for embedding generation and semantic knowledge ingestion.  

---

## RAG (Retrieval-Augmented Generation)

**Type:** AI Technique  
**Description:** Combines LLM reasoning with external factual retrieval from a vector database.  
**Usage:** Generates context-aware, verifiable AI responses using embeddings from Gold data.  

---

## Embedding Pipeline

**Type:** AI Data Pipeline  
**Description:** Transforms curated (Gold) datasets into semantic embeddings stored in a vector database.  
**Usage:** Enables context retrieval for downstream LLM queries.  

---

## Observability Stack (OTel)

**Type:** Monitoring and Tracing Platform  
**Description:** OpenTelemetry stack for distributed metrics, logs, and traces.  
**Usage:** Provides observability across brokers, gateways, microservices, and pipelines.  

---

## Prometheus

**Type:** Metrics Collector  
**Description:** Time-series monitoring system for metrics and alerting.  
**Usage:** Collects metrics from microservices and Flink clusters.  

---

## Grafana

**Type:** Visualization Platform  
**Description:** Analytics platform for dashboards and visualizations.  
**Usage:** Displays system health, lag, throughput, and latency dashboards.  

---

## Loki

**Type:** Log Aggregation System  
**Description:** Horizontal-scalable, low-cost log storage optimized for Prometheus labels.  
**Usage:** Centralized log collection for observability stack.  

---

## Apicurio / Confluent Schema Registry

**Type:** Schema Registry Platform  
**Description:** Central repository for message schemas with versioning and compatibility rules.  
**Usage:** Enforces consistent schema evolution across event producers and consumers.  

---

## REST

**Type:** Protocol  
**Description:** Representational State Transfer; architectural style for web APIs using HTTP.  
**Usage:** Used between client applications, RAG API, and backend services.  

---

## gRPC

**Type:** Protocol  
**Description:** High-performance, binary RPC framework using HTTP/2 and Protocol Buffers.  
**Usage:** Enables fast communication between AI microservices and the Vector DB.  

---

## HTTPS

**Type:** Network Protocol  
**Description:** Secure version of HTTP using TLS encryption.  
**Usage:** Communication between web/mobile clients and API gateways.  

---

## JSON / Avro / Parquet

**Type:** Serialization Formats  
**Description:** Structured data formats for messages and storage.  
**Usage:** JSON for REST APIs; Avro/Parquet for compact event and analytical data storage.  

---

## dbt on ClickHouse / Postgres

**Type:** Data Transformation Layer  
**Description:** dbt transformations executed on ClickHouse or PostgreSQL backends.  
**Usage:** Produces Gold marts used by AI ingestion pipelines.  

---

## Airflow / Prefect + dbt

**Type:** Orchestration Stack  
**Description:** Batch orchestration of dbt transformations and ML tasks.  
**Usage:** Automates data curation and embedding refresh workflows.  

---

## Hybrid Search

**Type:** Information Retrieval Technique  
**Description:** Combines dense vector search with sparse lexical scoring for better relevance.  
**Usage:** Used in Qdrant to retrieve documents for RAG pipelines.  

---

## Event-Driven Architecture

**Type:** Architectural Style  
**Description:** System design centered around asynchronous event communication between components.  
**Usage:** Core integration model across MobilityCorp’s microservices and telemetry ingestion.  

---

## AI Ingestion Pipeline

**Type:** AI Data Flow  
**Description:** Process for preparing structured and unstructured enterprise data for AI retrieval.  
**Usage:** Ingests curated data, applies embedding models, and updates the vector store.  

---

## MQTT

MQTT, which stands for Message Queuing Telemetry Transport, is a lightweight, publish-subscribe messaging protocol designed for the Internet of Things (IoT) and machine-to-machine (M2M) communication. It is ideal for devices with limited resources or network bandwidth, as it uses a broker to efficiently route messages to and from devices based on topics. Key components include the broker, publishers (devices that send messages), and subscribers (devices that receive messages).

---

## Fact Table

A fact table is a central component in a data warehouse that stores numerical measurements and metrics about business events, such as sales, costs, or inventory levels. It contains measures (quantitative data) and foreign keys that link to dimension tables, which provide descriptive context. Fact tables are designed to be large and grow over time as new business data is added. 

---

## Sink

In computer science, a sink is the destination where data or a process ends. The term is used in various contexts, such as a data sink in data pipelines where data is stored or sent after processing, or an event sink, a class or function that receives events. In digital electronics, a sink is an output that absorbs current from a load.

---

## dbt

dbt (data build tool) operates within a database, specifically a data warehouse, to perform data transformations. It is not a database itself, but rather a tool that orchestrates and manages the "T" (Transformation) in an ELT (Extract, Load, Transform) process.

---

## Avro

**Type:** Serialization Format  
**Description:** A compact, fast, binary data serialization system developed within the Apache Hadoop project.  
**Key Features:**  

- Uses JSON for defining schemas and binary for encoding data.  
- Supports schema evolution, allowing new fields or defaults without breaking existing consumers.  
- Self-describing, meaning serialized files embed schema information.  
**Usage:**  
Used for serializing messages in Kafka topics and long-term storage in the Bronze layer, ensuring cross-language compatibility and strong schema enforcement via the Schema Registry.  

---

## Parquet

**Type:** Columnar Storage Format  
**Description:** Open-source, column-oriented storage format optimized for analytical queries on large datasets.  
**Key Features:**  

- Highly efficient compression and encoding schemes per column.  
- Enables predicate pushdown, reducing I/O for analytical workloads.  
- Interoperable across big data frameworks like Spark, Presto, Trino, and dbt.  
**Usage:**  
Used in the Silver and Bronze layers to store telemetry and domain event data in a space-efficient and query-friendly format, typically as output of Kafka Connect sinks or Airflow/dbt transformations.  

---
