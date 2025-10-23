# Problem Statement

## Overview

MobilityCorp faces three interconnected operational challenges that impact customer satisfaction, fleet utilization, and operational costs. These challenges represent significant barriers to achieving strategic goals and require architectural solutions that leverage AI/ML capabilities.

---

## Problem 1: Vehicle Availability & Distribution

### The Challenge

- **"Vehicles are frequently not in the right locations when customers need them."**

### Current State Pain Points

#### Customer Impact

- **High Failure Rate**: Customers search for vehicles but find none available nearby
- **Booking Abandonment**: Users give up and switch to competitors (Lime, Bird, Tier)
- **Poor Experience**: Frustration with unreliable availability damages brand perception
- **Lost Revenue**: Each failed booking represents lost revenue opportunity

#### Operational Impact

- **Manual Redistribution**: Staff reactively move vehicles based on gut feeling
- **Inefficient Routes**: No optimization for redistribution logistics
- **High Costs**: Labor and fuel costs for moving vehicles
- **Time Lag**: Vehicles moved after demand has already peaked

### Root Causes

1. **Lack of Demand Prediction**
   - No forecasting of future demand patterns
   - Cannot anticipate peak times or locations
   - Reactive rather than proactive approach

2. **No Historical Analysis**
   - Past usage patterns not leveraged
   - Seasonal, weekly, daily trends ignored
   - Event-based demand spikes not anticipated

3. **Suboptimal Redistribution**
   - No systematic approach to vehicle placement
   - Inefficient routes for staff movements
   - Poor prioritization of which vehicles to move

4. **External Factors Ignored**
   - Weather impact on demand not considered
   - Events (concerts, sports, festivals) not factored
   - Public transit disruptions not integrated

### Quantified Impact

| Metric | Current State | Business Impact |
|--------|---------------|-----------------|
| Booking Failure Rate | ~25-30% | Lost revenue, customer churn |
| Manual Redistribution Cost | High | Operational inefficiency |
| Vehicle Utilization | Suboptimal | Underutilized fleet capacity |
| Customer Satisfaction | Below target | Brand damage, competitive disadvantage |

### Desired Future State

- **Predictive Placement**: Vehicles positioned based on forecasted demand
- **Proactive Redistribution**: Staff receive optimized routes before demand peaks
- **Dynamic Balancing**: Real-time adjustments based on actual vs. predicted demand
- **Event Integration**: External events factored into placement decisions
- **Improved Availability**: 95%+ success rate for customer vehicle searches

---

## Problem 2: Battery & Charging Management

### The Challenge

- **"Electric vehicles frequently run out of charge, requiring prioritization of battery swaps."**

### Current State Pain Points

#### Customer Impact

- **Stranded Rentals**: Vehicles become unusable mid-journey
- **Safety Concerns**: Customers worried about running out of charge
- **Booking Disappointment**: Vehicle shows available but has insufficient charge
- **Negative Reviews**: Poor battery management damages reputation

#### Operational Impact

- **Fleet Downtime**: Vehicles with low battery unavailable for bookings
- **Emergency Response**: Reactive battery swaps disrupt planned operations
- **Staff Inefficiency**: Unplanned trips to charge/swap batteries
- **Revenue Loss**: Significant portion of fleet offline at any given time

### Root Causes

1. **No Charge Prediction**
   - Cannot forecast when vehicles will need charging
   - No visibility into battery degradation patterns
   - Usage patterns not analyzed for charge consumption

2. **Reactive Maintenance**
   - Vehicles only charged when battery critical or depleted
   - No preventive charging strategy
   - Staff respond to issues rather than prevent them

3. **Poor Prioritization**
   - No systematic way to prioritize which vehicles to charge first
   - High-demand locations may have uncharged vehicles
   - Critical batteries not identified until too late

4. **Lack of Integration**
   - Battery status not integrated with demand forecasting
   - Charging needs not coordinated with redistribution
   - No holistic view of fleet health

### Quantified Impact

| Metric | Current State | Business Impact |
|--------|---------------|-----------------|
| Fleet Downtime (Low Battery) | 15-20% | Lost revenue, poor availability |
| Emergency Battery Swaps | High frequency | Operational disruption |
| Customer Complaints (Battery) | Significant | Customer dissatisfaction |
| Staff Time (Unplanned Charging) | Excessive | Inefficient resource utilization |

### Desired Future State

- **Predictive Charging**: Forecast battery needs 24-48 hours in advance
- **Proactive Swaps**: Batteries replaced before reaching critical levels
- **Prioritized Maintenance**: High-demand vehicles charged first
- **Integrated Operations**: Charging coordinated with redistribution routes
- **Minimal Downtime**: 95%+ fleet availability with adequate charge

---

## Problem 3: Customer Engagement & Retention

### The Challenge

**"Most vehicle usage is ad-hoc; MobilityCorp wants to increase usage by regular commuters."**

### Current State Pain Points

#### Business Impact

- **Low Retention**: Users book once or twice, then don't return
- **Ad-Hoc Usage**: Unpredictable demand, difficult to plan operations
- **Limited Subscriptions**: Few customers convert to regular memberships
- **Price Sensitivity**: Users shop around for cheapest option per trip
- **No Loyalty**: Easy to switch to competitors

#### Customer Impact

- **Generic Experience**: No personalization or tailored recommendations
- **Missed Opportunities**: Users unaware of better vehicle options
- **No Incentives**: Regular users not rewarded for loyalty
- **Lack of Engagement**: No proactive communication or reminders

### Root Causes

1. **No Personalization**
   - All customers treated identically
   - Individual preferences and patterns ignored
   - No tailored recommendations or offers

2. **Lack of Insights**
   - Cannot identify potential regular commuters
   - No visibility into customer journey patterns
   - Churn risk not identified

3. **Generic Marketing**
   - One-size-fits-all promotions
   - No targeted campaigns for user segments
   - Ineffective engagement strategies

4. **Missed Retention Opportunities**
   - No proactive outreach to lapsed users
   - Potential commute patterns not recognized

### Quantified Impact

| Metric | Current State | Business Impact |
|--------|---------------|-----------------|
| Repeat Usage Rate | Low (~20-30%) | Revenue unpredictability |
| Subscription Conversion | <10% | Lost recurring revenue |
| Customer Lifetime Value | Below target | Inefficient CAC payback |
| Engagement Rate | Minimal | Weak customer relationships |

### Desired Future State

- **Personalized Recommendations**: Suggest vehicles based on history and preferences
- **Commuter Identification**: Recognize and target potential regular users
- **Targeted Incentives**: Tailored offers to convert ad-hoc users to subscribers
- **Proactive Engagement**: Reminders and suggestions at optimal times
- **Loyalty Programs**: Reward and retain high-value customers
- **Increased Retention**: 50%+ repeat usage, 25%+ subscription conversion

---

## Interconnected Nature of Problems

These three problems are deeply interconnected:

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  Poor Vehicle          Low Battery         Weak Customer    │
│  Availability    ←───→ Management    ←───→ Engagement      │
│                                                             │
│  • No vehicles nearby  • Vehicles offline  • Generic exp    │
│  • Booking failures    • Emergency swaps   • No loyalty     │
│  • High costs          • Staff disruption  • Low retention  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
                            ↓
              Requires Integrated AI Solution
                            ↓
         ┌──────────────────────────────────────┐
         │ Predictive Analytics Platform        │
         │  • Demand forecasting                │
         │  • Battery health prediction         │
         │  • Customer behavior analysis        │
         │  • Integrated optimization           │
         └──────────────────────────────────────┘
```

### Integration Points

1. **Availability ↔ Battery**
   - Cannot redistribute vehicles with low batteries
   - Battery swaps should consider demand locations
   - Charging stations must be optimally placed

2. **Availability ↔ Engagement**
   - Poor availability drives customer churn
   - Customer preferences inform vehicle placement
   - Personalized recommendations require available vehicles

3. **Battery ↔ Engagement**
   - Battery issues damage customer trust
   - Usage patterns inform charging predictions
   - Reliable vehicles improve retention

---

## Business Priorities

Based on competitive pressure and revenue impact:

### Priority 1: Vehicle Availability (Critical)

**Rationale**: Directly impacts revenue and competitive position. Customers will switch to competitors if vehicles consistently unavailable.

### Priority 2: Battery Management (High)

**Rationale**: Enables improved availability and customer satisfaction. Vehicles offline due to batteries cannot generate revenue.

### Priority 3: Customer Engagement (High)

**Rationale**: Long-term sustainability and growth. Converting ad-hoc users to subscribers creates predictable recurring revenue.

---

## Success Criteria

Solutions must deliver measurable improvements:

| Problem Area | Key Metric | Current | Target | Timeline |
|--------------|-----------|---------|--------|----------|
| **Availability** | Booking success rate | 70-75% | >95% | 6 months |
| **Availability** | Redistribution cost | Baseline | -30% | 12 months |
| **Battery** | Fleet downtime (battery) | 15-20% | <5% | 6 months |
| **Battery** | Emergency swaps | High | -60% | 12 months |
| **Engagement** | Repeat usage rate | 20-30% | >50% | 12 months |
| **Engagement** | Subscription conversion | <10% | >25% | 18 months |

---

## Constraints & Considerations

### Technical Constraints

- Must work with existing GPS/telematics infrastructure
- Mobile apps on iOS and Android
- Multi-language and multi-currency support required
- GDPR and data privacy compliance mandatory

### Operational Constraints

- Staff training and adoption required
- Cannot disrupt current operations during rollout
- Must integrate with existing CRM and payment systems
- Gradual rollout across locations

### Business Constraints

- Budget limitations for AI/ML infrastructure
- Vendor lock-in risks with AI service providers
- Uncertainty in AI technology evolution
- Validation and monitoring of non-deterministic systems

---

## Document Control

- **Version**: 1.0
- **Last Updated**: 2025-10-20
- **Owner**: BRINUS Team (O'Reilly Q4 2025 Kata)
- **Status**: Draft
- **References**: [01_business_context.md](01_business_context.md)
