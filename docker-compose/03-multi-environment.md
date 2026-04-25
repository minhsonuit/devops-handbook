# Docker Compose Multi-Environment

> Ngay bat dau: ___

## Override files

Docker Compose tu dong merge `docker-compose.yml` + `docker-compose.override.yml`:

```bash
# Tu dong doc 2 file
docker compose up -d

# Chi dinh file cu the
docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d
```

### docker-compose.yml (base)

```yaml
version: "3.8"
services:
  api:
    image: myapp:latest
    networks:
      - backend

networks:
  backend:
```

### docker-compose.override.yml (dev — tu dong load)

```yaml
services:
  api:
    ports:
      - "5001:5001"
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
    volumes:
      - ./src:/app       # Hot reload
```

### docker-compose.prod.yml

```yaml
services:
  api:
    ports:
      - "80:5001"
    environment:
      - ASPNETCORE_ENVIRONMENT=Production
    restart: unless-stopped
    deploy:
      resources:
        limits:
          memory: 512M
```

## env_file va .env

### .env (doc boi Docker Compose, dung cho variable substitution)

```env
TAG=v1.2.3
DB_PASSWORD=secret123
COMPOSE_PROJECT_NAME=myproject
```

### .env.production

```env
TAG=v1.2.3
DB_PASSWORD=${DB_PASSWORD_FROM_VAULT}
```

### Su dung trong compose

```yaml
services:
  api:
    image: myregistry/api:${TAG:-latest}    # Default "latest" neu TAG khong co
    env_file:
      - .env.${ENVIRONMENT:-development}    # Load .env.development hoac .env.production
```

```bash
# Chay voi environment cu the
ENVIRONMENT=production docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d
```

## Profiles (Compose 2.1+)

```yaml
services:
  api:
    image: myapp:latest

  debug:
    image: busybox
    profiles:
      - debug                # Chi start khi chi dinh profile

  monitoring:
    image: prom/prometheus
    profiles:
      - monitoring
```

```bash
# Chi start api (khong co debug, monitoring)
docker compose up -d

# Start them debug tools
docker compose --profile debug up -d

# Start tat ca
docker compose --profile debug --profile monitoring up -d
```
