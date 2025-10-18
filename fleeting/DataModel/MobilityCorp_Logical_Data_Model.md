## MobilityCorp Logical Data Model

Date: October 18, 2025

This document defines the logical data entities for the AI-Enhanced Mobility Platform. It represents core data tables and their primary attributes without implying specific database technology or implementation details.

### 1. ParkingSpot

Stores information about each designated parking location, its geolocation, and current occupancy state.

| Column          | Description                                                       | Example             |
|-----------------|-------------------------------------------------------------------|---------------------|
| ParkingSpotID   | Unique identifier for each parking spot                           | BH-A1               |
| ParkingName     | Descriptive name of the parking spot                              | Berlin Hauptbahnhof |
| Latitude        | Geographical latitude                                             | 52.5255             |
| Longitude       | Geographical longitude                                            | 13.3697             |
| OccupancyStatus | Indicates whether the spot is occupied or vacant                  | Occupied            |
| VehicleID       | Identifier of the vehicle currently occupying the spot (nullable) | ES-GER-00457        |

### 2. Vehicle

Stores metadata and dynamic operational data for each fleet vehicle.

| Column             | Description                                       | Example              |
|--------------------|---------------------------------------------------|----------------------|
| VehicleID          | Unique identifier for each vehicle                | ES-GER-00457         |
| VehicleType        | Type of vehicle                                   | Electric Scooter     |
| Latitude           | Current latitude of vehicle                       | 52.5200              |
| Longitude          | Current longitude of vehicle                      | 13.4050              |
| CurrentChargeLevel | Current battery charge level (%)                  | 68                   |
| LastFullyChargedAt | Timestamp when vehicle was last fully charged     | 2025-10-17 03:40 UTC |
| OperationalStatus  | Operational state (Active, Maintenance, Disabled) | Active               |

### 3. Booking

Records vehicle rental transactions, including scheduled and actual times and durations.

| Column            | Description                          | Example              |
|-------------------|--------------------------------------|----------------------|
| BookingID         | Unique identifier for each booking   | BK-2025-0417         |
| VehicleID         | Reference to Vehicle entity          | ES-GER-00457         |
| UserID            | Reference to User entity             | U-78945              |
| BookingStartTime  | Start date and time of booking       | 2025-10-17 08:00 UTC |
| PlannedReturnTime | Scheduled return date and time       | 2025-10-17 10:00 UTC |
| ActualReturnTime  | Actual return date and time          | 2025-10-17 09:45 UTC |
| OriginalDuration  | Planned duration of rental (minutes) | 120                  |
| ActualDuration    | Actual duration of rental (minutes)  | 105                  |

### 4. ChargingSession

Captures each instance of a vehicle being connected to a charger, including energy and duration metrics.

| Column             | Description                                    | Example              |
|--------------------|------------------------------------------------|----------------------|
| ChargingSessionID  | Unique identifier for each charging event      | CH-2025-0823         |
| VehicleID          | Reference to Vehicle entity                    | ES-GER-00457         |
| ParkingSpotID      | Reference to ParkingSpot where charging occurs | BH-A1                |
| StartTime          | Timestamp when charging began                  | 2025-10-17 09:45 UTC |
| EndTime            | Timestamp when charging completed              | 2025-10-17 11:15 UTC |
| EnergyDeliveredKWh | Total energy transferred during session        | 3.8                  |

### 5. User

Stores registered customer information necessary for bookings and authentication.

| Column            | Description                                | Example                 |
|-------------------|--------------------------------------------|-------------------------|
| UserID            | Unique identifier for each user            | U-78945                 |
| FullName          | Full name of the user                      | Alex Müller             |
| Email             | User email address                         | alex.muller@example.com |
| PhoneNumber       | Registered contact number                  | +49 170 1234567         |
| PreferredLanguage | Language preference for localization       | de-DE                   |
| PreferredCurrency | Preferred currency for payments            | EUR                     |
| AccountStatus     | Current account status (Active, Suspended) | Active                  |