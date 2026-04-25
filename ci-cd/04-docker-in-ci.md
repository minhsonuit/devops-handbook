# Docker in CI

> Ngay bat dau: ___

## Dockerfile cho .NET

```dockerfile
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
COPY *.csproj .
RUN dotnet restore
COPY . .
RUN dotnet publish -c Release -o /app

FROM mcr.microsoft.com/dotnet/aspnet:8.0
WORKDIR /app
COPY --from=build /app .
EXPOSE 5001
HEALTHCHECK CMD curl -f http://localhost:5001/health || exit 1
ENTRYPOINT ["dotnet", "MyApp.dll"]
```

## Build va push trong CI

```bash
# Build
docker build -t myregistry/api:$(git rev-parse --short HEAD) .
docker build -t myregistry/api:latest .

# Push
docker login myregistry.azurecr.io -u $USERNAME -p $PASSWORD
docker push myregistry/api:$(git rev-parse --short HEAD)
docker push myregistry/api:latest
```

## Best practices

- Dung multi-stage build de giam image size
- Tag bang commit SHA, khong chi dung `latest`
- Scan image truoc khi push: `docker scout cves myapp:latest`
- Dung `.dockerignore` de loai file khong can
- Cache layer: copy csproj truoc, restore, roi moi copy source
