# Azure Networking

> Ngay bat dau: ___

## VNet (Virtual Network)

```bash
az network vnet create --resource-group myapp-rg --name myVNet \
  --address-prefix 10.0.0.0/16 --subnet-name default --subnet-prefix 10.0.1.0/24
```

## NSG (Network Security Group)

```bash
# Tao NSG
az network nsg create --resource-group myapp-rg --name myNSG

# Them rule
az network nsg rule create --resource-group myapp-rg --nsg-name myNSG \
  --name AllowHTTP --priority 100 --destination-port-ranges 80 --access Allow \
  --protocol Tcp --direction Inbound

az network nsg rule create --resource-group myapp-rg --nsg-name myNSG \
  --name AllowHTTPS --priority 110 --destination-port-ranges 443 --access Allow \
  --protocol Tcp --direction Inbound
```

## Load Balancer vs Application Gateway

| | Load Balancer | Application Gateway |
|---|---|---|
| Layer | L4 (TCP/UDP) | L7 (HTTP/HTTPS) |
| Features | Basic routing | Path routing, WAF, SSL |
| Use case | Non-HTTP traffic | Web apps |
| Cost | Thap hon | Cao hon |

## Private Endpoints

Truy cap Azure services (SQL, Storage) qua private IP thay vi public internet.
