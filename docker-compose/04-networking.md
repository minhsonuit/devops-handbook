# Docker Compose Networking

> Ngay bat dau: ___

## Mac dinh

Docker Compose tu tao 1 network cho tat ca services trong file:

```bash
# Ten mac dinh: {project_name}_default
docker network ls | grep myproject
```

Services goi nhau bang **ten service** (Docker DNS):

```bash
# Tu api container goi db container
curl http://db:5432
```

## Custom networks

```yaml
services:
  nginx:
    networks:
      - frontend
      - backend

  api:
    networks:
      - backend

  db:
    networks:
      - backend      # DB chi nhin thay API, khong nhin thay nginx

networks:
  frontend:
  backend:
```

## External network (chia se giua cac compose)

```yaml
# Compose file 1: tao network
networks:
  shared:
    name: my_shared_network

# Compose file 2: dung network da co
networks:
  shared:
    external: true
    name: my_shared_network
```

## DNS va service discovery

```bash
# Xem DNS resolution trong container
docker compose exec api sh -c "getent hosts db"
docker compose exec api sh -c "nslookup db"

# Kiem tra network
docker network inspect myproject_backend
```

## Debug networking

```bash
# Xem network cua container
docker inspect CONTAINER --format '{{json .NetworkSettings.Networks}}'

# Ping giua cac container
docker compose exec api ping db

# Xem port listening
docker compose exec api ss -tlnp
```
