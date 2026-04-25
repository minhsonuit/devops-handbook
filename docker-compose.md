# Docker Compose

## Lenh co ban

```bash
docker compose up -d
docker compose down
docker compose ps
docker compose restart
docker compose start
docker compose stop
docker compose build
docker compose pull
docker compose exec app sh
docker compose config
docker compose top
docker compose images
```

## Lenh hay dung trong van hanh

### Chay mot service rieng

```bash
docker compose up -d redis
```

### Scale service

```bash
docker compose up -d --scale api=3
```

### Recreate service

```bash
docker compose up -d --force-recreate
```

### Khong recreate dependency

```bash
docker compose up -d --no-deps app
```

### Validate file compose

```bash
docker compose config
```

### Dung nhieu file compose

```bash
docker compose -f docker-compose.yml -f docker-compose.override.yml up -d
```

## Ky thuat viet compose

### 1. Dung `.env`

- Tach cau hinh khoi file compose
- De doi moi truong dev, staging, production

### 2. Dung `depends_on`

- Dieu phoi thu tu khoi dong
- Nen ket hop `healthcheck` de giam loi khoi dong som

### 3. Dung `healthcheck`

```yaml
services:
  app:
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s
      timeout: 5s
      retries: 3
```

### 4. Dung volume hop ly

- Bind mount cho code khi dev
- Named volume cho database va data ben vung

### 5. Dung profile

```yaml
services:
  mailhog:
    profiles: ["dev"]
```

Chay:

```bash
docker compose --profile dev up -d
```

## Debug Compose

```bash
docker compose ps
docker compose logs -f
docker compose logs app
docker compose exec app sh
docker compose top
```

## Best practices

- Tach service ro vai tro: `app`, `db`, `redis`, `nginx`.
- Khong hard-code secret trong file compose.
- Dung `restart` policy khi can.
- Kiem tra `docker compose config` truoc khi deploy.
- Giu file compose gon, tach file override khi can.
