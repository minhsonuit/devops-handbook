# Incident Response

> Ngay bat dau: ___

## Quy trinh khi co su co

```
1. DETECT   — Phat hien: alert, user report, monitoring
2. TRIAGE   — Danh gia: muc do, impact, urgency
3. CONTAIN  — Ngan chan: rollback, scale, failover
4. FIX      — Sua chua: root cause fix
5. VERIFY   — Xac nhan: test, monitor
6. DOCUMENT — Postmortem: timeline, root cause, prevention
```

## Severity levels

| Level | Mo ta | Response time |
|-------|-------|---------------|
| P1 - Critical | System down, data loss | 15 phut |
| P2 - High | Major feature broken | 1 gio |
| P3 - Medium | Minor feature broken | 4 gio |
| P4 - Low | Cosmetic, non-urgent | Next business day |

## Quick actions

```bash
# Rollback deployment
docker compose down
docker compose up -d    # Voi image version cu

# K8s rollback
kubectl rollout undo deployment/api

# Scale up de chiu tai
docker service scale api=5
kubectl scale deploy api --replicas=5
```

## Postmortem template

```markdown
# Incident: [Ten su co]
## Summary
- Date: YYYY-MM-DD
- Duration: X hours
- Impact: [ai bi anh huong]
- Severity: P1/P2/P3

## Timeline
- HH:MM — [su kien]
- HH:MM — [phat hien]
- HH:MM — [hanh dong]
- HH:MM — [resolved]

## Root Cause
[Mo ta nguyen nhan goc]

## Resolution
[Da lam gi de fix]

## Prevention
- [ ] [Hanh dong de ngua 1]
- [ ] [Hanh dong de ngua 2]

## Lessons Learned
- [Bai hoc rut ra]
```
