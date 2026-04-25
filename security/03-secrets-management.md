# Secrets Management

> Ngay bat dau: ___

## Khong bao gio

- Hard-code secrets trong source code
- Commit secrets vao Git
- Log secrets ra stdout

## Phuong phap quan ly

| Phuong phap | Khi nao dung |
|-------------|-------------|
| Environment variables | Dev, don gian |
| Docker secrets | Docker Swarm |
| K8s Secrets | Kubernetes |
| Azure Key Vault | Azure production |
| HashiCorp Vault | Multi-cloud, advanced |

## Docker secrets (Swarm)

```bash
echo "mypassword" | docker secret create db_password -
docker service create --secret db_password myapp
# Trong container: cat /run/secrets/db_password
```

## K8s Secrets

```bash
kubectl create secret generic app-secrets --from-literal=DB_PASSWORD=mypassword
```

```yaml
env:
  - name: DB_PASSWORD
    valueFrom:
      secretKeyRef:
        name: app-secrets
        key: DB_PASSWORD
```

## Azure Key Vault

```bash
az keyvault create --name mykeyvault --resource-group myapp-rg
az keyvault secret set --vault-name mykeyvault --name DbPassword --value "mypassword"
az keyvault secret show --vault-name mykeyvault --name DbPassword --query value -o tsv
```

## .NET integration

```csharp
// Azure Key Vault
builder.Configuration.AddAzureKeyVault(
    new Uri("https://mykeyvault.vault.azure.net/"),
    new DefaultAzureCredential());

// User Secrets (dev only)
// dotnet user-secrets set "DbPassword" "mypassword"
```
