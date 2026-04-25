# Kubernetes Concepts

> Ngay bat dau: ___

## K8s la gi

Kubernetes = he thong orchestration container ở quy mo lon.

```
kubectl → API Server → etcd (state store)
                     → Scheduler → assign Pod to Node
                     → Controller Manager → ensure desired state
                     → kubelet (on each Node) → run containers
```

## Core objects

### Pod — don vi nho nhat

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app
spec:
  containers:
    - name: app
      image: myapp:v1
      ports:
        - containerPort: 5001
```

### Deployment — quan ly Pods

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: api
  template:
    metadata:
      labels:
        app: api
    spec:
      containers:
        - name: api
          image: myapp:v1
          ports:
            - containerPort: 5001
          resources:
            requests:
              memory: "128Mi"
              cpu: "250m"
            limits:
              memory: "512Mi"
              cpu: "1000m"
```

### Service — expose Pods ra network

```yaml
apiVersion: v1
kind: Service
metadata:
  name: api-service
spec:
  selector:
    app: api
  ports:
    - port: 80
      targetPort: 5001
  type: ClusterIP          # Chi truy cap noi bo cluster
```

| Service Type | Mo ta |
|-------------|-------|
| ClusterIP | Internal only (default) |
| NodePort | Expose qua port tren moi node |
| LoadBalancer | Tao cloud load balancer |

### ConfigMap & Secret

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  ASPNETCORE_ENVIRONMENT: "Production"
  DB_HOST: "postgres-service"
---
apiVersion: v1
kind: Secret
metadata:
  name: app-secrets
type: Opaque
data:
  DB_PASSWORD: "BASE64_ENCODED_PASSWORD"    # base64 encoded
```

### Namespace

```bash
kubectl create namespace staging
kubectl get pods -n staging
kubectl get pods --all-namespaces
```
