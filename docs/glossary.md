# Glossary

## CloudEvents

CloudEvents is an open specification for describing event data in a consistent way across services, platforms, and message brokers.

### Core Idea

CloudEvents defines a standard schema for event metadata—things like the event’s source, type, timestamp, and unique ID—so that different systems (Kafka, AWS EventBridge, Google Pub/Sub, Azure Event Grid, etc.) can interoperate seamlessly.

Think of it as “JSON for events” — a portable format that makes events self-describing and transport-agnostic.

## MQTT

MQTT, which stands for Message Queuing Telemetry Transport, is a lightweight, publish-subscribe messaging protocol designed for the Internet of Things (IoT) and machine-to-machine (M2M) communication. It is ideal for devices with limited resources or network bandwidth, as it uses a broker to efficiently route messages to and from devices based on topics. Key components include the broker, publishers (devices that send messages), and subscribers (devices that receive messages).