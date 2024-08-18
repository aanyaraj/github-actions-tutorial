# 🌟 Containerize and Deploy Your Website on AWS ECS with GitHub Actions

Welcome to the GitHub Actions Tutorial repository! This project guides you through setting up a continuous deployment pipeline using **GitHub Actions**, **Docker**, **Amazon ECS**, and **ECR**. Whether you're new to cloud deployments or looking to refine your skills, this tutorial will help you streamline your deployment process.

## 🚀 Project Overview

This project demonstrates a straightforward pipeline setup that uses GitHub Actions to automate the deployment of a Docker container to Amazon ECS, with ECR serving as the container registry. Ideal for anyone looking to enhance their cloud deployment workflows!

---

## 🛠️ Step 1: Build and Test the Docker Image

1. **Build the Docker image**:

    ```bash
    docker build -t github-actions-tutorial:latest .
    ```

2. **Run the container**:

    ```bash
    docker run -p 8080:80 github-actions-tutorial:latest
    ```

3. **Verify**:

    Open your browser and navigate to [http://localhost:8080](http://localhost:8080) to view the website.

    > **Note:** Ensure your Docker daemon is running to avoid issues when building and running the container.

---

## 📦 Step 2: Push the Docker Image to a Container Registry (ECR)

1. **Authenticate via AWS CLI**:

    Make sure your AWS CLI is configured with the correct credentials:

    ```bash
    aws configure
    ```

2. **Create an ECR repository**:

    ```bash
    aws ecr create-repository --repository-name github-actions-tuto
    ```

3. **Log in to the ECR**:

    ```bash
    aws ecr get-login-password --region <your-region> | docker login --username AWS --password-stdin <your-account-id>.dkr.ecr.<your-region>.amazonaws.com/github-actions-tutorial
    ```

4. **Tag and Push the image**:

    ```bash
    docker tag github-actions-tutorial:latest <your-account-id>.dkr.ecr.<your-region>.amazonaws.com/github-actions-tutorial
    docker push <your-account-id>.dkr.ecr.<your-region>.amazonaws.com/github-actions-tutorial:latest
    ```

    > **Note:** Replace `<your-region>` and `<your-account-id>` with your actual AWS region and account ID.

---

## 🚀 Step 3: Set Up GitHub Actions for Deployment

1. **Create a deployment workflow**:

    In your repository, create a `.github/workflows/deploy.yml` file. This file will contain your GitHub Actions workflow configuration for deploying to ECS.

    > **Note:** Customize this file to include the necessary build, push, and deploy steps according to your project requirements.

---

## 🛠️ Step 4: Create ECS Task Definition and Service

1. **Create an ECS cluster**:

    ```bash
    aws ecs create-cluster --cluster-name github-actions-tutorial-ecs-cluster
    ```

2. **Define the ECS Task**:

    Create an `ecs-task-def.json` file in your repository with the necessary configuration. This file will describe the container settings for your ECS service.

3. **Set up required IAM roles**:

    Ensure you have the correct IAM roles to allow ECS to pull the image from ECR and create logs in CloudWatch.

4. **Create an ECS Security Group**:

    Configure a security group that allows HTTP and HTTPS traffic to your service.

    > **Note:** Ensure your security group is correctly configured to allow inbound traffic on the necessary ports.

---

## 📊 Step 5: Create CloudWatch Log Group

1. **Create a log group**:

    ```bash
    aws logs create-log-group --log-group-name /ecs/github-actions-tutorial-log-group
    ```

    > **Tip:** Use descriptive names for your log groups to easily identify and manage logs.

---

## 📝 Step 6: Register the Task Definition

1. **Register the ECS Task Definition**:

    ```bash
    aws ecs register-task-definition --cli-input-json file://ecs-task-def.json
    ```

    If using `--profile` and `--region` options:

    ```bash
    aws ecs register-task-definition --cli-input-json "$(cat ./.aws/ecs-task-def.json)" --profile <your-profile> --region <your-region>
    ```

    > **Note:** Ensure your task definition JSON is properly formatted and includes all required parameters.

---

## ⚙️ Step 7: Create an ECS Service

1. **Create the ECS Service**:

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

    > **Note:** Replace `<your-subnet-id>`, `<your-security-group-id>`, and other placeholders with your actual values.

---

## 🔍 Step 8: Check ECS Task Status

1. **Check the ECS task status**:

    ```bash
    aws ecs describe-tasks --cluster github-actions-tutorial-ecs-cluster \
    --tasks <your-task-id> \
    --profile <your-profile> \
    --region <your-region>
    ```

    > **Tip:** Regularly check the task status to monitor your deployment and ensure everything is running smoothly.

---

## 📥 Step 9: Commit Code Changes

1. **Deploy via GitHub Actions**:

    Commit and push your changes to GitHub. The GitHub Actions workflow will trigger and deploy your code to ECS.

    > **Note:** Ensure your GitHub repository secrets are properly configured to store sensitive information like AWS credentials.
