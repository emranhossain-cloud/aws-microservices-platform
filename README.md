# Production-Grade Microservices Platform on AWS

## Overview
This project demonstrates a production-grade microservices platform on AWS using Docker, Terraform, ECS Fargate, GitHub Actions, and CloudWatch.

## Initial Services
- user-service
- product-service
- order-service
- gateway

## Phase 2 — AWS Infrastructure Foundation (Terraform)

### Overview

In this phase, the core AWS networking infrastructure was provisioned using **Terraform Infrastructure as Code (IaC)**.  
This infrastructure establishes the **network foundation** required for deploying a production-grade microservices platform on AWS.

The design follows **best practices used in real cloud environments**, including:

- Custom Virtual Private Cloud (VPC)
- Multi-Availability Zone subnet architecture
- Public and private network separation
- Internet connectivity through an Internet Gateway
- Terraform-based infrastructure provisioning

---

## VPC Configuration

A custom **Virtual Private Cloud (VPC)** was created to isolate the platform infrastructure.

**Configuration**

- VPC Name: `aws-microservices-platform-vpc`
- CIDR Block: `10.0.0.0/16`
- DNS Support: Enabled
- DNS Hostnames: Enabled

The VPC provides a secure and isolated networking environment where all application infrastructure will run.

---

## Subnet Architecture

Subnets were created within the VPC to support a **multi-tier architecture** and **high availability across multiple availability zones**.

### Public Subnets

Public subnets allow resources to communicate with the internet.

Typical use cases for public subnets include:

- Application Load Balancers
- NAT Gateways
- Bastion Hosts

**Subnets**

- `public-subnet-a` → `10.0.1.0/24` (us-east-1a)
- `public-subnet-b` → `10.0.2.0/24` (us-east-1b)

These subnets are configured to automatically assign **public IP addresses** to launched resources.

---

### Private Subnets

Private subnets are used for internal services that should **not be directly accessible from the internet**.

Typical use cases for private subnets include:

- Application services
- ECS containers
- Backend microservices
- Databases

**Subnets**

- `private-subnet-a` → `10.0.3.0/24` (us-east-1a)
- `private-subnet-b` → `10.0.4.0/24` (us-east-1b)

This design ensures backend services remain secure while still communicating within the VPC.

---

## Internet Gateway

An **Internet Gateway (IGW)** was attached to the VPC to enable internet connectivity.

**Configuration**

- Internet Gateway Name: `aws-microservices-platform-igw`

The Internet Gateway allows resources in **public subnets** to communicate with external networks.

---

## Public Route Table

A dedicated **public route table** was created to manage internet traffic for public subnets.

### Route Configuration
## Phase 3.2 — First Microservice + Docker Container

In this phase, the first backend microservice was implemented and containerized.

### Completed work
- Initialized a standalone Node.js microservice in `services/user-service`
- Installed Express
- Created API endpoints:
  - `GET /health`
  - `GET /users`
- Added a `start` script in `package.json`
- Created a service-specific `Dockerfile`
- Built Docker image:
  - `user-service:v1`
- Ran the service in a container
- Verified the service with Docker and curl

### Commands used
```bash
npm init -y
npm install express
npm start
docker build -t user-service:v1 .
docker run -d -p 3001:3001 --name user-service-container user-service:v1
docker ps
curl http://localhost:3001/health
docker logs user-service-container
docker stop user-service-container
docker rm user-service-container



## Technical notes for docs/

```markdown
# Phase 3.2 Technical Notes

## Objective
Build and containerize the first microservice in the platform.

## What was built
- A Node.js + Express microservice named `user-service`
- A health endpoint for operational checks
- A sample `/users` endpoint
- A Docker image for isolated packaging and runtime

## Why it matters
Containerization ensures:
- consistency across environments
- easier CI/CD automation
- portability to ECS, EKS, and Kubernetes
- cleaner microservice deployment boundaries

## Operational validation
- verified image creation with `docker images`
- verified running container with `docker ps`
- verified API response with `curl`

## Phase 3.3 — Add Product Service + Docker Compose

In this phase, the platform was expanded into a multi-service backend environment.

### Completed work
- Built a second microservice: `product-service`
- Added API endpoints:
  - `GET /health`
  - `GET /products`
- Created a `Dockerfile` for `product-service`
- Added root `docker-compose.yml`
- Used Docker Compose to run:
  - `user-service`
  - `product-service`
- Verified both services with curl and Docker logs

### Services and ports
- `user-service` → `3001`
- `product-service` → `3002`

### Commands used
```bash
docker build -t product-service:v1 .
docker compose up -d
docker ps
curl http://localhost:3001/health
curl http://localhost:3002/health
curl http://localhost:3001/users
curl http://localhost:3002/products
docker compose logs
docker compose down

## Phase 3.4 — API Gateway Service

In this phase, an API Gateway was added as the central access layer for backend services.

### Completed work
- Built `api-gateway` using Node.js, Express, and Axios
- Added endpoints:
  - `GET /health`
  - `GET /users`
  - `GET /products`
- Configured gateway to forward requests to:
  - `user-service`
  - `product-service`
- Added `api-gateway` to `docker-compose.yml`
- Used Docker Compose networking for service-to-service communication
- Verified gateway routing through localhost port `3000`

### Services and ports
- `api-gateway` → `3000`
- `user-service` → `3001`
- `product-service` → `3002`

### Commands used
```bash
npm init -y
npm install express axios
docker compose up -d --build
curl http://localhost:3000/health
curl http://localhost:3000/users
curl http://localhost:3000/products
docker compose logs api-gateway
docker compose down

# 🚀 Phase 4.1 — Kubernetes Cluster Setup (K3s on AWS EC2)

## 📌 Overview
In this phase, I set up a lightweight Kubernetes cluster using K3s on an AWS EC2 instance to enable orchestration of containerized microservices.

## ⚙️ Environment
- AWS EC2 (Ubuntu 22.04)
- Kubernetes (K3s)
- kubectl
- Local environment: WSL (Ubuntu)

## 🔐 Security Group Configuration
- SSH (22)
- HTTP (80)
- Custom TCP (6443 — Kubernetes API)

> Note: Port 6443 is open for learning purposes only.

## 🔑 SSH Access
```bash
cp /mnt/c/Users/emran/Desktop/projects/aws-microservices-platform.pem ~/.ssh/
chmod 400 ~/.ssh/aws-microservices-platform.pem

ssh -i ~/.ssh/aws-microservices-platform.pem ubuntu@<EC2-PUBLIC-IP>

### Phase 4.2 - Docker Image Push

Tagged and pushed microservices to Docker Hub:

- aws-microservices-platform-api-gateway
- aws-microservices-platform-product-service
- aws-microservices-platform-user-service

Each service is now available for Kubernetes deployment.
