# Docker Advanced Debugging

> Ngay bat dau: ___

## Debug container dang chay

```bash
# Vao container (co shell)
docker exec -it CONTAINER sh
docker exec -it CONTAINER bash

# Container khong co shell → dung debug image
docker run -it --rm --pid=container:CONTAINER --net=container:CONTAINER \
  nicolaka/netshoot bash
# netshoot co: curl, dig, nslookup, tcpdump, ss, iptables, strace...
```

## Xem processes tu host

```bash
# Tim PID cua container tren host
docker inspect CONTAINER --format '{{.State.Pid}}'

# Xem processes cua container tu host
docker top CONTAINER
docker top CONTAINER aux

# Nsenter — vao namespace cua container tu host
PID=$(docker inspect CONTAINER --format '{{.State.Pid}}')
nsenter -t $PID -n ss -tlnp           # Xem network
nsenter -t $PID -m cat /etc/hosts     # Xem files
nsenter -t $PID -p ps aux             # Xem processes
```

## Strace — trace system calls

```bash
# Cai strace trong container (hoac dung netshoot)
docker exec -it CONTAINER strace -p 1 -f

# Tu host
PID=$(docker inspect CONTAINER --format '{{.State.Pid}}')
strace -p $PID -f -e trace=network    # Chi network calls
strace -p $PID -f -e trace=file       # Chi file operations
```

## Filesystem debugging

```bash
# Xem filesystem changes
docker diff CONTAINER
# A = Added, C = Changed, D = Deleted

# Xem storage driver
docker info | grep "Storage Driver"

# Xem disk usage chi tiet
docker system df -v

# Overlay2 — xem layers
docker inspect CONTAINER --format '{{.GraphDriver.Data.MergedDir}}'
ls $(docker inspect CONTAINER --format '{{.GraphDriver.Data.MergedDir}}')
```

## Network debugging

```bash
# Tcpdump trong container
docker exec CONTAINER tcpdump -i eth0 -n port 5001

# Tu host (vao network namespace)
PID=$(docker inspect CONTAINER --format '{{.State.Pid}}')
nsenter -t $PID -n tcpdump -i eth0 -n port 5001

# Kiem tra DNS
docker exec CONTAINER cat /etc/resolv.conf
docker exec CONTAINER getent hosts service-name

# Kiem tra connectivity
docker exec CONTAINER curl -v http://other-service:port
```

## Export container de phan tich

```bash
# Export filesystem
docker export CONTAINER > container_fs.tar
tar -tf container_fs.tar | head -50

# Save image (bao gom layers)
docker save myapp:latest > image.tar
docker save myapp:latest | gzip > image.tar.gz

# Load lai
docker load < image.tar.gz
```

## Inspect chi tiet

```bash
# Xem tat ca thong tin
docker inspect CONTAINER | jq .

# Nhung truong hay dung
docker inspect CONTAINER --format '{{.State.Status}}'
docker inspect CONTAINER --format '{{.State.StartedAt}}'
docker inspect CONTAINER --format '{{.State.OOMKilled}}'
docker inspect CONTAINER --format '{{.HostConfig.Memory}}'
docker inspect CONTAINER --format '{{json .NetworkSettings.Networks}}' | jq .
docker inspect CONTAINER --format '{{json .Mounts}}' | jq .
docker inspect CONTAINER --format '{{.Config.Env}}' | tr ' ' '\n'
```
