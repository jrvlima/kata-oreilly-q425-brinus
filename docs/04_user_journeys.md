# User Journeys & Flows

## Overview

This document maps user journeys for key personas interacting with the MobilityCorp platform. Each journey identifies touchpoints, pain points, and opportunities for AI-driven improvements.

---

## Customer Journeys

### Journey 1: Commuter Vehicle Booking (Commuter Chris)

**Scenario**: Chris needs to book an eBike for his morning commute after receiving a public transit delay notification.

#### Current State Journey

```plain
┌─────────────────────────────────────────────────────────────────────┐
│ Phase 1: Awareness & Discovery                                      │
└─────────────────────────────────────────────────────────────────────┘
1. Receives transit delay notification (7:45 AM)
2. Decides to use alternative transportation
3. Opens MobilityCorp app

   😐 Pain Point: Must check multiple apps (Lime, Tier, Bolt) to compare
   💡 AI Opportunity: Proactive notification predicting delays + vehicle availability

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 2: Search & Selection                                         │
└─────────────────────────────────────────────────────────────────────┘
4. Searches for vehicles near home location
5. Filters for eBikes only
6. Finds 0-1 vehicles available (often none)

   😞 Pain Point: Poor availability during rush hour
   💡 AI Opportunity: Predictive placement based on commute patterns

7. If found: Checks battery level (sometimes too low)
8. If not found: Switches to competitor app or gives up

   😞 Pain Point: Wasted time checking multiple apps
   💡 AI Opportunity: Personalized recommendations + guaranteed availability

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 3: Booking & Unlock                                           │
└─────────────────────────────────────────────────────────────────────┘
9. Taps "Reserve" (30-min hold)
10. Walks to vehicle location (5 minutes)
11. Scans QR code or uses NFC to unlock

   😐 Pain Point: Reservation expires if delayed
   💡 AI Opportunity: Dynamic reservation windows based on distance

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 4: Usage & Travel                                             │
└─────────────────────────────────────────────────────────────────────┘
12. Rides eBike to office (20 minutes)
13. Monitors battery level during trip

   😟 Pain Point: Anxiety about battery running out mid-trip
   💡 AI Opportunity: Route optimization + battery range predictions

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 5: Return & Payment                                           │
└─────────────────────────────────────────────────────────────────────┘
14. Arrives at office parking spot
15. Takes photo of parked vehicle
16. Locks vehicle via app
17. Reviews trip cost and payment confirmation

   😐 Pain Point: Uncertain daily costs (per-minute pricing)
   💡 AI Opportunity: Subscription/commuter plan recommendation

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 6: Post-Trip                                                  │
└─────────────────────────────────────────────────────────────────────┘
18. Rates experience (optional)
19. No further engagement until next need

   😞 Pain Point: No proactive engagement or loyalty building
   💡 AI Opportunity: Personalized offers, commute pattern recognition
```

#### Future State Journey (AI-Enhanced)

```plain
1. [PROACTIVE] Receives notification: "Transit delayed. eBike reserved for you near home. Battery 95%."
2. Opens app, sees personalized recommendation
3. Confirms booking (already optimally placed vehicle)
4. Unlocks with NFC (seamless)
5. Rides with confidence (route + battery visibility)
6. Returns, automatic payment via subscription plan
7. [PROACTIVE] Evening reminder: "Book return trip for 6pm?"
```

**Journey Improvements**:

- ⏱️ Time saved: ~5-10 minutes (no searching)
- 😊 Reduced friction: Proactive + personalized
- 💰 Cost optimization: Subscription recommendation
- 🔄 Increased retention: Regular engagement

---

### Journey 2: Tourist Multi-Day Booking (Tourist Tina)

**Scenario**: Tina arrives in Amsterdam and wants to explore using MobilityCorp vehicles during her 4-day stay.

#### Current State Journey

```plain
┌─────────────────────────────────────────────────────────────────────┐
│ Phase 1: Onboarding                                                 │
└─────────────────────────────────────────────────────────────────────┘
1. Sees MobilityCorp vehicles near hotel
2. Searches "bike rental Amsterdam" on phone
3. Downloads app from app store
4. Creates account (email, password, payment method)
5. Verifies identity and payment

   😐 Pain Point: Lengthy onboarding when eager to explore
   💡 AI Opportunity: Quick start guide, multi-language support

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 2: First Booking                                              │
└─────────────────────────────────────────────────────────────────────┘
6. Searches for vehicle near hotel
7. Confused by vehicle types and pricing
8. Selects eBike (seems easiest)
9. Books and unlocks vehicle

   😐 Pain Point: Unclear which vehicle type best for tourists
   💡 AI Opportunity: "Tourist mode" with guided recommendations

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 3: Usage & Navigation                                         │
└─────────────────────────────────────────────────────────────────────┘
10. Opens Google Maps for navigation to attraction
11. Parks at attraction, locks vehicle
12. Returns later, searches for another vehicle

   😐 Pain Point: Switching between apps for navigation
   💡 AI Opportunity: In-app navigation with attraction suggestions

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 4: Multi-Day Usage                                            │
└─────────────────────────────────────────────────────────────────────┘
13. Books vehicles 5-6 times over 4 days
14. Each time: search, book, pay individually
15. Frustrated by per-minute costs adding up

   😞 Pain Point: No multi-day package or tourist discount
   💡 AI Opportunity: Automatic tourist package recommendation

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 5: Post-Visit                                                 │
└─────────────────────────────────────────────────────────────────────┘
16. Leaves Amsterdam, never uses app again
17. No follow-up or re-engagement

   😞 Pain Point: No retention for future city visits
   💡 AI Opportunity: Multi-city offers, referral incentives
```

#### Future State Journey (AI-Enhanced)

```plain
1. Quick onboarding with "Tourist Mode" (English, simplified)
2. [RECOMMENDATION] "4-day Amsterdam Pass: Unlimited rides €45"
3. Purchases pass, immediate access to all vehicles
4. [PERSONALIZED] In-app attraction suggestions with vehicle availability
5. [NAVIGATION] Turn-by-turn directions within app
6. [ENGAGEMENT] Daily tip: "Popular route to Vondelpark"
7. [POST-VISIT] Email: "Visiting Barcelona? Use code AMSTERDAM20"
```

**Journey Improvements**:

- 🎯 Simplified experience: Tourist-focused mode
- 💰 Better value: Package pricing
- 🗺️ Integrated navigation: No app switching
- 🔄 Retention: Multi-city engagement

---

### Journey 3: Event-Based Booking (Event-Goer Emma)

**Scenario**: Emma is attending a concert at 8pm and needs transportation home afterward (public transit stops at 11pm).

#### Current State Journey

```plain
┌─────────────────────────────────────────────────────────────────────┐
│ Phase 1: Planning (Day Before Event)                                │
└─────────────────────────────────────────────────────────────────────┘
1. Buys concert ticket (8pm start)
2. Checks event end time (~11pm, after last metro)
3. Plans to use MobilityCorp for return trip

   😐 Pain Point: Cannot pre-book (only 30-min advance)
   💡 AI Opportunity: Event-based advance booking

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 2: During Event                                               │
└─────────────────────────────────────────────────────────────────────┘
4. Enjoys concert
5. 10:45pm - concert ending, checks app
6. Sees 100+ other attendees all trying to book
7. No vehicles available near venue (high demand)

   😞 Pain Point: Surge demand, zero availability
   💡 AI Opportunity: Predictive pre-positioning for events

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 3: Scrambling for Alternative                                 │
└─────────────────────────────────────────────────────────────────────┘
8. Walks 10 minutes away from venue, still no vehicles
9. Checks competitor apps (same problem)
10. Resorts to expensive taxi

   😡 Pain Point: Poor experience, considers not using again
   💡 AI Opportunity: Event detection + proactive notifications

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 4: Post-Event                                                 │
└─────────────────────────────────────────────────────────────────────┘
11. Frustrated, leaves negative review
12. Unlikely to rely on MobilityCorp for next event

   😞 Pain Point: Lost trust and future revenue
   💡 AI Opportunity: Recovery offers, guaranteed availability programs
```

#### Future State Journey (AI-Enhanced)

```plain
1. [PROACTIVE] Buys concert ticket, MobilityCorp detects event via calendar integration
2. [NOTIFICATION] "Concert at Palau ends 11pm. Reserve scooter now?"
3. Pre-books guaranteed scooter for 11pm (event-based booking)
4. [POSITIONING] AI redistributes vehicles to venue before event ends
5. Leaves concert, finds multiple vehicles available
6. Smooth return trip, arrives home safely
7. [ENGAGEMENT] "Next concert at Razzmatazz? Pre-book now."
```

**Journey Improvements**:

- 🎫 Event integration: Automatic detection
- ✅ Guaranteed availability: Pre-booking option
- 🚀 Better positioning: AI predicts surge demand
- 😊 Improved satisfaction: Reliable late-night option

---

### Journey 4: Family Weekend Trip (Family Fred)

**Scenario**: Fred needs an electric car for a weekend family trip to visit relatives 50km outside Paris.

#### Current State Journey

```plain
┌─────────────────────────────────────────────────────────────────────┐
│ Phase 1: Planning (5 Days Before)                                   │
└─────────────────────────────────────────────────────────────────────┘
1. Family decides to visit relatives this Saturday
2. Opens MobilityCorp app
3. Searches for electric cars for Saturday 9am-5pm
4. Finds 2 available cars at nearby parking spot

   😐 Pain Point: Limited selection, must book early
   💡 AI Opportunity: Demand prediction for family bookings

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 2: Booking                                                    │
└─────────────────────────────────────────────────────────────────────┘
5. Selects 4-seat electric car
6. Books fixed 8-hour duration
7. Reviews pricing (~€80 for 8 hours)
8. Confirms booking

   😐 Pain Point: Expensive compared to traditional rental
   💡 AI Opportunity: Family plan or weekend package pricing

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 3: Pickup (Saturday Morning)                                  │
└─────────────────────────────────────────────────────────────────────┘
9. Walks to parking spot with family
10. Unlocks car via NFC
11. Inspects car (cleanliness, damage)
12. Checks battery level (85%)

   😐 Pain Point: Concerned about range for 100km round trip
   💡 AI Opportunity: Range assurance + charging station suggestions

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 4: Usage                                                      │
└─────────────────────────────────────────────────────────────────────┘
13. Drives to relatives' home (50km, 45 minutes)
14. Battery drops to 60%
15. Visits family for 4 hours
16. Returns to Paris (50km)
17. Battery at 35%, sufficient but stressful

   😟 Pain Point: Range anxiety throughout trip
   💡 AI Opportunity: Real-time range predictions + alerts

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 5: Return                                                     │
└─────────────────────────────────────────────────────────────────────┘
18. Returns car to designated parking spot
19. Takes photo verification
20. Plugs in car to charging station (as required)
21. Locks vehicle, confirms return in app

   😐 Pain Point: Must remember to plug in or risk penalty
   💡 AI Opportunity: Automated reminders, gamification

22. Receives receipt and payment confirmation
23. No follow-up or engagement

   😐 Pain Point: No incentive to book again
   💡 AI Opportunity: Family loyalty program, next trip suggestions
```

#### Future State Journey (AI-Enhanced)

```plain
1. [RECOMMENDATION] "Weekend family trip? Electric car available Sat 9am-5pm."
2. Books car, sees route planning tool
3. [RANGE ASSURANCE] "Battery sufficient for 120km. Charging stations mapped."
4. Pickup: Pre-inspected, charged to 95%
5. During trip: Real-time range updates, no anxiety
6. [REMINDER] "Return in 30 min. Remember to plug in charging cable."
7. Returns, automatic return verification
8. [ENGAGEMENT] "Great trip! Family plan: Save 20% on future bookings."
```

**Journey Improvements**:

- 🔋 Range confidence: AI predictions + charging integration
- 📍 Route planning: Built-in navigation and charging stops
- 💰 Cost efficiency: Family plan recommendations
- 🔄 Retention: Targeted family-friendly offers

---

## Operations Journeys

### Journey 5: Fleet Redistribution (Dispatcher Dan)

**Scenario**: Dan manages morning redistribution to prepare for evening commute demand.

#### Current State Journey

```plain
┌─────────────────────────────────────────────────────────────────────┐
│ Phase 1: Morning Assessment (8:00 AM)                               │
└─────────────────────────────────────────────────────────────────────┘
1. Logs into operations dashboard
2. Reviews overnight vehicle distribution
3. Sees many vehicles clustered downtown (from last night)
4. Knows residential areas will need vehicles for evening commute

   😐 Pain Point: Relies on intuition and experience
   💡 AI Opportunity: Predictive demand heat maps

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 2: Planning Redistribution                                    │
└─────────────────────────────────────────────────────────────────────┘
5. Manually identifies which vehicles to move
6. Checks battery levels (many low after last night)
7. Tries to balance redistribution + charging needs
8. Creates task list for field technicians

   😞 Pain Point: Time-consuming, suboptimal decisions
   💡 AI Opportunity: Automated redistribution recommendations

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 3: Task Assignment                                            │
└─────────────────────────────────────────────────────────────────────┘
9. Assigns 3 field techs to different areas
10. Sends task lists via messaging app
11. No route optimization

   😐 Pain Point: Inefficient routes, wasted time
   💡 AI Opportunity: Optimized multi-stop routing

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 4: Monitoring & Adjustments                                   │
└─────────────────────────────────────────────────────────────────────┘
12. Throughout day: monitors real-time demand
13. Receives alerts about vehicles with critical battery
14. Must reactively reassign techs to emergency swaps

   😞 Pain Point: Constant firefighting, disrupted plans
   💡 AI Opportunity: Proactive battery predictions

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 5: End of Day Review                                          │
└─────────────────────────────────────────────────────────────────────┘
15. Reviews metrics: availability, utilization, customer complaints
16. Sees several "no vehicles available" incidents
17. Wonders if different decisions would have helped

   😞 Pain Point: No feedback loop for learning
   💡 AI Opportunity: Performance analytics + learning system
```

#### Future State Journey (AI-Enhanced)

```plain
1. [DASHBOARD] Opens dashboard with AI-generated insights
2. [RECOMMENDATIONS] "Move 15 vehicles from Downtown to Residential. Predicted demand: 85%"
3. Reviews confidence scores, approves plan
4. [AUTO-ASSIGN] System assigns techs with optimized routes
5. [PROACTIVE] "Battery alerts: 8 vehicles need swaps by 4pm"
6. Monitors real-time execution, system self-adjusts
7. [ANALYTICS] End-of-day report: "95% availability achieved, 30% cost reduction"
```

**Journey Improvements**:

- ⏱️ Time savings: 2-3 hours daily decision-making → 30 min review
- 📈 Better outcomes: Data-driven decisions beat intuition
- 🔄 Proactive: Prevent problems vs. react to them
- 📊 Learning: AI improves from feedback

---

### Journey 6: Battery Swap (Field Technician Felipe)

**Scenario**: Felipe performs his assigned battery swaps and redistribution tasks.

#### Current State Journey

```plain
┌─────────────────────────────────────────────────────────────────────┐
│ Phase 1: Morning Briefing (8:30 AM)                                 │
└─────────────────────────────────────────────────────────────────────┘
1. Receives task list from dispatcher (10 vehicles to move, 5 batteries to swap)
2. Loads spare batteries into van
3. Reviews locations on map (all over city)

   😐 Pain Point: Route not optimized, unclear priority
   💡 AI Opportunity: Prioritized tasks with turn-by-turn routing

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 2: First Tasks (9:00 AM)                                      │
└─────────────────────────────────────────────────────────────────────┘
4. Drives to first vehicle location (15 minutes)
5. Arrives: vehicle already rented by customer (task obsolete)
6. Contacts dispatcher for update

   😞 Pain Point: Wasted trip, no real-time status
   💡 AI Opportunity: Real-time task updates, skip if vehicle moved

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 3: Battery Swaps (10:00 AM - 12:00 PM)                        │
└─────────────────────────────────────────────────────────────────────┘
7. Drives to 5 battery swap locations
8. Several vehicles already critically low (should have been swapped yesterday)
9. Performs swaps: unlock, remove battery, install fresh battery, lock
10. Documents each swap in mobile app (photo, signature)

   😞 Pain Point: Too late for some swaps, customers already affected
   💡 AI Opportunity: Earlier predictions, preventive swaps

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 4: Vehicle Redistribution (12:00 PM - 4:00 PM)                │
└─────────────────────────────────────────────────────────────────────┘
11. Drives to vehicles that need redistribution
12. Manually rides scooter/bike to destination, returns to van
13. Inefficient: lots of driving, back-and-forth

   😐 Pain Point: Slow process, physically tiring
   💡 AI Opportunity: Multi-vehicle coordination, team routing

┌─────────────────────────────────────────────────────────────────────┐
│ Phase 5: End of Shift (5:00 PM)                                     │
└─────────────────────────────────────────────────────────────────────┘
14. Completes 8 of 15 assigned tasks
15. Returns to depot, reports unfinished tasks
16. Feels frustrated by inefficiency

   😞 Pain Point: Low completion rate, demotivating
   💡 AI Opportunity: Realistic task loads, achievable daily goals
```

#### Future State Journey (AI-Enhanced)

```plain
1. [MOBILE APP] Opens app, sees prioritized, routed task list
2. [NAVIGATION] Turn-by-turn directions to first stop
3. [REAL-TIME] Task auto-removed when vehicle rented
4. [EFFICIENT] Optimized route saves 45 minutes driving
5. [PROACTIVE] Battery swaps before critical (customers unaffected)
6. [COMPLETION] Finishes all 15 tasks by 4:30 PM
7. [FEEDBACK] App shows: "Great day! 15/15 completed. +10 points."
```

**Journey Improvements**:

- ⏱️ Efficiency: 60% more tasks completed
- 😊 Satisfaction: Clear goals, achievable targets
- 🎯 Effectiveness: Right vehicles at right times
- 📈 Performance: Gamification and recognition

---

## Journey Insights Summary

### Cross-Cutting Themes

| Theme | Customer Impact | Operations Impact | AI Solution |
|-------|----------------|-------------------|-------------|
| **Availability** | Booking failures, frustration | Inefficient redistribution | Predictive demand forecasting |
| **Battery Issues** | Mid-trip anxiety, unusable vehicles | Emergency swaps, downtime | Predictive charging models |
| **Personalization** | Generic experience, no loyalty | Unpredictable demand | Customer behavior analysis |
| **Proactivity** | Reactive searching | Firefighting mode | Event detection, notifications |
| **Integration** | App-switching friction | Siloed operations | Unified platform with ML |

### Priority Journey Improvements

1. **Commuter Chris** - Highest retention potential, needs proactive availability
2. **Dispatcher Dan** - Operations efficiency multiplier effect
3. **Event-Goer Emma** - High churn risk, needs event-based solutions
4. **Field Tech Felipe** - Execution enabler, AI can 2x efficiency

---

## Document Control

- **Version**: 1.0
- **Last Updated**: 2025-10-20
- **Owner**: BRINUS Team (O'Reilly Q4 2025 Kata)
- **Status**: Draft
- **References**: [02_problem_statement.md](02_problem_statement.md), [03_personas.md](03_personas.md)
