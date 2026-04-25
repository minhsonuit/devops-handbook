# Docker Image Optimization

> Ngay bat dau: ___

## Tai sao image size quan trong

- Pull nhanh hon → deploy nhanh hon
- It attack surface → an toan hon
- Tiet kiem storage va bandwidth

## So sanh base images

| Base image | Size | Dung khi |
|-----------|------|----------|
| `aspnet:8.0` (Debian) | ~220MB | Mac dinh, day du |
| `aspnet:8.0-alpine` | ~110MB | Nho hon, nhung co the gap loi glibc |
| `aspnet:8.0-chiseled` | ~110MB | Distroless, khong co shell — production |
| `scratch` | 0MB | Self-contained apps |

## Multi-stage build toi uu

```dockerfile
# ---- Stage 1: Restore ----
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS restore
WORKDIR /src
COPY *.csproj .
RUN dotnet restore
# Layer nay chi rebuild khi csproj thay doi

# ---- Stage 2: Build ----
FROM restore AS build
COPY . .
RUN dotnet publish -c Release -o /app --no-restore

# ---- Stage 3: Runtime ----
FROM mcr.microsoft.com/dotnet/aspnet:8.0-alpine AS runtime
WORKDIR /app
COPY --from=build /app .

# Non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

EXPOSE 5001
HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
  CMD wget -qO- http://localhost:5001/health || exit 1
ENTRYPOINT ["dotnet", "MyApp.dll"]
```

## .dockerignore

```
**/bin/
**/obj/
**/.vs/
**/.git/
**/node_modules/
**/*.user
**/TestResults/
**/Dockerfile*
**/.dockerignore
**/*.md
```

## Layer caching tips

```dockerfile
# SAI — moi lan code doi, restore lai
COPY . .
RUN dotnet restore
RUN dotnet publish

# DUNG — tach restore va build
COPY *.csproj .              # Thay doi it → cache hit
RUN dotnet restore
COPY . .                     # Source code thay doi nhieu
RUN dotnet publish --no-restore
```

## Kiem tra image size

```bash
# Xem size
docker images myapp
docker history myapp:latest            # Xem tung layer
docker history myapp:latest --no-trunc # Chi tiet

# So sanh
docker images --format "{{.Repository}}:{{.Tag}} {{.Size}}" | sort -k2 -h

# Phan tich chi tiet voi dive
docker run --rm -it -v /var/run/docker.sock:/var/run/docker.sock wagoodman/dive myapp:latest
```

## Giam layer

```dockerfile
# SAI — nhieu layer
RUN apt-get update
RUN apt-get install -y curl
RUN apt-get install -y wget
RUN rm -rf /var/lib/apt/lists/*

# DUNG — gom thanh 1 layer
RUN apt-get update && \
    apt-get install -y --no-install-recommends curl wget && \
    rm -rf /var/lib/apt/lists/*
```
