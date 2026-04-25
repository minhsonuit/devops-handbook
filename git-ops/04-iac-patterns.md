# IaC Patterns

> Ngay bat dau: ___

## Cau truc project Terraform

```
infra/
├── modules/
│   ├── networking/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   ├── aks/
│   └── database/
├── environments/
│   ├── dev/
│   │   ├── main.tf
│   │   └── terraform.tfvars
│   ├── staging/
│   └── production/
├── backend.tf
└── versions.tf
```

## Modules

```hcl
# modules/aks/main.tf
resource "azurerm_kubernetes_cluster" "main" {
  name                = var.cluster_name
  location            = var.location
  resource_group_name = var.resource_group_name
  dns_prefix          = var.dns_prefix
  
  default_node_pool {
    name       = "default"
    node_count = var.node_count
    vm_size    = var.vm_size
  }
}

# environments/production/main.tf
module "aks" {
  source              = "../../modules/aks"
  cluster_name        = "prod-cluster"
  location            = "Southeast Asia"
  resource_group_name = "prod-rg"
  node_count          = 3
  vm_size             = "Standard_D2s_v3"
}
```

## K8s manifests structure

```
k8s/
├── base/                    # Kustomize base
│   ├── deployment.yaml
│   ├── service.yaml
│   └── kustomization.yaml
├── overlays/
│   ├── dev/
│   │   ├── kustomization.yaml
│   │   └── patch-replicas.yaml
│   └── production/
│       ├── kustomization.yaml
│       └── patch-replicas.yaml
```

## Best practices

- Tach infra repo va app repo
- Dung modules cho reuse
- Environment-specific configs bang tfvars hoac Kustomize overlays
- Review changes truoc khi apply (PR-based workflow)
- Tag infrastructure versions
