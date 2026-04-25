# Docker Networking

> Ngay bat dau: ___

## Network drivers

| Driver | Mo ta | Khi nao dung |
|--------|-------|--------------|
| bridge | Default, isolate tren 1 host | Development, single host |
| host | Dung truc tiep network cua host | Performance critical |
| overlay | Ket noi containers giua nhieu hosts | Docker Swarm, multi-host |
| none | Khong co network | Security isolation |

## Lenh quan ly network

```bash
# List
docker network ls

# Tao network
docker network create my-network
docker network create --driver bridge --subnet 172.20.0.0/16 my-network

# Kiem tra chi tiet
docker network inspect my-network

# Ket noi/ngat container
docker network connect my-network container-name
docker network disconnect my-network container-name

# Xoa
docker network rm my-network
docker network prune           # Xoa tat ca unused networks
```

## DNS trong Docker

```bash
# Containers trong cung network goi nhau bang ten
docker network create app-net
docker run -d --name db --network app-net postgres:15
docker run -d --name api --network app-net myapp

# Tu api, goi db bang ten:
docker exec api sh -c "getent hosts db"
# → 172.20.0.2  db

# Docker DNS server: 127.0.0.11
```

## Port mapping

```bash
docker run -d -p 8080:80 nginx           # host:container
docker run -d -p 127.0.0.1:8080:80 nginx # Chi bind localhost
docker run -d -P nginx                    # Random port
docker port container-name               # Xem port mapping
```

## Troubleshooting

```bash
# Container co the goi nhau khong?
docker exec api ping db
docker exec api sh -c "nc -zv db 5432"

# Xem IP cua container
docker inspect container-name --format '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}'

# Xem network cua container
docker inspect container-name --format '{{json .NetworkSettings.Networks}}' | python3 -m json.tool
```
