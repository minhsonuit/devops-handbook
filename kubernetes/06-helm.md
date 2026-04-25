# Helm Charts

> Ngay bat dau: ___

## Helm la gi

Package manager cho Kubernetes — nhu NuGet cho .NET.

## Lenh co ban

```bash
# Cai dat
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm search repo nginx

# Cai chart
helm install my-nginx bitnami/nginx
helm install my-nginx bitnami/nginx -f values.yaml
helm install my-nginx bitnami/nginx --set service.type=NodePort

# Quan ly
helm list
helm status my-nginx
helm upgrade my-nginx bitnami/nginx -f values.yaml
helm rollback my-nginx 1
helm uninstall my-nginx

# Xem template truoc khi install
helm template my-nginx bitnami/nginx -f values.yaml
```

## Tao chart rieng

```bash
helm create my-app
# Tao cau truc:
# my-app/
# ├── Chart.yaml
# ├── values.yaml
# ├── templates/
# │   ├── deployment.yaml
# │   ├── service.yaml
# │   └── ingress.yaml
# └── charts/
```

## values.yaml

```yaml
replicaCount: 3
image:
  repository: myregistry/api
  tag: "v1.0.0"
  pullPolicy: IfNotPresent
service:
  type: ClusterIP
  port: 80
resources:
  limits:
    cpu: 1000m
    memory: 512Mi
  requests:
    cpu: 250m
    memory: 128Mi
```
