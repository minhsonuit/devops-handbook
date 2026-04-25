# Container Security

> Ngay bat dau: ___

## Image security

```bash
# Scan image
docker scout cves myapp:latest
docker scout quickview myapp:latest

# Dung base image nho
FROM mcr.microsoft.com/dotnet/aspnet:8.0-alpine    # Alpine nho hon debian

# Khong chay root
USER 1000

# Multi-stage build de giam attack surface
```

## Runtime security

```yaml
# docker-compose
services:
  api:
    read_only: true
    tmpfs: [/tmp]
    security_opt:
      - no-new-privileges:true
    user: "1000:1000"
    cap_drop:
      - ALL
```

## Best practices

- Luon cap nhat base image
- Khong luu secrets trong image (dung env hoac vault)
- Scan image trong CI pipeline
- Dung digest thay vi tag cho critical images
- Gioi han resource (CPU, memory)
