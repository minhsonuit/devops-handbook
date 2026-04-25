# DevOps Knowledge Base

> Tu .NET Developer → DevOps Engineer
> Background: Senior .NET developer (C#, ASP.NET Core, EF Core, SQL Server)
> Bat dau: 2026-04-25

## Muc tieu

- Hieu sau ve infrastructure: Docker, Kubernetes, Cloud
- Thao tac thanh thao CLI: Linux, networking, scripting
- Monitor va tuning system: Prometheus, Grafana, ELK, APM
- Tu dong hoa: CI/CD, IaC, GitOps
- Bao mat: DevSecOps, hardening, audit
- Van hanh chuyen nghiep: SRE, incident response, capacity planning

## Cau truc thu muc

```
.
├── roadmap/                     ← Lo trinh 5 phases & tracking tien do
│
│── FOUNDATION ──────────────────────────────────────────
├── linux/                       ← Linux CLI, filesystem, processes, users
├── networking/                  ← TCP/IP, DNS, firewall, load balancing
├── scripting/                   ← Bash, PowerShell, automation recipes
│
│── CONTAINERS ──────────────────────────────────────────
├── docker-compose/              ← Multi-container orchestration
├── docker-advanced/             ← Image optimization, BuildKit, hardening
├── nginx/                       ← Reverse proxy, TLS, tuning, security
│
│── ORCHESTRATION & CLOUD ───────────────────────────────
├── kubernetes/                  ← K8s concepts, kubectl, Helm, production
├── ci-cd/                       ← GitHub Actions, Azure DevOps, .NET pipelines
├── cloud/                       ← Azure fundamentals, networking, cost
├── git-ops/                     ← GitOps, Terraform, ArgoCD, IaC patterns
│
│── DATA & MESSAGING ────────────────────────────────────
├── database-ops/                ← PostgreSQL, SQL Server, Redis operations
├── message-queues/              ← Kafka, RabbitMQ, messaging patterns
│
│── OBSERVABILITY & RELIABILITY ─────────────────────────
├── monitoring/                  ← Prometheus, Grafana, ELK, alerting
├── observability/               ← OpenTelemetry, structured logging, tracing
├── sre/                         ← SLO/SLI, error budgets, capacity planning
├── performance-testing/         ← k6 load testing, benchmarking
│
│── SECURITY & OPS ──────────────────────────────────────
├── security/                    ← Container, network, secrets, hardening
├── troubleshooting/             ← Debug methodology, incident response
│
│── WISDOM ──────────────────────────────────────────────
└── lessons-learned/             ← ⭐ Bai hoc xuong mau, anti-patterns, gotchas
```

## Danh muc tai lieu chi tiet

### 🐧 Foundation

| Thu muc | Files | Noi dung |
|---------|-------|----------|
| [linux/](linux/) | 5 | [CLI basics](linux/01-cli-basics.md) · [Filesystem](linux/02-filesystem.md) · [Processes](linux/03-processes.md) · [Users](linux/04-users-and-permissions.md) · [Disk](linux/05-disk-and-storage.md) |
| [networking/](networking/) | 5 | [TCP/IP](networking/01-tcp-ip-basics.md) · [DNS](networking/02-dns.md) · [Firewall](networking/03-firewall.md) · [Load Balancing](networking/04-load-balancing.md) · [Docker Net](networking/05-docker-networking.md) |
| [scripting/](scripting/) | 4 | [Bash basics](scripting/01-bash-basics.md) · [Patterns](scripting/02-bash-patterns.md) · [PowerShell](scripting/03-powershell-for-devops.md) · [Automation](scripting/04-automation-recipes.md) |

### 🐳 Containers

| Thu muc | Files | Noi dung |
|---------|-------|----------|
| [docker-compose/](docker-compose/) | 4 | [Basics](docker-compose/01-basics.md) · [Production patterns](docker-compose/02-patterns.md) · [Multi-env](docker-compose/03-multi-environment.md) · [Networking](docker-compose/04-networking.md) |
| [docker-advanced/](docker-advanced/) | 4 | [Image optimization](docker-advanced/01-image-optimization.md) · [BuildKit](docker-advanced/02-buildkit.md) · [Hardening](docker-advanced/03-production-hardening.md) · [Debugging](docker-advanced/04-debugging-advanced.md) |
| [nginx/](nginx/) | 9+config | [Overview](nginx/01-overview.md) · [Config](nginx/02-config-guide.md) · [Tuning](nginx/03-tuning-and-params.md) · [Monitoring](nginx/04-monitoring-and-logs.md) · [Ops](nginx/05-operations-checklist.md) · [Security](nginx/06-security.md) · [SSL](nginx/07-ssl-certificates.md) · [Troubleshooting](nginx/08-troubleshooting.md) · [Gzip](nginx/09-gzip-and-performance.md) · [Config mau](nginx/nginx.sample.conf) |

### ☸️ Orchestration & Cloud

| Thu muc | Files | Noi dung |
|---------|-------|----------|
| [kubernetes/](kubernetes/) | 7 | [Concepts](kubernetes/01-concepts.md) · [kubectl](kubernetes/02-kubectl.md) · [Networking](kubernetes/03-networking.md) · [Storage](kubernetes/04-storage.md) · [Debugging](kubernetes/05-debugging.md) · [Helm](kubernetes/06-helm.md) · [Production](kubernetes/07-production.md) |
| [ci-cd/](ci-cd/) | 5 | [Concepts](ci-cd/01-concepts.md) · [GitHub Actions](ci-cd/02-github-actions.md) · [Azure DevOps](ci-cd/03-azure-devops.md) · [Docker CI](ci-cd/04-docker-in-ci.md) · [.NET pipeline](ci-cd/05-dotnet-pipeline.md) |
| [cloud/](cloud/) | 5 | [Concepts](cloud/01-cloud-concepts.md) · [Azure](cloud/02-azure-fundamentals.md) · [Networking](cloud/03-azure-networking.md) · [DevOps](cloud/04-azure-devops.md) · [Cost](cloud/05-cost-management.md) |
| [git-ops/](git-ops/) | 4 | [GitOps](git-ops/01-gitops-concepts.md) · [Terraform](git-ops/02-terraform-basics.md) · [ArgoCD](git-ops/03-argocd.md) · [IaC patterns](git-ops/04-iac-patterns.md) |

### 🗄️ Data & Messaging

| Thu muc | Files | Noi dung |
|---------|-------|----------|
| [database-ops/](database-ops/) | 5 | [PostgreSQL](database-ops/01-postgresql.md) · [SQL Server](database-ops/02-sql-server.md) · [Redis](database-ops/03-redis.md) · [Connection pooling](database-ops/04-connection-pooling.md) · [Migration](database-ops/05-migration-strategies.md) |
| [message-queues/](message-queues/) | 3 | [Kafka](message-queues/01-kafka-operations.md) · [RabbitMQ](message-queues/02-rabbitmq.md) · [Patterns](message-queues/03-patterns.md) |

### 📊 Observability & Reliability

| Thu muc | Files | Noi dung |
|---------|-------|----------|
| [monitoring/](monitoring/) | 5+compose | [Overview](monitoring/01-monitoring-overview.md) · [Prometheus](monitoring/02-prometheus.md) · [Grafana](monitoring/03-grafana.md) · [ELK](monitoring/04-elk-stack.md) · [Alerting](monitoring/05-alerting.md) · [Compose mau](monitoring/docker-compose.monitoring.sample.yml) |
| [observability/](observability/) | 4 | [Structured logging](observability/01-structured-logging.md) · [OpenTelemetry](observability/02-opentelemetry.md) · [Tracing](observability/03-distributed-tracing.md) · [APM .NET](observability/04-apm-dotnet.md) |
| [sre/](sre/) | 4 | [SLO/SLI/SLA](sre/01-slo-sli-sla.md) · [Error budgets](sre/02-error-budgets.md) · [Toil reduction](sre/03-toil-reduction.md) · [Capacity planning](sre/04-capacity-planning.md) |
| [performance-testing/](performance-testing/) | 3 | [k6](performance-testing/01-k6-load-testing.md) · [Strategies](performance-testing/02-testing-strategies.md) · [Benchmarking](performance-testing/03-benchmarking.md) |

### 🔒 Security & Operations

| Thu muc | Files | Noi dung |
|---------|-------|----------|
| [security/](security/) | 6 | [Container](security/01-container-security.md) · [Network](security/02-network-security.md) · [Secrets](security/03-secrets-management.md) · [Hardening](security/04-hardening.md) · [Audit](security/05-audit-and-compliance.md) · [Git Secrets](security/06-git-secret-scanning.md) |
| [troubleshooting/](troubleshooting/) | 5 | [Methodology](troubleshooting/01-methodology.md) · [Docker](troubleshooting/02-docker-issues.md) · [Network](troubleshooting/03-network-issues.md) · [Performance](troubleshooting/04-performance.md) · [Incidents](troubleshooting/05-incident-response.md) |

### ⭐ Wisdom

| Thu muc | Files | Noi dung |
|---------|-------|----------|
| [lessons-learned/](lessons-learned/) | 7 | [Production disasters](lessons-learned/01-production-disasters.md) · [Misconceptions](lessons-learned/02-common-misconceptions.md) · [Debug principles](lessons-learned/03-debugging-principles.md) · [Anti-patterns](lessons-learned/04-architecture-antipatterns.md) · [Operational wisdom](lessons-learned/05-operational-wisdom.md) · [.NET gotchas](lessons-learned/06-dotnet-devops-gotchas.md) · [Security horrors](lessons-learned/07-security-horror-stories.md) |

### 📄 Tai lieu goc (root)

| File | Noi dung |
|------|----------|
| [docker-container.md](docker-container.md) | Quan ly Docker container |
| [docker-compose.md](docker-compose.md) | Docker Compose lenh co ban |
| [docker-swarm.md](docker-swarm.md) | Docker Swarm |
| [docker-logs.md](docker-logs.md) | Xem va loc Docker logs |
| [docker-compose.yml](docker-compose.yml) | File compose mau thuc te |
| [nginx-ops.md](nginx-ops.md) | Nginx operations nhanh |
| [grep-cheatsheet.md](grep-cheatsheet.md) | Grep & regex |

## Thong ke

```
📁 20 thu muc chuyen de
📄 100+ files tai lieu
🔧 Moi file co lenh CLI chay duoc ngay
📐 Tat ca config mau co suffix .sample
🇻🇳 Tieng Viet khong dau
```

## Lo trinh hoc

Xem chi tiet: [roadmap/devops-roadmap.md](roadmap/devops-roadmap.md)

```
Phase 1: Foundation      → linux, networking, docker-compose
Phase 2: Operations      → monitoring, nginx, scripting, database-ops
Phase 3: Orchestration   → kubernetes, ci-cd, docker-advanced
Phase 4: Cloud & Scale   → cloud, git-ops, sre, performance-testing
Phase 5: Mastery         → observability, security, lessons-learned
```

## Quy tac chung

- Moi thu muc co `README.md` la muc luc
- Tai lieu viet tieng Viet khong dau de nhanh va thuc dung
- Uu tien **lenh CLI thuc te** hon ly thuyet suong
- Moi topic co **vi du co the chay ngay**
- Ghi chu ngay bat dau hoc de tracking progress
- Config mau dung suffix `.sample` — thay placeholders truoc khi dung
