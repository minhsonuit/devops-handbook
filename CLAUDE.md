# CLAUDE.md — Claude Code Agent Config

## Project Overview

DevOps Knowledge Base — structured learning repository for a senior .NET developer building DevOps expertise. Contains operational docs, config samples, CLI references, and hands-on guides.

## Critical Rules

1. **Ask before guessing** — when information is ambiguous, ask for clarification
2. **Use MCP tools first** — search databases, containers, logs via MCP before relying on internal knowledge
3. **Vietnamese (no diacritics)** — all documentation in this repo uses Vietnamese without accents
4. **Practical over theoretical** — every topic must include runnable CLI examples
5. **Respect directory structure** — place content in the correct topic directory
6. **Update indexes** — always update the directory's README.md when adding files

## Directory Layout

| Directory | Content |
|-----------|---------|
| `roadmap/` | Learning roadmap, progress tracking |
| `docker/` | Docker fundamentals, container operations |
| `docker-compose/` | Multi-container orchestration |
| `nginx/` | Reverse proxy, TLS, security, tuning (mature) |
| `linux/` | Linux CLI, filesystem, processes, systemd |
| `networking/` | TCP/IP, DNS, firewall, load balancing |
| `monitoring/` | Prometheus, Grafana, ELK, APM |
| `kubernetes/` | K8s concepts, manifests, operations |
| `ci-cd/` | CI/CD pipelines (GitHub Actions, Azure DevOps) |
| `cloud/` | AWS, Azure, GCP fundamentals |
| `security/` | DevSecOps, container security, hardening |
| `scripting/` | Bash, PowerShell for automation |
| `troubleshooting/` | Debugging methodology, root cause analysis |
| `git-ops/` | GitOps workflows, Infrastructure as Code |

## Content Standards

- Numbered files for ordering: `01-basics.md`, `02-advanced.md`
- Config samples use `.sample` suffix
- Cross-reference with relative links
- Each file starts with `# Title` and includes practical examples
- No hardcoded secrets — use placeholders

## User Context

- **Background**: Senior .NET developer (C#, ASP.NET Core, EF Core, SQL Server)
- **Current skills**: Docker basics, Nginx, PostgreSQL, Redis, basic Linux
- **Learning goals**: Kubernetes, Cloud (Azure primary), monitoring stack, CI/CD, Linux admin
- **Work domain**: POS systems, API services, retail/pharmacy

## Available MCP Servers

| Server | Use for |
|--------|---------|
| `docker` | Container inspection, logs |
| `mssql` | SQL Server queries |
| `postgres` | PostgreSQL queries |
| `redis` | Redis key inspection |
| `elasticsearch` | Log searching |
| `ax-schema` | AX database schema |
| `ax2012-code-reader` | AX 2012 X++ source code |
