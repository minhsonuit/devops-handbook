# Testing Strategies

> Ngay bat dau: ___

## Cac loai test

| Loai | Muc dich | Pattern |
|------|----------|---------|
| **Smoke** | Verify basic functionality | 1-5 VUs, 1 min |
| **Load** | Verify under expected load | 50-100 VUs, 10 min |
| **Stress** | Tim breaking point | Ramp up cho den fail |
| **Spike** | Verify sudden traffic burst | 0 → 500 VUs → 0 |
| **Soak** | Tim memory leaks, resource exhaustion | 50 VUs, 2-4 hours |

## Khi nao chay test nao

```
1. Truoc moi release    → Smoke test (2 phut)
2. Hang tuan            → Load test (10 phut)
3. Truoc su kien lon    → Stress test + Spike test
4. Sau major changes    → Soak test (2-4h)
5. Capacity planning    → Stress test de biet max
```

## Stress test — tim breaking point

```javascript
export const options = {
  stages: [
    { duration: '2m', target: 50 },
    { duration: '2m', target: 100 },
    { duration: '2m', target: 200 },
    { duration: '2m', target: 400 },    // Tim diem fail
    { duration: '2m', target: 800 },
    { duration: '2m', target: 0 },
  ],
};
```

Khi chay, theo doi:
- Response time tang → dau hieu dau tien
- Error rate tang → dang dat gioi han
- Timeout → da vuot gioi han

## Report template

```markdown
# Performance Test Report
- Date: YYYY-MM-DD
- Environment: staging
- Test type: Load test

## Results
| Metric | Value | Threshold | Status |
|--------|-------|-----------|--------|
| p95 latency | 320ms | < 500ms | ✅ PASS |
| p99 latency | 890ms | < 1000ms | ✅ PASS |
| Error rate | 0.1% | < 1% | ✅ PASS |
| Throughput | 500 req/s | > 300 req/s | ✅ PASS |
| Max concurrent | 200 VUs | > 100 VUs | ✅ PASS |

## Bottlenecks identified
- CPU reach 85% at 400 VUs
- DB connection pool maxed at 350 VUs

## Recommendations
- Scale API to 3 replicas before sale event
- Increase DB connection pool to 200
```
