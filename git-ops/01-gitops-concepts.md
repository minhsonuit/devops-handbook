# GitOps Concepts

> Ngay bat dau: ___

## GitOps la gi

Git la **single source of truth** cho infrastructure va application.

```
Developer push code → Git → CI build → Image pushed to registry
                        ↓
Git (k8s manifests) → GitOps operator (ArgoCD/Flux) → K8s cluster
```

## Principles

1. **Declarative** — mo ta trang thai mong muon, khong phai buoc thuc hien
2. **Versioned** — moi thay doi co history trong Git
3. **Automated** — tu dong ap dung thay doi
4. **Self-healing** — tu dong sua khi trang thai lech

## Push vs Pull model

| | Push | Pull |
|---|------|------|
| AI | CI pipeline push changes | GitOps operator pull changes |
| Vi du | kubectl apply trong CI | ArgoCD sync tu Git |
| Security | CI can cluster access | Chi operator can cluster access |
| Khuyen nghi | Nho, don gian | Production, enterprise |
