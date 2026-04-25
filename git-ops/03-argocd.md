# ArgoCD

> Ngay bat dau: ___

## ArgoCD la gi

GitOps operator cho Kubernetes — tu dong sync K8s cluster voi Git repo.

## Cai dat

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Lay password admin
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

# Port forward UI
kubectl port-forward svc/argocd-server -n argocd 8080:443
# Truy cap: https://localhost:8080
```

## ArgoCD CLI

```bash
argocd login localhost:8080
argocd app list
argocd app get my-app
argocd app sync my-app
argocd app history my-app
argocd app rollback my-app REVISION
```

## Application CRD

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-api
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/myorg/k8s-manifests.git
    targetRevision: main
    path: apps/api
  destination:
    server: https://kubernetes.default.svc
    namespace: production
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```
