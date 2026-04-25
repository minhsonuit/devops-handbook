# Kubernetes Storage

> Ngay bat dau: ___

## Concepts

```
PersistentVolume (PV)      — Tai nguyen storage thuc te
PersistentVolumeClaim (PVC) — Yeu cau storage tu Pod
StorageClass               — Loai storage (SSD, HDD, cloud disk)
```

## PVC va PV

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: postgres-data
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: standard
---
# Dung trong Pod/Deployment
spec:
  containers:
    - name: db
      image: postgres:15
      volumeMounts:
        - mountPath: /var/lib/postgresql/data
          name: db-storage
  volumes:
    - name: db-storage
      persistentVolumeClaim:
        claimName: postgres-data
```

## Access modes

| Mode | Mo ta |
|------|-------|
| ReadWriteOnce (RWO) | 1 node doc/ghi |
| ReadOnlyMany (ROX) | Nhieu node doc |
| ReadWriteMany (RWX) | Nhieu node doc/ghi |

## Lenh quan ly

```bash
kubectl get pv
kubectl get pvc
kubectl get storageclass
kubectl describe pvc postgres-data
```
