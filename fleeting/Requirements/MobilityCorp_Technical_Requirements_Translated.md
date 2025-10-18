## Technical Requirements Document (TRD)

NOTE: THIS IS NOT A DELIVERABLE.

Project Title: AI-Enhanced Mobility Platform for MobilityCorp

Prepared By: BRINUS – Prashant, Rafael, Harish, Khaled, Joao

Date: October 17, 2025

### 1. Introduction

This Technical Requirements Document provides a technical interpretation of the business requirements outlined in the MobilityCorp Software Requirements Document. The purpose is to translate business objectives into technical capability statements, without prescribing specific solutions, frameworks, or implementation methods.

### 2. Scope

The document applies to the AI-enhanced mobility platform supporting electric scooters, eBikes, electric cars, and vans. It includes requirements for system behavior, technical features, interoperability, and quality attributes. No design or technology choices are implied.

### 3. Technical Requirements

#### 3.1 Vehicle Management

TR-01: System shall support continuous vehicle position tracking through integrated telematics capability. (Event based to a central topic. MQTT?)

TR-02: System shall support remote lock, unlock, and disable operations through secure communication channels.

TR-03: System shall record and manage real-time vehicle state data in a centralized environment. (Event based to a central topic. MQTT ?)

#### 3.2 Booking System

TR-04: System shall include configurable booking logic supporting different rules by vehicle category. (Booking Rules Engine- one for cars and vans, another for scooters and bikes)

TR-05: System shall support both advance and short-term booking scenarios.(App-android and iPhone)

TR-06: System shall provide real-time visibility of vehicle availability. (GPS module with geofencing)

TR-07: System shall include analytical capability for forecasting demand and optimizing distribution. (Predictive analytics)

#### 3.3 Return Process

TR-08: System shall verify vehicle return location using geolocation validation.(Designated spots need not be the same as original spot) (GPS module with geofencing)

TR-09: System shall support visual proof capture for vehicle return compliance. (Image reading)

TR-10: System shall verify EV charging connection status at the time of return. (vehicle to app MQTT)

TR-11: System shall collect structured user feedback, including fault reporting. (HTTPS)

#### 3.4 Charging and Distribution

TR-12: System shall monitor power levels of vehicles and trigger alerts for battery replacement or charging. (MQTT)

TR-13: System shall support workforce task management for redistribution and battery servicing activities. (Operational Module)

TR-14: System shall enable demand-based identification of priority locations for fleet positioning. (Predictive analytics)

#### 3.5 Parking Module

TR-15: System shall uniquely identify each parking spot and charging location. When the vehicle enters or leaves the parking spot, the system shall mark the spot accordingly as occupied/unoccupied.

### 4. Non-Functional Technical Requirements

TR-15: Platform shall support internationalization (i18n) and localization (l10n) for multi-language and multi-currency operation.

TR-16: Platform shall ensure data confidentiality, integrity, and secure access control. (TLS)

TR-17: Platform shall maintain high availability and fault tolerance suitable for 24/7 operation. (Cloud based

TR-18: Platform shall scale to accommodate fleet and user growth without service degradation.

TR-19: Platform shall provide real-time responsiveness appropriate for live tracking and booking interactions.

### 5. Constraints

TR-20: All vehicles shall include a positioning capability compatible with system tracking requirements.

TR-21: Customer mobile devices shall support contactless communication for lock/unlock functionality.

TR-22: System shall require vehicle return confirmation with visual verification data.

TR-23: System shall validate EV charging status before closing a rental transaction.

TR-24: Manual staff intervention workflows shall be supported for operational activities such as redistribution.

### 6. Assumptions

TR-25: End-users are assumed to have smartphones capable of supporting mobile application requirements.

TR-26: Operational staff are assumed to have access to vehicles and tools for battery replacement and redistribution.

TR-27: Vehicles are assumed to include onboard systems capable of data exchange with platform services.

### 7. Requirements Traceability Matrix (RTM)

| Business Req. ID   | Business Requirement Summary         | Technical Req. ID   | Technical Requirement Summary                         |
|--------------------|--------------------------------------|---------------------|-------------------------------------------------------|
| BR-01              | Vehicle GPS tracking                 | TR-01               | Vehicle position tracking capability.                 |
| BR-02              | Remote unlock/disable                | TR-02               | Support for remote control operations.                |
| BR-03              | Booking logic per vehicle type       | TR-04               | Configurable booking logic by category.               |
| BR-04              | Predict demand for vehicle placement | TR-07               | Analytical forecasting capability.                    |
| BR-05              | Multi-language and currency support  | TR-15               | Internationalization and localization support.        |
| BR-06              | High availability and performance    | TR-17–TR-19         | Scalability, uptime, and responsiveness requirements. |