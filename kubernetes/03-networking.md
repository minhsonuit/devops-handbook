# Kubernetes Networking

> Ngay bat dau: ___

## Service types

```yaml
# ClusterIP — chi truy cap trong cluster
apiVersion: v1
kind: Service
metadata:
  name: api
spec:
  type: ClusterIP
  selector:
    app: api
  ports:
    - port: 80
      targetPort: 5001

# NodePort — expose qua port tren moi node (30000-32767)
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 5001
      nodePort: 30080

# LoadBalancer — tao cloud LB
spec:
  type: LoadBalancer
  ports:
    - port: 80
      targetPort: 5001
```

## Ingress

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  tls:
    - hosts:
        - api.example.com
      secretName: tls-secret
  rules:
    - host: api.example.com
      http:
        paths:
          - path: /api
            pathType: Prefix
            backend:
              service:
                name: api-service
                port:
                  number: 80
          - path: /ui
            pathType: Prefix
            backend:
              service:
                name: ui-service
                port:
                  number: 80
```

## DNS trong K8s

```bash
# Service DNS format:
# <service>.<namespace>.svc.cluster.local
# Vi du: api.default.svc.cluster.local

# Trong cung namespace, chi can ten service:
curl http://api-service:80
```

## NetworkPolicy

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-allow-api-only
spec:
  podSelector:
    matchLabels:
      app: db
  policyTypes:
    - Ingress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: api
      ports:
        - port: 5432
```

## Debug networking

```bash
kubectl run debug --image=busybox -it --rm -- sh
# Trong pod:
wget -qO- http://api-service:80/health
nslookup api-service
```
