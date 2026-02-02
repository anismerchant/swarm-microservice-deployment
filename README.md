## 1. Project Overview

This project builds on the earlier _deploying-web-app-using-ansible_ baseline and evolves from **single-host container orchestration using Docker Compose** to **service-based orchestration using Docker Swarm** on AWS EC2.

The focus is on demonstrating **infrastructure provisioning, host configuration, and container orchestration**, following industry-standard DevOps separation of concerns rather than application development.


## 2. Tool Responsibility Matrix

```
Terraform        → Infrastructure (VPC, Subnet, Security Groups, EC2)
Ansible          → Host configuration (Docker installation, readiness)
Docker Engine    → Container runtime
Docker Compose   → Single-host, multi-tier application baseline
Docker Swarm     → Service orchestration, replication, overlay networking
```

## 3. High-Level Architecture (Evolution)

### Compose (single-host baseline)

```
Local Machine
   |
   | terraform apply
   v
AWS EC2
   |
   | ansible-playbook
   v
Docker Engine
   |
   | docker-compose up
   v
--------------------------------
| Frontend | API | Database    |
--------------------------------
```

### Swarm (service-based orchestration)

```
Local Machine
   |
   | terraform apply
   v
AWS EC2 (Swarm Manager)
   |
   | docker stack deploy
   v
----------------------------------------
| Frontend (replicas) | API (replicas) |
|        Database (1 replica)          |
----------------------------------------
```

## 4. Repository Structure

```
terraform/   → Infrastructure provisioning (AWS)
ansible/     → Host configuration and bootstrap
docker/      → Container orchestration (Compose + Swarm stack)
docs/        → Architecture, runbook, troubleshooting
```


## 5. What This Project Demonstrates

- Infrastructure provisioning using **Terraform** with clear module boundaries
- Host configuration and repeatability using **Ansible**
- Containerized application execution using **Docker Engine**
- Single-host orchestration using **Docker Compose** as a baseline
- Service-based orchestration using **Docker Swarm**, including:
  - replicated services
  - overlay networking
  - routing mesh and load balancing

- Clear separation between **infrastructure**, **configuration**, and **runtime orchestration**
- Practical understanding of how containerized systems evolve from development to orchestration-focused deployments

This mirrors real-world DevOps workflows where application code, infrastructure, and orchestration concerns are managed independently.
