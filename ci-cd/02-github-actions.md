# GitHub Actions

> Ngay bat dau: ___

## Workflow co ban

File: `.github/workflows/ci.yml`

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup .NET
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '8.0.x'

      - name: Restore
        run: dotnet restore

      - name: Build
        run: dotnet build --no-restore

      - name: Test
        run: dotnet test --no-build --verbosity normal
```

## Build va push Docker image

```yaml
jobs:
  docker:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and Push
        uses: docker/build-push-action@v5
        with:
          push: true
          tags: |
            myregistry/api:latest
            myregistry/api:${{ github.sha }}
```

## Secrets

Settings → Secrets and variables → Actions → New repository secret

```yaml
# Su dung trong workflow
${{ secrets.DB_PASSWORD }}
${{ secrets.DOCKER_PASSWORD }}
```

## Matrix builds

```yaml
jobs:
  test:
    strategy:
      matrix:
        dotnet-version: ['6.0.x', '8.0.x']
        os: [ubuntu-latest, windows-latest]
    runs-on: ${{ matrix.os }}
    steps:
      - uses: actions/setup-dotnet@v4
        with:
          dotnet-version: ${{ matrix.dotnet-version }}
```
