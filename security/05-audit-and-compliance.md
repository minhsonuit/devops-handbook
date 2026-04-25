# Audit & Compliance

> Ngay bat dau: ___

## Logging cho audit

- Tat ca access phai co log
- Log ai, lam gi, luc nao, tu dau
- Khong duoc xoa hoac sua log (immutable)

## RBAC (Role-Based Access Control)

```yaml
# K8s RBAC
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer
rules:
  - apiGroups: [""]
    resources: ["pods", "services"]
    verbs: ["get", "list", "watch"]
  - apiGroups: ["apps"]
    resources: ["deployments"]
    verbs: ["get", "list"]
```

## Audit tools

| Tool | Muc dich |
|------|----------|
| `auditd` | Linux system audit |
| Azure Activity Log | Azure resource changes |
| K8s Audit Log | API server requests |
| Docker events | Container lifecycle |

```bash
# Docker events
docker events --since 1h
docker events --filter type=container --filter event=die
```
