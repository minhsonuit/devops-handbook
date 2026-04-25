# Docker BuildKit

> Ngay bat dau: ___

## Bat BuildKit

```bash
# Environment variable
DOCKER_BUILDKIT=1 docker build .

# Hoac set mac dinh trong /etc/docker/daemon.json
{ "features": { "buildkit": true } }

# Docker Compose
COMPOSE_DOCKER_CLI_BUILD=1 DOCKER_BUILDKIT=1 docker compose build
```

## Cache mount — NuGet cache

```dockerfile
# syntax=docker/dockerfile:1
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
COPY *.csproj .

# Mount NuGet cache — khong download lai moi lan build
RUN --mount=type=cache,target=/root/.nuget/packages \
    dotnet restore

COPY . .
RUN --mount=type=cache,target=/root/.nuget/packages \
    dotnet publish -c Release -o /app --no-restore
```

## Secret mount — khong luu secret trong image

```dockerfile
# syntax=docker/dockerfile:1
# Mount secret khi build, khong luu vao layer
RUN --mount=type=secret,id=nuget_config,dst=/root/.nuget/NuGet/NuGet.Config \
    dotnet restore

# Build voi secret
docker build --secret id=nuget_config,src=./NuGet.Config .
```

## Multi-platform build

```bash
# Tao builder
docker buildx create --name mybuilder --use

# Build cho nhieu platform
docker buildx build --platform linux/amd64,linux/arm64 \
  -t myapp:latest --push .
```

## Build arguments

```dockerfile
ARG DOTNET_VERSION=8.0
FROM mcr.microsoft.com/dotnet/sdk:${DOTNET_VERSION} AS build
ARG BUILD_CONFIG=Release
RUN dotnet publish -c ${BUILD_CONFIG} -o /app
```

```bash
docker build --build-arg DOTNET_VERSION=7.0 --build-arg BUILD_CONFIG=Debug .
```

## Inline cache (CI/CD)

```bash
# Build voi cache metadata
docker buildx build \
  --cache-to type=inline \
  --cache-from type=registry,ref=myregistry/myapp:cache \
  -t myregistry/myapp:latest --push .
```
