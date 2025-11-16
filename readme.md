# Conduit Docker Project – AWS ECS Deployment

This repository contains the Conduit project, consisting of a Django backend and an Angular frontend, deployed via Docker and AWS ECS Fargate.
It serves as an example of containerization, AWS deployment, and can later be extended with a CI/CD pipeline.

## Table of Contents
- [Quickstart](#quickstart)
- [Usage](#usage)
    - [Environment Variables](#environment-variables)
    - [Volumes](#volumes)
    - [Restarting and Stopping Containers](#restarting-and-stopping-containers)
- [Security](#security)
- [AWS ECS Deployment](#aws-ecs-deployment)
- [CI/CD Pipeline](#ci/cd-pipeline)
- [Extras](#extras)

## Quickstart

### Prerequisites
* Docker & Docker Compose
* AWS CLI configured (aws configure)
* AWS account with ECR, ECS, and Fargate permissions
* Optional: GitHub Actions for automated deployment

### Steps
1. Clone this repository:
```bash
git clone git@github.com:A-Marbach/conduit-container.git
cd conduit-container
```
2. Initialize submodules (backend and frontend):

```bash
git submodule update --init --recursive
```
3. Prepare the backend environment:

```bash
cp backend/.env.example backend/.env
```

4. Build Docker images locally (optional):

```bash
docker-compose build --no-cache
docker-compose up -d
```

5. Access the application locally:
* Frontend: http://<your_vm_ip>:8282
* Backend API: http://<your_vm_ip>:5000/admin/


## Usage

* Use the frontend at /
* Backend API available at /api/
 * Ensure environment variables are properly set before starting

### Environment Variables

Backend .env:

```
| Variable               | Description                       | Default                     |
|------------------------|-----------------------------------|-----------------------------|
| DJANGO_ALLOWED_HOSTS   | Allowed hosts for Django          | *                           |
| SECRET_KEY             | Django secret key                 | changeme                    |
| DEBUG                  | Debug mode                        | True                        |
| BACKEND_VERSION        | Backend Docker image tag          | latest                      |
```
Add additional variables as needed for database, API keys, etc.


### Volumes

* backend_data: Persistent storage for backend

* frontend_data: Persistent storage for frontend build files


### Restarting and Stopping Containers

Restart and Stopping containers:
```bash
docker-compose restart
docker-compose down
```
### Security
* Do not commit .env files, SSH keys, passwords, or any sensitive information to the repository.
* Do not include IP addresses or credentials in the frontend code.

### AWS ECS Deployment

1. Create ECR repositories

``` bash
aws ecr create-repository --repository-name conduit-backend
aws ecr create-repository --repository-name conduit-frontend
```
2. Tag and push Docker images

```bash
docker tag conduit-backend:latest <aws_account_id>.dkr.ecr.<region>.amazonaws.com/conduit-backend:latest
docker push <aws_account_id>.dkr.ecr.<region>.amazonaws.com/conduit-backend:latest

docker tag conduit-frontend:latest <aws_account_id>.dkr.ecr.<region>.amazonaws.com/conduit-frontend:latest
docker push <aws_account_id>.dkr.ecr.<region>.amazonaws.com/conduit-frontend:latest
```
3. ECS Cluster & Task Definition

* Cluster: conduit-cluster (Fargate)
* Task Definition: conduit-task
    * Container Ports: Backend 5000, Frontend 80
    * CPU: 0.5 vCPU, RAM: 2 GB
    * Network Mode: awsvpc
    * Environment Variables from .env or AWS Secrets Manager

4. ECS Sercive
* Starts the task on the cluster
* Desired Tasks: 1
* Security Group Ports: Frontend 80, Backend 5000
* Public IP assigned

5. Access
* Frontend URL: http://<Fargate_Public_IP>/
* Backend API: http://<Fargate_Public_IP>:5000/



### Extras

* You can add additional frontend features or backend apps as needed.



> **Note:**  
> Edit the `.env` files if you want custom credentials.  
> Do **not** commit `.env` files to the repository, as they contain sensitive information.


Trigger workflow