# Network Security

> Ngay bat dau: ___

## TLS Everywhere

- Tat ca traffic external → HTTPS
- Internal services: xem xet mTLS
- Xem chi tiet: [nginx/07-ssl-certificates.md](../nginx/07-ssl-certificates.md)

## mTLS (Mutual TLS)

- Ca client va server xac thuc lan nhau bang certificate
- Dung trong service mesh (Istio, Linkerd)
- Bao ve service-to-service communication

## Network segmentation

```
Public subnet  → Nginx/LB only
Private subnet → API, DB, Redis (khong truy cap truc tiep tu internet)
```

## Firewall rules

- Default deny — chi allow nhung gi can thiet
- Xem chi tiet: [networking/03-firewall.md](../networking/03-firewall.md)

## Security headers

- Xem chi tiet: [nginx/06-security.md](../nginx/06-security.md)
