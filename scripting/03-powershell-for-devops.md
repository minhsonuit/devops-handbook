# PowerShell for DevOps

> Ngay bat dau: ___
> Loi the cho .NET dev: da quen PowerShell tu Visual Studio / Windows

## Basics

```powershell
# Variables
$name = "DevOps"
Write-Host "Hello $name"

# Get date
$today = Get-Date -Format "yyyy-MM-dd"

# Conditions
if ($status -eq 200) { Write-Host "OK" } else { Write-Host "Error" }

# Loop
1..10 | ForEach-Object { Write-Host "Item $_" }
foreach ($server in @("web1", "web2")) { Test-Connection $server -Count 1 }
```

## DevOps tasks

```powershell
# HTTP request
$response = Invoke-WebRequest -Uri "http://localhost:5001/health"
$response.StatusCode

# REST API
$data = Invoke-RestMethod -Uri "http://localhost:5001/api/status"
$data | ConvertTo-Json

# Docker
docker ps --format "{{.Names}}" | ForEach-Object {
    Write-Host "Container: $_"
    docker inspect $_ --format '{{.State.Status}}'
}

# File operations
Get-ChildItem /var/log -Recurse -Filter "*.log" |
    Where-Object { $_.LastWriteTime -lt (Get-Date).AddDays(-30) } |
    Remove-Item
```

## Azure PowerShell

```powershell
# Install
Install-Module -Name Az -Scope CurrentUser

# Login
Connect-AzAccount

# List resources
Get-AzResourceGroup
Get-AzWebApp
Get-AzAksCluster
```
