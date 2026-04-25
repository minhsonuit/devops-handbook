# AGENTS.md — Codex / OpenAI Agent Config

## Project Context

This is a **DevOps Knowledge Base** — a structured learning repository built by a senior .NET developer transitioning into DevOps engineering. The repository contains operational documentation, configuration samples, CLI cheat sheets, and hands-on guides.

## Repository Purpose

- Document infrastructure knowledge: Docker, Kubernetes, Cloud, Networking
- Collect practical CLI commands, configs, and troubleshooting guides
- Track learning progress over time
- Serve as a quick-reference ops handbook for production systems

## Key Conventions

1. **Language**: All documentation is written in Vietnamese (khong dau — no diacritics) for speed
2. **Structure**: Each topic has its own directory with a `README.md` index
3. **Practical first**: Prioritize runnable CLI examples over abstract theory
4. **Real configs**: Include real-world config samples (nginx, docker-compose, k8s manifests)
5. **No secrets**: Config files use placeholders like `YOUR_DOMAIN`, `YOUR_PASSWORD`

## Directory Structure

```
├── roadmap/           ← Learning roadmap & progress tracking
├── docker/            ← Docker fundamentals & operations
├── docker-compose/    ← Multi-container orchestration
├── nginx/             ← Reverse proxy, TLS, tuning
├── linux/             ← Linux CLI, filesystem, processes
├── networking/        ← TCP/IP, DNS, firewall, load balancing
├── monitoring/        ← Prometheus, Grafana, ELK
├── kubernetes/        ← K8s concepts & operations
├── ci-cd/             ← CI/CD pipelines
├── cloud/             ← AWS / Azure / GCP
├── security/          ← DevSecOps, hardening
├── scripting/         ← Bash, PowerShell
├── troubleshooting/   ← Debugging methodology
└── git-ops/           ← GitOps, Infrastructure as Code
```

## How to Contribute as an AI Agent

- When creating new content, place it in the appropriate topic directory
- Always update the directory's `README.md` when adding new files
- Use numbered filenames for ordering: `01-topic.md`, `02-topic.md`
- Include practical examples with real commands
- When creating config samples, use `.sample` suffix: `prometheus.sample.yml`
- Cross-reference related docs using relative links

## User Background

- Senior .NET developer (C#, ASP.NET Core, Entity Framework)
- Familiar with: SQL Server, PostgreSQL, Redis, Docker basics
- Learning: Kubernetes, Cloud (Azure primary), monitoring, CI/CD, Linux administration
- Work context: POS systems, API services, retail/pharmacy domain
