# Azure Fundamentals

> Ngay bat dau: ___

## Core services

| Service | Muc dich | Tuong duong AWS |
|---------|----------|-----------------|
| Azure App Service | Host web app (.NET) | Elastic Beanstalk |
| AKS | Managed Kubernetes | EKS |
| Azure SQL | Managed SQL Server | RDS |
| Azure Container Registry | Docker registry | ECR |
| Azure DevOps | CI/CD + project mgmt | CodePipeline |
| Key Vault | Secrets management | Secrets Manager |
| Azure Monitor | Monitoring + logs | CloudWatch |

## Azure CLI co ban

```bash
# Login
az login

# Resource Groups
az group create --name myapp-rg --location southeastasia
az group list --output table
az group delete --name myapp-rg

# App Service (.NET)
az webapp create --resource-group myapp-rg --plan myplan --name myapp --runtime "DOTNET|8.0"
az webapp deploy --resource-group myapp-rg --name myapp --src-path ./publish.zip

# AKS
az aks create --resource-group myapp-rg --name mycluster --node-count 2
az aks get-credentials --resource-group myapp-rg --name mycluster
kubectl get nodes

# Container Registry
az acr create --resource-group myapp-rg --name myregistry --sku Basic
az acr login --name myregistry
docker push myregistry.azurecr.io/api:v1
```
