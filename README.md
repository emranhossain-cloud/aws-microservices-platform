# Production-Grade Microservices Platform on AWS

## Overview
This project demonstrates a production-grade microservices platform on AWS using Docker, Terraform, ECS Fargate, GitHub Actions, and CloudWatch.

## Initial Services
- user-service
- product-service
- order-service
- gateway

## Phase 2 — Subnet Architecture

Subnets were created within the VPC to support multi-tier architecture.

### Public Subnets
- public-subnet-a (10.0.1.0/24)
- public-subnet-b (10.0.2.0/24)

### Private Subnets
- private-subnet-a (10.0.3.0/24)
- private-subnet-b (10.0.4.0/24)

### Purpose

Public subnets:
- Load balancers
- NAT gateways

Private subnets:
- Application servers
- Databases
