# AURA — System Architecture

**Document:** System Architecture  
**Project:** AURA — AI Unified Response & Automation  
**Version:** 1.0  
**Status:** Initial architecture baseline

## 1. Purpose

This document defines the high-level architecture of AURA, including its architectural style, major components, responsibilities, interactions, and system boundaries.

It establishes the architectural foundation for implementation and subsequent detailed design.

## 2. Architecture Overview

AURA will use a **modular monolithic architecture** supported by external infrastructure and integration services.

The backend will be implemented using Python and FastAPI.

The application will be organized into independent functional modules with clearly defined responsibilities and interfaces.

The initial architecture prioritizes:
- Maintainability and modularity.
- Reliable business workflow execution.
- AI integration and orchestration.
- Human oversight and authorization.
- External system integration.
- Observability and traceability.
- Future extensibility.

Microservices are intentionally excluded from the initial implementation.

## 3. High-Level System Architecture

```text
                   EXTERNAL SYSTEMS
        ┌─────────────┬───────────────┐
        │ Email Server│    Calendly   │
        │  IMAP/SMTP  │    Webhooks   │
        └──────┬──────┴───────┬───────┘
               │              │
               ▼              ▼
┌───────────────────────────────────────────┐
│                  AURA                     │
│                                           │
│  ┌─────────────────────────────────────┐  │
│  │         Presentation Layer          │  │
│  │          FastAPI / Dashboard        │  │
│  └──────────────────┬──────────────────┘  │
│                     │                     │
│  ┌──────────────────▼──────────────────┐  │
│  │          Application Layer          │  │
│  │                                     │  │
│  │  Use Cases / Workflow Coordination │  │
│  │  Background Job Coordination       │  │
│  └──────────────────┬──────────────────┘  │
│                     │                     │
│  ┌──────────────────▼──────────────────┐  │
│  │             Domain Layer            │  │
│  │                                     │  │
│  │  Customers / Requests               │  │
│  │  Tickets / Tasks                    │  │
│  │  Approvals / Business Rules         │  │
│  │  Risk and Execution Policies        │  │
│  └──────────────────┬──────────────────┘  │
│                     │                     │
│  ┌──────────────────▼──────────────────┐  │
│  │          AI and Intelligence        │  │
│  │                                     │  │
│  │  Classification / Extraction        │  │
│  │  RAG / Knowledge Retrieval          │  │
│  │  Decision Support / Agent Logic     │  │
│  └──────────────────┬──────────────────┘  │
│                     │                     │
│  ┌──────────────────▼──────────────────┐  │
│  │          Integration Layer          │  │
│  │                                     │  │
│  │  Email / Calendly / AI Providers    │  │
│  │  n8n Integration                    │  │
│  └──────────────────┬──────────────────┘  │
└─────────────────────┼─────────────────────┘
                      │
             ┌────────▼────────┐
             │   PostgreSQL    │
             │                 │
             │ Business Data   │
             │ Workflow State  │
             │ Vector Data     │
             └─────────────────┘

        ┌─────────────────────────────┐
        │       Infrastructure        │
        │                             │
        │ Background Job Processing  │
        │ Logging and Observability   │
        │ Docker / Docker Compose     │
        └─────────────────────────────┘
```

This diagram represents logical responsibilities rather than a final deployment topology.

## 4. Architectural Style

### 4.1 Modular Monolith

AURA will initially be deployed as one primary backend application.

Internally, the application will be divided into functional modules.

Each module must:
- Have a clearly defined responsibility.
- Expose well-defined interfaces.
- Avoid unnecessary dependencies on unrelated modules.
- Keep its business logic within its own boundaries.
- Be independently testable where practical.

The modular monolith approach provides a balance between architectural structure and implementation simplicity.

It also avoids the operational complexity of distributed services during the MVP phase.

### 4.2 Layered Architecture

The backend will follow a layered architecture.

**Presentation Layer**

Responsible for:
- HTTP API endpoints.
- Request validation.
- Authentication and authorization entry points.
- Dashboard communication.
- Webhook endpoints.

**Application Layer**

Responsible for:
- Coordinating business use cases.
- Managing workflow execution.
- Calling domain services.
- Coordinating background jobs.
- Managing transactions across application operations.

**Domain Layer**

Responsible for:
- Business entities and rules.
- Customer and request lifecycle.
- Ticket and task management.
- Approval policies.
- Risk classification rules.
- Action execution constraints.

The domain layer must not depend directly on external infrastructure or AI providers.

**AI Layer**

Responsible for:
- Request classification.
- Information extraction.
- Knowledge retrieval.
- AI-assisted decision support.
- Agent orchestration.
- Structured AI outputs.

AI outputs must be validated before being used by business workflows.

**Integration Layer**

Responsible for:
- Email communication.
- Calendly communication.
- AI provider communication.
- n8n integration.
- External API interactions.

External systems must be accessed through controlled interfaces rather than directly from business logic.

**Infrastructure Layer**

Responsible for:
- Database connectivity.
- Background job infrastructure.
- Persistent storage.
- Logging.
- Monitoring.
- Containerized deployment.

## 5. Core Application Modules

| Module | Responsibility |
|---|---|
| Customer Management | Customer records, identification, and customer history. |
| Request Management | Incoming requests, request classification data, and lifecycle management. |
| Ticket Management | Operational tickets and their status transitions. |
| Task Management | Internal operational tasks and assignments. |
| Workflow Management | Coordination and execution of business workflows. |
| Approval Management | Approval requests, decisions, and authorization checks. |
| AI Orchestration | Coordination of AI operations and agent execution. |
| Knowledge Management | Document ingestion, indexing, and retrieval. |
| Communication | Incoming and outgoing customer communications. |
| Integration Management | External service adapters and integration configuration. |
| Audit and Observability | Execution history, structured logging, and operational events. |

These modules are logical boundaries. They do not initially represent separate deployable services.

## 6. Workflow Orchestration Strategy

AURA will own the execution of its core business workflows.

Core business workflows include:
- Customer request processing.
- Customer identification.
- Ticket creation.
- Risk evaluation.
- Approval handling.
- Action execution.
- Request lifecycle management.
- Failure recovery.

AURA will maintain workflow state and enforce business rules internally.

### 6.1 Role of n8n

n8n will be used selectively for supporting automation and external integrations.

Potential use cases include:
- Additional notification workflows.
- Supporting external service integrations.
- Scheduled automation.
- Integration prototypes.
- Non-critical automation tasks.

n8n must not become the source of truth for AURA business state.

It must not independently authorize protected actions or bypass AURA's approval mechanisms.

### 6.2 Workflow Ownership

AURA remains responsible for:
- Business workflow state.
- Execution permissions.
- Risk policies.
- Approval decisions.
- Idempotency.
- Audit history.
- Recovery decisions.

This ensures consistent business behavior regardless of which external integrations are used.

## 7. Background Processing

Some operations may take too long to execute within a normal HTTP request.

Examples include:
- Email polling and processing.
- AI model requests.
- Document ingestion.
- Embedding generation.
- Knowledge indexing.
- External integration calls.
- Retry and recovery operations.

These operations will use asynchronous background processing.

The initial architecture will separate:
- API request handling.
- Background job submission.
- Background job execution.
- Persistent workflow state.

The exact queue and worker technology will be selected during TASK 10.

Background jobs must support appropriate retry behavior, failure reporting, and idempotency.

## 8. AI Architecture Principles

AI capabilities will be integrated as services within the AURA architecture.

AI will support:
- Classification.
- Structured information extraction.
- Knowledge retrieval.
- Decision support.
- Response generation.
- Agent-based task execution where justified.

The AI layer must not have unrestricted authority over business operations.

The application must independently validate:
- AI output structure.
- Required information.
- Risk classification.
- Execution permissions.
- Approval status.
- Action parameters.

The AI provider must be replaceable through a provider abstraction where practical.

## 9. Data Management

PostgreSQL is the proposed primary database.

It will initially support:
- Customer records.
- Incoming requests.
- Tickets and tasks.
- Approval records.
- Workflow state.
- Audit events.
- Knowledge document metadata.
- Vector embeddings through pgvector.

Persistent state must be stored independently of application process memory.

The detailed data model and database design will be defined in TASK 09.

## 10. External Integrations

AURA will initially integrate with:

| Integration | Purpose | Communication |
|---|---|---|
| Email Server | Receive customer requests | IMAP |
| Email Server | Send customer communications | SMTP |
| Calendly | Appointment scheduling | API and webhooks |
| AI Provider | AI processing and reasoning | Provider API |
| n8n | Supporting workflow automation | API / Webhooks |

External integration implementations must be isolated behind appropriate adapters.

Integration failures must not silently corrupt business state.

## 11. Security Architecture

Security must be enforced across all architectural layers.

Key principles:

- Authentication for protected application functions.
- Role-based authorization where appropriate.
- Server-side approval enforcement.
- Secure credential management.
- Input validation.
- Protection against prompt injection and untrusted external instructions.
- Audit logging of sensitive operations.
- Least-privilege integration access.

AI-generated instructions must never override application authorization rules.

A high-risk action must not execute without explicit approval from an authorized user.

## 12. Reliability and Observability

AURA must provide visibility into application and workflow execution.

The architecture must support:
- Structured application logs.
- Request and workflow identifiers.
- AI execution metadata.
- Integration error reporting.
- Background job monitoring.
- Retry and recovery mechanisms.
- Persistent workflow state.
- Audit history.

Critical workflow operations must be traceable from initial customer request through final execution outcome.

## 13. Deployment Architecture

The initial deployment will use Docker and Docker Compose.

The intended environments are:

**Development**
- Windows development machine.
- Local application development.
- Local containers and dependencies.
- Git-based version control.

**Demonstration / Staging**
- Linux home server.
- SSH-based administration.
- Docker Compose deployment.
- Persistent database storage.

The architecture should remain portable to future cloud infrastructure.

Kubernetes and distributed deployment are not required for the MVP.

## 14. Architectural Constraints

The initial architecture must respect the following constraints:

1. The backend uses a modular monolithic design.
2. Python and FastAPI are the initial backend technologies.
3. Core business workflows remain under AURA control.
4. n8n is a supporting automation tool, not the business source of truth.
5. Background processing is required for long-running operations.
6. PostgreSQL is the proposed primary database.
7. External services must be accessed through controlled integration interfaces.
8. AI execution must be subject to application-level validation and authorization.
9. The system must support containerized deployment.
10. The architecture must remain practical to implement within the initial project timeframe.

## 15. Future Evolution

The architecture should allow future expansion without requiring an immediate redesign.

Potential future improvements include:
- Separating selected modules into services when justified.
- Supporting multiple businesses and tenants.
- Additional AI providers.
- Additional external integrations.
- More advanced agent orchestration.
- Distributed background processing.
- Cloud deployment.
- Advanced monitoring and analytics.

Such changes must be driven by actual scalability, reliability, or business requirements.

## 16. Conclusion

AURA will begin as a modular monolithic application with clear architectural boundaries.

Its core business logic, workflow state, and authorization policies will remain under the control of the AURA backend.

AI will provide intelligence and decision support, while the application remains responsible for validating and executing business operations.

This architecture provides a practical foundation for building a reliable, extensible, and production-oriented AI automation platform.

---

**End of document**