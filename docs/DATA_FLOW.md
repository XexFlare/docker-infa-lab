# Data Flow

This document describes how a typical AI request moves through the system.

## Example Flow: AI Request via phoenix.ai

1. User accesses:
   phoenix.ai

2. Request enters Web Layer:
   - Routed via Traefik
   - Sent to phoenix_ai_app

3. Application Layer:
   - phoenix_ai_app processes request
   - Delegates logic to phoenix_ai_orchestrator

4. Orchestration Layer:
   - Determines appropriate AI service
   - Routes request to VPS ANGEL

5. Inference Layer (VPS ANGEL):
   - jessica-ai-api receives request
   - jessica-ai processes inference
   - Response generated

6. Response Path:
   - Returned to orchestrator
   - Passed back to phoenix_ai_app
   - Displayed to user

---

## Key Observations

- Web Layer does not directly execute AI workloads
- AI/ML Layer is not used for real-time inference
- Inference is fully isolated on VPS ANGEL
- All cross-layer communication occurs via controlled APIs