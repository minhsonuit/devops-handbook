# k6 Load Testing

> Ngay bat dau: ___

## k6 la gi

Tool load testing viet bang Go, script bang JavaScript. Nhe, nhanh, de dung.

## Cai dat

```bash
# macOS
brew install k6

# Docker
docker run --rm -i grafana/k6 run - < script.js
```

## Script co ban

```javascript
// load-test.js
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  vus: 50,              // 50 virtual users
  duration: '30s',      // Chay 30 giay

  thresholds: {
    http_req_duration: ['p(95)<500'],    // p95 < 500ms
    http_req_failed: ['rate<0.01'],      // Error rate < 1%
  },
};

export default function () {
  const res = http.get('http://localhost:5001/api/health');

  check(res, {
    'status is 200': (r) => r.status === 200,
    'response time < 500ms': (r) => r.timings.duration < 500,
  });

  sleep(1);
}
```

```bash
k6 run load-test.js
```

## Scenarios nang cao

```javascript
export const options = {
  scenarios: {
    // Ramp up → hold → ramp down
    smoke: {
      executor: 'ramping-vus',
      startVUs: 0,
      stages: [
        { duration: '1m', target: 10 },    // Ramp up
        { duration: '3m', target: 10 },    // Hold
        { duration: '1m', target: 0 },     // Ramp down
      ],
    },

    // Spike test
    spike: {
      executor: 'ramping-vus',
      startVUs: 0,
      stages: [
        { duration: '10s', target: 100 },  // Spike len 100 users
        { duration: '1m', target: 100 },   // Hold
        { duration: '10s', target: 0 },    // Drop
      ],
    },

    // Constant arrival rate
    constant_load: {
      executor: 'constant-arrival-rate',
      rate: 100,                           // 100 requests/s
      timeUnit: '1s',
      duration: '5m',
      preAllocatedVUs: 50,
      maxVUs: 200,
    },
  },
};
```

## Test API voi authentication

```javascript
import http from 'k6/http';
import { check } from 'k6';

const BASE_URL = 'http://localhost:5001';

export function setup() {
  // Login 1 lan
  const loginRes = http.post(`${BASE_URL}/api/auth/login`, JSON.stringify({
    username: 'test',
    password: 'test123',
  }), { headers: { 'Content-Type': 'application/json' } });

  return { token: loginRes.json('token') };
}

export default function (data) {
  const headers = {
    Authorization: `Bearer ${data.token}`,
    'Content-Type': 'application/json',
  };

  const res = http.get(`${BASE_URL}/api/orders`, { headers });
  check(res, { 'status is 200': (r) => r.status === 200 });
}
```

## Output ket qua

```bash
# Console (default)
k6 run script.js

# JSON
k6 run --out json=results.json script.js

# Prometheus
k6 run --out experimental-prometheus-rw script.js

# InfluxDB + Grafana
k6 run --out influxdb=http://localhost:8086/k6 script.js
```

## Doc ket qua

```
http_req_duration......: avg=123ms min=10ms med=100ms max=2000ms p(90)=200ms p(95)=350ms
http_req_failed........: 0.50%    ✓ 25    ✗ 4975
http_reqs..............: 5000     166.6/s
vus....................: 50
```

Quan trong nhat:
- `p(95)` — 95% requests nhanh hon gia tri nay
- `http_req_failed` — ty le loi
- `http_reqs` — throughput (req/s)
