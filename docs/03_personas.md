# User Personas

## Overview

This document defines key user personas for the MobilityCorp platform. Understanding these personas drives requirements, user journey design, and architectural decisions.

---

## Customer Personas

### Persona 1: Commuter Chris

- **Profile**
  - **Age**: 28
  - **Occupation**: Marketing Manager
  - **Location**: Berlin, Germany
  - **Tech Savvy**: High
  - **Income Level**: Middle-income professional

**Background**
Chris lives 5km from his office in Berlin's city center. He doesn't own a car and relies on public transit, but his U-Bahn line is often delayed. He's looking for a reliable backup option for his daily commute.

- **Goals & Motivations**
  - Get to work on time reliably
  - Avoid public transit when it's crowded or delayed
  - Save money compared to taxi/Uber
  - Environmentally conscious transportation
  - Flexible last-mile solution

- **Pain Points**
  - Public transit delays and overcrowding
  - Can't find vehicles when he needs them (8am rush)
  - Doesn't want to commit to owning a scooter/bike
  - Needs predictable daily commute costs
  - Concerned about vehicle battery running out mid-trip

- **Technology Usage**
  - Heavy smartphone user (iPhone)
  - Uses multiple mobility apps (Lime, Tier, Bolt)
  - Comfortable with NFC and mobile payments
  - Checks apps the night before to plan commute
  - Expects real-time availability and notifications

- **Booking Behavior**
  - **Frequency**: 3-4 times per week (backup to transit)
  - **Booking Window**: Usually books evening before or 30-60 min advance
  - **Vehicle Preference**: eBike (fast, weather-protected)
  - **Duration**: 20-30 minute trips
  - **Price Sensitivity**: Moderate (values reliability over lowest price)

- **Needs from Platform**
  - Reliable vehicle availability near home/office
  - Predictable pricing (subscription or commuter plan)
  - Battery level visibility before booking
  - Quick booking and unlock process
  - Route planning integration

**Potential for Engagement**
**HIGH** - Perfect candidate for subscription/commuter plan. Uses platform regularly for predictable commute pattern.

---

### Persona 2: Tourist Tina

- **Profile**
  - **Age**: 35
  - **Occupation**: Teacher (on vacation)
  - **Location**: Visiting Amsterdam, Netherlands
  - **Tech Savvy**: Medium
  - **Income Level**: Middle-income

**Background**
Tina is visiting Amsterdam for 4 days with her partner. She wants to explore the city efficiently without renting a traditional car or relying solely on walking and public transit.

- **Goals & Motivations**
  - Explore multiple attractions in limited time
  - Flexible, spontaneous sightseeing
  - Authentic local experience
  - Avoid tourist bus crowds
  - Safe and easy to use

- **Pain Points**
  - Unfamiliar with local geography
  - Doesn't speak Dutch fluently
  - Worried about getting lost or stranded
  - Concerned about complicated booking/payment
  - Needs multi-day access without commitment

- **Technology Usage**
  - Moderate smartphone user (Android)
  - Uses Google Maps heavily for navigation
  - Prefers apps with strong English support
  - Uses credit card for most payments
  - Reads reviews before trying new services

- **Booking Behavior**
  - **Frequency**: First-time user, multiple bookings during 4-day visit
  - **Booking Window**: Spontaneous (5-30 min advance)
  - **Vehicle Preference**: eBike or scooter (easy to use, tourist-friendly)
  - **Duration**: 1-3 hour trips for sightseeing
  - **Price Sensitivity**: Low-moderate (vacation mindset)

- **Needs from Platform**
  - Multi-language support (English interface)
  - Tourist-friendly vehicle locations (near hotels, attractions)
  - Clear pricing in familiar currency or conversion
  - Simple onboarding and account creation
  - GPS navigation integration
  - Customer support in English

**Potential for Engagement**
**LOW** - One-time user, but may recommend or re-use in other cities. Focus on satisfaction and word-of-mouth.

---

### Persona 3: Event-Goer Emma

- **Profile**
  - **Age**: 24
  - **Occupation**: University Student
  - **Location**: Barcelona, Spain
  - **Tech Savvy**: Very High
  - **Income Level**: Student/Budget-conscious

**Background**
Emma frequently attends concerts, festivals, and social events around Barcelona. She needs transportation late at night when public transit is limited or unavailable.

- **Goals & Motivations**
  - Last-mile connectivity to/from events
  - Avoid expensive late-night taxis
  - Safe transportation home after events
  - Split rides with friends
  - Environmentally friendly choice

- **Pain Points**
  - Events end when public transit stops
  - Taxi surge pricing after concerts
  - No vehicles available after big events (everyone trying to leave)
  - Concerned about safety late at night
  - Limited budget as a student

- **Technology Usage**
  - Always-on smartphone user (iPhone)
  - Heavy social media and messaging app user
  - Checks event schedules and transportation ahead of time
  - Comfortable with app-based services
  - Uses mobile payments exclusively

- **Booking Behavior**
  - **Frequency**: 1-2 times per week (weekend nights)
  - **Booking Window**: Plans ahead for events (1-2 days advance if possible)
  - **Vehicle Preference**: Scooter (quick, cheap) or shared car with friends
  - **Duration**: 15-45 minute trips late night
  - **Price Sensitivity**: High (student budget)

- **Needs from Platform**
  - Vehicle availability near event venues
  - Ability to pre-book for specific event end times
  - Budget-friendly pricing or student discounts
  - Safety features (track my ride, emergency contact)
  - Group booking or ride splitting
  - Notifications about vehicle availability near events

**Potential for Engagement**
**MEDIUM** - Regular weekend user. Responsive to student promotions and event-based offers.

---

### Persona 4: Family Fred

- **Profile**
  - **Age**: 42
  - **Occupation**: Software Developer
  - **Location**: Paris, France
  - **Tech Savvy**: High
  - **Income Level**: Upper-middle income, family of 4

**Background**
Fred and his family don't own a car (city living) but occasionally need one for weekend trips, grocery shopping, or visiting family in suburbs. They need larger vehicle options.

- **Goals & Motivations**
  - Occasional car access without ownership costs
  - Family-sized vehicles (4+ seats, cargo space)
  - Weekend getaways and errands
  - Flexible rental periods
  - Cost-effective alternative to car ownership

- **Pain Points**
  - Most micro-mobility options too small for family
  - Traditional car rental requires advance planning and office visits
  - Needs cargo space for groceries or luggage
  - Wants convenient pickup/return locations
  - Concerned about vehicle cleanliness and safety for children

- **Technology Usage**
  - Professional technology user
  - Uses desktop and mobile apps
  - Plans trips in advance (books 3-7 days ahead)
  - Values reliability and reviews
  - Comfortable with digital processes

- **Booking Behavior**
  - **Frequency**: 2-3 times per month (weekends, errands)
  - **Booking Window**: 3-7 days in advance
  - **Vehicle Preference**: Electric car or van
  - **Duration**: 4-8 hour fixed duration rentals
  - **Price Sensitivity**: Low-moderate (values convenience)

- **Needs from Platform**
  - Booking electric cars/vans 7 days in advance
  - Fixed duration rentals with clear pricing
  - Parking spot locations convenient to home
  - Vehicle cleanliness and safety standards
  - Child seat availability
  - Transparent damage/insurance policies

**Potential for Engagement**
**MEDIUM** - Regular occasional user. May subscribe to family plan if pricing attractive.

---

## Operations Personas

### Persona 5: Dispatcher Dan

- **Profile**
  - **Age**: 38
  - **Occupation**: Fleet Operations Manager
  - **Location**: Berlin Operations Center
  - **Tech Savvy**: Medium-High
  - **Experience**: 5 years in logistics/operations

**Background**
Dan manages day-to-day fleet operations for MobilityCorp's Berlin locations. He coordinates field technicians, monitors vehicle status, and makes redistribution and charging decisions.

- **Responsibilities**
  - Monitor real-time fleet status and availability
  - Assign redistribution tasks to field technicians
  - Prioritize battery swaps and vehicle maintenance
  - Respond to customer support escalations
  - Analyze daily operational metrics
  - Plan staffing for peak demand periods

- **Goals & Motivations**
  - Maximize fleet availability and utilization
  - Minimize operational costs
  - Efficient staff scheduling and task assignment
  - Prevent customer complaints
  - Meet operational KPIs

- **Pain Points**
  - Overwhelmed by manual decision-making
  - Reactive rather than proactive operations
  - No visibility into future demand
  - Difficult to prioritize competing tasks
  - Staff often sent to wrong locations
  - Emergency battery swaps disrupt planned work
  - No data-driven tools for optimization

- **Technology Usage**
  - Uses operations dashboard daily (desktop + tablet)
  - Coordinates with staff via mobile messaging
  - Accesses vehicle telemetry and GPS data
  - Reviews reports and dashboards
  - Comfortable with operational software

- **Daily Workflow**
  1. Morning: Review overnight vehicle status and distribution
  2. AM: Assign field staff redistribution tasks for lunch/evening peaks
  3. Midday: Monitor real-time issues and adjust plans
  4. PM: Coordinate battery swaps based on charge levels
  5. Evening: Review day's metrics and plan for next day

- **Needs from Platform**
  - Predictive demand forecasts by location and time
  - Recommended redistribution plans with optimized routes
  - Battery health predictions and prioritized swap lists
  - Real-time fleet health dashboard
  - Staff task assignment and tracking
  - Performance analytics and reporting
  - Alerts for critical issues

- **AI System Interaction**
  - **Primary User** of AI-driven operational tools
  - Needs explainable recommendations (why move these vehicles?)
  - Requires ability to override AI suggestions
  - Values confidence scores and uncertainty indicators
  - Provides feedback to improve AI models

---

### Persona 6: Field Technician Felipe

- **Profile**
  - **Age**: 29
  - **Occupation**: Fleet Maintenance Technician
  - **Location**: Barcelona Field Operations
  - **Tech Savvy**: Medium
  - **Experience**: 2 years with MobilityCorp

**Background**
Felipe works in the field, physically redistributing vehicles, swapping batteries, and performing basic maintenance. He drives a van equipped with spare batteries and tools.

- **Responsibilities**
  - Redistribute vehicles based on dispatcher assignments
  - Perform battery swaps and charging
  - Conduct basic vehicle inspections and cleaning
  - Respond to customer support requests (stuck vehicles)
  - Report vehicle damage or maintenance issues
  - Document completed tasks via mobile app

- **Goals & Motivations**
  - Complete assigned tasks efficiently
  - Minimize driving time and distance
  - Clear daily task list by end of shift
  - Earn performance bonuses
  - Maintain safe work practices

- **Pain Points**
  - Receives tasks that are no longer relevant (vehicle moved by customer)
  - Inefficient routes waste time and fuel
  - Battery swaps often for already-dead vehicles (too late)
  - Unclear prioritization of tasks
  - Mobile app sometimes slow or confusing
  - Changes to assignments mid-route

- **Technology Usage**
  - Uses mobile app on company-provided smartphone
  - Basic GPS navigation skills
  - Scans NFC for vehicle access and task completion
  - Takes photos for documentation
  - Prefers simple, clear interfaces

- **Daily Workflow**
  1. Morning: Receive task list for day from dispatcher
  2. Drive optimized route to redistribute vehicles
  3. Perform battery swaps at designated locations
  4. Check for and resolve customer support issues
  5. Document completed work in mobile app
  6. Return to depot, report issues, restock supplies

- **Needs from Platform**
  - Clear, prioritized task list on mobile app
  - Turn-by-turn navigation to task locations
  - Real-time updates if tasks change
  - Quick task completion workflow (NFC scan, photo, done)
  - Battery swap instructions and vehicle access.
  - Communication channel with dispatcher
  - Performance tracking and feedback

- **AI System Interaction**
  - **End User** of AI-generated task recommendations
  - Needs simple, actionable instructions
  - Provides implicit feedback through task completion data
  - Values time savings from optimized routes

---

## Platform Administration Personas

### Persona 7: Product Manager Patricia

- **Profile**
  - **Age**: 34
  - **Occupation**: Product Manager - Mobility Platform
  - **Location**: MobilityCorp HQ (Amsterdam)
  - **Tech Savvy**: High
  - **Experience**: 8 years in tech product management

**Background**
Patricia owns the MobilityCorp platform roadmap and prioritizes features based on business goals, user feedback, and competitive analysis.

- **Responsibilities**
  - Define platform vision and roadmap
  - Prioritize feature development
  - Analyze user metrics and KPIs
  - Conduct market and competitive research
  - Collaborate with engineering and operations teams
  - Manage stakeholder expectations

- **Goals & Motivations**
  - Increase customer retention and engagement
  - Improve operational efficiency metrics
  - Launch AI-driven features successfully
  - Demonstrate ROI of platform investments
  - Scale platform to new markets

- **Pain Points**
  - Uncertainty around AI feature adoption
  - Difficulty validating non-deterministic AI outputs
  - Balancing innovation with reliability
  - Limited visibility into operational impact of features
  - Vendor/provider lock-in concerns for AI services

- **Technology Usage**
  - Heavy user of analytics platforms (Mixpanel, Amplitude)
  - Product management tools (Jira, Confluence)
  - User research and feedback tools
  - Prototype and design review tools
  - Business intelligence dashboards

- **Decision-Making Needs**
  - Data-driven insights on feature performance
  - A/B testing capabilities for AI features
  - Customer sentiment analysis
  - Operational impact metrics
  - Competitive benchmarking

- **Needs from Platform**
  - AI feature performance analytics
  - Customer engagement metrics by segment
  - Operational efficiency dashboards
  - A/B testing framework for AI recommendations
  - User feedback collection and analysis
  - Model performance and drift monitoring

---

### Persona 8: Data Scientist Deepa

- **Profile**
  - **Age**: 31
  - **Occupation**: Senior Data Scientist / ML Engineer
  - **Location**: MobilityCorp Tech Hub (Berlin)
  - **Tech Savvy**: Expert
  - **Experience**: 6 years in ML/AI

**Background**
Deepa develops and maintains the AI/ML models that power demand forecasting, battery predictions, and customer personalization.

- **Responsibilities**
  - Train and deploy ML models
  - Monitor model performance and drift
  - Conduct experiments and A/B tests
  - Analyze data quality and feature engineering
  - Collaborate with product and engineering teams
  - Research new AI techniques and capabilities

- **Goals & Motivations**
  - Build accurate, reliable models
  - Minimize model latency and costs
  - Ensure model fairness and bias mitigation
  - Demonstrate business impact of ML work
  - Stay current with AI/ML advances

- **Pain Points**
  - Data quality issues from vehicle telemetry
  - Cold start problems for new locations
  - Model drift due to changing patterns
  - Balancing model complexity vs. explainability
  - Vendor model limitations and costs
  - Validating non-deterministic outputs

- **Technology Usage**
  - Python, TensorFlow, PyTorch, scikit-learn
  - Cloud ML platforms (AWS SageMaker, Azure ML)
  - MLOps tools (MLflow, Kubeflow)
  - Data engineering tools (Spark, Airflow)
  - Jupyter notebooks, VS Code
  - Git, CI/CD pipelines

- **Daily Workflow**

1. Monitor model performance dashboards
2. Analyze data pipelines and feature quality
3. Run experiments and hyperparameter tuning
4. Review model predictions vs. actuals
5. Collaborate with product team on new features
6. Document models and results

- **Needs from Platform**
  - Scalable data pipeline infrastructure
  - Model training and deployment automation
  - Real-time and batch inference capabilities
  - Model monitoring and observability
  - Experimentation framework (A/B testing)
  - Feature store for shared features
  - Model versioning and rollback capabilities

- **AI System Role**
  - **Builder and Maintainer** of AI models
  - Requires flexible architecture for experimentation
  - Needs observability into model behavior
  - Values ability to swap models/providers
  - Focuses on continuous improvement

---

## Persona Summary Matrix

| Persona | Type | Frequency | Tech Savvy | AI Interaction | Business Priority |
|---------|------|-----------|-----------|----------------|-------------------|
| Commuter Chris | Customer | Daily/Weekly | High | Consumer (recommendations) | High - Retention target |
| Tourist Tina | Customer | One-time | Medium | Consumer (recommendations) | Medium - Experience focus |
| Event-Goer Emma | Customer | Weekly | Very High | Consumer (recommendations) | Medium - Engagement opportunity |
| Family Fred | Customer | Monthly | High | Consumer (booking optimization) | Medium - Niche segment |
| Dispatcher Dan | Operations | Daily | Med-High | Primary user (decisions) | Critical - Operations efficiency |
| Field Tech Felipe | Operations | Daily | Medium | End user (tasks) | High - Execution enabler |
| Product Manager Patricia | Admin | Daily | High | Strategic oversight | Critical - Platform owner |
| Data Scientist Deepa | Admin | Daily | Expert | Builder/maintainer | Critical - AI capability |

---

## Persona Usage in Requirements

These personas inform:

1. **Functional Requirements**: Features must serve persona needs
2. **User Journeys**: Workflows designed around persona behaviors
3. **Non-Functional Requirements**: Quality attributes match persona expectations
4. **Product Backlog**: User stories written from persona perspectives
5. **Architectural Decisions**: System design considers persona constraints

---

## Document Control

- **Version**: 1.0
- **Last Updated**: 2025-10-20
- **Owner**: BRINUS Team (O'Reilly Q4 2025 Kata)
- **Status**: Draft
- **References**: [01_business_context.md](01_business_context.md), [02_problem_statement.md](02_problem_statement.md)
