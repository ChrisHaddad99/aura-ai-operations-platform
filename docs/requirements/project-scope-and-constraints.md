# AURA — Project Scope and Constraints

**Document:** Project Scope and Constraints  
**Project:** AURA — AI Unified Response & Automation  
**Version:** 1.0  
**Status:** Initial baseline

**Related documents:**
- `docs/requirements/project-charter.md`
- `docs/requirements/functional-requirements.md`
- `docs/requirements/non-functional-requirements.md`
- `docs/requirements/use-cases-and-user-stories.md`
- `docs/requirements/acceptance-criteria-and-definition-of-done.md`

---

# 1. Purpose

This document defines the scope, boundaries, assumptions, constraints, and priorities of the initial AURA implementation.

Its purpose is to prevent uncontrolled scope expansion and ensure that development remains focused on delivering a complete, demonstrable, and reliable business automation platform.

The initial implementation is designed as a portfolio flagship project demonstrating production-oriented AI automation engineering.

# 2. Project Scope

## 2.1 Project Vision

AURA is an AI-powered business operations platform that automates repetitive administrative and operational workflows while maintaining human oversight over sensitive decisions and actions.

The initial implementation targets small technical service and maintenance businesses.

AURA will demonstrate how AI, workflow automation, knowledge retrieval, external integrations, and human approval mechanisms can work together in a coherent system.

## 2.2 Target Business Scenario

The initial reference business is a technical maintenance and services company.

The company receives customer requests through email, including:
- Equipment malfunction reports.
- Maintenance requests.
- Installation requests.
- Technical support questions.
- Requests for appointments.
- General service inquiries.

AURA processes these communications and coordinates the resulting business workflow.

The expected process is:

1. Receive a customer email.
2. Identify the customer.
3. Classify and prioritize the request.
4. Extract structured information.
5. Retrieve relevant technical knowledge.
6. Create a ticket and operational tasks.
7. Determine appropriate next actions.
8. Execute permitted low-risk actions automatically.
9. Request human approval for actions requiring authorization.
10. Communicate with the customer.
11. Coordinate appointments through Calendly.
12. Track the request lifecycle and execution history.

## 2.3 MVP Scope

The Minimum Viable Product (MVP) must implement the following capabilities.

### A. Request Intake
- Receive emails through IMAP.
- Extract email metadata and content.
- Handle basic email attachments.
- Detect duplicate incoming messages.
- Store original communications.
- Create internal request records.

### B. Customer Management
- Maintain basic customer records.
- Identify customers using available email information.
- Associate requests with customers.
- Retrieve customer request history.

### C. AI Request Processing
- Classify incoming requests.
- Extract structured information.
- Determine request priority.
- Identify missing or ambiguous information.
- Handle uncertain or invalid AI outputs safely.

### D. Knowledge Retrieval
- Ingest internal business documents.
- Generate and store document embeddings.
- Retrieve relevant knowledge using RAG.
- Provide source references for retrieved information.
- Avoid unsupported technical claims when relevant information is unavailable.

### E. Ticket and Task Management
- Create tickets from customer requests.
- Maintain ticket lifecycle states.
- Create operational tasks.
- Associate tasks with tickets and customers.
- Track updates and execution history.

### F. Decision-Making and Automation
- Determine appropriate actions based on request context.
- Classify actions according to risk.
- Execute permitted low-risk actions automatically.
- Require human authorization for high-risk actions.
- Record execution outcomes.

### G. Human Approval
- Provide a web dashboard.
- Display pending approval requests.
- Allow authorized users to approve or reject actions.
- Enforce authorization on the backend.
- Record approval decisions and timestamps.

### H. Customer Communication
- Send emails through SMTP.
- Send request acknowledgments.
- Request missing information.
- Send relevant customer updates.
- Generate Calendly scheduling links.

### I. Appointment Scheduling
- Integrate with Calendly.
- Receive booking events through webhooks.
- Associate appointments with customer requests.
- Track appointment status.

### J. Reliability and Observability
- Maintain persistent request and workflow state.
- Implement retry and recovery mechanisms.
- Prevent unintended duplicate actions.
- Record important AI and workflow events.
- Provide structured logs and basic operational monitoring.
- Support controlled reprocessing of failed requests.

### K. Development and Deployment
- Use containerized services.
- Provide a Docker Compose development environment.
- Implement automated tests.
- Configure a GitHub Actions CI pipeline.
- Document installation and configuration.
- Deploy a working demonstration environment to the Linux home server.

# 3. Explicitly Out of Scope for the MVP

The following capabilities are intentionally excluded from the initial implementation.

| Capability | Reason for Exclusion |
|---|---|
| Multi-tenant SaaS architecture | Adds significant authentication, isolation, billing, and operational complexity. |
| Full CRM functionality | AURA only needs basic customer management initially. |
| Native mobile applications | A web dashboard is sufficient for the initial demonstration. |
| Multiple communication channels | Email is sufficient to demonstrate the core workflow. |
| Autonomous financial transactions | Introduces unnecessary financial and authorization risks. |
| Autonomous execution of high-risk actions | High-risk actions always require explicit human authorization. |
| Full ERP integration | Not required for the initial reference scenario. |
| Advanced workforce management | Basic task creation and tracking are sufficient. |
| Complex predictive analytics | Not necessary to validate the core platform. |
| Kubernetes deployment | Docker Compose is sufficient for the initial environment. |
| Distributed microservices architecture | A modular monolith is preferred initially. |
| Enterprise identity federation | Basic secure authentication and authorization are sufficient for the MVP. |
| Advanced multi-agent collaboration | Agent complexity should only be introduced when justified by real workflows. |

These capabilities may be considered in future versions.

# 4. Technical Constraints

## 4.1 Development Environment

The project will use two primary environments:

**Development machine**
- Windows laptop.
- PowerShell terminal.
- Local development tools.
- Git for version control.

**Deployment and testing machine**
- Linux-based home server.
- Remote access through SSH.
- Docker-based deployment.

The development and deployment environments must remain sufficiently consistent to reduce environment-specific issues.

## 4.2 Deployment

- Docker and Docker Compose will be used for containerization and service orchestration.
- Application components must be configurable through environment variables or appropriate configuration files.
- Persistent data must use appropriate storage volumes.
- Secrets must not be committed to Git.
- The deployment must be reproducible using documented instructions.

## 4.3 Architecture

- The initial backend architecture will use a modular monolith.
- Core business logic must remain independent of external integration implementations where practical.
- External services must be accessed through clearly defined interfaces.
- The architecture must allow future replacement of AI providers and integrations.
- Microservices must only be introduced if justified by concrete requirements.

## 4.4 External Dependencies

The initial system depends on:
- An AI model provider.
- An email server supporting IMAP and SMTP.
- Calendly.
- A PostgreSQL database.
- Container infrastructure.

External dependencies may introduce availability, rate-limit, cost, and configuration constraints.

AURA must handle external service failures without silently losing workflow state.

# 5. Business and Operational Constraints

## 5.1 Human Oversight

AURA is designed to automate business operations, not eliminate accountability.

The following principles are mandatory:

- High-risk actions require explicit human authorization.
- Approval permissions must be enforced by the backend.
- AI-generated recommendations must not automatically grant execution permission.
- Approval and execution decisions must be auditable.
- Uncertain AI outputs must be handled conservatively.

## 5.2 Data Protection

- Customer information must be handled securely.
- Only necessary information should be collected and stored.
- Credentials and API keys must be protected.
- Sensitive information must not be unnecessarily exposed in logs.
- Data retention and deletion policies must be documented.
- External AI services must only receive data permitted by the configured privacy policy.

## 5.3 Availability

The initial MVP is a demonstration and development platform, not a mission-critical enterprise service.

Nevertheless:
- Unexpected application failures must not silently destroy request data.
- Important state must be persisted.
- Integration failures must be visible.
- Recovery procedures must be documented.
- Backups must be considered for persistent business data.

# 6. Project Priorities

Development priorities are ordered as follows.

| Priority | Area | Objective |
|---|---|---|
| P0 | Core request workflow | Receive, register, process, and track customer requests. |
| P0 | Human approval and security | Prevent unauthorized execution of protected actions. |
| P1 | AI processing and RAG | Demonstrate structured AI processing and knowledge retrieval. |
| P1 | Workflow automation | Coordinate request processing and business actions. |
| P1 | External integrations | Demonstrate email and Calendly integration. |
| P1 | Reliability and observability | Provide traceability, failure handling, and recovery. |
| P2 | Web dashboard | Provide operational visibility and approval management. |
| P2 | Deployment and documentation | Deliver a reproducible demonstration environment. |
| P3 | Advanced AI capabilities | Explore additional agent and automation capabilities after MVP validation. |

Security and data integrity are mandatory across all priorities, not optional enhancements.

# 7. Project Constraints and Trade-offs

The project is developed within a limited initial timeframe of approximately one to two months.

The following trade-offs guide implementation:

### Simplicity over premature complexity
Use a modular monolith rather than introducing microservices without a demonstrated need.

### Working workflows over isolated demonstrations
Prioritize complete end-to-end business scenarios over disconnected technical experiments.

### Reliability over excessive autonomy
Use human approval and explicit execution policies rather than maximizing autonomous behavior.

### Practical integration over theoretical architecture
Implement real integrations with email, Calendly, databases, and AI services.

### Replaceability over unnecessary abstraction
Keep provider-dependent components replaceable without creating excessive abstraction layers.

### Demonstrable quality over feature count
Prioritize tests, traceability, observability, security, and reproducible deployment.

# 8. MVP Success Criteria

The MVP will be considered successful when it can demonstrate the following complete workflow:

1. A customer sends a service request by email.
2. AURA retrieves and stores the email.
3. AURA identifies the customer.
4. AI classifies the request and extracts structured information.
5. AURA retrieves relevant business knowledge.
6. AURA creates a ticket and associated tasks.
7. AURA determines the appropriate actions.
8. Permitted low-risk actions execute automatically.
9. High-risk actions are blocked until explicitly approved by an authorized user.
10. AURA communicates with the customer.
11. AURA can coordinate an appointment through Calendly.
12. The complete processing history can be inspected through the dashboard.
13. Failures can be detected, investigated, and safely recovered.
14. Automated tests validate the critical business workflows.
15. The application can be deployed reproducibly using Docker Compose.

The system must demonstrate that these capabilities work together as one coherent application.

# 9. Future Expansion

After the MVP is validated, potential expansion areas include:

- Additional AI providers and model evaluation.
- Additional communication channels.
- More advanced agent orchestration.
- Additional business integrations.
- More sophisticated approval policies.
- Advanced analytics and reporting.
- Multi-tenant architecture.
- Configurable business workflows.
- Industry-specific automation packages.
- SaaS deployment and commercial service offerings.

Future expansion must be driven by validated business needs rather than added solely for technical complexity.

# 10. Scope Management

Any significant change to the MVP scope must be evaluated against:

- Business value.
- Implementation effort.
- Technical complexity.
- Security implications.
- Impact on existing requirements.
- Impact on the project timeline.

New features must not compromise the delivery of the core end-to-end workflow.

Changes to project scope must be reflected in the relevant project documentation and Git history.

---

**End of document**