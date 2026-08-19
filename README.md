# Food Delivery Analytics Platform

A production-inspired backend system that simulates how modern food delivery platforms process transactional data and serve analytical insights in near real-time.

The project demonstrates:

- High-performance backend development in Go
- PostgreSQL OLTP and OLAP architecture
- Event-driven data synchronization using PostgreSQL LISTEN/NOTIFY
- CDC (Change Data Capture) worker pipelines
- Analytics APIs powered by denormalized fact tables
- Dockerized microservices
- Observability with Prometheus
- Structured logging
- Load testing with k6

---

# Architecture Overview

![Architecture Diagram](./docs/images/architecture.png)

> Replace with actual architecture diagram.

```text
                   ┌────────────────────┐
                   │   Order Service    │
                   └─────────┬──────────┘
                             │
                             ▼
                  ┌─────────────────────┐
                  │      OLTP DB        │
                  │     PostgreSQL      │
                  └─────────┬───────────┘
                            │
                     LISTEN/NOTIFY
                            │
                            ▼
                  ┌─────────────────────┐
                  │     CDC Worker      │
                  └─────────┬───────────┘
                            │
                            ▼
                  ┌─────────────────────┐
                  │      OLAP DB        │
                  │  fact_order_sales   │
                  └─────────┬───────────┘
                            │
                            ▼
                  ┌─────────────────────┐
                  │ Analytics Service   │
                  └─────────┬───────────┘
                            │
                            ▼
                     Dashboard APIs

                            ▲
                            │
                      Prometheus
```

---

# Tech Stack

## Backend

- Go 1.26
- Chi Router
- PostgreSQL
- pgx
- Docker
- Docker Compose

## Analytics

- OLTP → OLAP Architecture
- Fact Tables
- CDC Pipeline
- PostgreSQL LISTEN/NOTIFY

## Observability

- Prometheus

## Testing

- k6 Load Testing

---

# Project Structure

```text
food-delivery-analytics/

├── services/
│
│   ├── order-service/
│   │
│   ├── cdc-worker/
│   │
│   └── analytics-service/
│
├── database/
│
│   ├── oltp/
│   │   └── migrations/
│   │
│   └── olap/
│       └── migrations/
│
├── infra/
│   └── prometheus/
│
├── tests/
│   └── load/
│
├── docs/
│   ├── images/
│   └── benchmarks/
│
├── docker-compose.yml
│
└── README.md
```

---

# Features

## Week 1 — Foundation

### Dockerized Infrastructure

- Docker Compose setup
- PostgreSQL OLTP database
- PostgreSQL OLAP database
- Health checks
- Persistent volumes

### Database Design

- Fully normalized 3NF schema
- Customers
- Restaurants
- Orders
- Order Items

### Migrations

- Version-controlled SQL migrations
- Up and Down scripts

### Deliverables

✅ Docker Compose

✅ PostgreSQL

✅ 3NF Schema

✅ Migrations

---

## Week 2 — Order Service

### REST APIs

Implemented transactional APIs for order creation.

### Highlights

- Request validation
- Context propagation
- Context timeouts
- Transaction management
- Repository pattern
- Service layer architecture

### Example

```http
POST /orders
```

```json
{
  "customer_id": 1,
  "restaurant_id": 1,
  "item_id": 1,
  "quantity": 2,
  "price": 199
}
```

### Deliverables

✅ Order Service

✅ Transactions

✅ Validation

✅ Context Timeout

---

## Week 3 — CDC Pipeline

### Event Driven Synchronization

Implemented Change Data Capture using PostgreSQL LISTEN/NOTIFY.

### Flow

```text
Order Created
      │
      ▼
PostgreSQL Trigger
      │
      ▼
pg_notify()
      │
      ▼
CDC Worker
      │
      ▼
Fact Table Sync
```

### Components

#### PostgreSQL Trigger

Automatically emits events when orders are inserted.

#### CDC Worker

Consumes notifications and processes order events.

#### Fact Table

Denormalized analytical storage.

### Deliverables

✅ LISTEN / NOTIFY

✅ CDC Worker

✅ Fact Table Population

---

## Week 4 — Analytics Platform

### Analytics API

Provides low-latency reporting endpoints.

### Endpoints

#### Dashboard

```http
GET /analytics/dashboard
```

#### Revenue

```http
GET /analytics/revenue
```

#### Orders

```http
GET /analytics/orders
```

### Fact Table Benefits

All analytical queries execute against:

```sql
fact_order_sales
```

No expensive joins required.

### Performance Optimization

- Composite indexes
- Aggregation optimization
- Query benchmarking

### Deliverables

✅ Analytics Service

✅ Fact Table Queries

✅ Index Optimization

✅ Query Benchmarking

---

## Week 5 — Production Readiness

### Observability

#### Prometheus Metrics

Metrics exposed through:

```http
GET /metrics
```

Tracked metrics:

- Request Count
- Request Duration
- CDC Events Processed
- CDC Failures
- Database Query Latency

### Structured Logging

Implemented JSON logging using Zap.

Example:

```json
{
  "level": "info",
  "msg": "order_created",
  "order_id": 123
}
```

### Health Checks

```http
GET /health
```

Used by:

- Docker
- Service Monitoring
- Availability Checks

### Load Testing

Implemented k6 tests for:

- Order Creation API
- Analytics Dashboard API

### Deliverables

✅ Prometheus

✅ Structured Logs

✅ Health Checks

✅ k6 Load Testing

---

# Database Design

## OLTP Schema

![OLTP Schema](./docs/images/oltp-schema.png)

> Replace with schema screenshot.

### Tables

- customers
- restaurants
- orders
- order_items

### Normalization

3NF compliant schema designed for transactional workloads.

---

## OLAP Schema

![OLAP Schema](./docs/images/olap-schema.png)

> Replace with schema screenshot.

### Fact Table

```sql
fact_order_sales
```

Columns include:

- order_id
- restaurant_id
- customer_id
- total_item_count
- total_order_amount
- order_status
- date_key

Optimized for aggregation queries.

---

# API Documentation

## Order Service

### Create Order

```http
POST /orders
```

### Health

```http
GET /health
```

### Metrics

```http
GET /metrics
```

---

## Analytics Service

### Dashboard

```http
GET /analytics/dashboard
```

### Revenue

```http
GET /analytics/revenue
```

### Orders

```http
GET /analytics/orders
```

### Health

```http
GET /health
```

### Metrics

```http
GET /metrics
```

---

# Screenshots

## Order Creation

![Order API](./docs/images/order-api.png)

> Replace with screenshot.

---

## CDC Worker Processing

![CDC Worker](./docs/images/cdc-worker.png)

> Replace with screenshot.

---

## Analytics Dashboard Response

![Analytics Dashboard](./docs/images/dashboard-response.png)

> Replace with screenshot.

---

## Prometheus Metrics

![Prometheus](./docs/images/prometheus.png)

> Replace with screenshot.

---

## Docker Containers

![Docker](./docs/images/docker-containers.png)

> Replace with screenshot.

---

# Benchmark Results

## Analytics Query

```sql
EXPLAIN ANALYZE
SELECT
    restaurant_name,
    COUNT(order_id),
    SUM(total_order_amount)
FROM fact_order_sales
WHERE restaurant_id = 1
GROUP BY restaurant_name;
```

### Results

| Metric | Value |
|----------|----------|
| Average | TBD |
| P95 | TBD |
| P99 | TBD |

---

## k6 Results

### Order Service

| Metric | Value |
|----------|----------|
| Requests | TBD |
| Avg Latency | TBD |
| P95 | TBD |
| Error Rate | TBD |

### Analytics Service

| Metric | Value |
|----------|----------|
| Requests | TBD |
| Avg Latency | TBD |
| P95 | TBD |
| Error Rate | TBD |

---

# Running Locally

## Start Services

```bash
docker compose up --build
```

## Stop Services

```bash
docker compose down
```

---

# Monitoring

Prometheus:

```text
http://localhost:9090
```

Order Service Metrics:

```text
http://localhost:8080/metrics
```

Analytics Service Metrics:

```text
http://localhost:8081/metrics
```

---

# Future Improvements

- Kafka-based CDC
- Grafana Dashboards
- Redis Caching
- CI/CD Pipeline (GitHub Actions)
---

# Resume Highlights

This project demonstrates:

- Backend Engineering
- Distributed Systems Fundamentals
- Event-Driven Architecture
- Change Data Capture (CDC)
- Database Design
- OLTP & OLAP Modeling
- Go Concurrency
- Docker & Containerization
- Observability
- Performance Engineering
- Load Testing
- Production Readiness

---

# License

MIT License
