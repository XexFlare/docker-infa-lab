# 🧱 Infrastructure Overview

This project represents a multi-node, multi-layer Docker infrastructure designed to run AI systems, web platforms, and enterprise applications in isolated but coordinated environments.

> This architecture is based on a real deployed system, with sensitive values and configurations replaced for security.

**Infrastructure Overview Diagram**
```
VPS PALADIN
└── ALPHA
    └── Portainer
        ├── servercluster_fire_ai_stack (Web Layer)
        │   └── traefik
        │       ├── agent_image_x
        │       ├── agent_vision_x
        │       ├── phoenix_ai_app
        │       ├── phoenix_ai_command
        │       ├── phoenix_ai_orchestrator
        │       ├── webportal_claw
        │       └── webportal_odoo
        │
        ├── servercluster_ice_ai_stack (AI / ML Layer)
        │   ├── mlflow
        │   ├── mlflow-fert-forecast
        │   └── openclaw_x
        │
        └── servercluster_lightning_ai_stack (Systems Layer)
            └── odoo_ferterp

VPS ANGEL
└── Jessica (Inference Layer)
    ├── jessica-ai
    ├── jessica-ai-architecture
    ├── jessica-ai-api
    ├── jessica-ai-chat
    └── jessica-ai-server
```

---

## 🖥️ Nodes & Container Layers

## VPS PALADIN (Primary Platform Node)

This node hosts multiple **Docker-in-Docker (DinD) environments**, each acting as an isolated execution layer with a specific responsibility.

---

### 🔥 servercluster_fire DinD (Web Layer)

Primary entry point for user-facing services.

**Responsibilities:**
- External routing
- User-facing applications
- Web portals

**Services:**
- traefik (reverse proxy within DinD)
- agent_image_x
- agent_vision_x
- phoenix_ai_app
- phoenix_ai_command
- phoenix_ai_orchestrator
- webportal_claw
- webportal_odoo

**Architecture Note:**

Traefik operates *inside* this DinD layer.

Due to Docker-in-Docker isolation:
- Direct routing to services outside this layer is restricted
- **Web portal containers act as controlled access bridges**
- These expose services across container boundaries via HTTP interfaces

---

### ❄️ servercluster_ice DinD (AI / ML Layer)

Dedicated to machine learning and AI workloads.

**Responsibilities:**
- Model training & tracking
- AI service execution
- Experimentation environments

**Services:**
- mlflow (experiment tracking)
- mlflow-fert-forecast (ML pipeline)
- openclaw_x (AI system runtime)

---

### ⚡ servercluster_lightning DinD (Systems Layer)

Enterprise and operational systems.

**Responsibilities:**
- Business systems
- ERP workflows
- Operational data handling

**Services:**
- odoo_ferterp (ERP platform)

---

### portainer
**🧭 Centralized Management (Portainer)**

A single Portainer instance is used to manage all Docker environments across VPS PALADIN and VPS ANGEL.

**Implementation**

Each Docker-in-Docker (DinD) layer exposes its own Docker daemon, registered in Portainer as a separate environment via:

- Docker socket / remote API
- Portainer Agent (for remote nodes)

Example environments:
- PALADIN-fire (web layer)
- PALADIN-ice (ML layer)
- PALADIN-lightning (systems layer)
- ANGEL-inference (AI node)

**Operational Model**

Portainer provides a unified interface to:

- Deploy Docker Compose stacks per environment
- Manage containers (lifecycle, logs, updates)
- Operate across nodes without direct SSH access

Deployments are scoped by environment:
- Web → fire
- ML → ice
- Systems → lightning
- Inference → ANGEL

**Architecture Role**

Portainer acts as a centralized control layer over multiple isolated Docker daemons.

Each DinD layer is network-isolated, with cross-layer communication handled via controlled HTTP interfaces (portals/APIs).

---


## VPS ANGEL (AI Inference Node)

Dedicated to **AI inference and modular AI service architecture**, separate from platform and training environments.

**Responsibilities:**
- Model inference
- AI orchestration
- Scalable AI services

**Services (Jessica AI ecosystem):**
- jessica-ai (core inference engine)
- jessica-ai-architecture (system design layer)
- jessica-ai-api (API interface)
- jessica-ai-chat (user interaction layer)
- jessica-ai-server (backend orchestration)

---

### 🌐 Domain Routing

Traffic is routed via Traefik (within the Web Layer DinD) using domain-based routing.

Domains are mapped to services and subservices:

- phoenix.ai → AI platform entry point
- fertep.com → ERP / business systems
- xclaw.com → AI tooling

Jessica AI ecosystem:
- architecture.jessica.ai
- api.jessica.ai
- chat.jessica.ai
- server.jessica.ai

Due to Docker-in-Docker isolation, Traefik cannot directly route to containers in other layers (other DinDs) or VPS nodes. 
To handle this, **web portal containers act as HTTP bridge endpoints**, receiving routed traffic and forwarding requests to target services (ML layer, systems layer, or VPS ANGEL) via internal or external APIs.
This enforces controlled cross-layer access while maintaining full network isolation between DinD environments.

---

### ⚙️ Key Characteristics

- Multi-node architecture (separate VPS roles)
- Multi-layer Docker-in-Docker segmentation
- Clear separation of:
  - Web layer (routing & UI)
  - AI/ML layer (training & experimentation)
  - Systems layer (ERP & operations)
  - Inference layer (dedicated AI execution node)
- Reverse proxy routing via Traefik
- Stack-based deployment via Portainer
- Cross-layer communication via controlled access points (web portals)
