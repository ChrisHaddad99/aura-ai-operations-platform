# AURA — Use Cases and User Stories

**Document:** Use Cases and User Stories  
**Project:** AURA — AI Unified Response & Automation  
**Version:** 1.0  
**Status:** Initial baseline  
**Related documents:**
- `docs/requirements/project-charter.md`
- `docs/requirements/functional-requirements.md`
- `docs/requirements/non-functional-requirements.md`

---

# 1. Purpose

This document describes the main interactions between AURA, its users, and external systems.

It translates the functional requirements into practical business scenarios. These scenarios will help guide the system architecture, implementation, and testing.

The initial scenarios are based on a small technical maintenance and services company that receives customer requests by email.

# 2. Actors

| Actor | Description |
|---|---|
| Customer | External person or organization submitting a service request. |
| Operations Staff | Employee responsible for managing customer requests and operational tasks. |
| Manager / Approver | Person authorized to review and approve actions requiring human oversight. |
| Administrator | Person responsible for configuring and maintaining AURA. |
| AURA System | The platform responsible for processing requests and coordinating workflows. |
| Email Server | External service used to receive and send emails through IMAP and SMTP. |
| Calendly | External scheduling service used to coordinate appointments. |
| AI Model Provider | External AI service used for classification, information extraction, reasoning, and response generation. |

# 3. Use Case Catalogue

| ID | Use Case | Primary Actor | Description |
|---|---|---|---|
| UC-001 | Receive Customer Request | Customer | A customer submits a request by email, which AURA retrieves and registers. |
| UC-002 | Process and Classify Request | AURA System | AURA analyzes the request, identifies its type, extracts relevant information, and determines its priority. |
| UC-003 | Retrieve Business Knowledge | AURA System | AURA searches internal knowledge sources to obtain information relevant to the request. |
| UC-004 | Request Human Approval | Manager / Approver | AURA requests authorization before executing an action that requires human oversight. |
| UC-005 | Execute Authorized Action | AURA System | AURA executes an action automatically when permitted or after receiving the required approval. |
| UC-006 | Schedule Customer Appointment | Customer / Operations Staff | AURA coordinates an appointment using Calendly. |
| UC-007 | Handle Processing Failure | Administrator / Operations Staff | AURA detects a processing failure, records it, and supports recovery or manual intervention. |

# 4. Detailed Use Cases

## UC-001 — Receive Customer Request

**Primary actor:** Customer  
**Supporting system:** Email Server

### Preconditions
- AURA is configured with valid IMAP connection details.
- The email service is available.
- The customer can send an email to the configured business address.

### Main flow
1. The customer sends an email describing a technical issue or service request.
2. The email server receives the message.
3. AURA retrieves the message through IMAP.
4. AURA extracts the sender, subject, message body, timestamp, and available attachments.
5. AURA checks whether the message has already been processed.
6. If the message is new, AURA stores the original communication and creates a request record.
7. AURA initiates the request-processing workflow.

### Alternative flows
- **A1 — Duplicate message:** AURA detects that the message has already been processed and avoids creating a duplicate request.
- **A2 — Invalid email content:** AURA stores the message and marks it for review if required information cannot be extracted.
- **A3 — Attachment processing failure:** AURA records the failure and continues processing the available email content when possible.
- **A4 — Email server unavailable:** AURA records the integration failure and retries retrieval according to its retry policy.

### Expected outcome
The incoming email is stored and associated with a request record, ready for processing.

**Related requirements:** FR-001 to FR-005.

---

## UC-002 — Process and Classify Request

**Primary actor:** AURA System  
**Supporting actor:** Operations Staff

### Preconditions
- A customer request has been registered.
- The AI processing service is configured and available.

### Main flow
1. AURA retrieves the request content.
2. AURA identifies the customer using available sender and customer information.
3. AURA analyzes the request using an AI model.
4. AURA classifies the request into an appropriate category, such as:
   - Maintenance
   - Repair
   - Installation
   - Technical support
   - General inquiry
5. AURA extracts structured information, such as equipment, location, issue description, and requested service.
6. AURA determines the request priority.
7. AURA identifies missing or ambiguous information.
8. AURA stores the structured analysis and processing result.
9. AURA continues to the appropriate workflow.

### Alternative flows
- **A1 — Unknown customer:** AURA marks the customer identity as unresolved and requests staff review or additional information.
- **A2 — Insufficient information:** AURA identifies missing information and initiates a clarification workflow.
- **A3 — AI processing failure:** AURA records the error and schedules a retry or manual review.
- **A4 — Low-confidence classification:** AURA flags the result for staff review rather than relying on an uncertain classification.

### Expected outcome
The request contains structured information, a classification, a priority, and any identified information gaps.

**Related requirements:** FR-006 to FR-010, FR-012.

---

## UC-003 — Retrieve Business Knowledge

**Primary actor:** AURA System

### Preconditions
- A customer request has been registered.
- Relevant business knowledge has been ingested into the knowledge base.
- The retrieval service is available.

### Main flow
1. AURA identifies the information needed to process the request.
2. AURA generates a search query from the request context.
3. AURA searches the internal knowledge base.
4. The retrieval system returns relevant documents or document sections.
5. AURA evaluates the retrieved information for relevance.
6. AURA provides relevant context to the AI processing workflow.
7. AURA records the retrieval results and associated references.

### Alternative flows
- **A1 — No relevant information found:** AURA records that no suitable information was retrieved and avoids inventing technical facts.
- **A2 — Knowledge base unavailable:** AURA records the failure and follows the configured retry or manual-review process.
- **A3 — Conflicting information:** AURA flags the conflict for human review when it cannot establish a reliable answer.

### Expected outcome
AURA has relevant internal knowledge available to support its decisions or responses, or has explicitly identified that suitable information is unavailable.

**Related requirements:** FR-013 to FR-016.

---

## UC-004 — Request Human Approval

**Primary actor:** Manager / Approver  
**Supporting system:** AURA Web Dashboard

### Preconditions
- AURA has identified an action that requires human authorization.
- An authorized approver is configured.
- The action and its context have been recorded.

### Main flow
1. AURA determines that the proposed action requires human approval.
2. AURA creates an approval request.
3. AURA records the proposed action, its context, and the reason approval is required.
4. AURA displays the pending approval in the web dashboard.
5. The approver reviews the request.
6. The approver approves or rejects the proposed action.
7. AURA records the decision, approver, and timestamp.
8. AURA updates the workflow state.
9. If approved, AURA allows the corresponding execution workflow to continue.
10. If rejected, AURA prevents execution and records the rejection.

### Alternative flows
- **A1 — Approval pending:** The request remains pending until an authorized decision is made.
- **A2 — Approval rejected:** AURA prevents execution and records the reason when provided.
- **A3 — Unauthorized user:** AURA rejects the attempted approval and records the event.
- **A4 — Duplicate approval attempt:** AURA prevents the same approval request from being processed more than once.
- **A5 — Approval service unavailable:** AURA preserves the pending request and makes it available when the dashboard is restored.

### Expected outcome
The proposed action has an auditable approval decision. No protected action is executed without the required authorization.

**Related requirements:** FR-024 to FR-027, FR-033 to FR-035.

---

## UC-005 — Execute Authorized Action

**Primary actor:** AURA System

### Preconditions
- AURA has identified an action to execute.
- The action is classified according to its risk level.
- Any required approval has been obtained.
- The required integration or service is available.

### Main flow
1. AURA evaluates the proposed action and its risk level.
2. AURA checks whether the action is permitted to execute automatically.
3. If approval is required, AURA verifies that a valid approval exists.
4. AURA validates the action parameters.
5. AURA executes the action through the appropriate integration or internal service.
6. AURA records the execution result.
7. AURA updates the associated request, ticket, or task.
8. AURA continues the workflow according to the result.

### Alternative flows
- **A1 — Low-risk action:** AURA executes the action automatically if it meets the configured execution policy.
- **A2 — Medium-risk action:** AURA follows the configured policy, which may require additional checks or human approval.
- **A3 — High-risk action:** AURA requires explicit human authorization before execution.
- **A4 — Approval missing:** AURA blocks execution.
- **A5 — Execution failure:** AURA records the failure and initiates the appropriate retry or recovery process.
- **A6 — Duplicate execution attempt:** AURA uses idempotency controls to prevent unintended repeated actions.

### Expected outcome
The action is executed only when permitted, and its result is recorded and linked to the originating request.

**Related requirements:** FR-021 to FR-024, FR-028, FR-033 to FR-035.

---

## UC-006 — Schedule Customer Appointment

**Primary actors:** Customer, Operations Staff  
**Supporting system:** Calendly

### Preconditions
- A customer request requires an appointment.
- AURA is configured to integrate with Calendly.
- The business has an available scheduling configuration.

### Main flow
1. AURA determines that an appointment is needed.
2. AURA generates or retrieves the appropriate Calendly scheduling link.
3. AURA sends the scheduling link to the customer by email.
4. The customer opens the link and selects an available appointment.
5. Calendly processes the booking.
6. Calendly sends a booking event to AURA through a webhook.
7. AURA validates and processes the event.
8. AURA associates the appointment with the customer request.
9. AURA updates the request or ticket with the appointment details.
10. AURA records the scheduling event.

### Alternative flows
- **A1 — Customer does not book:** The request remains open and may receive a follow-up.
- **A2 — Booking cancelled:** AURA updates the appointment status and the associated request.
- **A3 — Duplicate webhook event:** AURA detects the duplicate and avoids processing the same booking event twice.
- **A4 — Webhook processing failure:** AURA records the failure and supports controlled recovery.
- **A5 — Calendly unavailable:** AURA records the integration failure and allows staff to handle scheduling manually.

### Expected outcome
The appointment is associated with the correct customer request and its status is tracked by AURA.

**Related requirements:** FR-029 to FR-031.

---

## UC-007 — Handle Processing Failure

**Primary actors:** AURA System, Administrator, Operations Staff

### Preconditions
- AURA is processing a request or executing a workflow.
- A failure occurs during processing or integration.

### Main flow
1. AURA detects an error or unsuccessful operation.
2. AURA records the error and relevant execution context.
3. AURA identifies the affected request and workflow.
4. AURA determines whether the operation can be retried safely.
5. If appropriate, AURA schedules a retry according to the configured retry policy.
6. If the retry succeeds, AURA resumes the workflow.
7. If retries are exhausted or recovery is not safe, AURA marks the operation as failed.
8. AURA makes the failure visible to authorized staff.
9. Staff can investigate and initiate controlled recovery or manual processing.
10. AURA records the recovery outcome.

### Alternative flows
- **A1 — Temporary external service failure:** AURA retries the operation using an appropriate delay.
- **A2 — Permanent failure:** AURA stops automatic retries and flags the request for intervention.
- **A3 — Unsafe retry:** AURA does not automatically repeat an operation that could create duplicate or harmful effects.
- **A4 — Manual recovery:** An authorized user reviews the failure and initiates an appropriate recovery action.

### Expected outcome
Failures are visible, traceable, and recoverable where possible. AURA does not silently lose requests or repeat unsafe operations.

**Related requirements:** FR-032 to FR-038.

# 5. User Stories

User stories describe the expected value from the perspective of each user.

## Customer

**US-001 — Submit a service request**

As a customer, I want to submit a service request by email so that I can contact the business without using a separate application.

**US-002 — Receive confirmation**

As a customer, I want to receive an acknowledgment when my request is received so that I know the business has registered it.

**US-003 — Provide missing information**

As a customer, I want to receive a clarification request when information is missing so that the business can process my request correctly.

**US-004 — Schedule an appointment**

As a customer, I want to receive a scheduling link so that I can select an appointment that suits my availability.

**US-005 — Receive request updates**

As a customer, I want to receive relevant updates about my request so that I know its progress.

## Operations Staff

**US-006 — View incoming requests**

As an operations employee, I want to see incoming customer requests in a central dashboard so that I can monitor and manage them.

**US-007 — Review structured request information**

As an operations employee, I want requests to contain extracted customer, equipment, issue, and priority information so that I do not have to manually interpret every email.

**US-008 — Review uncertain information**

As an operations employee, I want uncertain or incomplete AI results to be flagged so that I can correct them before they cause operational problems.

**US-009 — Access relevant technical knowledge**

As an operations employee, I want relevant internal technical documentation to be retrieved for a request so that I can make better-informed decisions.

**US-010 — Track operational tasks**

As an operations employee, I want to see tasks associated with customer requests so that I can follow up on the required work.

**US-011 — Monitor failures**

As an operations employee, I want failed or blocked requests to be visible so that I can intervene before they are forgotten.

## Manager / Approver

**US-012 — Review pending approvals**

As a manager, I want to see actions awaiting approval so that I can control sensitive business operations.

**US-013 — Understand proposed actions**

As a manager, I want each approval request to explain the proposed action and its context so that I can make an informed decision.

**US-014 — Approve or reject actions**

As a manager, I want to approve or reject proposed actions so that AURA only executes actions that are properly authorized.

**US-015 — Review approval history**

As a manager, I want to see who approved or rejected an action and when so that decisions remain traceable.

## Administrator

**US-016 — Configure integrations**

As an administrator, I want to configure email, Calendly, and AI provider integrations so that AURA can connect to the business systems it needs.

**US-017 — Monitor system health**

As an administrator, I want to monitor system health and integration failures so that I can identify operational problems.

**US-018 — Investigate execution history**

As an administrator, I want to inspect workflow and AI execution history so that I can understand why a request was processed in a particular way.

**US-019 — Recover failed operations**

As an administrator, I want to retry or recover failed operations safely so that temporary failures do not require restarting the entire process.

# 6. Important Business Rules

The following rules apply across the use cases:

| ID | Business Rule |
|---|---|
| BR-001 | Every incoming customer request must be stored and associated with a unique internal request identifier. |
| BR-002 | Duplicate incoming messages must not create duplicate requests or unintended repeated actions. |
| BR-003 | AI-generated classifications and extracted information must be stored with their processing context. |
| BR-004 | AURA must not invent technical information when relevant knowledge cannot be retrieved. |
| BR-005 | Actions must be classified according to their risk before execution. |
| BR-006 | High-risk actions require explicit human authorization. |
| BR-007 | An action requiring approval must not execute while approval is pending or rejected. |
| BR-008 | Approval decisions must be associated with an authorized user and timestamp. |
| BR-009 | External integration events must be validated and processed safely, including duplicate events. |
| BR-010 | Failed operations must be recorded and must not silently disappear from the workflow. |
| BR-011 | Automatic retries must not cause unintended duplicate or unsafe actions. |
| BR-012 | Important workflow transitions and execution outcomes must be traceable. |

# 7. Scope and Assumptions

These use cases describe the initial MVP.

The following assumptions apply:

- Email is the primary customer request channel.
- IMAP is used to receive incoming emails.
- SMTP is used to send outgoing emails.
- Calendly is the initial appointment scheduling integration.
- AURA provides a web dashboard for operational visibility and human approvals.
- The initial deployment targets one business environment.
- AI-generated decisions are subject to configured risk and authorization policies.
- The initial implementation focuses on one technical maintenance business scenario.

Future versions may introduce additional communication channels, scheduling providers, business domains, and multi-tenant capabilities.

# 8. Traceability and Validation

Use cases and user stories must remain traceable to the functional requirements.

The detailed use cases identify the relevant requirement IDs. User stories describe the expected user value and will be used to derive implementation tasks and acceptance tests.

During implementation, each use case should be validated through one or more tests covering:

- Successful execution of the main flow.
- Relevant alternative flows.
- Error and recovery scenarios.
- Authorization and access-control behavior.
- Duplicate-event and idempotency behavior.
- Expected state changes and audit records.

A use case is considered implemented when its main flow and required alternative flows satisfy their associated acceptance criteria.

---

**End of document**