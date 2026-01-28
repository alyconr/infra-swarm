# Infra Swarm - Real Estate Agent Infrastructure

This repository contains the Infrastructure as Code (IaC) for the ServiglobalAI Agent platform, deployed on a Docker Swarm cluster.

## Architecture Overview

The infrastructure is designed to be modular and scalable, separated into three main stacks:

1.  **Infra Stack (`infra/`)**: Core networking and edge routing.
2.  **Management Stack (`management/`)**: Cluster management and monitoring.
3.  **Automation Stack (`automation/`)**: Business logic and workflow automation.

## Stack Details

### 1. Infra Stack (`infra/docker-compose.yml`)

- **Traefik**: Acts as the Edge Router and Reverse Proxy. It handles:
  - Automatic SSL certificate generation (Let's Encrypt).
  - Routing traffic to services based on Host rules.
  - public_net: External overlay network for ingress traffic.

### 2. Management Stack (`management/portainer-stack.yml`)

- **Portainer Agent**: Provides a management interface for the Docker Swarm cluster.
- **Traefik Dashboard**: Securely exposed via Traefik for monitoring routing configuration.

### 3. Automation Stack (`automation/n8n-stack.yml`)

- **n8n**: Workflow automation tool for the real estate agent logic.
- **PostgreSQL**: Dedicated database for n8n data persistence.
- **Networking**:
  - `n8n_net`: Internal isolated network for n8n <-> Postgres communication.
  - `public_net`: Attached to n8n for external access via Traefik.

## Deployment

Deployment is automated via **GitHub Actions** (`.github/workflows/deploy-infra.yml`).

### Workflow: `Deploy Infra`

- **Trigger**: Push to `main` branch.
- **Actions**:
  1.  Checkout code.
  2.  Copy files to the remote server via SCP.
  3.  Deploy/Update stacks on the Swarm manager node using `docker stack deploy`.

## Prerequisites & Setup

1.  **Docker Swarm**: A initialized swarm cluster.
2.  **Networks**: Ensure `public_net` is created externally if not managed by the stack:
    ```bash
    docker network create -d overlay public_net
    ```
3.  **Environment Variables**:
    - Create a `.env` file or configure secrets in your CI/CD pipeline for sensitive values (Postgres credentials, domains, emails).

## Directory Structure

```
.
├── .github/                # GitHub Actions Workflows
├── automation/             # n8n and database stack
├── infra/                  # Traefik and networking stack
├── management/             # Portainer and monitoring stack
└── README.md               # Project documentation
```
