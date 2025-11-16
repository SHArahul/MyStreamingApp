## Orchestration & Scaling Assignment — StreamingApp (MERN Stack)

This project demonstrates end-to-end containerization, CI/CD, cloud orchestration, deployment scaling, and monitoring of a MERN-based Streaming Application using Docker, Amazon ECR, Jenkins, EKS, Helm, and CloudWatch.

Main Repository: UnpredictablePrashant/StreamingApp

Table of Contents

Project Overview

Architecture Diagram


## Prerequisites

Step 1 — Clone Repository

Step 2 — Containerize the Application

Step 3 — AWS Environment Setup

Step 4 — Continuous Integration (jenkins)

Step 5 — Kubernetes Deployment (EKS)

Step 6 — Monitoring & Logging

Step 7 — Documentation

Step 8 — Final Validation

Folder Structure

License

📦 Project Overview

This assignment covers the entire lifecycle of a cloud-native MERN application:

Containerization of frontend and backend

Automated builds & deployments using Jenkins

Image registry management with AWS ECR

Kubernetes orchestration using Amazon EKS and Helm charts

Monitoring & alerting using AWS CloudWatch

ChatOps integrations using SNS + Slack/Teams/Telegram

Architecture Diagram

```
GitHub Repo → Jenkins (CI) → Amazon ECR → EKS Cluster (Helm Deployments)
                                                ↓                                                
                                         CloudWatch Logs & Metrics
                                                ↓                                             
                                          SNS → Slack/Teams Alerts

```
Prerequisites

Ensure the following tools/services are available:

Local Tools

Docker

AWS CLI

Kubectl

eksctl

Helm

Git

Cloud Services

AWS ECR

AWS IAM

Amazon EC2 (for Jenkins)

Amazon EKS

CloudWatch

# Step 1 — Clone Repository

git clone https://github.com/UnpredictablePrashant/StreamingApp.git
cd StreamingApp

# Step 2 — Containerize the Application

```
2.1 Create Dockerfiles

Frontend Dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build
EXPOSE 3000
CMD ["npm", "start"]

Backend Dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 5000
CMD ["npm", "start"]

```

2.2 Build Docker images

docker build -t streamingapp-frontend ./frontend

docker build -t streamingapp-backend ./backend

2.3 Create ECR Repositories

aws ecr create-repository --repository-name streamingapp-frontend

aws ecr create-repository --repository-name streamingapp-backend

2.4 Authenticate Docker to ECR

aws ecr get-login-password \

 | docker login --username AWS --password-stdin <AWS_ACCOUNT_ID>.dkr.ecr.<region>.amazonaws.com

2.5 Push images to ECR

docker tag streamingapp-frontend:latest <ECR_URL>/streamingapp-frontend:latest

docker tag streamingapp-backend:latest <ECR_URL>/streamingapp-backend:latest

docker push <ECR_URL>/streamingapp-frontend:latest

docker push <ECR_URL>/streamingapp-backend:latest

Step 3 — AWS Environment Setup

Install AWS CLI:

sudo apt install awscli

aws configure


You will need:

Access Key

Secret Key

Default region

Output format

Step 4 — Continuous Integration (CI) using Jenkins

4.1 Install Jenkins on EC2

Launch Ubuntu EC2 instance

Install Jenkins

sudo apt update
sudo apt install openjdk-17-jdk -y

wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -

sudo sh -c 'echo deb https://pkg.jenkins.io/debian binary/ > /etc/apt/sources.list.d/jenkins.list'

sudo apt update

sudo apt install jenkins -y

sudo systemctl enable --now jenkins

4.2 Configure Jenkins Plugins

Docker Pipeline

AWS Credentials Plugin

Git Plugin

Kubernetes Plugin (optional)

4.3 Create Jenkins Pipelines

```
Sample Jenkinsfile
pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/UnpredictablePrashant/StreamingApp.git'
            }
        }
        stage('Build Docker Images') {
            steps {
                sh 'docker build -t frontend ./frontend'
                sh 'docker build -t backend ./backend'
            }
        }
        stage('Push to ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region us-east-1 | \
                docker login --username AWS --password-stdin $ECR_URL

                docker tag frontend:latest $ECR_URL/frontend:latest
                docker tag backend:latest $ECR_URL/backend:latest

                docker push $ECR_URL/frontend:latest
                docker push $ECR_URL/backend:latest
                '''
            }
        }
    }
    triggers {
        githubPush()
    }
}
```

Step 5 — Kubernetes Deployment (EKS)

5.1 Create EKS Cluster

eksctl create cluster \
  --name streamingapp-cluster \

  --region us-east-1 \
  
  --nodegroup-name ng-default \
  
  --nodes 2

5.2 Deploy using Helm

Create Helm chart:

helm create streamingapp


Update templates for:

Frontend Deployment

Backend Deployment

Service (LoadBalancer / ClusterIP)

Deploy the chart:

helm install streamingapp ./streamingapp

Step 6 — Monitoring and Logging

6.1 Enable CloudWatch Metrics

Enable Container Insights

Add CloudWatch agent DaemonSet if needed

6.2 Centralize Logs

Configure logs through:

CloudWatch Logs

FluentBit on EKS (optional)

Step 7 — Documentation

Include:

Architecture diagrams

CI/CD pipeline flowchart

Helm chart structure

EKS cluster configuration

Jenkins job configurations

Upload all documentation to /docs in the repository.

✔️ Step 8 — Final Validation

Verify the following:

✔ Frontend is publicly accessible
✔ Backend API endpoints are responding
✔ Pods auto-scale under load
✔ Jenkins pipeline triggers on commits
✔ ECR images are updated
✔ Logs and metrics visible in CloudWatch

Bonus Step 9 — ChatOps Integration
9.1 Create SNS Topics

aws sns create-topic --name deployment-alerts

9.2 Integrate SNS with Slack / Teams / Telegram

Use:

AWS Chatbot (Slack, Teams)

Telegram Bot API + SNS → Lambda → Telegram

Send notifications for:

Deployment success

Build failure

Scaling events

📂 Folder Structure
```
StreamingApp/
│
├── backend/
│   ├── Dockerfile
│   └── src/…
│
├── frontend/
│   ├── Dockerfile
│   └── src/…
│
├── helm/
│   └── streamingapp/
│
└── docs/
    ├── architecture.png
    ├── eks-setup.md
    ├── jenkins-ci.md
```

## Screenshots:


---
--- 
