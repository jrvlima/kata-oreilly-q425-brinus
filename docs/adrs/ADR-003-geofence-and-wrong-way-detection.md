# ADR-003: Geo-Fence Enforcement and Wrong-Way Detection AI

## Status
Accepted

## Context

MobilityCorp operates electric scooters, eBikes, cars, and vans in multiple EU cities. Each city has different regulations:
- **Restricted zones**: Areas where vehicles are prohibited (pedestrian zones, private property)
- **Speed-limited zones**: Areas requiring reduced speed (school zones, residential areas, bike paths)
- **One-way streets**: Directional restrictions for traffic flow
- **Parking zones**: Designated drop-off/pickup areas

### Safety & Compliance Challenges

**Current Problems**:
1. **Regulatory Violations**: Users unintentionally enter restricted zones → city fines for MobilityCorp
2. **Safety Risks**: Users going wrong way on one-way streets → accidents, liability
3. **User Confusion**: Visitors unfamiliar with city rules (tourists don't know local one-way streets)
4. **Manual Enforcement**: Staff must manually review violations → slow, expensive, reactive

**Impact**:
- **City Fines**: €50-500 per violation × 100+ violations/month = €5K-50K/month in fines
- **Safety Incidents**: 5-10 wrong-way collisions/year → insurance claims, bad press
- **User Experience**: Users frustrated by fines for unintentional violations
- **Regulatory Risk**: Cities threatening to revoke operating permits if violations continue

### Requirements from Session-1 Q&A

- **Q3**: Geo-restrictions exist - certain zones where bikes/scooters can't be left, speed-limited zones
- **Q15**: Different countries have different rules (EU multi-country compliance)
- **All vehicles have GPS** trackers for real-time location tracking

### Startup Constraints

- **Real-time requirement**: Must prevent violations BEFORE they happen (not after)
- **Safety-critical**: Cannot have latency - decisions must be <1 second
- **Multi-city complexity**: 10+ cities, each with unique zone definitions
- **No manual intervention**: Must be automated 24/7

## Decision

We will implement **Edge AI-based Geo-Fence Enforcement and Wrong-Way Detection** with progressive intervention:

### Two Core Features

#### 1. Geo-Fence Enforcement (Restricted & Speed-Limited Zones)

**Purpose**: Prevent vehicles from entering prohibited zones or exceeding speed limits in controlled areas

**How it Works**:
```
Vehicle approaches geo-fence boundary:
  ↓
Edge AI (on vehicle) detects proximity (50m warning zone)
  ↓
Speed-Limited Zone:
  - Gradual speed reduction (e.g., 25 km/h → 15 km/h over 10 seconds)
  - Audio warning: "Entering school zone, speed limit 15 km/h"
  - Visual indicator on app (if screen exists)

Restricted Zone:
  - Gradual speed reduction → full stop at boundary
  - Audio warning: "Restricted zone ahead, please turn around"
  - Prevent motor from engaging if user tries to cross boundary
  - If user persists (3+ attempts): Push notification + log violation
```

**Progressive Intervention**:
- **50m before boundary**: Audio warning + gradual slowdown
- **At boundary**: Motor disabled in restricted direction (can still turn around)
- **3+ attempts to cross**: Escalate to push notification + customer support call

#### 2. Wrong-Way Detection (One-Way Streets)

**Purpose**: Prevent users from going the wrong way on one-way streets

**How it Works**:
```
Vehicle moving on street:
  ↓
Edge AI detects:
  - Current GPS location
  - Direction of travel (heading)
  - Street orientation (from map data)
  ↓
If going wrong way on one-way street:

First Attempt:
  - Silent intervention: Motor gradually slows/stops
  - NO notification (user thinks "vehicle not working in this direction")
  - User can still reverse or turn around
  - Vehicle works normally in correct direction

Second Attempt (user tries again within 30 seconds):
  - Audio warning: "⚠️ This is a one-way street, you're going the wrong way"
  - Visual notification (if app open): "Wrong direction detected"
  - Motor disabled until user changes direction
  - Log violation for review

Third+ Attempt:
  - Push notification: "Safety alert: You're attempting to go wrong way"
  - Customer support call (automated): "Hi, we noticed you're going wrong way..."
  - Report to staff for manual review (may indicate stolen vehicle)
```

**Why Silent First Attempt?**
- Less confrontational (user doesn't feel "policed")
- May be accidental (user testing vehicle, not yet moving)
- Avoids false alarms (GPS drift, map errors)
- Better UX: "vehicle just doesn't go this way" vs "YOU'RE DOING SOMETHING WRONG"

## Technical Architecture

### Edge AI for Real-Time Decisions

**Why Edge (On-Vehicle) AI?**

| Requirement | Edge AI | Cloud AI | Verdict |
|-------------|---------|----------|---------|
| **Latency** | <100ms | 500-2000ms | ✅ Edge (safety-critical) |
| **Offline Operation** | Works without internet | Fails if no connection | ✅ Edge (network drops common) |
| **Privacy** | Location stays on device | Sends GPS to cloud | ✅ Edge (GDPR-friendly) |
| **Cost** | One-time hardware ($50) | API calls ($0.001/check × 1M/day) | ✅ Edge (cheaper at scale) |
| **Reliability** | No network dependency | Cloud outage = system down | ✅ Edge (safety-critical) |

**Verdict**: Edge AI is required for geo-fence enforcement (safety-critical, real-time)

### Hardware: GPS + IMU + Edge Processor

**Components on Each Vehicle**:
```
┌─────────────────────────────────────────────────┐
│           Vehicle Edge AI Unit                  │
│                                                 │
│  GPS Module (10Hz updates, <2m accuracy)       │
│    ↓                                            │
│  IMU (Inertial Measurement Unit)                │
│    - Accelerometer (detect movement direction)  │
│    - Gyroscope (detect turns)                   │
│    - Compass (heading/bearing)                  │
│    ↓                                            │
│  Edge Processor (Raspberry Pi 4 / Jetson Nano) │
│    - Map matching algorithm                     │
│    - Geo-fence proximity detection              │
│    - Direction analysis (wrong-way check)       │
│    - Speed governor logic                       │
│    ↓                                            │
│  Motor Controller Interface                     │
│    - PWM signals to limit speed                 │
│    - Emergency stop capability                  │
│    ↓                                            │
│  Audio Output (speaker)                         │
│    - Pre-recorded warnings (multi-language)     │
│    ↓                                            │
│  4G/5G Module (for updates, not real-time)     │
│    - Sync map data (nightly)                    │
│    - Upload violation logs (batch)              │
└─────────────────────────────────────────────────┘
```

**Cost per Vehicle**: ~$80-100 (GPS $15, IMU $10, Raspberry Pi $35, 4G module $20, assembly $10)

### Map Data & Zone Definitions

**Data Sources**:

1. **CDS CURBS API** (from ADR-001):
   - Official city curb regulations
   - Restricted zones (pedestrian areas, private property)
   - Speed-limited zones (school zones, residential)
   - Updated weekly by city authorities

2. **OpenStreetMap (OSM)**:
   - One-way street data (`oneway=yes`, `oneway=-1`)
   - Street geometry (polylines with direction)
   - Updated daily (volunteers + city data imports)

3. **MobilityCorp Custom Zones**:
   - High-incident areas (learned from violations)
   - Temporary zones (events, construction)
   - Company policy zones (no scooters on highways)

**Map Storage on Device**:
```
Format: Vector tiles (MBTiles)
Size: ~50MB per city (compressed)
Update: Nightly sync via WiFi (when vehicle docked)
Fallback: 7-day cached data if sync fails

Data Structure (per zone):
{
  "zone_id": "berlin_pedestrian_01",
  "type": "restricted",
  "geometry": [polygon coordinates],
  "severity": "hard" | "soft",
  "audio_warning_de": "Fußgängerzone, bitte umdrehen",
  "audio_warning_en": "Pedestrian zone, please turn around"
}

One-Way Streets:
{
  "street_id": "osm_12345",
  "name": "Friedrichstraße",
  "direction": "north" (0-360 degrees),
  "tolerance": 45 (degrees ± allowed deviation)
}
```

### AI Algorithm: Map Matching + Direction Analysis

**Step 1: Map Matching** (Determine which street vehicle is on)

```python
def map_match(gps_lat, gps_lon, gps_heading, gps_speed):
    """
    Match GPS position to closest street segment
    Input: Raw GPS (±5m error)
    Output: Snapped position on street + street metadata
    """
    # Find nearby street segments (within 20m)
    candidates = query_nearby_streets(gps_lat, gps_lon, radius=20)

    # Score each candidate by:
    # - Distance to street (closer = higher score)
    # - Heading alignment (parallel to street = higher score)
    # - Previous street (continuity = higher score)

    best_match = max(candidates, key=lambda s:
        score_distance(s) * 0.4 +
        score_heading(s, gps_heading) * 0.4 +
        score_continuity(s, previous_street) * 0.2
    )

    return best_match  # Street segment with metadata
```

**Step 2: Geo-Fence Proximity Check**

```python
def check_geofence(current_position, current_speed, current_heading):
    """
    Check if vehicle approaching restricted/speed-limited zone
    Returns: (zone_type, distance_to_boundary, action)
    """
    # Query zones within 100m
    nearby_zones = query_zones(current_position, radius=100)

    for zone in nearby_zones:
        distance = calculate_distance_to_boundary(current_position, zone.boundary)

        if zone.type == "restricted":
            if distance < 50:  # 50m warning zone
                return ("restricted", distance, "gradual_stop")
            elif distance < 5:  # At boundary
                return ("restricted", distance, "hard_stop")

        elif zone.type == "speed_limited":
            if distance < 50:
                target_speed = zone.speed_limit
                return ("speed_limited", distance, f"reduce_to_{target_speed}")

    return (None, None, "normal")
```

**Step 3: Wrong-Way Detection**

```python
def detect_wrong_way(street, gps_heading, gps_speed):
    """
    Check if vehicle going wrong way on one-way street
    Input: Matched street + current heading
    Output: (is_wrong_way, confidence)
    """
    if not street.is_oneway:
        return (False, 0.0)  # Two-way street, no wrong-way possible

    # Street direction: 0° = North, 90° = East, 180° = South, 270° = West
    street_direction = street.oneway_direction

    # Calculate angle difference
    angle_diff = abs(normalize_angle(gps_heading - street_direction))

    # If traveling opposite direction (150-210° difference)
    if 150 <= angle_diff <= 210:
        confidence = 1.0 - (abs(angle_diff - 180) / 30)  # Peak confidence at 180°
        return (True, confidence)

    return (False, 0.0)

# Persistence check (avoid false alarms)
def persistent_wrong_way_check():
    """
    Only trigger if wrong-way detected for 3+ consecutive readings (3 seconds)
    """
    wrong_way_readings = []

    while True:
        result = detect_wrong_way(...)
        wrong_way_readings.append(result)

        # Keep last 3 readings
        if len(wrong_way_readings) > 3:
            wrong_way_readings.pop(0)

        # Trigger only if 3/3 readings confirm wrong-way
        if len(wrong_way_readings) == 3 and all(r[0] for r in wrong_way_readings):
            avg_confidence = sum(r[1] for r in wrong_way_readings) / 3
            if avg_confidence > 0.7:
                return True

        time.sleep(1)  # Check every second
```

### Motor Control: Progressive Speed Reduction

**Gradual Slowdown Algorithm** (smoother than hard braking):

```python
def apply_speed_limit(current_speed_kmh, target_speed_kmh, distance_to_zone_m):
    """
    Gradually reduce speed as approaching zone
    Input: Current speed, target speed, distance to boundary
    Output: PWM signal to motor controller (0-100%)
    """
    if distance_to_zone_m > 50:
        return 100  # Full power, not yet in warning zone

    if distance_to_zone_m <= 0:
        # Inside restricted zone
        if target_speed_kmh == 0:
            return 0  # Hard stop
        else:
            # Enforce speed limit (e.g., 15 km/h in school zone)
            return min(100, (target_speed_kmh / current_speed_kmh) * 100)

    # Warning zone (50m → 0m): Linear deceleration
    # Example: 50m away = 80% power, 25m away = 40% power, 0m = target
    decel_factor = distance_to_zone_m / 50.0
    target_power = (target_speed_kmh / current_speed_kmh) * 100

    return int(target_power + (100 - target_power) * decel_factor)
```

**User Experience**:
- At 50m: Speed gradually reduces from 25 km/h → 15 km/h over 10 seconds (comfortable deceleration)
- At 25m: Speed at ~20 km/h (midpoint)
- At 0m: Speed at target (15 km/h for speed zone, 0 km/h for restricted zone)
- Audio warning plays once at 50m mark
- If user tries to accelerate: Motor limits to calculated power (can't override)

### Wrong-Way Intervention: Silent First, Warn Second

**State Machine**:

```
State: NORMAL
  ↓ (detect wrong-way for 3+ seconds)
State: WRONG_WAY_ATTEMPT_1
  - Action: Gradual motor slowdown to 0
  - Audio: NONE (silent intervention)
  - Visual: NONE
  - Log: timestamp, location, direction
  - Timeout: 30 seconds (if user stops trying, reset to NORMAL)
  ↓ (user tries again within 30 seconds)
State: WRONG_WAY_ATTEMPT_2
  - Action: Motor disabled in wrong direction
  - Audio: "⚠️ One-way street, wrong direction"
  - Visual: App notification (if connected)
  - Log: escalated violation
  - Timeout: 60 seconds
  ↓ (user tries again)
State: WRONG_WAY_ATTEMPT_3+
  - Action: Motor disabled, vehicle immobilized
  - Audio: "Safety alert, please contact support"
  - Push Notification: "Wrong-way detected, please turn around"
  - Escalate: Flag for staff review (possible theft/malicious use)
```

## Multi-Language Support

**Audio Warnings**:
- Pre-recorded in 10 EU languages (German, French, Spanish, Italian, Dutch, Polish, etc.)
- Language auto-selected based on user account preference
- Fallback to English if language unavailable
- Each warning <3 seconds (short, clear message)

**Examples**:
- German: "Einbahnstraße, falsche Richtung" (One-way street, wrong direction)
- French: "Sens unique, mauvaise direction"
- Spanish: "Calle de un solo sentido, dirección incorrecta"

## Privacy & GDPR Compliance

**What We Track**:
- ✅ Current location (for geo-fence/wrong-way detection) - necessary for service
- ✅ Violation logs (timestamp + location + type) - legal compliance
- ❌ Full journey history (NOT tracked) - privacy-first

**Data Minimization**:
```
Stored on Device (Real-time):
- Last 10 GPS readings (rolling buffer, 10 seconds of history)
- Current street segment
- Active geo-fence zones nearby

Uploaded to Cloud (Batch, Nightly):
- Violation events only:
  {
    "vehicle_id": "ES-GER-00457",
    "event_type": "wrong_way_attempt_2",
    "timestamp": "2025-10-21T14:32:15Z",
    "location": {"lat": 52.5200, "lon": 13.4050},
    "street_name": "Friedrichstraße",
    "user_id": "U-78945"  (hashed, not reversible)
  }

NOT Stored:
- Full GPS traces
- Where user went before/after violation
- Patterns of user movement
```

**GDPR Article 6 Basis**: Legitimate interest (safety, regulatory compliance)

**User Rights**:
- View violation history (via app)
- Request deletion of logs (after 90 days)
- Opt-out: NOT POSSIBLE (safety feature, required for service)

## Cloud Integration (Non-Real-Time)

**Cloud Backend Role** (NOT on critical path):

1. **Map Updates** (nightly):
   - Download updated CDS curb regulations from cities
   - Download OSM one-way street data
   - Push updates to vehicles (when docked, WiFi connection)

2. **Violation Analytics** (batch):
   - Aggregate violations by zone (identify problem areas)
   - Detect systematic issues (entire street misclassified as one-way)
   - Train ML models to predict high-violation zones

3. **Staff Dashboard**:
   - Show violation heatmap (where users struggle most)
   - Flag vehicles with repeated violations (potential theft)
   - Customer support: Waive fines for tourists (first-time violations)

**Example Dashboard View**:
```
Berlin - October 2025 Violations:
- Wrong-way attempts: 450 (↓15% from Sept)
- Top violation street: "Unter den Linden" (120 attempts)
  → Action: Add more signage, notify city
- Restricted zone entries: 85 (↑5% from Sept)
  → Action: Increase warning distance from 50m to 75m
```

## Validation & Testing

**How to Ensure It Works**:

### 1. Pre-Deployment Testing

**Synthetic Test Routes**:
```
Test 1: Approach Restricted Zone
- Drive vehicle toward known restricted zone (e.g., Berlin Brandenburger Tor)
- Expected: Audio warning at 50m, gradual stop at boundary
- Pass criteria: Vehicle stops within 2m of boundary, warning plays

Test 2: Wrong-Way on One-Way Street
- Attempt to go wrong way on "Friedrichstraße" (known one-way)
- Expected:
  - Attempt 1: Silent slowdown
  - Attempt 2: Audio warning + motor disabled
- Pass criteria: Motor disabled within 3 seconds of wrong-way detection

Test 3: Speed-Limited Zone
- Enter school zone (15 km/h limit) at 25 km/h
- Expected: Gradual reduction to 15 km/h over 10 seconds
- Pass criteria: Speed at 15±2 km/h within 15 seconds
```

**Edge Cases**:
- GPS signal loss (tunnels, underpasses): Use last known position + IMU dead reckoning
- Map errors (OSM incorrectly marks two-way as one-way): Manual override via support
- Tourists (unfamiliar with zones): First violation = warning only, no fine

### 2. Production Monitoring

**Real-Time Metrics** (Prometheus + Grafana):

```
Metric 1: Geo-Fence Interventions per Day
- Target: <100 per 1000 rentals
- Alert: If >200, investigate (new restricted zone not in maps?)

Metric 2: Wrong-Way Detections per Day
- Attempt 1 (silent): 50-100 expected
- Attempt 2 (warning): <20 expected
- Attempt 3+ (escalated): <5 expected
- Alert: If Attempt 3+ spikes (map error or theft ring?)

Metric 3: False Positive Rate
- User disputes: "I wasn't going wrong way"
- Target: <5% of violations
- Investigation: Review GPS trace + street geometry

Metric 4: Motor Control Response Time
- Time from detection to motor slowdown
- Target: <500ms
- Alert: If >1s (hardware issue, CPU overload?)
```

### 3. User Feedback Loop

**Dispute Resolution**:
```
User clicks "Report Error" in app:
  ↓
Staff reviews:
  - GPS trace (was vehicle really on one-way street?)
  - OSM data (is street actually one-way?)
  - Violation log (confidence score, number of attempts)
  ↓
If legitimate error:
  - Waive fine
  - Flag street for map correction
  - Add to training data for improving detection
  ↓
If legitimate violation:
  - Fine upheld
  - Send educational notification (map showing one-way direction)
```

## Cost Analysis

### Hardware Costs (One-Time)

**Per Vehicle**:
- GPS module (10Hz, <2m accuracy): $15
- IMU (accelerometer + gyroscope + compass): $10
- Edge processor (Raspberry Pi 4 or equivalent): $35
- 4G module (for map updates): $20
- Speaker (audio warnings): $5
- Installation + wiring: $15
- **Total per vehicle: $100**

**Fleet-Wide** (10,000 vehicles):
- 10,000 × $100 = **$1M one-time investment**

### Operating Costs (Monthly)

**Map Data Updates**:
- OSM updates: Free (open data)
- CDS curb data: Free (city APIs)
- Bandwidth: 50MB/vehicle/month × $0.10/GB = $0.50/vehicle/month
- **Total: $5K/month** for 10K vehicles

**Violation Review**:
- 100 disputes/month × 15 mins each = 25 staff-hours/month
- 25 hours × €25/hour = **€625/month**

**Total Operating Cost**: ~$5.6K/month (€5.2K)

### Savings vs Current State

**Avoided City Fines**:
- Current: 100 violations/month × €200 avg fine = **€20K/month**
- With AI: 10 violations/month × €200 = €2K/month
- **Savings: €18K/month** (€216K/year)

**Avoided Insurance Claims** (wrong-way accidents):
- Current: 5 accidents/year × €10K avg claim = €50K/year
- With AI: 1 accident/year = €10K/year
- **Savings: €40K/year**

**Net Annual Savings**:
- Avoided fines: €216K
- Avoided accidents: €40K
- Operating costs: -€62K (€5.2K × 12)
- **Total: €194K/year savings**

**ROI**: $1M investment / €194K savings = **5-year payback** (excluding intangible benefits like better city relations)

## Consequences

### Positive

**Safety**:
- 80% reduction in wrong-way incidents (near-elimination)
- Prevent accidents before they happen (proactive, not reactive)
- Protect users from unintentional violations (better UX)

**Regulatory Compliance**:
- Meet city geo-fence requirements (keeps operating permits)
- Demonstrate responsible operation (city partnership)
- Reduce fines by 90% (€216K/year savings)

**User Experience**:
- Silent first intervention (less confrontational)
- Multi-language audio warnings (inclusive)
- Educational (users learn city rules)
- No surprise fines (prevented at source)

**Operational Efficiency**:
- Automated 24/7 (no manual monitoring needed)
- Real-time prevention (not after-the-fact enforcement)
- Data-driven improvements (violation heatmaps identify problem zones)

### Negative

**User Frustration**:
- "Why did my vehicle stop?" (silent intervention confusing)
- False positives (GPS errors, map inaccuracies)
- Tourists unfamiliar with zones (learning curve)
- Power users feel "policed" (loss of freedom)

**Technical Challenges**:
- GPS accuracy: ±5m error can cause false alarms
- Map data quality: OSM errors lead to incorrect enforcement
- Tunnels/underpasses: GPS signal loss requires fallback (IMU)
- Hardware failures: GPS module breaks → vehicle unsafe to operate

**Privacy Concerns**:
- Tracking user location (even if not stored long-term)
- Potential for misuse (surveillance, tracking patterns)
- GDPR compliance burden (data minimization, user rights)

**Cost**:
- $1M upfront investment (significant for startup)
- Hardware maintenance (GPS modules fail over time)
- Map updates require bandwidth (ongoing cost)

### Mitigation Strategies

**User Experience**:
- First violation: Warning only, no fine (educational grace period)
- Clear in-app map showing zones (transparency)
- Dispute mechanism (one-click "Report Error")
- Tourist mode: Extra leniency (first 3 violations = warnings)

**Technical Reliability**:
- GPS + IMU fusion (dead reckoning when GPS drops)
- Confidence scoring (only enforce if >70% sure)
- Fallback to cloud check (if edge uncertain, ask cloud for second opinion)
- Weekly map quality audits (detect OSM errors)

**Privacy**:
- Data minimization (only log violations, not full journeys)
- Anonymization (hash user IDs in logs)
- Transparency (show users what data is collected)
- GDPR compliance (right to erasure after 90 days)

**Cost**:
- Phase 1: Pilot with 500 vehicles ($50K investment)
- Measure ROI before full rollout
- Consider leasing hardware (reduce upfront cost)

## Implementation Roadmap

### Phase 1: Pilot (Month 1-3)
**Scope**: 500 scooters in Berlin

- Install edge AI units on 500 vehicles
- Load Berlin map data (OSM + CDS curbs)
- Test geo-fence enforcement (10 known restricted zones)
- Test wrong-way detection (20 known one-way streets)
- **Success Metrics**:
  - 80% reduction in violations
  - <10% false positive rate
  - User satisfaction >4/5

### Phase 2: Expand (Month 4-6)
**Scope**: 5,000 vehicles across 3 cities (Berlin, Paris, Amsterdam)

- Scale hardware installation
- Multi-language audio warnings (German, French, Dutch, English)
- Integrate city-specific zones (each city has unique rules)
- **Success Metrics**:
  - Maintain <10% false positives across cities
  - €50K/month in avoided fines
  - <50 user disputes/month

### Phase 3: Full Fleet (Month 7-12)
**Scope**: 10,000 vehicles across 10 EU cities

- Complete hardware rollout
- ML-based map correction (learn from disputes)
- Predictive zone updates (identify new restrictions automatically)
- **Success Metrics**:
  - 90% reduction in city fines
  - <5% false positive rate
  - €200K/year net savings

## Related Decisions

- ADR-001: Adopt Open Mobility Foundation Standards (CDS CURBS API for zone data)
- ADR-002: Computer Vision AI for Operational Verification (complementary safety features)
- ADR-004: Edge Computing Strategy for Real-Time AI (to be created)
- ADR-005: Privacy and Location Data Handling (to be created)

## References

**Standards**:
- [CDS CURBS API](https://www.openmobilityfoundation.org/about-cds/) - City curb regulations
- [OpenStreetMap One-Way Tags](https://wiki.openstreetmap.org/wiki/Key:oneway) - Street direction data

**Technical**:
- [Raspberry Pi 4 Specs](https://www.raspberrypi.com/products/raspberry-pi-4-model-b/) - Edge processor
- [GPS Module Accuracy](https://www.u-blox.com/en/product/neo-m8-series) - ±2m with SBAS
- [Map Matching Algorithms](https://en.wikipedia.org/wiki/Map_matching) - Snap GPS to street

**Privacy**:
- [GDPR Article 6](https://gdpr.eu/article-6-how-to-process-personal-data-legally/) - Legal basis for location tracking
- [GDPR Data Minimization](https://gdpr.eu/recital-39-principle-of-data-minimisation/) - Store only necessary data

## Notes

**Date**: October 22, 2025
**Authors**: BRINUS Team (Rafael, Prashant, Khaled, João Vinagre)
**Reviewers**: To be reviewed by competition judges

**Key Innovation for Judges**:
- ✅ **Edge AI for safety-critical real-time decisions** (not cloud-dependent)
- ✅ **Silent first intervention** (better UX than immediate warnings)
- ✅ **Privacy-first design** (minimal location data storage)
- ✅ **Multi-language support** (inclusive EU-wide operation)
- ✅ **Progressive enforcement** (educational, not punitive)

**Estimated Business Impact**:
- **Safety**: 80% reduction in wrong-way incidents
- **Compliance**: 90% reduction in city fines (€216K/year savings)
- **ROI**: 5-year payback on $1M hardware investment
- **User Experience**: Prevent violations before they happen (no surprise fines)
