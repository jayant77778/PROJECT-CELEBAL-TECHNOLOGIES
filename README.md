
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
![1](https://github.com/user-attachments/assets/4d59a7e7-37df-4e10-947a-99fa9df68809)
![2](https://github.com/user-attachments/assets/7f498a40-46cc-4086-b26e-b414624592cf)
![2a](https://github.com/user-attachments/assets/1ee7471d-ce40-48b9-8c54-a9b4ce68e3ca)

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
![2b](https://github.com/user-attachments/assets/3ed0c9e7-abf8-46ac-b485-6afe279b0a83)

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
![3](https://github.com/user-attachments/assets/36d9f9f5-14f9-4a16-9607-1595633bfad2)
![4](https://github.com/user-attachments/assets/940e6bfb-8ed4-4aac-8da0-0fbe7467983f)


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
![5a](https://github.com/user-attachments/assets/1414d51d-d58c-44f5-809d-e4dd3a9f4ae9)
![6](https://github.com/user-attachments/assets/de69ff12-ffbd-4c8c-a880-678f84128455)
![6a](https://github.com/user-attachments/assets/6fd4186a-1518-4d42-9bc3-0f20839dbb5c)
###  Finally it done successfully
![7](https://github.com/user-attachments/assets/3c5a560c-6edb-4277-8db5-d3a8f4694cd7)
![8](https://github.com/user-attachments/assets/f2c3f5da-d4bd-46dd-9ac7-e77df9de2e11)
![9](https://github.com/user-attachments/assets/fd09d07f-6e62-4b3b-a3d9-12f61f2569a6)
### Now IIS SERVER 
![10](https://github.com/user-attachments/assets/0db817bb-4eed-4c23-9af3-109ef323c228)
![11](https://github.com/user-attachments/assets/079c7206-0e6a-42fd-9e69-ada4a3fe7224)
![12](https://github.com/user-attachments/assets/0532945b-46f4-401e-bee6-633aae4d911e)
![12a](https://github.com/user-attachments/assets/fbbf70e3-97d2-4386-936d-ba94e65fba14)
![13](https://github.com/user-attachments/assets/bb694025-2f15-43dc-9103-6b30c9c984a4)

###  SO NOW OUR PROJECT IS SUCCESFULLY DEPLOY AND RUN 

![14](https://github.com/user-attachments/assets/66cd42d1-92f4-4c89-a300-4c392d1c1eca)

### 5. Register the VM with Azure DevOps
Run the provided registration script on the VM to register it as a deployment target.
or use your own windows setup registered your machine in development group.
![Screenshot 2024-07-20 132726](https://github.com/user-attachments/assets/047f6a3a-cd65-492b-91a3-8cdbc3bc15e0)


### 6. Monitor and Validate
Use Azure Monitor to track application performance and validate the deployment by accessing the application on the IIS server.
![15](https://github.com/user-attachments/assets/b2532f02-ebd9-45aa-b961-35e664925668)

![16](https://github.com/user-attachments/assets/78682789-ba15-4e9e-b42a-ac65c6f3725f)
![17](https://github.com/user-attachments/assets/d676121f-20bd-4a79-a59c-061ba39bb3ce)
![18](https://github.com/user-attachments/assets/eee5d5c6-f341-47b4-9347-3e54cf07e280)

### Result 

![Screenshot 2024-07-20 130708](https://github.com/user-attachments/assets/fcea6378-6bde-4552-8b2e-6c11425e35d3)

### Output
After deploying the CI/CD pipeline and integrating my HTML repository, my project is live on the IIS server with the help of the deployment.
![14](https://github.com/user-attachments/assets/a81ca78b-e344-4953-b3b2-6b55569bcc3e)

## Helpful Documents
- [Deploying to IIS using Azure DevOps YAML Pipelines](https://learn.microsoft.com/en-us/azure/devops/pipelines/release/deploy-webdeploy-iis-deploygroups?view=azure-devops&tabs=net)
- [Medium Article on IIS Deployment](https://medium.com/dvt-engineering/how-to-deploy-to-iis-using-azure-devops-yaml-pipelines-a5987f1b9b78)

---

This README file provides a comprehensive guide to setting up CI/CD pipelines for deploying a .NET application to a Windows IIS server using Azure DevOps. Follow the steps carefully to ensure a successful implementation.
