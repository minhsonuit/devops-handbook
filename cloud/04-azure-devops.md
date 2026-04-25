# Azure DevOps

> Ngay bat dau: ___

## Components

| Component | Muc dich |
|-----------|----------|
| Repos | Git repositories |
| Pipelines | CI/CD |
| Boards | Project management (Agile) |
| Artifacts | Package management (NuGet, npm) |
| Test Plans | Test management |

## Pipeline cho .NET + Docker + AKS

```yaml
trigger:
  - main

variables:
  acrName: 'myregistry'
  imageName: 'api'
  tag: '$(Build.BuildId)'

stages:
  - stage: Build
    jobs:
      - job: Build
        pool:
          vmImage: 'ubuntu-latest'
        steps:
          - task: Docker@2
            inputs:
              containerRegistry: 'ACR-Connection'
              repository: '$(imageName)'
              command: 'buildAndPush'
              Dockerfile: '**/Dockerfile'
              tags: |
                $(tag)
                latest

  - stage: Deploy
    dependsOn: Build
    jobs:
      - deployment: DeployAKS
        environment: 'production'
        strategy:
          runOnce:
            deploy:
              steps:
                - task: KubernetesManifest@1
                  inputs:
                    action: 'deploy'
                    kubernetesServiceConnection: 'AKS-Connection'
                    manifests: 'k8s/*.yml'
                    containers: '$(acrName).azurecr.io/$(imageName):$(tag)'
```

## Service Connections

Settings → Service connections → tao connection toi ACR, AKS, Azure subscription.
