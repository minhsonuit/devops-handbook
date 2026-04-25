# Kubernetes Production

> Ngay bat dau: ___

## Resource limits (bat buoc)

```yaml
resources:
  requests:          # Minimum guaranteed
    memory: "128Mi"
    cpu: "250m"      # 0.25 CPU core
  limits:            # Maximum allowed
    memory: "512Mi"
    cpu: "1000m"     # 1 CPU core
```

## Health checks

```yaml
livenessProbe:       # Restart neu fail
  httpGet:
    path: /health
    port: 5001
  initialDelaySeconds: 30
  periodSeconds: 10

readinessProbe:      # Bo khoi Service neu fail
  httpGet:
    path: /ready
    port: 5001
  initialDelaySeconds: 5
  periodSeconds: 5
```

## HPA (Horizontal Pod Autoscaler)

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: api-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: api
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
```

## PDB (Pod Disruption Budget)

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: api-pdb
spec:
  minAvailable: 1     # Luon giu it nhat 1 pod
  selector:
    matchLabels:
      app: api
```

## Security

```yaml
spec:
  containers:
    - name: api
      securityContext:
        runAsNonRoot: true
        runAsUser: 1000
        readOnlyRootFilesystem: true
        allowPrivilegeEscalation: false
```
