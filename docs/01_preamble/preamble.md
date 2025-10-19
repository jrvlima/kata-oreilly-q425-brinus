# MobilityWeave Architectural Document: Preamble

1. Application Context

## Context

MobilityCorp provide short-term rental for last-mile transport!

- Electric scooters
- eBikes
- Electric cars and vans

Our customers sign up and can rent available vehicles

MobilityCorp operates in multiple locations, mostly in city locations but is increasingly offering its car and van rental to more suburban locations

We want you to come up with a new architecture for MobilityCorp, incorporating AI functionality where appropriate

## Problems to be Resolved

Our customers keep complaining that the right vehicles aren't in the right places!

- How do we know when people will want to use the vehicles?
- Can we anticipate customer needs?

We also have problems with our electric vehicles running out of charge

- Can we work out how to prioritise which vehicles to switch out batteries? (for bikes and scooters)

We want our customers to use our services more frequently

- Right now, most of our customers just use them on an ad-hoc basis we'd like more of them to rely on our fleet for regular trips like daily commutes

## Personas

| Persona                                      | Description                                                                                 | Goals                                                                               | Pain Points                                                                         | Opportunities for AI                                                                             |
| -------------------------------------------- | ------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| **Sofia – Urban Commuter (Customer)**        | 28, marketing analyst, lives downtown. Uses scooters/eBikes for short commutes.             | Quick, reliable access to nearby vehicles. Predictable availability near home/work. | Often finds no scooters near her in the morning. Sometimes battery is low.          | Predictive demand models to position vehicles; personalized recommendations for recurring trips. |
| **Carlos – Suburban Worker (Customer)**      | 42, project manager, suburban area. Uses electric vans occasionally.                        | Plan weekend trips or home improvement transport. Reliable booking ahead.           | Vans unavailable at desired suburban stations. Long trip times reduce availability. | Demand forecasting for suburban use; route optimization; dynamic fleet balancing.                |
| **Maya – Fleet Operator (Staff)**            | 33, MobilityCorp field ops. Drives van to swap scooter batteries and redistribute vehicles. | Optimize daily routes and time. Know which vehicles need charge or relocation.      | No prioritization of which vehicles to attend first. Manual planning.               | AI-assisted route optimization; predictive battery failure alerts.                               |
| **Andre – Operations Manager (Staff)**       | 45, manages fleet and city operations.                                                      | Ensure balanced distribution and minimize idle vehicles.                            | Data overload; decisions are reactive.                                              | AI dashboards for predictive insights and anomaly detection.                                     |
| **MobilityCorp AI System (Virtual Persona)** | Intelligent layer that learns from telemetry, bookings, and user behavior.                  | Optimize utilization, anticipate demand, enhance user experience.                   | Data silos, inconsistent feedback loop.                                             | Autonomous recommendations, demand prediction, and energy optimization.                          |


## User Flows

### 1. Vehicle Booking Flow

AI touchpoints: demand prediction, route recommendations, auto-suggest alternate vehicles, feedback analysis.

![Vehicle Booking Flow](assets/VehicleBookingFlow.png)

### 2. Battery Swap / Fleet Maintenance Flow

AI touchpoints: predictive maintenance, optimal route planning, prioritization algorithm.

![Battery Swap / Fleet Maintenance Flow](assets/BatterySwap_FleetMaintenance.png)

### 3. Customer Retention Flow

AI touchpoints: behavioral segmentation, recommendation engine, retention optimization.

![Customer Retention Flow](assets/CustomerRetentionFlow.png)

## Product Backlog Building (PBB) Canvas Summary

| **Block**                 | **Content**                                                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Product Vision**        | Enable MobilityCorp to deliver reliable, AI-driven mobility services — ensuring the right vehicle, at the right place, at the right time.                                                                                                                                                                                                                                                                           |
| **Business Goals**        | 1️⃣ Increase vehicle utilization rate by 25%<br>2️⃣ Reduce “empty bay” complaints by 40%<br>3️⃣ Optimize energy/battery usage by 20%<br>4️⃣ Improve customer retention via personalized recommendations.                                                                                                                                                                                                            |
| **Target Users**          | Urban/Suburban commuters, fleet operators, city managers.                                                                                                                                                                                                                                                                                                                                                           |
| **Main Features (Epics)** | 🚗 **Smart Demand Forecasting** – ML-driven demand prediction per location/time.<br>🔋 **Battery Optimization** – Predictive charge management & routing.<br>🗺️ **Fleet Rebalancing Assistant** – AI route planner for redistribution.<br>🧠 **Customer Engagement AI** – Personalized commute recommendations & loyalty engine.<br>⚙️ **Ops Dashboard** – Predictive analytics and decision support for managers. |
| **User Stories Examples** | - As *Sofia*, I want to see available scooters near me in the morning so I can commute quickly.<br>- As *Maya*, I want the system to show which batteries to replace first so I can plan my route efficiently.<br>- As *Andre*, I want to see tomorrow’s predicted vehicle demand so I can allocate resources proactively.                                                                                          |
| **AI Use Cases**          | - Time-series forecasting for demand prediction (Prophet / LSTM / SageMaker Forecast).<br>- Predictive maintenance (battery degradation model).<br>- Recommender systems (personalized commute plans).<br>- Natural language sentiment analysis on user feedback.                                                                                                                                                   |
| **Metrics / KPIs**        | Fleet utilization %, downtime hours, average wait time per customer, # of low-battery incidents, retention rate increase.                                                                                                                                                                                                                                                                                           |
| **Risks / Constraints**   | Data sparsity, non-deterministic AI behavior, privacy compliance (GDPR), multi-city scaling.                                                                                                                                                                                                                                                                                                                        |
| **Validation Strategy**   | A/B tests for recommendations, shadow-mode testing for predictive models, human-in-loop feedback review.                                                                                                                                                                                                                                                                                                            |
