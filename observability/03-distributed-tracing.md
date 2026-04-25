# Distributed Tracing

> Ngay bat dau: ___

## Distributed tracing la gi

Theo doi 1 request khi no di qua nhieu services.

```
User → Nginx → API Gateway → Order Service → Inventory Service → DB
  └── trace-id: abc-123 ──────────────────────────────────────────┘
```

Moi buoc la 1 **span**. Tat ca spans cung trace-id = 1 **trace**.

## Jaeger

```yaml
services:
  jaeger:
    image: jaegertracing/all-in-one:latest
    ports:
      - "16686:16686"    # Jaeger UI
      - "4317:4317"      # OTLP gRPC
      - "4318:4318"      # OTLP HTTP
    environment:
      COLLECTOR_OTLP_ENABLED: "true"
```

UI: http://localhost:16686

## Doc 1 trace

```
Trace: abc-123
├── [200ms] Nginx reverse proxy
├── [180ms] API Gateway /api/orders
│   ├── [5ms] Auth middleware
│   ├── [150ms] Order Service CreateOrder
│   │   ├── [10ms] Validate request
│   │   ├── [50ms] SQL: INSERT INTO Orders
│   │   ├── [80ms] Inventory Service ReserveStock
│   │   │   └── [70ms] SQL: UPDATE Inventory
│   │   └── [5ms] Publish OrderCreated event
│   └── [5ms] Serialize response
```

Tu trace nay thay: request mat 200ms, trong do Inventory chiem 80ms → bottleneck.

## Khi nao can tracing

| Trieu chung | Tracing giup gi |
|-------------|----------------|
| Request cham nhung khong biet service nao | Tim span cham nhat |
| Loi 500 random | Tim service nao throw exception |
| Timeout | Tim service nao khong response |
| Performance regression | So sanh traces truoc/sau deploy |

## Head-based vs Tail-based sampling

```
Head-based: Quyet dinh trace ngay tu dau (random 10%)
  → Don gian, nhung co the miss request quan trong

Tail-based: Thu thap tat ca, chi luu nhung trace co loi hoac cham
  → Chinh xac hon, nhung ton tai nguyen hon
```

```yaml
# OTel Collector — tail-based sampling
processors:
  tail_sampling:
    decision_wait: 10s
    policies:
      - name: errors
        type: status_code
        status_code: { status_codes: [ERROR] }
      - name: slow
        type: latency
        latency: { threshold_ms: 2000 }
      - name: sample
        type: probabilistic
        probabilistic: { sampling_percentage: 10 }
```
