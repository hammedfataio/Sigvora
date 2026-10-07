# Sigvora — System Requirements Specification

> **Document Status:** Product Foundation  
> **Version:** 0.1.0  
> **Project:** Sigvora  
> **Product Category:** Trustworthy AI Customer-Request Intelligence Platform  
> **Initial Demonstration Domain:** Fictional UK Financial Services  
> **Source:** `docs/BUSINESS_CASE.md`

---

# 1. Purpose

This document translates the Sigvora business case into explicit,
traceable and testable system requirements.

The requirements define what Sigvora must achieve before implementation
decisions are treated as final.

Sigvora is designed to transform an unstructured customer request into a
structured, evidence-grounded and governable operational decision.

The target workflow is:

```text
Customer Request
       ↓
Request Understanding
       ↓
Signal Extraction
       ↓
Risk & Priority Assessment
       ↓
Knowledge Retrieval
       ↓
Evidence-Grounded Recommendation
       ↓
Routing
       ↓
Governance Decision
       ↓
Human Review / Controlled Action
       ↓
SLA Tracking
       ↓
Audit + Feedback
```

---

# 2. Requirements Philosophy

Sigvora will follow five requirements principles.

## 2.1 Traceability

Every major implementation capability should map to a requirement.

## 2.2 Testability

A requirement should be written so that evidence can later demonstrate
whether it has been satisfied.

## 2.3 Risk Awareness

Higher-risk decisions require stronger controls.

## 2.4 AI Is Not the Default Solution

Deterministic mechanisms should be preferred where AI is unnecessary.

## 2.5 Safe Failure

Failure of an AI component must not silently become an unsafe decision.

---

# 3. Requirement Priority

Requirements use the following priority levels:

| Priority | Meaning |
|---|---|
| MUST | Required for the core Sigvora product |
| SHOULD | Important but not required for the first vertical slice |
| COULD | Valuable future enhancement |
| OUT | Explicitly outside the current scope |

---

# 4. Core Domain Objects

Sigvora should model the following major objects:

```text
CustomerRequest
      │
      ├── Classification
      ├── Signal
      ├── RiskAssessment
      ├── Priority
      ├── Evidence
      ├── Recommendation
      ├── RoutingDecision
      ├── GovernanceDecision
      ├── SLA
      ├── HumanDecision
      └── AuditEvent
```

These are logical domain concepts.

Their exact implementation will be determined during architecture design.

---

# 5. Request Ingestion Requirements

### FR-001 — Create Customer Request

**Priority:** MUST

The system shall accept a customer request containing, at minimum:

- request text;
- submission timestamp;
- channel;
- and request identifier.

**Acceptance evidence:**

A valid request can be submitted and receives a unique internal case ID.

---

### FR-002 — Request Validation

**Priority:** MUST

The system shall validate incoming requests before AI processing.

The system shall reject or safely handle:

- missing request text;
- malformed payloads;
- unsupported formats;
- and requests exceeding configured limits.

---

### FR-003 — Preserve Original Request

**Priority:** MUST

The original customer request shall remain available after downstream AI
processing.

AI-generated transformations shall not overwrite the original input.

---

### FR-004 — Request Status

**Priority:** MUST

Each request shall maintain a lifecycle status.

Initial states should support at least:

```text
RECEIVED
TRIAGING
ROUTED
IN_REVIEW
RESOLVED
CLOSED
```

---

# 6. Request Understanding Requirements

### FR-010 — Intent Classification

**Priority:** MUST

Sigvora shall determine the primary intent of a customer request.

Initial categories should include:

- account access;
- card issue;
- payment issue;
- potential fraud;
- complaint;
- financial difficulty;
- document / statement request;
- privacy / data request;
- technical support;
- general enquiry;
- unknown.

---

### FR-011 — Structured Classification Output

**Priority:** MUST

Classification results shall use a defined schema rather than unrestricted
free text.

The result shall contain at least:

```text
category
confidence
model/version
timestamp
```

---

### FR-012 — Unknown Classification

**Priority:** MUST

The system shall support an `UNKNOWN` or equivalent state when a reliable
classification cannot be produced.

The system shall not force every request into a known category.

---

### FR-013 — Multi-Signal Requests

**Priority:** SHOULD

The system should identify secondary concerns when a request contains more
than one operational issue.

Example:

> "My card was stolen and I also cannot access my account."

---

# 7. Signal Extraction Requirements

### FR-020 — Signal Extraction

**Priority:** MUST

Sigvora shall extract decision-relevant signals from customer requests.

Potential signals include:

- suspected unauthorised transaction;
- stolen card;
- account-access problem;
- monetary amount;
- financial difficulty;
- complaint;
- privacy concern;
- security concern;
- deadline;
- repeated service failure.

---

### FR-021 — Signal Provenance

**Priority:** MUST

Each extracted signal shall retain a relationship to the source request.

Where practical, the system should retain the text span or evidence from
which the signal was derived.

---

### FR-022 — Signal Confidence

**Priority:** SHOULD

AI-derived signals should include an associated confidence or equivalent
uncertainty indicator where technically meaningful.

---

### FR-023 — Deterministic Signals

**Priority:** SHOULD

Signals that can be reliably identified using deterministic validation or
business logic should not require an LLM solely for extraction.

---

# 8. Risk Requirements

### RISK-001 — Risk Assessment

**Priority:** MUST

Sigvora shall assign an operational risk level to a request.

Initial levels:

```text
LOW
MEDIUM
HIGH
CRITICAL
```

---

### RISK-002 — Risk Factors

**Priority:** MUST

Risk assessment shall consider explicit factors rather than relying only
on unrestricted LLM judgement.

Factors may include:

- financial exposure;
- potential customer harm;
- security impact;
- privacy impact;
- vulnerability indicators;
- urgency;
- and service impact.

---

### RISK-003 — Risk Explanation

**Priority:** MUST

The system shall retain the primary factors that contributed to a risk
decision.

---

### RISK-004 — Risk and Sentiment Separation

**Priority:** MUST

The system shall not treat negative sentiment as sufficient evidence of
high operational risk.

---

### RISK-005 — Configurable Risk Rules

**Priority:** SHOULD

Deterministic risk rules should be configurable without requiring changes
to LLM prompts.

---

# 9. Priority Requirements

### PRI-001 — Priority Assignment

**Priority:** MUST

Each request shall receive an operational priority.

Initial levels:

```text
P1 — Critical
P2 — High
P3 — Normal
P4 — Low
```

---

### PRI-002 — Priority Factors

**Priority:** MUST

Priority shall be derived from relevant factors such as:

- risk;
- urgency;
- potential impact;
- request type;
- and applicable service rules.

---

### PRI-003 — Priority Explanation

**Priority:** MUST

The system shall retain the reason for the assigned priority.

---

### PRI-004 — Priority Override

**Priority:** MUST

An authorised human user shall be able to override an AI/system-assigned
priority.

The override shall create an audit event.

---

# 10. Knowledge and RAG Requirements

### RAG-001 — Approved Knowledge Store

**Priority:** MUST

Sigvora shall maintain a controlled knowledge collection used for
retrieval.

Initial knowledge may include synthetic:

- policies;
- procedures;
- SLA guidance;
- escalation rules;
- and customer-service knowledge articles.

---

### RAG-002 — Knowledge Provenance

**Priority:** MUST

Every retrieved knowledge item shall retain source metadata.

At minimum:

```text
document identifier
title
version where available
retrieval timestamp
```

---

### RAG-003 — Relevant Evidence Retrieval

**Priority:** MUST

The system shall retrieve knowledge relevant to the current request before
generating policy-dependent recommendations.

---

### RAG-004 — Evidence Visibility

**Priority:** MUST

Users shall be able to identify which knowledge sources supported a
recommendation.

---

### RAG-005 — No-Evidence Behaviour

**Priority:** MUST

If no sufficiently relevant evidence is retrieved, Sigvora shall not
invent organisational policy.

The system shall instead:

- abstain;
- request human review;
- or clearly state that supporting knowledge was unavailable.

---

### RAG-006 — Evidence Relevance

**Priority:** MUST

Retrieved evidence shall include a relevance score or equivalent ranking
signal where supported by the retrieval architecture.

---

### RAG-007 — Knowledge Versioning

**Priority:** SHOULD

The system should support version metadata for organisational knowledge so
that decisions can later be traced to the knowledge available at the time.

---

# 11. Recommendation Requirements

### REC-001 — Recommendation Generation

**Priority:** MUST

Sigvora shall generate a structured recommended next step when sufficient
evidence exists.

---

### REC-002 — Recommendation Schema

**Priority:** MUST

A recommendation shall contain at least:

```text
recommended_action
reason
supporting_evidence
uncertainty
governance_status
```

---

### REC-003 — Evidence Support

**Priority:** MUST

Policy-dependent recommendations shall reference supporting retrieved
evidence.

---

### REC-004 — Unsupported Claims

**Priority:** MUST

The system shall be designed to minimise unsupported claims and enable
their measurement during evaluation.

---

### REC-005 — Alternative Action

**Priority:** SHOULD

Where multiple reasonable next steps exist, Sigvora should be capable of
presenting an alternative rather than falsely implying that only one
action is possible.

---

# 12. Routing Requirements

### ROUTE-001 — Routing Decision

**Priority:** MUST

Sigvora shall recommend an operational destination for a request.

Initial destinations may include:

- Customer Support;
- Digital Support;
- Payments Investigation;
- Fraud / Card Security;
- Complaints;
- Financial Support;
- Privacy / Data;
- Human Triage.

---

### ROUTE-002 — Routing Explanation

**Priority:** MUST

Routing decisions shall include the primary reason for the selected
destination.

---

### ROUTE-003 — Low-Confidence Routing

**Priority:** MUST

Requests with insufficient routing confidence shall be sent to human
triage rather than silently routed to an arbitrary team.

---

### ROUTE-004 — Human Rerouting

**Priority:** MUST

Authorised users shall be able to reroute a request.

The original and updated routing decisions shall remain auditable.

---

# 13. RSG Governance Requirements

RSG represents:

```text
RISK
What could go wrong?

SIGNAL
What important information do we observe?

GOVERNANCE
What is the system permitted to do?
```

---

### GOV-001 — Governance Evaluation

**Priority:** MUST

A governance decision shall be produced before an AI-recommended
operational action is executed.

---

### GOV-002 — Governance Outcomes

**Priority:** MUST

The system shall support at least:

```text
ALLOW
REVIEW
ESCALATE
ABSTAIN
```

---

### GOV-003 — Risk-Sensitive Authority

**Priority:** MUST

Higher-risk requests shall not automatically receive greater AI authority.

Governance controls shall be capable of restricting automation as risk
increases.

---

### GOV-004 — Governance Independence

**Priority:** MUST

Governance decisions shall not depend solely on the LLM's own statement
that an action is safe.

---

### GOV-005 — Human Approval

**Priority:** MUST

Actions classified as requiring human approval shall not be marked as
authorised until an authorised user explicitly approves them.

---

### GOV-006 — Governance Reason

**Priority:** MUST

Every governance outcome shall record its reason.

---

# 14. Uncertainty and Abstention Requirements

### TAI-001 — Uncertainty Representation

**Priority:** MUST

Sigvora shall explicitly represent uncertainty when relevant information
is missing, ambiguous or conflicting.

---

### TAI-002 — Abstention

**Priority:** MUST

The system shall be capable of declining to produce a definitive
recommendation.

---

### TAI-003 — Missing Evidence

**Priority:** MUST

Missing supporting knowledge shall be treated as a decision condition, not
silently ignored.

---

### TAI-004 — Conflicting Evidence

**Priority:** MUST

Where retrieved sources materially conflict, the system shall expose the
conflict and require appropriate review.

---

### TAI-005 — Confidence Is Not Authority

**Priority:** MUST

A high model confidence score shall not independently authorise a
high-impact action.

---

# 15. Human-in-the-Loop Requirements

### HITL-001 — Review Queue

**Priority:** MUST

Sigvora shall provide a mechanism for requests requiring human review.

---

### HITL-002 — Human Decision

**Priority:** MUST

An authorised reviewer shall be able to:

- approve;
- reject;
- modify;
- reroute;
- or escalate

a recommendation where applicable.

---

### HITL-003 — Human Reason

**Priority:** SHOULD

The system should allow reviewers to record a reason when overriding an AI
decision.

---

### HITL-004 — Preserve AI Decision

**Priority:** MUST

Human modification shall not erase the original AI recommendation.

Both shall remain available for audit and evaluation.

---

# 16. SLA Requirements

### SLA-001 — SLA Assignment

**Priority:** MUST

Sigvora shall associate applicable service expectations with a request
based on configured business rules.

---

### SLA-002 — Deadline Calculation

**Priority:** MUST

The system shall calculate an SLA deadline where applicable.

---

### SLA-003 — SLA State

**Priority:** MUST

The system shall support at least:

```text
ON_TRACK
AT_RISK
BREACHED
```

---

### SLA-004 — Escalation

**Priority:** SHOULD

The system should surface requests approaching or exceeding their service
target.

---

### SLA-005 — SLA Independence

**Priority:** MUST

SLA calculations shall be deterministic and shall not depend on an LLM for
basic time arithmetic.

---

# 17. Audit Requirements

### AUD-001 — Decision Audit Trail

**Priority:** MUST

Important lifecycle events shall generate audit records.

Examples include:

- request creation;
- classification;
- risk assignment;
- priority assignment;
- evidence retrieval;
- recommendation;
- routing;
- governance decision;
- human override;
- SLA escalation;
- resolution.

---

### AUD-002 — Audit Immutability

**Priority:** MUST

Application users shall not be able to silently overwrite historical audit
events.

---

### AUD-003 — AI Metadata

**Priority:** MUST

Where applicable, AI-generated decisions shall retain metadata such as:

- model/provider identifier;
- model version where available;
- prompt/workflow version;
- timestamp;
- and processing outcome.

---

### AUD-004 — Decision Reconstruction

**Priority:** SHOULD

The audit trail should contain sufficient information to reconstruct the
major factors influencing a historical decision.

---

# 18. Security Requirements

### SEC-001 — Authentication

**Priority:** MUST

Protected application functionality shall require authenticated access.

---

### SEC-002 — Authorisation

**Priority:** MUST

Sensitive actions shall be restricted according to user role or
permission.

---

### SEC-003 — Secret Management

**Priority:** MUST

API keys, credentials and secrets shall not be committed to the source
repository.

---

### SEC-004 — Input Validation

**Priority:** MUST

User-controlled input shall be validated before downstream processing.

---

### SEC-005 — Data Minimisation

**Priority:** MUST

The portfolio shall avoid unnecessary collection or storage of sensitive
customer information.

---

### SEC-006 — Synthetic Portfolio Data

**Priority:** MUST

Demonstration customer data shall be synthetic, fictional, anonymised or
appropriately licensed.

---

### SEC-007 — Prompt Injection Defence

**Priority:** MUST

Retrieved documents and customer text shall be treated as untrusted input
to the AI pipeline.

The architecture shall include controls designed to reduce instruction
injection and unauthorised tool/action execution.

---

# 19. Reliability Requirements

### REL-001 — AI Provider Failure

**Priority:** MUST

Failure of an AI provider shall not cause an incoming request to disappear.

---

### REL-002 — Retrieval Failure

**Priority:** MUST

Failure of the retrieval system shall produce a safe failure state rather
than an unsupported policy recommendation.

---

### REL-003 — Retry Safety

**Priority:** SHOULD

Retryable operations should be designed to avoid unintended duplicate
processing.

---

### REL-004 — Graceful Degradation

**Priority:** MUST

Where AI functionality is unavailable, Sigvora shall preserve the request
and make it available for human handling.

---

# 20. Observability Requirements

### OBS-001 — Structured Logging

**Priority:** MUST

Backend services shall produce structured operational logs.

---

### OBS-002 — Request Correlation

**Priority:** MUST

Processing events for a customer request shall be traceable using a
correlation or case identifier.

---

### OBS-003 — AI Observability

**Priority:** MUST

The system shall record operational information necessary to evaluate AI
behaviour, including:

- latency;
- success/failure;
- model/workflow identity;
- retrieval results;
- and governance outcome.

---

### OBS-004 — Error Visibility

**Priority:** MUST

Operational failures shall be visible rather than silently suppressed.

---

# 21. Performance Requirements

### NFR-001 — Interactive Performance

**Priority:** SHOULD

The user interface should remain responsive while AI processing occurs.

Long-running AI operations should not unnecessarily block unrelated UI
interaction.

---

### NFR-002 — Measurable Latency

**Priority:** MUST

End-to-end processing latency shall be measurable.

---

### NFR-003 — Timeout Behaviour

**Priority:** MUST

External AI and retrieval calls shall have defined timeout behaviour.

---

# 22. API Requirements

### API-001 — API-First Backend

**Priority:** MUST

Core Sigvora capabilities shall be accessible through documented backend
APIs.

---

### API-002 — Schema Validation

**Priority:** MUST

API request and response bodies shall use explicit schemas.

---

### API-003 — Error Contract

**Priority:** MUST

API errors shall use a consistent error structure.

---

### API-004 — Versioning

**Priority:** SHOULD

Public application APIs should support an explicit versioning strategy.

---

# 23. User Interface Requirements

### UI-001 — Request Queue

**Priority:** MUST

Users shall be able to view customer requests and their current status.

---

### UI-002 — Case Detail

**Priority:** MUST

Users shall be able to inspect a request including:

- original message;
- classification;
- signals;
- risk;
- priority;
- retrieved evidence;
- recommendation;
- routing;
- governance;
- SLA state;
- and audit information.

---

### UI-003 — Human Review

**Priority:** MUST

The interface shall provide an explicit workflow for requests requiring
human review.

---

### UI-004 — Decision Explanation

**Priority:** MUST

The interface shall expose why important AI-supported decisions were made.

---

### UI-005 — Evidence Access

**Priority:** MUST

Supporting evidence shall be accessible from the recommendation rather
than hidden from the user.

---

# 24. Evaluation Requirements

### EVAL-001 — Classification Evaluation

**Priority:** MUST

The project shall evaluate intent classification using an appropriate
labelled test dataset.

---

### EVAL-002 — Risk Evaluation

**Priority:** MUST

The project shall measure risk/signal detection performance.

---

### EVAL-003 — Routing Evaluation

**Priority:** MUST

Correct routing shall be measured.

---

### EVAL-004 — Retrieval Evaluation

**Priority:** MUST

The RAG system shall be evaluated independently of answer generation.

Candidate metrics include:

- Precision@K;
- Recall@K;
- MRR;
- or another justified retrieval metric.

---

### EVAL-005 — Grounding Evaluation

**Priority:** MUST

The project shall measure whether recommendations are supported by
retrieved evidence.

---

### EVAL-006 — Abstention Evaluation

**Priority:** MUST

The system shall be tested on cases where insufficient evidence exists.

---

### EVAL-007 — Governance Evaluation

**Priority:** MUST

The project shall test whether governance rules prevent prohibited
actions.

---

### EVAL-008 — Failure Evaluation

**Priority:** MUST

Evaluation shall include failure scenarios such as:

- AI outage;
- retrieval failure;
- ambiguous requests;
- conflicting evidence;
- and malicious/untrusted input.

---

# 25. DevOps and Delivery Requirements

### DEV-001 — Automated Tests

**Priority:** MUST

The repository shall include automated tests for critical application
behaviour.

---

### DEV-002 — Continuous Integration

**Priority:** MUST

Repository changes shall be validated through CI.

---

### DEV-003 — Reproducible Environment

**Priority:** MUST

The project shall define reproducible backend and frontend development
environments.

---

### DEV-004 — Configuration Separation

**Priority:** MUST

Environment-specific configuration shall be separated from application
code.

---

### DEV-005 — Deployment

**Priority:** SHOULD

The final portfolio should demonstrate a reproducible deployment strategy.

---

# 26. Initial Roles

The first implementation may support:

```text
AGENT
Handles standard requests.

REVIEWER
Reviews restricted or escalated decisions.

ADMIN
Manages system configuration and knowledge.
```

Role definitions may evolve during architecture design.

---

# 27. Explicit Non-Requirements

The initial Sigvora portfolio does **not** require:

- real banking-system integration;
- access to real customer accounts;
- real payment execution;
- autonomous fraud blocking;
- production banking certification;
- replacement of a CRM;
- replacement of a contact centre;
- unrestricted autonomous agents;
- or real customer financial data.

These are deliberately outside the portfolio boundary.

---

# 28. MVP Requirements

The first vertical slice must demonstrate:

| ID | Capability |
|---|---|
| MVP-01 | Submit a synthetic customer request |
| MVP-02 | Classify its intent |
| MVP-03 | Extract important signals |
| MVP-04 | Assess risk |
| MVP-05 | Assign priority |
| MVP-06 | Retrieve relevant knowledge |
| MVP-07 | Generate an evidence-grounded recommendation |
| MVP-08 | Recommend routing |
| MVP-09 | Apply governance |
| MVP-10 | Send restricted cases for human review |
| MVP-11 | Assign and display SLA state |
| MVP-12 | Preserve an audit trail |

If these twelve capabilities do not work together, the core Sigvora
workflow is not yet complete.

---

# 29. Requirements Traceability

The intended traceability model is:

```text
BUSINESS PAIN
      ↓
BUSINESS_CASE.md
      ↓
REQUIREMENTS.md
      ↓
Requirement ID
      ↓
Architecture Component
      ↓
Implementation
      ↓
Automated Test / Experiment
      ↓
Measured Evidence
```

Example:

```text
Business problem:
High-risk requests can be hidden in ordinary language.

        ↓

RISK-001 / RISK-002

        ↓

Risk Assessment Component

        ↓

Implementation

        ↓

Risk Evaluation Dataset

        ↓

Precision / Recall / F1

        ↓

Evidence that the requirement is or is not satisfied
```

---

# 30. Definition of Done

A requirement shall not be considered complete merely because code exists.

Where applicable, completion should require:

```text
IMPLEMENTED
     +
TESTED
     +
DOCUMENTED
     +
OBSERVABLE
     +
EVALUATED
```

This definition is particularly important for AI functionality.

A prompt that appears to work during a manual demonstration is not
sufficient evidence of reliability.

---

# 31. Requirement Change Control

As Sigvora evolves, requirements may change.

Changes should preserve:

- requirement IDs where practical;
- rationale;
- traceability;
- and version history.

New technologies should not be added to the architecture without a
requirement or engineering constraint that justifies them.

---

# 32. Next Artefact

The next document is:

`docs/ARCHITECTURE.md`

The architecture will answer:

> **How will Sigvora satisfy these requirements?**

It will define:

- system boundaries;
- frontend;
- backend services;
- data layer;
- AI orchestration;
- RAG pipeline;
- risk engine;
- governance engine;
- workflow orchestration;
- SLA engine;
- audit subsystem;
- authentication and authorisation;
- observability;
- external model-provider boundaries;
- failure paths;
- and deployment topology.

Architecture decisions will be mapped back to the requirement IDs in this
document.
