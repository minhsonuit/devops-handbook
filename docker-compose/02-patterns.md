# Docker Compose Production Patterns

> Ngay bat dau: ___

## Health checks

```yaml
services:
  api:
    image: myapp:latest
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:5001/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s    # Cho app khoi dong xong moi bat dau check
```

Health check cho cac service pho bien:

```yaml
# PostgreSQL
healthcheck:
  test: ["CMD-SHELL", "pg_isready -U postgres"]
  interval: 10s
  timeout: 5s
  retries: 5

# Redis
healthcheck:
  test: ["CMD", "redis-cli", "ping"]
  interval: 10s
  timeout: 5s
  retries: 5

# SQL Server
healthcheck:
  test: ["CMD", "/opt/mssql-tools/bin/sqlcmd", "-S", "localhost", "-U", "sa", "-P", "$$SA_PASSWORD", "-Q", "SELECT 1"]
  interval: 15s
  timeout: 10s
  retries: 5

# Nginx
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost/nginx_status"]
  interval: 30s
  timeout: 5s
  retries: 3
```

## Resource limits

```yaml
services:
  api:
    deploy:
      resources:
        limits:
          cpus: "1.0"
          memory: 512M
        reservations:
          cpus: "0.25"
          memory: 128M
```

## Restart policy

```yaml
services:
  api:
    restart: unless-stopped    # Restart tru khi bi stop thu cong

# Cac gia tri:
# no              — khong restart
# always          — luon restart (ke ca khi stop thu cong → restart khi Docker daemon start)
# on-failure      — chi restart khi exit code != 0
# unless-stopped  — nhu always nhung khong restart neu da stop thu cong
```

## Logging

```yaml
services:
  api:
    logging:
      driver: json-file
      options:
        max-size: "10m"       # Moi file log toi da 10MB
        max-file: "3"         # Giu toi da 3 file
```

## Multi-stage build

```dockerfile
# Dockerfile cho .NET app
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
COPY *.csproj .
RUN dotnet restore
COPY . .
RUN dotnet publish -c Release -o /app

FROM mcr.microsoft.com/dotnet/aspnet:8.0
WORKDIR /app
COPY --from=build /app .
EXPOSE 5001
ENTRYPOINT ["dotnet", "MyApp.dll"]
```

## Security

```yaml
services:
  api:
    read_only: true           # Filesystem read-only
    tmpfs:
      - /tmp                  # Chi cho phep ghi vao /tmp
    security_opt:
      - no-new-privileges:true
    user: "1000:1000"         # Khong chay root
```

## Compose file mau production

```yaml
version: "3.8"

services:
  api:
    image: myregistry/api:${TAG:-latest}
    ports:
      - "5001:5001"
    environment:
      - ASPNETCORE_ENVIRONMENT=Production
    env_file:
      - .env
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:5001/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
    restart: unless-stopped
    logging:
      driver: json-file
      options:
        max-size: "10m"
        max-file: "3"
    deploy:
      resources:
        limits:
          cpus: "1.0"
          memory: 512M
    networks:
      - backend

  db:
    image: postgres:15
    volumes:
      - db_data:/var/lib/postgresql/data
    environment:
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped
    networks:
      - backend

volumes:
  db_data:

networks:
  backend:
```
