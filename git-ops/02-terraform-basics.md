# Terraform Basics

> Ngay bat dau: ___

## Terraform la gi

Infrastructure as Code — viet code de tao/quan ly cloud resources.

## Workflow

```bash
terraform init      # Download providers
terraform plan      # Xem truoc thay doi
terraform apply     # Ap dung thay doi
terraform destroy   # Xoa tat ca resources
```

## HCL co ban

```hcl
# Provider
provider "azurerm" {
  features {}
}

# Resource Group
resource "azurerm_resource_group" "main" {
  name     = "myapp-rg"
  location = "Southeast Asia"
}

# App Service Plan
resource "azurerm_service_plan" "main" {
  name                = "myapp-plan"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  os_type             = "Linux"
  sku_name            = "B1"
}

# Variables
variable "environment" {
  default = "production"
}

# Output
output "resource_group_id" {
  value = azurerm_resource_group.main.id
}
```

## State management

```bash
# State luu trang thai hien tai cua infrastructure
# KHONG commit terraform.tfstate vao Git
# Dung remote backend:

terraform {
  backend "azurerm" {
    resource_group_name  = "terraform-state-rg"
    storage_account_name = "tfstatestorage"
    container_name       = "tfstate"
    key                  = "prod.terraform.tfstate"
  }
}
```

## Best practices

- Luon `terraform plan` truoc `apply`
- Dung remote state (Azure Blob, S3)
- Dung modules cho code reusable
- Dung workspaces cho multi-environment
- Review plan truoc khi approve
