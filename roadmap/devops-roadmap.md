# DevOps Learning Roadmap

> .NET Developer → DevOps Engineer
> Bat dau: 2026-04-25

## Tong quan lo trinh

```
Phase 1: Foundation (Thang 1-2)
├── Linux essentials
├── Docker mastery
├── Networking fundamentals
└── Bash scripting

Phase 2: Operations (Thang 2-4)
├── Nginx & reverse proxy (da co)
├── Monitoring stack (Prometheus + Grafana)
├── Log management (ELK / Loki)
├── CI/CD pipelines
└── Troubleshooting methodology

Phase 3: Orchestration (Thang 4-6)
├── Kubernetes fundamentals
├── K8s operations & debugging
├── Helm charts
└── GitOps (ArgoCD / Flux)

Phase 4: Cloud & Scale (Thang 6-9)
├── Cloud provider (Azure primary)
├── Infrastructure as Code (Terraform)
├── Security hardening
└── High availability & disaster recovery

Phase 5: Mastery (Thang 9-12)
├── Performance tuning
├── Chaos engineering
├── Cost optimization
└── Architecture patterns
```

## Chi tiet tung phase

### Phase 1: Foundation

| STT | Topic | Thu muc | Trang thai | Ghi chu |
|-----|-------|---------|------------|---------|
| 1.1 | Linux CLI co ban | `linux/` | 🟡 Bat dau | cd, ls, grep, find, ps, top, df |
| 1.2 | Linux filesystem & permissions | `linux/` | ⬜ Chua | /etc, /var, chmod, chown |
| 1.3 | Linux processes & systemd | `linux/` | ⬜ Chua | ps, kill, systemctl, journalctl |
| 1.4 | Docker fundamentals | `docker/` | ✅ Co ban | Container, image, volume, network |
| 1.5 | Docker Compose | `docker-compose/` | ✅ Co ban | Multi-service, depends_on, networks |
| 1.6 | Docker Swarm | `docker/` | ✅ Co ban | Service, stack, overlay network |
| 1.7 | Networking: TCP/IP, DNS | `networking/` | ⬜ Chua | IP, port, DNS, firewall |
| 1.8 | Bash scripting | `scripting/` | ⬜ Chua | Variables, loops, functions |

### Phase 2: Operations

| STT | Topic | Thu muc | Trang thai | Ghi chu |
|-----|-------|---------|------------|---------|
| 2.1 | Nginx mastery | `nginx/` | ✅ Day du | 9 files + config mau |
| 2.2 | Prometheus + Grafana | `monitoring/` | ⬜ Chua | Metrics, alerting, dashboards |
| 2.3 | ELK / Loki stack | `monitoring/` | ⬜ Chua | Log aggregation |
| 2.4 | CI/CD pipelines | `ci-cd/` | ⬜ Chua | GitHub Actions, Azure DevOps |
| 2.5 | Troubleshooting method | `troubleshooting/` | ⬜ Chua | Root cause analysis framework |
| 2.6 | Docker production tips | `docker/` | 🟡 Bat dau | Multi-stage, health check, resource limits |

### Phase 3: Orchestration

| STT | Topic | Thu muc | Trang thai | Ghi chu |
|-----|-------|---------|------------|---------|
| 3.1 | K8s concepts | `kubernetes/` | ⬜ Chua | Pod, Service, Deployment, ConfigMap |
| 3.2 | K8s networking | `kubernetes/` | ⬜ Chua | Ingress, Service types, NetworkPolicy |
| 3.3 | K8s storage | `kubernetes/` | ⬜ Chua | PV, PVC, StorageClass |
| 3.4 | K8s operations | `kubernetes/` | ⬜ Chua | kubectl, debugging, rollout |
| 3.5 | Helm charts | `kubernetes/` | ⬜ Chua | Template, values, dependencies |
| 3.6 | GitOps (ArgoCD) | `git-ops/` | ⬜ Chua | Declarative, auto-sync |

### Phase 4: Cloud & Scale

| STT | Topic | Thu muc | Trang thai | Ghi chu |
|-----|-------|---------|------------|---------|
| 4.1 | Azure fundamentals | `cloud/` | ⬜ Chua | AKS, App Service, Azure DevOps |
| 4.2 | Terraform basics | `git-ops/` | ⬜ Chua | HCL, state, modules |
| 4.3 | Container security | `security/` | ⬜ Chua | Image scanning, runtime security |
| 4.4 | Network security | `security/` | ⬜ Chua | TLS, mTLS, service mesh |
| 4.5 | HA & DR | `cloud/` | ⬜ Chua | Multi-AZ, backup, failover |

## Nguyen tac hoc

1. **Hoc theo van de thuc te** — khong hoc suong, lay van de dang gap de hoc
2. **Hands-on first** — chay lenh truoc, doc ly thuyet sau
3. **Ghi chep moi ngay** — moi buoi hoc tao/cap nhat it nhat 1 file
4. **Review dinh ky** — moi 2 tuan review lai nhung gi da hoc
5. **Lien ket voi .NET** — lien he voi kien thuc .NET dev da co

## Tracking tien do

Moi khi hoan thanh 1 topic:
- Doi trang thai tu ⬜ → 🟡 (dang hoc) → ✅ (da co)
- Ghi ngay hoan thanh
- Cap nhat README.md cua thu muc tuong ung
