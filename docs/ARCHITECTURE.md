### 1. Architecture Overview

State the evolution in one paragraph:

> This architecture builds on a Terraform + Ansible baseline and introduces Docker Compose to run a multi-tier application on a single EC2 host, reflecting common industry patterns for small to mid-scale deployments.

---

### 2. Responsibility Boundaries (critical)

```
Terraform
  - VPC / networking
  - Security groups
  - EC2 instance

Ansible
  - OS bootstrap
  - Docker + Docker Compose installation
  - Host readiness

Docker Engine
  - Container runtime

Docker Compose
  - Application orchestration
  - Service networking
```

---

### 3. System Architecture

```
Local Machine
   |
   | terraform apply
   v
AWS Infrastructure
   |
   | ansible-playbook
   v
EC2 Host
   |
   | Docker Engine
   v
Docker Compose
   |
   |-- frontend (8080)
   |-- api (5000)
   |-- database (3306, internal)
```

```
Mental Model: Docker on EC2

EC2
 ├─ Docker Engine   ← added
 ├─ Docker Compose  ← added
 └─ Nginx           ← unchanged
```

---

### 4. Data Flow (browser → DB)

```
Browser
  |
  | http://PUBLIC_IP:8080
  v
Frontend Container
  |
  | HTTP API call
  v
API Container
  |
  | SQL
  v
Database Container
```

**Key note:**
Only **8080** and **5000** are exposed publicly.
Database traffic stays on the Docker network.

---

### 5. Why Docker Compose Here

Short justification:

- Single-host, multi-service orchestration
- Simple service discovery
- Clear separation between infra, config, runtime

Host configuration (Ansible):

- Installs Docker Engine and Docker Compose (plugin)
- Starts Docker daemon
- Grants non-root docker access (docker group)

---
### 6. Architectural Evolution: Docker Swarm

This project evolves from Docker Compose to Docker Swarm to introduce
service orchestration concepts such as replicas, self-healing, and
declarative service management.

Docker Swarm is enabled directly on the EC2 host. The node acts as both
Manager and Worker, which is sufficient for learning and demonstration
purposes while preserving the same architectural principles used in
larger clusters.

---

### 7. Swarm-Oriented System Architecture

```
Local Machine
|
| terraform apply
v
AWS Infrastructure
|
| ansible-playbook
v
EC2 Host
|
| docker swarm init
v
Docker Swarm (Manager + Worker)
|
| docker stack deploy
v
Swarm Services
|-- frontend (replicas)
|-- api (replicas)
|-- database (1 replica, persistent)
|-- visualizer
```

Mental Model: Swarm on EC2

```
EC2
├─ Docker Engine
├─ Swarm Mode (enabled)
└─ Services (not containers)
```
---

### 8. State Management and Scaling Model

- Frontend and API services are stateless and may be replicated.
- The database runs as a single-replica service and acts as the system’s
  single source of truth.
- State consistency is maintained at the database layer, not at the
  container or node level.

---

### 9. Why Docker Swarm

Docker Swarm is introduced to demonstrate:

- Declarative service management
- Horizontal scaling via replicas
- Automatic task rescheduling
- Built-in service discovery
- Cluster-level monitoring via Docker Visualizer
