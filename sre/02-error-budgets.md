# Error Budgets

> Ngay bat dau: ___

## Error budget la gi

```
SLO = 99.9% availability
Error budget = 100% - 99.9% = 0.1%

Trong 30 ngay:
  Total minutes = 30 * 24 * 60 = 43,200 phut
  Error budget  = 43,200 * 0.1% = 43.2 phut downtime duoc phep
```

## Su dung error budget

```
Error budget con du → Team duoc phep:
  ✅ Deploy features moi
  ✅ Thay doi infrastructure
  ✅ Thu nghiem (experiment)

Error budget sap het → Team phai:
  ❌ Dung deploy feature moi
  ✅ Focus vao reliability
  ✅ Fix bugs, improve monitoring
  ✅ Improve incident response
```

## Error budget policy

```markdown
# Error Budget Policy

## Khi error budget > 50%
- Deploy binh thuong
- Feature work uu tien

## Khi error budget 20-50%
- Giam toc do deploy
- Review ky hon truoc moi release
- Bat dau fix reliability issues

## Khi error budget < 20%
- Freeze feature releases
- Chi deploy bug fixes va reliability improvements
- Postmortem cho moi incident
- Leadership review

## Khi error budget = 0%
- Production freeze
- All hands on reliability
- Root cause analysis cho tat ca incidents
```

## Tracking error budget

```promql
# Error budget consumed (%)
(1 - (
  sum(rate(http_requests_total{status!~"5.."}[30d]))
  /
  sum(rate(http_requests_total[30d]))
)) / (1 - 0.999) * 100

# Neu ket qua > 100% → da vuot error budget
```

## Risk assessment cho releases

| Thay doi | Risk | Error budget needed |
|----------|------|-------------------|
| Config change | Low | 5% |
| Minor feature | Medium | 10% |
| Major feature | High | 20% |
| Infrastructure migration | Very High | 30% |
| Database schema change | High | 20% |
