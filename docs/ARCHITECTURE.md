# Architecture: Sovereign Career Matcher

## Overview

**Package ID:** `PKG-018`  
**Domain:** NLP & Resume Optimization  
**Microservice Port:** `8796`  
**n8n Webhook Path:** `career-matcher-trigger`  
**GitHub:** [BlackFoxgamingstudio/career-matcher](https://github.com/BlackFoxgamingstudio/career-matcher)

Resume-to-job NLP matching engine with semantic similarity scoring, skill gap analysis, ATS optimization, and employer fit prediction.

---

## System Architecture

```
                     ┌──────────────────────────────────┐
                     │       Sovereign Career Matcher      │
                     │       Port: 8796            │
                     ├──────────────┬───────────────────┤
   n8n Webhook ────▶ │  REST API    │   Core Engine     │
   HTTP POST         │  /api/v1/*   │   Dispatcher      │
                     └──────┬───────┴────────┬──────────┘
                            │                │
              ┌─────────────▼────────────────▼─────────┐
              │          Component Layer                 │
              │  ResumeParser    | JobMatcher      | SkillGapAnal  │
              └────────────────────────┬────────────────┘
                                       │
              ┌────────────────────────▼────────────────┐
              │      n8n Central Event Bus (:5678)       │
              └─────────────────────────────────────────┘
```

## Core Components

### `ResumeParser`
Handles all resumeparser operations. Exposes async methods callable from the core dispatcher.

### `JobMatcher`
Handles all jobmatcher operations. Exposes async methods callable from the core dispatcher.

### `SkillGapAnalyzer`
Handles all skillgapanalyzer operations. Exposes async methods callable from the core dispatcher.

### `ATSOptimizer`
Handles all atsoptimizer operations. Exposes async methods callable from the core dispatcher.

### `FitPredictor`
Handles all fitpredictor operations. Exposes async methods callable from the core dispatcher.

---

## API Contract

All interactions follow the SBB standard envelope:

```http
POST /api/v1/execute
Content-Type: application/json
X-SBB-API-Key: <api-key>

{
  "action": "<operation>",
  "payload": {},
  "trace_id": "optional-uuid"
}
```

**Success Response (HTTP 200):**
```json
{
  "status": "success",
  "data": {},
  "trace_id": "...",
  "timestamp": "2025-01-01T00:00:00Z"
}
```

**Health Check:**
```http
GET /health
→ {"status": "healthy", "service": "sovereign-career-matcher", "port": 8796}
```

## Integration Matrix

| System | Protocol | Direction | Purpose |
|--------|----------|-----------|---------|
| n8n Event Bus (:5678) | HTTP POST | Outbound | Event forwarding |
| n8n Webhook | HTTP POST | Inbound | Trigger execution |
| SBB Codebase Vault (:8766) | HTTP | Outbound | Code analysis |
| SBB Patterns Bible (:8794) | HTTP | Outbound | Standards validation |
| External APIs | HTTPS | Outbound | Domain-specific data |

## Deployment Architecture

```yaml
# docker-compose excerpt
sovereign-career-matcher:
  image: sovereign-career-matcher:latest
  ports: ["8796:8796"]
  healthcheck:
    test: curl -f http://localhost:8796/health
    interval: 30s
```

## Security Model

| Control | Implementation |
|---------|---------------|
| Authentication | `X-SBB-API-Key` header (env: `SBB_API_KEY`) |
| Rate Limiting | 100 req/min per client IP |
| Input Validation | Pydantic models (strict mode) |
| Container Security | Non-root user (`appuser:1001`) |
| Secrets | Environment variables only (never hardcoded) |
| TLS | Terminate at reverse proxy (nginx/caddy) |

## Tags
`nlp`, `resume`, `job-matching`, `ats`, `career`
