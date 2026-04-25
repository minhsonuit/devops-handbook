# Docker Container

## Lenh co ban

```bash
docker ps
docker ps -a
docker images
docker pull nginx
docker run -d --name web -p 8080:80 nginx
docker stop web
docker start web
docker restart web
docker rm web
docker exec -it web sh
docker inspect web
docker stats
docker cp file.txt web:/tmp/
docker top web
docker port web
```

## Lenh quan sat va kiem tra

```bash
docker inspect <container>
docker inspect <container> --format='{{.State.ExitCode}}'
docker stats
docker top <container>
docker port <container>
```

## Ky thuat hay dung

### 1. Mapping port

```bash
docker run -d --name web -p 8080:80 nginx
```

### 2. Mount source code vao container

```bash
docker run -v $(pwd):/app node:20
```

### 3. Truyen bien moi truong

```bash
docker run -e NODE_ENV=production -e PORT=3000 myapp
```

### 4. Gioi han tai nguyen

```bash
docker run --cpus=1 --memory=512m myapp
```

### 5. Dat healthcheck

```bash
docker run \
  --health-cmd="curl -f http://localhost:3000/health || exit 1" \
  --health-interval=30s \
  --health-timeout=5s \
  --health-retries=3 \
  myapp
```

### 6. Lam viec voi network

```bash
docker network ls
docker network inspect bridge
docker network create mynet
docker run -d --network mynet --name redis redis
docker run -it --network mynet alpine sh
```

### 7. Copy file ra vao container

```bash
docker cp ./local.txt web:/tmp/local.txt
docker cp web:/tmp/output.log ./output.log
```

## Debug nhanh

### Container vua thoat

```bash
docker ps -a
docker logs <container>
docker inspect <container>
```

### Kiem tra process ben trong

```bash
docker exec -it <container> sh
docker top <container>
```

### Kiem tra ket noi mang

```bash
docker exec -it <container> ping redis
docker exec -it <container> nslookup redis
```

## Best practices

- Dat ten container ro rang.
- Khong luu du lieu quan trong trong layer container.
- Day log ra `stdout/stderr`.
- Dung volume cho data can ton tai lau dai.
- Dung image nho gon va co version cu the, vi du `nginx:1.27`.
