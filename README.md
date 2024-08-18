# 🌟Containerize and Deploy Your Website on AWS ECS with GitHub Actions 


Welcome to the GitHub Actions Tutorial repository! This project will walk you through setting up a continuous deployment pipeline using GitHub Actions, Docker, ECS, and ECR.
## 🚀 Project Overview
This project demonstrates a simple pipeline setup using GitHub Actions to automate the deployment of a Docker container to Amazon ECS using ECR for container storage. Perfect for those eager to streamline their cloud deployments!


## Step 1: Build and Test the Docker Image 
1. Build the Docker image:

```bash
docker build -t github-actions-tutorial:latest .
```
2. Run the container:
```bash
docker run -p 8080:80 github-actions-tutorial:latest
```
3. Open your browser and navigate to [http://localhost:8080](http://localhost:8080) to browse the website.

## Step 2: Push the Docker Image to a Container Registry (ECR)
1. Authenticate via aws cli :
 ```bash
 aws configure
   ```




2. Create repository:
 ```bash
 aws aws ecr create-repository --repository-name github-actions-tuto
 ```

3. Log in to the ECR: 
```bash
aws ecr get-login-password --region <your-region> | docker login --username AWS --password-stdin <your-account-id>.dkr.ecr.<your-region>.amazonaws.com/github-actions-tutorial
```

4. Tag and Push the image 
```bash
docker tag github-actions-tutorial:latest <your-account-id>.dkr.ecr.<your-region>.amazonaws.com/github-actions-tutorial
docker push <your-account-id>.dkr.ecr.<your-region>.amazonaws.com/github-actions-tutorial1.amazonaws.com/github-tutorial-image:latest
```
## Step 3: Set Up GitHub Actions for Deployment

1. Create a `.github/workflows/deploy.yml` file in your repository. (Add your deployment configuration here)

## Step 4: Create ECS Task Definition and Service

1. Create an ECS cluster:

   ```bash
   aws ecs create-cluster --cluster-name github-actions-tutorial-ecs-cluster
   ```

2. Create the ECS Task Definition:

   Create an `ecs-task-def.json` file in your repository with the necessary configuration.

3. Create the roles required for the task definition.

4. Create an ECS Security Group to allow HTTP and HTTPS traffic.

## Step 5: Create CloudWatch Log Directory

1. Create a CloudWatch log group:

   ```bash
   aws logs create-log-group --log-group-name /ecs/github-actions-tutorial-log-group
   ```

## Step 6: Register the Task Definition

1. Register the Task Definition:

   ```bash
   aws ecs register-task-definition --cli-input-json file://ecs-task-def.json
   ```

   Or, if using the `--profile` and `--region` options:

   ```bash
   aws ecs register-task-definition --cli-input-json "$(cat ./.aws/ecs-task-def.json)" --profile <your-profile> --region <your-region>
   ```

## Step 7: Create an ECS Service

1. Create an ECS Service:

   ```bash
   aws ecs create-service \
   --cluster github-actions-tutorial-ecs-cluster \
   --service-name github-actions-tutorial-ecs-service \
   --task-definition github-actions-tutorial-task-def-family \
   --desired-count 1 \
   --launch-type FARGATE \
   --network-configuration "awsvpcConfiguration={subnets=[<your-subnet-id>],securityGroups=[<your-security-group-id>],assignPublicIp=ENABLED}" \
   --profile <your-profile> \
   --region <your-region>
   ```

## Step 8: Check ECS Task Status

1. To check the status of your ECS task, run:

   ```bash
   aws ecs describe-tasks --cluster github-actions-tutorial-ecs-cluster \
   --tasks <your-task-id> \
   --profile <your-profile> \
   --region <your-region>
   ```

## Step 9: Commit Code Changes

1. Commit code changes to deploy code from GitHub to ECS using GitHub Actions.


