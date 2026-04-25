# Kubernetes Debugging

> Ngay bat dau: ___

## Pod khong start

```bash
# 1. Xem trang thai
kubectl get pods
# Pending → khong du resource hoac scheduling issue
# CrashLoopBackOff → app crash lien tuc
# ImagePullBackOff → khong pull duoc image
# ErrImagePull → image khong ton tai hoac auth fail

# 2. Describe de xem events
kubectl describe pod POD_NAME

# 3. Xem logs
kubectl logs POD_NAME
kubectl logs POD_NAME --previous    # Log tu lan chay truoc (crash)

# 4. Exec vao container
kubectl exec -it POD_NAME -- sh
```

## Debug theo trang thai

| Status | Nguyen nhan | Fix |
|--------|-------------|-----|
| Pending | Khong du CPU/RAM | Tang resource hoac them node |
| CrashLoopBackOff | App crash | Xem logs --previous |
| ImagePullBackOff | Image sai hoac auth | Kiem tra image name, registry secret |
| OOMKilled | Het memory | Tang memory limits |
| Evicted | Node het disk | Don dep disk, tang PV |

## Debug network

```bash
# Tao pod debug
kubectl run debug --image=busybox -it --rm -- sh
# Ben trong:
wget -qO- http://service-name:port/health
nslookup service-name
ping service-name

# Port forward de test local
kubectl port-forward svc/api-service 8080:80
curl http://localhost:8080/health
```

## Events

```bash
kubectl get events --sort-by=.lastTimestamp
kubectl get events -n NAMESPACE --field-selector type=Warning
```

## Resource usage

```bash
kubectl top nodes
kubectl top pods
kubectl top pods --sort-by=memory
```
