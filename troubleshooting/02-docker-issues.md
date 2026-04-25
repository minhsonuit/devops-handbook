# Docker Issues

> Ngay bat dau: ___

## Container khong start

```bash
# Xem trang thai
docker ps -a | grep CONTAINER

# Xem log
docker logs CONTAINER

# Xem events
docker events --since 10m --filter container=CONTAINER

# Common issues:
# - Port da bi dung: "port is already allocated"
# - Image khong co: "No such image"
# - Permission denied: volume mount
# - OOM killed: het memory
```

## OOM Killed

```bash
# Kiem tra
docker inspect CONTAINER --format='{{.State.OOMKilled}}'
# true → container bi kill do het memory

# Fix: tang memory limit
docker run -m 512m myapp
# Hoac trong compose: deploy.resources.limits.memory
```

## Volume permissions

```bash
# Container chay user khac, khong doc/ghi duoc volume
# Fix: chown tren host
sudo chown -R 1000:1000 /path/to/volume

# Hoac chay container voi user root (khong khuyen khich)
docker run --user root myapp
```

## Network issues

```bash
# Container khong goi duoc nhau
docker network inspect NETWORK
docker exec CONTAINER ping OTHER_CONTAINER
docker exec CONTAINER getent hosts OTHER_CONTAINER

# Fix: dam bao cung network
docker network connect NETWORK CONTAINER
```

## Disk full

```bash
docker system df              # Xem Docker dung bao nhieu
docker system prune -a        # Xoa tat ca unused
docker volume prune           # Xoa unused volumes
docker image prune -a         # Xoa unused images
docker builder prune          # Xoa build cache
```
