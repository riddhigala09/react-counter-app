# Deploying Counter Application on AWS ECS using GitHub Actions Workflow.

In this project, I have implemented a CI/CD pipeline with  GitHub Actions for the automated deployment of a Counter WebApp on AWS ECS.

This workflow includes the following steps:
1. Check out the code from the GitHub repo.
2. Build an optimized docker image with a multistage build to serve with nginx as a reverse proxy.
3. Push image to AWS ECR (Elastic Container Registry).
4. Deploy to AWS ECS (Elastic Container Service).
5. Rollback functionality in case of failure.

## Steps:
### 1. Create ECR Repo:
1. Go to AWS ECR.
2. Make a Private Repository.
3. Name and create the repository.
![create_ecr_repo](https://github.com/user-attachments/assets/d7a9def1-52ff-4cf6-a0a0-6b88ee812be1)

### 2. Create ECS Cluster:
1. Go to AWS ECS.
2. Name the cluster.
![create_ecs_cluster_01](https://github.com/user-attachments/assets/adc25696-e593-45b6-bf47-f0e65ab07889)
3. Choose Fargate as the Infrastructure type.   
![create_ecs_cluster_02](https://github.com/user-attachments/assets/49dcece9-c430-446e-81ec-44984effc15b)
4. Create the cluster.
![create_ecs_cluster_04](https://github.com/user-attachments/assets/a5867b05-e66f-4450-a674-61b54126d782)

### 3. Create Task Definition:
1. On the ECS dashboard menu, select Task Definitions.
2. Name the task definition.
![create_taskdef_01](https://github.com/user-attachments/assets/be026b92-39ee-4bb6-aad9-149384765284)
3. Select Fargate as the Launch type. Choose Linux as the Operating System. Allocate CPU and Memory.
![create_taskdef_02](https://github.com/user-attachments/assets/0d5579b4-c21b-4db9-859f-7dbd45337cfa)
4. Fill in container details.
5. Name and Image URI. Copy the Image URI from the repo.
![create_taskdef_03](https://github.com/user-attachments/assets/06b036ec-b625-441d-98b8-f568cebcb67b)
6. Add "latest" as the tag.
7. I have given 80 for container port as I want my container to run on port 80.
8. Create Task Definition.
![create_taskdef_04](https://github.com/user-attachments/assets/38371a3f-cf31-422b-baa4-f656a3d40f13)

### 4. Create Service:
1. Go to the cluster and under the service section, click on Create service.
2. Select "capacity provider strategy".
![create_service_01](https://github.com/user-attachments/assets/bb5ff693-e54f-4f2d-9ddf-71c35212ec7b)
3. Choose "Service" as the application type.
4. Select the task definition and revision.
5. Give the Service Name and Desired Tasks.
![create_service_02](https://github.com/user-attachments/assets/29c65297-dcab-40b2-9153-416bf556b6f9)
6. In the "Deployment failure detection", check the box "Rollback on failure"
![deploy_01](https://github.com/user-attachments/assets/7410dfcd-c384-43fb-bcb9-7eae2d7dfd88)
7. In the networking section, choose default VPC and Subnets.
8. Create a new Security Group that allows HTTP traffic on port 80 from anywhere. Turn on Public IP.
![create_service_05](https://github.com/user-attachments/assets/3df6d8e3-7d9d-4dc8-883f-c71c26999c1a)
9. Create Service.
![create_service_03](https://github.com/user-attachments/assets/7970cf3c-8033-4e4f-8ece-4c62416feff8)

### 5. Create Dockerfile:
1. It is multistage dockerfile.
2. In stage 1, build and application.
3. In stage 2, serve the application using Nginx as a Reverse proxy.

### 6. Set up GitHub Repo:
1. Create a public repo in GitHub.
2. GitHub Actions needs to authenticate AWS to run the workflow, so we'll store the AWS Secret Access Key ID and Secret Access Key as GitHub Secrets.
3. Go to settings, scroll down to the security section, then "secrets and variables". 
![secrets](https://github.com/user-attachments/assets/e31eecb2-2160-4ef1-ab85-a163d46693b0)
4. Click on "Actions" and add "New Repository Secrets"
![secrets_02](https://github.com/user-attachments/assets/a74916d7-2fdc-4a5a-90f1-97431069f2d1)
5. Add secrets.

![secrets_03](https://github.com/user-attachments/assets/865e1d8c-f4ab-420e-9167-6994dbec5f14)

6. Open the AWS ECS console, go to "Task Definitions", and copy the JSON. 
![taskdef_json](https://github.com/user-attachments/assets/439eb98a-e40c-4a53-adda-04adc23260de)
7. Upload the application code, and dockerfile to the repo. Upload the task definition JSON as a ".json" file. In this case, "ReactApp_TaskDefiniton.json". 
![repo_01](https://github.com/user-attachments/assets/0c7ee7a3-5cbf-472e-a9ae-a0fbe7eb1ece)
8. GitHub Repo is set up.

### 7. Create Workflow:
1. Open the GitHub repo, and click on "Actions".
![repo_02](https://github.com/user-attachments/assets/160aeb46-69aa-4449-86ea-dc116c0a3f79)
2. Choose the template, "Deploy to Amazon ECS".
![create_workflow_02](https://github.com/user-attachments/assets/2b53fb21-ec22-4a61-8b2f-a1d002ef3ab7)
3. Change the following:

![workflow](https://github.com/user-attachments/assets/cc80a02c-ddd3-4fcb-b86b-f9c074990007)

![workflow_2](https://github.com/user-attachments/assets/f186f30f-409d-4467-8a3c-61fc87c95e77)

4. Commit changes.
5. After committing changes, go to "actions". We can see that our workflow has started to run.
![workflow_4](https://github.com/user-attachments/assets/019a81ed-3604-4694-bbbc-aa228494ef4f)
6. Workflow completes successfully.
![workflow_5](https://github.com/user-attachments/assets/a134e5e5-b896-4536-8968-79fc60e994de)

![job_02](https://github.com/user-attachments/assets/3399b14a-da59-4896-87ad-80a4d7f9a88e)

### 8. Access the application:
1. In the ECS cluster, select the "CounterApp" service.
2. Open the "Tasks" section, and select the task.
3. Copy the public IP, and paste it into the browser, or click the "open address".
```
http://<public_ip>:80
```
![service](https://github.com/user-attachments/assets/469737ce-8450-41ad-b5fa-f17267ae5e1e)

4. Access the application.

![app_02](https://github.com/user-attachments/assets/c2655f3e-d954-4f8b-821f-c7a2d275742c)

**<p align="center">THANK YOU!</p>**
