# Cloud Cost Management

> Ngay bat dau: ___

## Nguyen tac

1. **Right-sizing** — dung chon VM qua lon "cho chac"
2. **Reserved Instances** — commit 1-3 nam, giam 40-70%
3. **Spot/Preemptible** — workload co the bi gian doan
4. **Auto-scaling** — scale theo nhu cau, khong chay 24/7 max
5. **Cleanup** — xoa resource khong dung

## Azure cost tools

```bash
# Xem cost
az consumption usage list --top 10

# Budget alerts
# Azure Portal → Cost Management → Budgets → tao alert
```

## Tips tiet kiem

| Tip | Giam |
|-----|------|
| Tat dev/staging ngoai gio lam | 60%+ |
| Dung B-series VM (burstable) cho dev | 40%+ |
| Reserved Instance 1 nam | 40% |
| Azure Hybrid Benefit (co Windows license) | 40% |
| Compress va cache o Nginx | Giam bandwidth |
| Dung Azure CDN cho static files | Giam origin load |

## Monitoring cost

- Set budget alerts (50%, 80%, 100%)
- Review Cost Analysis hang tuan
- Tag resources de biet chi phi theo team/project
