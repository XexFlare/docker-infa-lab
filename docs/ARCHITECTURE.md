# Architecture Overview

This platform is a multi-node, multi-layer AI infrastructure designed to isolate workloads while enabling controlled interaction between services.

## Core Design Principle

The system is built on **functional isolation using Docker-in-Docker (DinD)**.

Each layer runs in its own DinD environment, enforcing strict separation between:

- Web / UI services
- AI / ML workloads
- Business systems (ERP)
- AI inference

---

## Node Structure

### VPS PALADIN (Primary Platform Node)

Hosts three isolated DinD environments:

#### 🔥 Web Layer (servercluster_fire)

Responsibilities:
- External routing (Traefik)
- User-facing applications
- Portal-based access bridging

Key Constraint:
- Cannot directly route to services outside its DinD layer
- Uses web portals as controlled access points

---

#### ❄️ AI/ML Layer (servercluster_ice)

Responsibilities:
- MLflow experiment tracking
- Model pipelines
- AI runtime environments

Designed for:
- Training
- Experimentation
- Iteration

---

#### ⚡ Systems Layer (servercluster_lightning)

Responsibilities:
- ERP (Odoo)
- Operational systems
- Business workflows

---

### VPS ANGEL (Inference Node)

Dedicated AI inference environment.

Responsibilities:
- Model execution
- AI service orchestration
- API exposure

Key Design Choice:
- Fully separated from training and UI layers to avoid resource contention and improve scalability.

---

## Routing Strategy

- Traefik runs inside the Web Layer DinD
- Domain-based routing is used for service exposure
- Cross-layer communication is handled via HTTP interfaces (portals and APIs)

---

## Key Architectural Decisions

- DinD per layer to enforce hard isolation boundaries
- Dedicated inference node for performance and scalability
- Portal-based bridging to manage cross-layer communication
- Domain-driven routing for clean service separation