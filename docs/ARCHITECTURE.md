# Sigvora — System Architecture

> **Document Status:** Architecture Baseline  
> **Version:** 0.2.0  
> **Project:** Sigvora  
> **Product:** Trustworthy AI Customer-Request Intelligence and Decision-Support Platform  
> **Initial Demonstration Domain:** Fictional UK Financial Services  
> **Architecture Style:** Modular Monolith with Explicit Domain Boundaries  
> **Source Documents:** `README.md`, `docs/BUSINESS_CASE.md`, `docs/REQUIREMENTS.md`

---

# 1. Purpose

This document defines the target system architecture for Sigvora.

Sigvora exists to solve a specific customer-operations problem:

> Organisations receive customer requests with different intents, urgency,
> risks and handling requirements, but understanding, prioritising,
> grounding, routing and governing those requests can require significant
> manual interpretation across fragmented knowledge and workflows.

Sigvora addresses this problem by transforming an incoming customer
request into a structured, evidence-grounded and governable
decision-support case.

The architecture therefore follows the customer-request lifecycle:

```text
CUSTOMER REQUEST
       ↓
REQUEST UNDERSTANDING
       ↓
DECISION SIGNALS
       ↓
RISK + PRIORITY
       ↓
ORGANISATIONAL KNOWLEDGE / RAG
       ↓
EVIDENCE-GROUNDED RECOMMENDATION
       ↓
ROUTING
       ↓
GOVERNANCE
       ↓
HUMAN REVIEW / CONTROLLED ACTION
       ↓
SLA TRACKING
       ↓
AUDIT + FEEDBACK + EVALUATION
```

Every major architectural component must support this lifecycle.

---

# 2. Product Boundary

Sigvora is:

> **A Trustworthy AI customer-request intelligence and decision-support
> platform that helps organisations understand, prioritise, route and
> safely act on customer requests using evidence-grounded AI,
> organisational knowledge and explicit governance controls.**

Sigvora is not:

- a generic chatbot;
- a standalone RAG application;
- a sentiment-analysis demo;
- an infrastructure-monitoring platform;
- an AIOps platform;
- an observability system;
- a CRM replacement;
- a banking core system;
- a fraud-detection engine;
- or an unrestricted autonomous agent.

The architecture must preserve this boundary.

---

# 3. Initial Demonstration Environment

The first Sigvora implementation models a fictional UK financial-services
customer-service environment.

Example request categories include:

```text
Account Access
Card Issues
Payment Problems
Potential Fraud
Complaints
Financial Difficulty
Document / Statement Requests
Privacy / Data Requests
Technical Support
General Enquiries
```

This domain provides meaningful variation in:

- urgency;
- customer impact;
- financial exposure;
- sensitivity;
- knowledge requirements;
- specialist ownership;
- SLA expectations;
- and required human oversight.

The domain is used to demonstrate the architecture.

It does not imply integration with a real bank or use of real customer
financial data.

---

# 4. Architectural Objective

The architecture must reliably answer:

```text
WHAT does the customer need?

WHAT important indicators are present?

HOW urgent is the request?

WHAT could happen if it is mishandled?

WHAT organisational knowledge applies?

WHAT should happen next?

WHO should handle it?

IS the available evidence sufficient?

WHAT is the AI permitted to do?

DOES a human need to intervene?

WHEN must the request be handled?

CAN the decision later be reconstructed?
```

These questions define Sigvora's architecture.

---

# 5. Architecture Principles

## AP-01 — Customer Request Is the Primary Domain Object

The architecture begins with a customer request, not an LLM prompt.

---

## AP-02 — Preserve the Original Request

AI processing must never replace or modify the original customer message.

---

## AP-03 — Persist Before AI Processing

A valid request should be persisted before expensive or external AI
processing begins.

---

## AP-04 — AI Is Not the Entire System

AI should be used where language understanding and reasoning provide
value.

Deterministic logic should be used where predictability is more
appropriate.

---

## AP-05 — Evidence Before Policy-Dependent Recommendation

A recommendation dependent on organisational policy should be grounded in
retrieved approved knowledge.

---

## AP-06 — Confidence Does Not Equal Authority

A highly confident model prediction does not automatically permit an
operational action.

---

## AP-07 — Governance Is Independent of Generative Reasoning

An LLM must not determine whether its own proposed action is authorised.

---

## AP-08 — Human Oversight Is Risk Sensitive

Higher-risk or uncertain cases should receive stronger human control.

---

## AP-09 — Safe Failure

Missing evidence, AI-provider failure or ambiguous interpretation must
lead to a safe state rather than fabricated certainty.

---

## AP-10 — Decisions Must Be Reconstructable

Sigvora should retain sufficient information to explain how an important
decision was reached.

---

## AP-11 — Evaluate Before Claiming Trust

Trustworthy AI claims must eventually be supported by measurable
evaluation.

---

# 6. Architecture Style

Sigvora will initially use a:

> **Modular Monolith with Explicit Domain Boundaries**

The initial portfolio does not require multiple independently deployed
microservices.

A modular monolith gives the project:

- clear component boundaries;
- simpler deployment;
- easier testing;
- transactional consistency;
- lower infrastructure complexity;
- and faster iteration.

The architecture can evolve later if scale, security isolation or
independent deployment genuinely requires service extraction.

---

# 7. Sigvora System Context

```text
                    ┌──────────────────────┐
                    │       Customer       │
                    └──────────┬───────────┘
                               │
                               │ submits request
                               ▼
                    ┌──────────────────────┐
                    │   Request Channel    │
                    │   Web / API          │
                    └──────────┬───────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────┐
│                         SIGVORA                              │
│                                                              │
│  Understand Customer Request                                 │
│             ↓                                                │
│  Extract Decision Signals                                    │
│             ↓                                                │
│  Assess Risk + Priority                                      │
│             ↓                                                │
│  Retrieve Organisational Knowledge                           │
│             ↓                                                │
│  Generate Grounded Recommendation                            │
│             ↓                                                │
│  Determine Routing                                           │
│             ↓                                                │
│  Apply Governance                                            │
│             ↓                                                │
│  Human Review where required                                 │
│             ↓                                                │
│  Track SLA + Audit Outcome                                   │
└───────────────┬───────────────────────────┬──────────────────┘
                │                           │
                ▼                           ▼
       ┌─────────────────┐         ┌──────────────────┐
       │ Customer-Service│         │ Approved AI /    │
       │ Agent / Reviewer│         │ Embedding Model  │
       └─────────────────┘         └──────────────────┘
```

Sigvora sits between the incoming customer request and the operational
handling decision.

---

# 8. Primary User Experience

The main Sigvora interface should not resemble a generic chatbot.

The principal experience is a customer-request operations workspace.

```text
┌──────────────────────────────────────────────────────────────┐
│                         SIGVORA                              │
├──────────────────────────────────────────────────────────────┤
│ REQUEST QUEUE                                                │
│                                                              │
│ P1  Potential Fraud        HIGH       00:18 remaining        │
│ P2  Payment Issue          MEDIUM     01:42 remaining        │
│ P2  Financial Difficulty   HIGH       REVIEW                 │
│ P3  Statement Request      LOW        ON TRACK               │
└──────────────────────────────────────────────────────────────┘
```

Selecting a request opens the case workspace.

---

# 9. Case Workspace

The case workspace makes Sigvora's reasoning visible.

```text
┌───────────────────────────────────────────────────────────────┐
│ CASE SGV-00127                         P1 • HIGH RISK         │
├───────────────────────────────┬───────────────────────────────┤
│ CUSTOMER REQUEST              │ REQUEST UNDERSTANDING         │
│                               │                               │
│ "My card was stolen..."       │ Intent: Potential Fraud       │
│                               │ Category: Card Security       │
├───────────────────────────────┼───────────────────────────────┤
│ DECISION SIGNALS              │ RISK + PRIORITY               │
│                               │                               │
│ • stolen card                 │ Risk: HIGH                    │
│ • unknown transactions        │ Priority: P1                  │
│ • financial exposure          │ Reason: active exposure       │
├───────────────────────────────┼───────────────────────────────┤
│ SUPPORTING EVIDENCE           │ ROUTING                       │
│                               │                               │
│ Fraud Procedure               │ Fraud / Card Security         │
│ Card Security Guidance        │                               │
├───────────────────────────────┼───────────────────────────────┤
│ AI RECOMMENDATION             │ GOVERNANCE                    │
│                               │                               │
│ Urgent escalation...          │ ESCALATE                      │
│                               │ Human handling required       │
├───────────────────────────────┴───────────────────────────────┤
│ SLA                     HUMAN REVIEW            AUDIT        │
└───────────────────────────────────────────────────────────────┘
```

This interface reinforces the product's purpose:

> help a user understand what the customer needs, why the case matters,
> what evidence applies and what should happen next.

---

# 10. High-Level Application Architecture

```text
┌─────────────────────────────────────────────────────────────┐
│                    SIGVORA WEB APP                          │
│                                                             │
│ Request Queue                                               │
│ Case Workspace                                              │
│ Human Review                                                │
│ Knowledge Evidence                                          │
│ SLA Status                                                  │
│ Audit Timeline                                              │
└──────────────────────────┬──────────────────────────────────┘
                           │
                         HTTPS
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                    SIGVORA API                              │
│                                                             │
│ Authentication                                              │
│ Request Validation                                          │
│ API Contracts                                               │
│ Authorisation                                               │
└──────────────────────────┬──────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│               CUSTOMER-REQUEST WORKFLOW                     │
│                                                             │
│  Request Management                                         │
│         ↓                                                   │
│  Request Understanding                                      │
│         ↓                                                   │
│  DecisionSignal Extraction                                  │
│         ↓                                                   │
│  Risk + Priority                                            │
│         ↓                                                   │
│  Knowledge Retrieval                                        │
│         ↓                                                   │
│  Recommendation                                             │
│         ↓                                                   │
│  Routing                                                    │
│         ↓                                                   │
│  Governance                                                 │
│         ↓                                                   │
│  Human Review                                               │
│         ↓                                                   │
│  SLA + Audit                                                │
└──────────────┬───────────────────────────────┬──────────────┘
               │                               │
               ▼                               ▼
      ┌─────────────────┐             ┌──────────────────┐
      │ Operational DB  │             │ AI / Embeddings  │
      └─────────────────┘             └──────────────────┘
               │
               ▼
      ┌─────────────────┐
      │ Knowledge Index │
      └─────────────────┘
```

---

# 11. Domain Model

The central aggregate is the customer case.

```text
CustomerRequest
      │
      ├── IntentClassification
      │
      ├── DecisionSignal[]
      │
      ├── RiskAssessment
      │
      ├── PriorityAssessment
      │
      ├── Evidence[]
      │
      ├── Recommendation
      │
      ├── RoutingDecision
      │
      ├── GovernanceDecision
      │
      ├── HumanDecision
      │
      ├── SLAState
      │
      └── AuditEvent[]
```

These domain objects preserve the reasoning chain from request to
operational outcome.

---

# 12. CustomerRequest

`CustomerRequest` represents the original incoming request.

Conceptually:

```text
CustomerRequest
├── id
├── original_text
├── channel
├── submitted_at
├── status
├── created_at
└── updated_at
```

The original request is immutable from the perspective of downstream AI
processing.

---

# 13. Request Understanding

The Request Understanding component answers:

> **What is the customer trying to achieve?**

It may determine:

```text
Intent
Category
Relevant Entities
Context
Confidence
```

Example:

```text
CUSTOMER

"I sent £4,800 yesterday but the person I paid
still hasn't received it."

                ↓

REQUEST UNDERSTANDING

Intent:
Investigate payment

Category:
Payment Issue

Entities:
Amount = £4,800

Context:
Sender reports debit but recipient reports non-receipt
```

The output must be structured.

---

# 14. DecisionSignal

A `DecisionSignal` is:

> **A structured fact or indicator identified in a customer request that
> may materially influence risk, priority, routing or governance.**

This name is deliberately used instead of the ambiguous technical term
`Signal`.

Example:

```text
"My card was stolen and there are payments
I don't recognise."

                 ↓

DecisionSignal: STOLEN_CARD

DecisionSignal: UNRECOGNISED_TRANSACTION

DecisionSignal: POSSIBLE_FINANCIAL_EXPOSURE
```

Conceptual structure:

```text
DecisionSignal
├── id
├── case_id
├── type
├── value
├── source_text
├── confidence
├── extraction_method
└── created_at
```

The source relationship must be preserved.

---

# 15. Risk Assessment

Risk answers:

> **What could happen if this customer request is delayed,
> misunderstood or handled incorrectly?**

Potential dimensions include:

```text
Customer Harm
Financial Exposure
Security
Privacy
Vulnerability
Service Impact
```

Risk output:

```text
RiskAssessment
├── level
├── factors[]
├── rationale
├── rule_results[]
└── created_at
```

Initial levels:

```text
LOW
MEDIUM
HIGH
CRITICAL
```

The system should not rely solely on unrestricted LLM judgement for risk.

---

# 16. Priority Assessment

Priority answers a different question:

> **How urgently should this request be handled?**

Initial levels:

```text
P1 — Critical
P2 — High
P3 — Normal
P4 — Low
```

Priority may consider:

```text
Risk
+
Urgency
+
Customer Impact
+
Request Type
+
Service Rules
```

This separation prevents Sigvora from treating every high-risk concept as
an identical queueing decision.

---

# 17. Why Sentiment Is Not Priority

Sigvora may use sentiment as contextual information, but sentiment must
not control risk or priority.

```text
"I'm furious that my statement hasn't arrived."

Negative sentiment
≠
Automatically high operational risk
```

while:

```text
"I don't recognise these payments."

Calm language
+
Potential financial harm
=
Potentially high operational risk
```

Therefore:

```text
SENTIMENT ≠ RISK

SENTIMENT ≠ PRIORITY
```

This is an architectural rule, not merely a prompt instruction.

---

# 18. Organisational Knowledge

Sigvora uses approved organisational knowledge to ground
policy-dependent recommendations.

Initial synthetic knowledge may include:

```text
Customer-Service Procedures
Payment Investigation Procedures
Fraud / Card Security Procedures
Complaint Handling Guidance
Financial-Difficulty Guidance
Privacy Procedures
Escalation Rules
SLA Rules
```

This knowledge is separate from the customer request.

---

# 19. RAG Architecture

RAG has two distinct processes.

## 19.1 Knowledge Ingestion

```text
Approved Document
       ↓
Validate
       ↓
Parse
       ↓
Chunk
       ↓
Attach Metadata
       ↓
Create Embeddings
       ↓
Index
```

Metadata should preserve information such as:

```text
document_id
title
version
section
chunk_id
effective_date
```

where applicable.

---

## 19.2 Request-Time Retrieval

```text
Customer Case
      ↓
Intent + Decision Signals
      ↓
Retrieval Query
      ↓
Candidate Evidence
      ↓
Ranking / Filtering
      ↓
Relevant Evidence
      ↓
Recommendation Context
```

Retrieval is therefore directly connected to the customer's problem.

---

# 20. Evidence

Retrieved knowledge becomes a first-class `Evidence` object.

```text
Evidence
├── id
├── case_id
├── document_id
├── chunk_id
├── content
├── relevance_score
├── source_metadata
└── retrieved_at
```

This prevents supporting knowledge from disappearing inside an LLM
prompt.

The user should eventually be able to inspect the evidence used by
Sigvora.

---

# 21. Evidence States

Sigvora must recognise that evidence is not always available or reliable.

Useful states include:

```text
AVAILABLE

MISSING

INSUFFICIENT

CONFLICTING

STALE
```

These states can affect recommendation and governance behaviour.

Example:

```text
Relevant policy unavailable
        ↓
Evidence = MISSING
        ↓
Do not fabricate policy
        ↓
REVIEW / ABSTAIN
```

---

# 22. Evidence-Grounded Recommendation

The Recommendation component answers:

> **Given the request, its risk and the available organisational
> evidence, what should happen next?**

Inputs:

```text
Customer Request
+
Intent
+
Decision Signals
+
Risk
+
Priority
+
Evidence
```

Output:

```text
Recommendation
├── proposed_action
├── rationale
├── evidence_refs[]
├── uncertainty
├── alternatives[]
├── model_metadata
└── created_at
```

Policy-dependent recommendations must reference supporting evidence.

---

# 23. Grounding Boundary

Sigvora must distinguish three types of information.

### Customer Fact

What the customer actually said.

### Organisational Evidence

What an approved policy or procedure states.

### AI Inference

What the model concludes from those inputs.

Example:

```text
CUSTOMER FACT

"I don't recognise these payments."

        ↓

ORGANISATIONAL EVIDENCE

Approved unauthorised-transaction procedure.

        ↓

AI INFERENCE

This request should enter the fraud-review workflow.
```

The architecture must not silently treat an inference as a verified fact.

---

# 24. Routing

Routing answers:

> **Who should handle this customer request?**

Potential destinations include:

```text
Customer Support
Digital Support
Payments Investigation
Fraud / Card Security
Complaints
Financial Support
Privacy / Data
Human Triage
```

Example:

```text
Intent:
Potential Fraud

Signals:
STOLEN_CARD
UNRECOGNISED_TRANSACTION

Risk:
HIGH

        ↓

Route:
Fraud / Card Security
```

Stable business routing rules should remain deterministic where possible.

---

# 25. RSG in Sigvora

RSG supports Sigvora's customer-request decision process.

It does not define the product itself.

## Signal

> What important indicators are present in the customer's request?

Represented technically through `DecisionSignal`.

## Risk

> What could happen if those indicators are mishandled?

Represented through `RiskAssessment`.

## Governance

> Given the risk, evidence and uncertainty, what is Sigvora permitted to
> do?

Represented through `GovernanceDecision`.

Therefore:

```text
CUSTOMER REQUEST
       ↓
DECISION SIGNALS
     [SIGNAL]
       ↓
RISK ASSESSMENT
      [RISK]
       ↓
EVIDENCE + RECOMMENDATION
       ↓
GOVERNANCE DECISION
   [GOVERNANCE]
       ↓
SAFE NEXT STEP
```

---

# 26. Governance Engine

Governance protects the boundary between:

```text
AI CAN RECOMMEND
```

and:

```text
SYSTEM IS AUTHORISED TO ACT
```

Inputs may include:

```text
Risk
Evidence State
Recommendation
Uncertainty
Requested Action
User/System Permission
Governance Policy
```

Outputs:

```text
ALLOW

REVIEW

ESCALATE

ABSTAIN
```

---

# 27. AI Cannot Authorise Itself

This architecture is prohibited:

```text
LLM
 ↓
Recommendation
 ↓
LLM decides recommendation is safe
 ↓
Action
```

Sigvora requires:

```text
LLM
 ↓
Structured Recommendation
 ↓
Schema Validation
 ↓
Governance Engine
 ↓
Authorisation Check
 ↓
ALLOW / REVIEW / ESCALATE / ABSTAIN
```

Governance is therefore an enforcement boundary outside generative
reasoning.

---

# 28. Human Review

Requests requiring human oversight enter the review workflow.

Examples include:

```text
High Risk

Low Confidence

Missing Evidence

Conflicting Evidence

Sensitive Customer Situation

Restricted Action
```

The reviewer may:

```text
APPROVE

REJECT

MODIFY

REROUTE

ESCALATE

REQUEST MORE INFORMATION
```

Sigvora preserves both:

```text
AI Recommendation
```

and:

```text
Human Decision
```

This allows later comparison and evaluation.

---

# 29. SLA Engine

SLA tracking connects intelligence to operational service delivery.

```text
Request Received
       ↓
Request Type + Priority
       ↓
Applicable SLA Rule
       ↓
Deadline
       ↓
Time Remaining
       ↓
ON_TRACK / AT_RISK / BREACHED
```

Basic deadline calculations are deterministic.

The LLM is not responsible for time arithmetic.

---

# 30. Customer-Request Lifecycle

The case state model should represent the actual customer-request
workflow.

```text
RECEIVED
    ↓
TRIAGING
    ↓
ASSESSED
    ↓
EVIDENCE_READY
    ↓
RECOMMENDATION_READY
    ↓
GOVERNANCE_EVALUATED
    ↓
┌───────────────┬────────────────┬────────────────┐
│               │                │                │
▼               ▼                ▼                ▼
ROUTED       IN_REVIEW        ESCALATED       ABSTAINED
│               │                │                │
└───────────────┴────────┬───────┴────────────────┘
                         ↓
                      RESOLVED
                         ↓
                       CLOSED
```

State transitions must be explicit.

---

# 31. Persistence Architecture

The operational database is the source of truth for customer cases.

It stores:

```text
CustomerRequest
IntentClassification
DecisionSignal
RiskAssessment
PriorityAssessment
EvidenceReference
Recommendation
RoutingDecision
GovernanceDecision
HumanDecision
SLAState
AuditEvent
```

The retrieval index serves a different purpose.

It supports search over organisational knowledge.

It is not the source of truth for operational case state.

---

# 32. Audit Architecture

Important decisions create audit events.

Examples:

```text
REQUEST_RECEIVED

INTENT_CLASSIFIED

SIGNAL_IDENTIFIED

RISK_ASSESSED

PRIORITY_ASSIGNED

EVIDENCE_RETRIEVED

RECOMMENDATION_GENERATED

ROUTE_SELECTED

GOVERNANCE_EVALUATED

HUMAN_OVERRIDE

SLA_ESCALATED

CASE_RESOLVED
```

An audit event may contain:

```text
event_id
case_id
actor_type
action
reason
previous_state
new_state
model_metadata
policy_metadata
timestamp
```

---

# 33. Decision Reconstruction

For an important historical case, Sigvora should eventually be able to
answer:

```text
What did the customer say?

How was the request classified?

Which DecisionSignals were identified?

What risk was assigned?

Why was that priority chosen?

Which knowledge was retrieved?

What did the AI recommend?

Which evidence supported it?

What was uncertain?

Where was the case routed?

What did governance permit?

Did a human override the AI?

What happened to the SLA?

What was the final outcome?
```

This is what auditability means within Sigvora.

---

# 34. Authentication and Authorisation

The initial user model may include:

```text
AGENT
REVIEWER
ADMIN
```

Conceptually:

### Agent

Handles standard customer requests.

### Reviewer

Handles cases requiring elevated human review.

### Admin

Manages approved system configuration and organisational knowledge.

Permissions should be enforced by the application.

They should not be inferred by the LLM.

---

# 35. Trust Boundaries

Sigvora must treat the following as untrusted inputs:

```text
Customer Messages

Uploaded / Retrieved Documents

External Model Responses
```

They must remain separate from:

```text
System Instructions

Application Permissions

Governance Policies

Authorisation Rules
```

This is important because customer text or retrieved content could contain
instructions intended to manipulate an AI model.

---

# 36. Safe AI Action Pattern

Model output must not directly trigger privileged actions.

Use:

```text
MODEL
  ↓
STRUCTURED PROPOSAL
  ↓
SCHEMA VALIDATION
  ↓
GOVERNANCE
  ↓
AUTHORISATION
  ↓
APPLICATION ACTION
```

This boundary is fundamental to Sigvora's Trustworthy AI design.

---

# 37. Failure Behaviour

Trustworthy AI includes behaviour when the system cannot make a reliable
decision.

---

## 37.1 Ambiguous Request

```text
Request
 ↓
Low Classification Confidence
 ↓
UNKNOWN
 ↓
Human Triage
```

---

## 37.2 Missing Organisational Evidence

```text
Policy-Dependent Question
        ↓
No Relevant Evidence
        ↓
Do Not Invent Policy
        ↓
REVIEW / ABSTAIN
```

---

## 37.3 Conflicting Evidence

```text
Evidence A
     ↘
     CONFLICT
     ↗
Evidence B
     ↓
Expose Conflict
     ↓
Human Review
```

---

## 37.4 AI Provider Failure

```text
Customer Request
       ↓
Persisted
       ↓
AI Provider Failure
       ↓
Failure Recorded
       ↓
Retry if Safe
       OR
Human Triage
```

The customer request remains available.

---

## 37.5 Governance Failure

If Sigvora cannot determine whether a sensitive action is authorised:

```text
DO NOT EXECUTE
```

The system fails closed.

---

# 38. Idempotency

Retries must not create duplicate operational effects.

Sigvora should protect against duplicate:

- customer cases;
- recommendations;
- routing transitions;
- approvals;
- SLA events;
- and audit records.

This becomes particularly important when external AI calls time out or are
retried.

---

# 39. Observability

Observability should help answer product-relevant questions.

Examples include:

```text
How many customer requests are being processed?

How long does triage take?

How often does classification fail?

How often does Sigvora abstain?

How often is no evidence found?

Which routes receive the most requests?

How often do humans override AI recommendations?

How many requests approach SLA breach?

How often do model calls fail?

How long does retrieval take?
```

This keeps observability connected to customer-request operations.

---

# 40. AI Traceability

AI-generated outputs should retain metadata where available.

```text
provider
model
model_version
prompt_version
workflow_version
retrieval_version
timestamp
latency
status
```

This enables later evaluation and debugging.

It does not imply perfect reproducibility of external models.

---

# 41. Evaluation Architecture

Evaluation is part of the architecture because Sigvora must prove its AI
behaviour.

```text
Synthetic Labelled Customer Requests
              ↓
         SIGVORA PIPELINE
              ↓
       Structured Outputs
              ↓
       Evaluation Harness
              ↓
            Metrics
              ↓
      Experiment Evidence
```

Evaluation should be separated into layers.

---

## 41.1 Request Understanding

Measure:

```text
Intent Classification
Category Classification
```

Possible metrics:

```text
Accuracy
Precision
Recall
Macro F1
```

---

## 41.2 DecisionSignal Detection

Measure whether important customer-request indicators are detected.

```text
Precision
Recall
F1
```

---

## 41.3 Risk and Priority

Evaluate:

```text
Risk Classification
Priority Assignment
Critical Misclassification
```

Particular attention should be paid to dangerous under-prioritisation.

---

## 41.4 Retrieval

Evaluate independently:

```text
Precision@K
Recall@K
MRR
```

where appropriate.

---

## 41.5 Grounding

Evaluate whether recommendations are actually supported by retrieved
evidence.

---

## 41.6 Routing

Measure correct first-time destination.

---

## 41.7 Abstention

Test whether Sigvora appropriately refuses to make unsupported decisions.

---

## 41.8 Governance

Test whether restricted actions are prevented.

---

## 41.9 Human-AI Interaction

Eventually measure:

```text
AI Recommendation Acceptance

Human Override

Human Escalation

Reason for Override
```

---

## 41.10 End-to-End Evaluation

Ultimately evaluate whether Sigvora transforms a request into an
appropriate operational outcome.

---

# 42. Technology Architecture

The technology stack should support the product rather than define it.

The proposed initial direction is:

| Layer | Proposed Direction |
|---|---|
| Web Application | React + TypeScript |
| Backend API | Python + FastAPI |
| Data Validation | Typed Python schemas |
| Operational Database | PostgreSQL |
| Knowledge Retrieval | PostgreSQL vector capability or justified equivalent |
| AI | Provider abstraction |
| Embeddings | Provider/local abstraction |
| Authentication | Standards-based authentication |
| Testing | Unit + integration + AI evaluation |
| Packaging | Docker |
| CI/CD | GitHub Actions |
| Observability | Structured logs, metrics and tracing where justified |

These are proposed engineering choices.

They are not claims that the capabilities have already been implemented.

---

# 43. Why React

Sigvora requires an interactive operational interface containing:

- request queues;
- case workspaces;
- review controls;
- evidence panels;
- SLA states;
- audit timelines;
- and dynamic case updates.

React with TypeScript is therefore an appropriate candidate for the
frontend.

---

# 44. Why FastAPI

Sigvora's backend requires:

- typed APIs;
- structured validation;
- asynchronous external calls;
- Python AI ecosystem integration;
- and automatically documented API contracts.

FastAPI is therefore an appropriate candidate for the backend.

The final choice should still be recorded as an architecture decision.

---

# 45. Why PostgreSQL

The core Sigvora data model is highly relational.

For example:

```text
CustomerRequest
       ↓
RiskAssessment
       ↓
Recommendation
       ↓
GovernanceDecision
       ↓
HumanDecision
```

Sigvora also requires transactional consistency and historical records.

A relational database is therefore appropriate for the operational source
of truth.

Using PostgreSQL-compatible vector retrieval may additionally allow the
portfolio to avoid unnecessary infrastructure during its initial stages.

---

# 46. Why Not Start With Microservices

The portfolio currently has no demonstrated requirement for independently
scaled distributed services.

Starting with microservices would add:

```text
Network Complexity
Deployment Complexity
Distributed Failure
Service Discovery
Distributed Transactions
Additional Observability
Higher Cost
```

without yet solving a demonstrated customer-request problem.

Therefore:

> **Modular monolith first. Extract services when evidence justifies it.**

---

# 47. Requirement Traceability

| Requirement Area | Architecture Component |
|---|---|
| Request ingestion | Request Management |
| Intent classification | Request Understanding |
| Decision signals | DecisionSignal Extraction |
| Risk | Risk Assessment |
| Priority | Priority Assessment |
| Organisational knowledge | Knowledge Management |
| RAG | Retrieval Pipeline |
| Recommendation | Recommendation Engine |
| Routing | Routing Engine |
| Governance | Governance Engine |
| Uncertainty / Abstention | AI + Governance |
| Human review | Review Workflow |
| SLA | SLA Engine |
| Audit | Audit Subsystem |
| Security | Identity + Trust Boundaries |
| Reliability | Workflow + Infrastructure |
| Observability | Observability Layer |
| Evaluation | Evaluation Harness |

---

# 48. Complete Sigvora Decision Path

The final architecture can be summarised as:

```text
1. Customer submits request
                ↓
2. Sigvora validates request
                ↓
3. Original request is persisted
                ↓
4. Intent/category are determined
                ↓
5. DecisionSignals are extracted
                ↓
6. Risk is assessed
                ↓
7. Priority is assigned
                ↓
8. Relevant organisational knowledge is retrieved
                ↓
9. Evidence relevance is assessed
                ↓
10. Evidence-grounded recommendation is generated
                ↓
11. Operational destination is determined
                ↓
12. Governance evaluates permitted authority
                ↓
13. ALLOW / REVIEW / ESCALATE / ABSTAIN
                ↓
14. Human review occurs where required
                ↓
15. Permitted workflow transition occurs
                ↓
16. SLA state is monitored
                ↓
17. Decision history is audited
                ↓
18. Outcome and feedback are retained
                ↓
19. Structured results become evaluation evidence
```

This is the architectural backbone of Sigvora.

---

# 49. Architecture Invariants

These rules must remain true as Sigvora evolves.

### INV-01

The original customer request is preserved.

### INV-02

Customer sentiment alone cannot determine operational risk.

### INV-03

A policy-dependent recommendation cannot fabricate organisational policy
when evidence is unavailable.

### INV-04

AI inference must not be silently represented as customer fact.

### INV-05

High model confidence does not grant operational authority.

### INV-06

The recommendation model cannot bypass governance.

### INV-07

Sensitive actions require appropriate authorisation.

### INV-08

Human overrides preserve the original AI recommendation.

### INV-09

AI-provider failure does not lose the customer request.

### INV-10

Retrieved documents and customer text are treated as untrusted input.

### INV-11

Operational customer-case state has an authoritative persistent source.

### INV-12

Trustworthy AI claims require evaluation evidence.

### INV-13

Every major Sigvora capability must support the customer-request
decision lifecycle.

---

# 50. Architecture Risks

| Risk | Response |
|---|---|
| Incorrect intent | Confidence + UNKNOWN + review |
| Missed high-risk signal | Signal evaluation + human escalation |
| Incorrect priority | Explainable rules + evaluation |
| Poor retrieval | Retrieval evaluation |
| Hallucinated policy | Evidence requirement |
| Missing evidence | Abstain / review |
| Conflicting evidence | Expose conflict |
| Prompt injection | Trust boundaries |
| Excessive AI authority | Governance enforcement |
| AI-provider outage | Persist + graceful degradation |
| Duplicate processing | Idempotency |
| Human automation bias | Visible evidence and uncertainty |
| Lost traceability | Structured audit |
| Premature complexity | Modular monolith |

---

# 51. Architecture Validation Scenario

A design should be tested against a realistic Sigvora request.

Customer:

```text
"My card was stolen yesterday.

I've checked my account today and there are three
transactions totalling £650 that I don't recognise."
```

Expected architectural journey:

```text
CustomerRequest
        ↓
IntentClassification
Potential Fraud / Card Security
        ↓
DecisionSignals
STOLEN_CARD
UNRECOGNISED_TRANSACTIONS
FINANCIAL_EXPOSURE = £650
        ↓
RiskAssessment
HIGH
        ↓
PriorityAssessment
P1
        ↓
Knowledge Retrieval
Relevant approved fraud/card-security procedure
        ↓
Evidence
Source and relevant sections retained
        ↓
Recommendation
Urgent specialist escalation
        ↓
Routing
Fraud / Card Security
        ↓
Governance
ESCALATE
        ↓
Human Handling
Required
        ↓
SLA
High-priority service target
        ↓
Audit
Complete decision chain retained
```

If an architectural component cannot explain its role in this scenario,
its inclusion in the core system should be challenged.

---

# 52. Architecture Definition of Done

This architecture is ready to guide implementation when it clearly
defines:

- the customer-request lifecycle;
- product boundaries;
- request understanding;
- DecisionSignals;
- risk and priority;
- RAG and evidence;
- recommendations;
- routing;
- RSG's supporting role;
- governance;
- human oversight;
- SLA handling;
- persistence;
- auditability;
- security boundaries;
- failure behaviour;
- observability;
- evaluation;
- and requirement traceability.

---

# 53. Current Evidence Status

This document describes the **target architecture**.

At this stage it must not be interpreted as evidence that:

- the complete application exists;
- classification accuracy has been established;
- retrieval quality has been validated;
- governance effectiveness has been experimentally demonstrated;
- SLA improvements have been measured;
- financial-services compliance has been certified;
- or Sigvora has been tested at enterprise scale.

Those claims require implementation and empirical evidence.

The portfolio will distinguish clearly between:

```text
DESIGNED

IMPLEMENTED

TESTED

EVALUATED

DEPLOYED
```

---

# 54. Next Design Artefact

The next document is:

`docs/AI_DESIGN.md`

It will define how AI specifically supports Sigvora's customer-request
workflow, including:

- intent classification;
- DecisionSignal extraction;
- structured AI outputs;
- confidence and uncertainty;
- RAG query construction;
- document chunking;
- embeddings;
- retrieval;
- reranking;
- evidence packaging;
- grounded recommendation generation;
- abstention;
- prompt boundaries;
- prompt-injection defence;
- model/provider abstraction;
- model and prompt versioning;
- fallback behaviour;
- AI observability;
- and AI evaluation.

The AI subsystem must remain subordinate to the Sigvora customer-request
decision process defined in this architecture.
