# Cloud Concepts

> Ngay bat dau: ___

## IaaS vs PaaS vs SaaS

| | IaaS | PaaS | SaaS |
|---|------|------|------|
| Ban quan ly | OS, runtime, app | App only | Khong gi |
| Provider quan ly | Hardware, network | OS, runtime, infra | Tat ca |
| Vi du | Azure VM, AWS EC2 | Azure App Service, Heroku | Office 365, Gmail |

## Core concepts

- **Region** — Vung dia ly (Southeast Asia, East US)
- **Availability Zone (AZ)** — Data center doc lap trong 1 region
- **VNet/VPC** — Mang ao rieng cua ban
- **Resource Group** — Nhom tai nguyen logic (Azure)
- **IAM** — Quan ly quyen truy cap

## Chi phi

- Pay-as-you-go
- Reserved instances (giam 40-70%)
- Spot/Preemptible (re nhat, co the bi thu hoi)
- Right-sizing: chon dung kich thuoc VM

## CLI tools

```bash
# Azure
az login
az group list
az vm list

# AWS
aws configure
aws ec2 describe-instances
aws s3 ls
```
