# Deployment Overview

This system is deployed across multiple VPS nodes with isolated Docker-in-Docker environments.

## Nodes

- VPS PALADIN → Platform services
- VPS ANGEL → AI inference

---

## Deployment Strategy

### 1. Docker-in-Docker Layers

Each layer runs independently:

- servercluster_fire → Web Layer
- servercluster_ice → AI/ML Layer
- servercluster_lightning → Systems Layer

Each layer:
- Has its own Docker daemon
- Is managed via Portainer
- Runs independent stacks

---

### 2. Service Deployment

Services are deployed via Docker Compose stacks within each DinD layer.

Example:

- Web services → deployed in servercluster_fire
- ML services → deployed in servercluster_ice
- ERP → deployed in servercluster_lightning
- Inference → deployed on VPS ANGEL

---

### 3. Routing

- Traefik runs in the Web Layer
- Domains map to services using labels and rules
- External traffic enters only through Traefik

---

## Environment Configuration

Sensitive values are excluded.

Example structure:

TRAEFIK_DOMAIN=example.com
MLFLOW_URI=http://mlflow:5000

JESSICA_API=http://jessica-api:8000