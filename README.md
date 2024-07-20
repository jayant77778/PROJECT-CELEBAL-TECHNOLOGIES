
# Project 08 - IIS Server Deployment using Azure DevOps

This project involves automating the deployment of applications to Windows IIS servers using Azure DevOps. It aims to streamline and ensure reliable delivery processes for the web application hosted on the servers, utilizing the .NET framework for development.

## Table of Contents
- [Business Problem Statement and Prerequisites](#business-problem-statement-and-prerequisites)
- [High-Level Architecture](#high-level-architecture)
- [CI/CD Process](#cicd-process)
- [Step-by-Step Solution](#step-by-step-solution)
- [Helpful Documents](#helpful-documents)

## Business Problem Statement and Prerequisites

### Business Problem Statement
A leading e-commerce company, with a critical .NET Core application, seeks to enhance its online platform by deploying updates and new features to its web application hosted on Windows IIS servers. To achieve this seamlessly while maintaining reliability and scalability, the company has chosen to implement Azure DevOps for automated deployment processes.

### Prerequisites
- Azure Virtual Machine with Windows IIS server installed.
- Azure DevOps Organization and project.
- .NET framework installed on development machines for application development.

## High-Level Architecture
```plaintext
Developer --> Azure CI Pipeline --> Azure CD Pipeline --> Virtual Machine (IIS Server)
                                                 |
                                            Azure Monitor
                                               |
                                          Log Analytics
```

## CI/CD Process
1. **Source Control Management**: Developers commit code changes to Azure DevOps Git repository.
2. **Continuous Integration (CI)**:
    - Azure DevOps CI pipeline triggers upon code commit.
    - It compiles code, runs tests, and produces artifacts.
3. **Continuous Deployment (CD)**:
    - CD pipeline deploys artifacts to target Windows IIS servers.
    - Deployment tasks include file copying and Windows IIS configuration.
4. **Validation**:
    - Automated tests or manual checks validate application functionality post-deployment.
5. **Monitoring and Feedback**:
    - Azure Monitor tracks application performance on Windows IIS servers.
    - Feedback guides future development iterations for improvement.

## Step-by-Step Solution

### 1. Create an Azure Virtual Machine with IIS Server
- Set up an Azure VM and install IIS Server on it. Make sure to configure the IIS server to host your .NET application.

### 2. Set up Azure DevOps Project
- Create a new project in Azure DevOps and set up a Git repository for your application code.

### 3. Configure CI Pipeline
Create a `azure-pipelines.yml` file in the root of your repository:
```yaml
trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: UseDotNet@2
  inputs:
    packageType: 'sdk'
    version: '5.x'

- task: DotNetCoreCLI@2
  inputs:
    command: 'restore'
    projects: '**/*.csproj'

- task: DotNetCoreCLI@2
  inputs:
    command: 'build'
    projects: '**/*.csproj'

- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    projects: '**/*.csproj'
    arguments: '--configuration Release --output $(Build.ArtifactStagingDirectory)'

- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(Build.ArtifactStagingDirectory)'
    ArtifactName: 'drop'
```

### 4. Configure CD Pipeline
Create a new release pipeline in Azure DevOps:
- **Artifacts**: Link the artifact from the CI pipeline.
- **Stages**: Add a stage for deployment to the IIS server.

Add the following tasks to the deployment job:
```yaml
steps:
- task: DownloadBuildArtifacts@0
  inputs:
    buildType: 'current'
    downloadType: 'single'
    artifactName: 'drop'
    downloadPath: '$(System.ArtifactsDirectory)'

- task: IISWebAppDeploymentOnMachineGroup@0
  inputs:
    WebSiteName: 'Default Web Site'
    Package: '$(System.ArtifactsDirectory)/drop'
```

### 5. Register the VM with Azure DevOps
Run the provided registration script on the VM to register it as a deployment target.

### 6. Monitor and Validate
Use Azure Monitor to track application performance and validate the deployment by accessing the application on the IIS server.

## Helpful Documents
- [Deploying to IIS using Azure DevOps YAML Pipelines](https://learn.microsoft.com/en-us/azure/devops/pipelines/release/deploy-webdeploy-iis-deploygroups?view=azure-devops&tabs=net)
- [Medium Article on IIS Deployment](https://medium.com/dvt-engineering/how-to-deploy-to-iis-using-azure-devops-yaml-pipelines-a5987f1b9b78)

---

This README file provides a comprehensive guide to setting up CI/CD pipelines for deploying a .NET application to a Windows IIS server using Azure DevOps. Follow the steps carefully to ensure a successful implementation.
