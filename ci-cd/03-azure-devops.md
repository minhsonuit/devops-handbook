# Azure DevOps Pipelines

> Ngay bat dau: ___

## YAML pipeline co ban

File: `azure-pipelines.yml`

```yaml
trigger:
  branches:
    include:
      - main

pool:
  vmImage: 'ubuntu-latest'

steps:
  - task: UseDotNet@2
    inputs:
      packageType: 'sdk'
      version: '8.0.x'

  - script: dotnet restore
    displayName: 'Restore'

  - script: dotnet build --configuration Release
    displayName: 'Build'

  - script: dotnet test --configuration Release --no-build
    displayName: 'Test'
```

## Multi-stage pipeline

```yaml
stages:
  - stage: Build
    jobs:
      - job: BuildJob
        steps:
          - script: dotnet publish -c Release -o $(Build.ArtifactStagingDirectory)
          - publish: $(Build.ArtifactStagingDirectory)
            artifact: app

  - stage: DeployStaging
    dependsOn: Build
    jobs:
      - deployment: DeployStaging
        environment: 'staging'
        strategy:
          runOnce:
            deploy:
              steps:
                - download: current
                  artifact: app
                - script: echo "Deploy to staging"

  - stage: DeployProd
    dependsOn: DeployStaging
    condition: succeeded()
    jobs:
      - deployment: DeployProd
        environment: 'production'
        strategy:
          runOnce:
            deploy:
              steps:
                - script: echo "Deploy to production"
```

## Variables va secrets

```yaml
variables:
  buildConfiguration: 'Release'
  # Secret: set trong Azure DevOps UI → Pipeline Variables → Lock icon

steps:
  - script: echo $(buildConfiguration)
  - script: echo $(DB_PASSWORD)    # Secret tu UI
```
