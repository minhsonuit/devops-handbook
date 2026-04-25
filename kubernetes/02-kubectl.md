# kubectl Cheat Sheet

> Ngay bat dau: ___

## Config & Context

```bash
kubectl config view
kubectl config get-contexts
kubectl config use-context my-cluster
kubectl config set-context --current --namespace=staging
```

## Get resources

```bash
kubectl get pods
kubectl get pods -o wide                    # Them IP, Node
kubectl get pods -A                         # Tat ca namespaces
kubectl get pods -l app=api                 # Filter theo label
kubectl get pods --sort-by=.status.startTime

kubectl get svc                             # Services
kubectl get deploy                          # Deployments
kubectl get nodes
kubectl get all                             # Tat ca resources
kubectl get events --sort-by=.lastTimestamp
```

## Describe & Debug

```bash
kubectl describe pod POD_NAME
kubectl describe svc SERVICE_NAME
kubectl describe deploy DEPLOY_NAME
kubectl describe node NODE_NAME
```

## Logs

```bash
kubectl logs POD_NAME
kubectl logs POD_NAME -c CONTAINER          # Multi-container pod
kubectl logs POD_NAME -f                    # Follow
kubectl logs POD_NAME --tail 100
kubectl logs POD_NAME --since 30m
kubectl logs -l app=api --all-containers    # Logs theo label
```

## Exec

```bash
kubectl exec -it POD_NAME -- sh
kubectl exec -it POD_NAME -- bash
kubectl exec POD_NAME -- cat /app/appsettings.json
kubectl exec POD_NAME -- env | grep DB
```

## Apply & Delete

```bash
kubectl apply -f deployment.yml
kubectl apply -f ./k8s/                     # Apply tat ca file trong dir
kubectl delete -f deployment.yml
kubectl delete pod POD_NAME
kubectl delete pod POD_NAME --force --grace-period=0
```

## Scale & Rollout

```bash
kubectl scale deploy api --replicas=5
kubectl rollout status deploy api
kubectl rollout history deploy api
kubectl rollout undo deploy api             # Rollback
kubectl rollout undo deploy api --to-revision=2
```

## Port forward (debug local)

```bash
kubectl port-forward pod/POD_NAME 8080:5001
kubectl port-forward svc/api-service 8080:80
# Sau do truy cap: http://localhost:8080
```

## Copy files

```bash
kubectl cp POD_NAME:/path/to/file ./local-file
kubectl cp ./local-file POD_NAME:/path/to/file
```
