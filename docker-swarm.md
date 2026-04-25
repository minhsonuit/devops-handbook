# Docker Swarm

## Khoi tao cluster

```bash
docker swarm init
docker swarm join-token worker
docker swarm join-token manager
docker node ls
docker node inspect <node>
docker node update --availability drain <node>
```

## Quan ly service

```bash
docker service ls
docker service ps myservice
docker service inspect myservice
docker service scale myservice=3
docker service update --image nginx:latest myservice
docker service update --rollback myservice
docker service rm myservice
```

## Quan ly stack

```bash
docker stack deploy -c docker-compose.yml mystack
docker stack ls
docker stack services mystack
docker stack ps mystack
docker stack rm mystack
```

## Ky thuat quan trong

### 1. Rolling update

```bash
docker service update \
  --update-parallelism 1 \
  --update-delay 10s \
  --image myapp:2.0 \
  myservice
```

### 2. Rollback

```bash
docker service update --rollback myservice
```

### 3. Placement constraints

```bash
docker service create \
  --constraint 'node.role==manager' \
  --name admin-ui \
  myimage
```

### 4. Replicated va global

- `replicated`: chi dinh so replica
- `global`: moi node chay 1 replica

### 5. Overlay network

```bash
docker network create --driver overlay app-net
```

### 6. Secrets va configs

```bash
docker secret ls
docker config ls
```

## Debug Swarm

```bash
docker node ls
docker service ls
docker service ps myservice
docker service inspect myservice
docker stack ps mystack
```

## Khi nao dung Swarm

- Can chay nhieu node
- Can replica va rolling update
- Can service discovery noi bo
- Can deploy stack nhanh ma khong muon vao Kubernetes

## Best practices

- Tach node manager va worker ro rang.
- Dung constraints cho workload dac biet.
- Kiem soat update bang rolling update.
- Dung secret/config thay vi nhung truc tiep vao image.
