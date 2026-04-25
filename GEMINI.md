# GEMINI.md — Google Gemini Agent Config

## Du an nay la gi

Day la **DevOps Knowledge Base** — kho kien thuc co cau truc, xay dung boi mot .NET developer dang chuyen sang DevOps. Repo nay chua tai lieu van hanh, config mau, CLI cheat sheet, va huong dan thuc hanh.

## Quy tac bat buoc

1. **Luon hoi lai** khi thong tin mo ho — khong doan
2. **Dung tool MCP** de tra cuu truoc khi tra loi tu internal knowledge
3. **Viet tieng Viet khong dau** trong tat ca tai lieu
4. **Uu tien thuc hanh** — moi topic phai co lenh CLI chay duoc ngay
5. **Khong tao file ngoai cau truc** — dat dung thu muc theo topic

## Cau truc thu muc

```
├── roadmap/           ← Lo trinh & tracking
├── docker/            ← Docker fundamentals
├── docker-compose/    ← Multi-container
├── nginx/             ← Reverse proxy (da day du)
├── linux/             ← Linux essentials
├── networking/        ← Mang
├── monitoring/        ← Giam sat
├── kubernetes/        ← K8s
├── ci-cd/             ← CI/CD
├── cloud/             ← Cloud providers
├── security/          ← Bao mat
├── scripting/         ← Bash/PowerShell
├── troubleshooting/   ← Debug he thong
└── git-ops/           ← GitOps, IaC
```

## Quy tac khi tao noi dung

- Dat file vao dung thu muc topic
- Cap nhat `README.md` cua thu muc do
- Dung so thu tu: `01-topic.md`, `02-topic.md`
- Config mau dung suffix `.sample`: `docker-compose.sample.yml`
- Link cheo giua cac doc bang relative path

## Background nguoi dung

- .NET developer nhieu nam (C#, ASP.NET Core, EF Core)
- Da biet: SQL Server, PostgreSQL, Redis, Docker co ban, Nginx
- Dang hoc: Kubernetes, Cloud (Azure), monitoring, CI/CD, Linux admin
- Moi truong thuc te: POS system, API services, nganh ban le duoc pham

## Khi nao can tra cuu

- Hoi ve AX 2012 → dung MCP `ax2012-code-reader` va `ax-schema`
- Hoi ve database → dung MCP `mssql`, `postgres`, `redis`
- Hoi ve container → dung MCP `docker`
- Hoi ve logs → dung MCP `elasticsearch`
