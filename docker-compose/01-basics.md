# Docker Compose Basics

> Ngay bat dau: ___

## Cau truc file docker-compose.yml

```yaml
version: "3.8"          # Phien ban compose file (nen dung 3.8+)

services:               # Danh sach services
  web:
    image: nginx:latest
    ports:
      - "80:80"
    depends_on:
      - api

  api:
    build: ./api         # Build tu Dockerfile
    ports:
      - "5001:5001"
    environment:
      - ASPNETCORE_ENVIRONMENT=Production
    volumes:
      - ./config:/app/config

volumes:                 # Named volumes
  db_data:

networks:                # Custom networks
  backend:
```

## Lenh co ban

```bash
# Khoi dong tat ca services
docker compose up -d

# Xem trang thai
docker compose ps

# Xem logs
docker compose logs
docker compose logs -f api          # Follow log cua 1 service
docker compose logs --tail 50 api   # 50 dong cuoi

# Dung va xoa
docker compose down                 # Dung + xoa container
docker compose down -v              # Dung + xoa ca volumes
docker compose down --rmi all       # Dung + xoa ca images

# Restart 1 service
docker compose restart api

# Build lai image
docker compose build api
docker compose up -d --build api    # Build + restart
```

## Services

```yaml
services:
  api:
    image: myapp:latest              # Dung image co san
    # HOAC
    build:                           # Build tu Dockerfile
      context: ./src
      dockerfile: Dockerfile
    
    container_name: my-api           # Dat ten container cu the
    
    ports:
      - "5001:5001"                  # host:container
      - "5002:5002/udp"              # UDP port
    
    environment:                     # Bien moi truong
      - DB_HOST=db
      - DB_PORT=5432
    
    env_file:                        # Doc tu file
      - .env
    
    volumes:
      - ./config:/app/config         # Bind mount
      - app_data:/app/data           # Named volume
    
    restart: unless-stopped          # Restart policy
    
    depends_on:                      # Thu tu khoi dong
      - db
      - redis
```

## depends_on nang cao (v3.8+)

```yaml
services:
  api:
    depends_on:
      db:
        condition: service_healthy   # Doi db healthy moi start
      redis:
        condition: service_started   # Chi doi start

  db:
    image: postgres:15
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
```

## Networks

```yaml
services:
  api:
    networks:
      - frontend
      - backend
  
  db:
    networks:
      - backend              # DB chi noi voi backend network

networks:
  frontend:
  backend:
    driver: bridge
```

Trong cung network, services goi nhau bang **ten service** (Docker DNS tu dong resolve).

```bash
# Tu container api, goi db bang ten:
curl http://db:5432
```

## Volumes

```yaml
services:
  db:
    volumes:
      - db_data:/var/lib/postgresql/data   # Persist data

volumes:
  db_data:                    # Docker quan ly
    driver: local
```

```bash
# Xem volumes
docker volume ls
docker volume inspect db_data
```

## Lenh debug

```bash
# Vao shell cua container
docker compose exec api sh
docker compose exec api bash

# Chay 1 lenh trong container
docker compose exec db psql -U postgres

# Xem config sau khi merge
docker compose config

# Xem events
docker compose events
```
