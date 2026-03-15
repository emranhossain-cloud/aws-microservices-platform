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
