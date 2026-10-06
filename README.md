# Signaly

> **AI-powered customer operations intelligence for turning overloaded message queues into prioritised, structured, and actionable work.**

## 2,847 unread customer messages. Which one should your team open first?

Customer operations teams can receive thousands of messages across email, support forms, chat, and other channels.

A routine password question may sit beside a suspected fraud report. A vulnerable customer's request may be buried beneath hundreds of low-priority enquiries. Traditional first-in-first-out queues do not necessarily reflect urgency, operational risk, or customer impact.

**Signaly** is a full-stack AI-powered customer operations platform designed to help teams determine **what needs attention first**.

It analyses unstructured customer messages and transforms them into structured operational signals such as category, priority, risk indicators, and routing recommendations—while keeping consequential decisions under appropriate human control.

---

## Why Signaly?

The core problem is not simply:

> "Can an LLM classify text?"

The engineering problem is:

> **Can an AI-assisted system reliably identify which customer requests require attention first, explain enough of its output to support review, operate within acceptable cost and latency, and fail safely when the AI is uncertain or unavailable?**

Signaly is being built around that question.

---

## The Problem

Consider a customer operations team starting Monday morning with:

**2,847 unread requests.**

The queue contains a mixture of:

- account enquiries;
- payment problems;
- complaints;
- cancellation requests;
- service failures;
- suspected fraud reports;
- potentially vulnerable customers;
- routine administrative questions.

A simple chronological queue treats every message primarily according to arrival time.

That creates several operational problems:

- critical requests can remain buried;
- manual triage consumes employee time;
- different employees may classify the same request differently;
- requests may be routed to the wrong team;
- growing message volumes increase operational pressure;
- managers have limited visibility into the risk profile of the queue.

Signaly investigates whether AI-assisted triage can improve this workflow without turning the language model into an uncontrolled decision-maker.

---

## Product Goal

Signaly converts an incoming customer message into a validated operational record that can help a human operator decide what should happen next.

A conceptual output might look like:

```json
{
  "category": "payment_issue",
  "priority": "critical",
  "risk_flags": [
    "suspected_fraud"
  ],
  "recommended_queue": "fraud_operations",
  "confidence": 0.91
}
```

> **Note:** This represents the intended product contract. The schema may evolve during implementation and evaluation.

The structured result can then be presented through an operations interface where users can review, filter, prioritise, and manage customer requests.

---

## Human-AI Boundary

Signaly is designed as a **decision-support system**, not an autonomous authority.

The AI may:

- classify messages;
- identify operational signals;
- estimate priority;
- flag potential risks;
- recommend routing;
- support queue prioritisation.

The AI must not independently:

- determine that fraud has occurred;
- block or restrict a customer's account;
- approve or reject financial transactions;
- make customer eligibility decisions;
- close significant complaints;
- make other consequential customer decisions solely from model output.

Where a decision may materially affect a customer, appropriate application controls and human oversight should remain in the decision path.

This boundary is an explicit engineering requirement rather than an assumption.

---

## Initial Scope

### In Scope

The first product iterations focus on:

- customer-message ingestion;
- structured message classification;
- category identification;
- priority estimation;
- risk flagging;
- routing recommendations;
- human review;
- operational queue management;
- processing metadata;
- AI evaluation;
- auditability.

### Out of Scope

The initial system will not attempt to provide:

- fully autonomous customer decision-making;
- definitive fraud determination;
- autonomous financial decisions;
- unsupervised outbound customer communication;
- integration with a real financial institution;
- processing of real customer PII for demonstration purposes.

These boundaries may evolve only through explicit requirements and architectural review.

---

## User Journey

```text
              CUSTOMER MESSAGE
                     │
                     ▼
        ┌─────────────────────────┐
        │   Signaly Operations    │
        │         Inbox           │
        └────────────┬────────────┘
                     │
                     ▼
        ┌─────────────────────────┐
        │      FastAPI API        │
        └────────────┬────────────┘
                     │
                     ▼
        ┌─────────────────────────┐
        │     Triage Service      │
        └────────────┬────────────┘
                     │
                     ▼
        ┌─────────────────────────┐
        │       AI Service        │
        └────────────┬────────────┘
                     │
                     ▼
        ┌─────────────────────────┐
        │  Validated Structured   │
        │         Output          │
        └────────────┬────────────┘
                     │
                     ▼
        ┌─────────────────────────┐
        │    Persistent Record    │
        └────────────┬────────────┘
                     │
                     ▼
        ┌─────────────────────────┐
        │     Human Review &      │
        │        Action           │
        └─────────────────────────┘
```

The architecture shown above represents the **target initial workflow** and will be refined as implementation progresses.

---

## Engineering Questions

Signaly is designed to investigate more than basic LLM integration.

The project will progressively answer questions such as:

1. How accurately can customer requests be classified?
2. How reliably can critical cases be identified?
3. What happens when the model is uncertain?
4. What happens when the model returns malformed output?
5. What happens when the AI provider becomes unavailable?
6. How should human corrections be captured?
7. How can model behaviour be evaluated reproducibly?
8. How much does each classification cost?
9. What latency is acceptable for the operational workflow?
10. How should sensitive information be handled?
11. How can the system detect regressions after an AI change?
12. What breaks when message volume increases significantly?

These questions will drive engineering decisions throughout development.

---

## Planned Technology Stack

| Layer | Technology |
|---|---|
| Frontend | React + TypeScript |
| Backend | FastAPI + Python |
| Data Validation | Pydantic |
| AI Layer | LLM integration behind an application abstraction |
| Persistence | SQL database |
| Backend Testing | Pytest |
| API | REST |
| Version Control | Git + GitHub |
| CI/CD | GitHub Actions |
| Containerisation | Docker |
| Observability | Structured logs, metrics, and tracing introduced progressively |

Technology choices are **planned rather than permanently fixed**.

Significant decisions and changes will be documented through Architecture Decision Records (ADRs).

---

## Architecture Evolution

Signaly will not pretend to begin with a finished production architecture.

The system will evolve as requirements become more demanding.

### Stage 1 — Walking Skeleton

```text
React
  │
  ▼
FastAPI
  │
  ▼
AI Service
```

### Stage 2 — Functional Product

```text
React
  │
  ▼
FastAPI
  │
  ├──── Triage Service
  │
  ├──── AI Service
  │
  └──── Persistence
```

### Later Production Candidate

```text
                   React
                     │
                     ▼
                  API Layer
                     │
          ┌──────────┼──────────┐
          ▼          ▼          ▼
       Triage      AI Gateway   Audit
       Service                  Service
          │          │          │
          └──────────┼──────────┘
                     ▼
                 Persistence
                     │
                     ▼
            Evaluation / Telemetry
```

The architecture will only become more complex when requirements justify that complexity.

---

## Evaluation Strategy

A successful HTTP response is **not evidence that an AI system works**.

Signaly will therefore evaluate the AI and the surrounding software separately.

### AI Quality

Potential measures include:

- classification accuracy;
- precision;
- recall;
- F1 score;
- critical-case recall;
- priority classification quality;
- structured-output validity;
- human override rate.

Particular attention will be paid to **false negatives on critical requests**, because incorrectly treating a high-risk message as routine may be more costly than unnecessarily escalating a routine request.

### Engineering Quality

The application will progressively measure:

- API latency;
- error rate;
- AI processing latency;
- validation failures;
- provider failures;
- test coverage;
- recovery behaviour;
- system reliability.

### Operational Quality

Experiments may evaluate:

- routing accuracy;
- escalation rate;
- simulated handling efficiency;
- queue prioritisation quality;
- cost per processed message.

Business-impact claims will be described as **experimental or simulated** unless supported by genuine operational evidence.

---

## Evidence Status

Signaly is currently in its **Product Foundation** stage.

Therefore:

| Evidence | Current Status |
|---|---|
| Working frontend | Not yet implemented |
| Working API | Not yet implemented |
| AI classifier | Not yet implemented |
| Evaluation dataset | Not yet established |
| Classification baseline | Pending |
| Critical-case recall | Pending |
| API latency benchmark | Pending |
| Cost benchmark | Pending |
| Security testing | Pending |
| Production deployment | Not yet implemented |

These values will be replaced with measured evidence as the project develops.

**No performance result will be fabricated to make the project appear more mature than it is.**

---

## Data Strategy

The project will use appropriately licensed public, generated, or synthetic data for development and evaluation.

Dataset documentation will record:

- source;
- licence where applicable;
- generation methodology where synthetic data is used;
- schema;
- labels;
- class distribution;
- preprocessing;
- train/evaluation separation where applicable;
- known limitations;
- potential bias;
- PII considerations.

Real customer personal information is not required to demonstrate the engineering problem.

---

## Failure-Aware Engineering

Signaly will deliberately test failure conditions rather than evaluating only the happy path.

Examples include:

```text
Malformed model output
        │
        ▼
Schema validation fails
        │
        ▼
Controlled handling
        │
        ▼
No unsafe record silently enters workflow
```

Other scenarios will include:

- AI provider timeout;
- rate limiting;
- unavailable dependencies;
- invalid input;
- database failure;
- low-confidence classifications;
- conflicting signals;
- unexpected message formats;
- model behaviour changes.

Failure behaviour will become part of the documented system contract.

---

## Security Principles

The project will progressively address risks including:

- exposure of customer information;
- insecure API access;
- excessive application permissions;
- prompt injection;
- malicious message content;
- unsafe model output;
- logging of sensitive information;
- secrets management;
- dependency vulnerabilities;
- unauthorised operational actions.

A dedicated threat model will be developed as the application architecture becomes concrete.

---

## Observability

The earliest application versions will begin with basic request-level telemetry.

Example fields may include:

```text
request_id
timestamp
model
classification_status
latency
token_usage
validation_status
error_type
```

Observability will mature alongside the architecture rather than being added only after development is complete.

---

## Development Roadmap

### Milestone 0 — Product Foundation

Define:

- business problem;
- stakeholders;
- product requirements;
- scope;
- human-AI boundaries;
- initial architecture;
- data strategy;
- evaluation strategy;
- engineering decisions.

### Milestone 1 — Walking Skeleton

Establish the first complete application path:

```text
React → FastAPI → AI Service → Structured Output
```

### Milestone 2 — Intelligence & Persistence

Introduce:

- classification contract;
- persistent records;
- validation;
- initial evaluation dataset;
- baseline AI evaluation.

### Milestone 3 — Operations Experience

Build:

- prioritised inbox;
- filters;
- message detail view;
- human review;
- correction workflow;
- queue management.

### Milestone 4 — Reliability & Security

Introduce:

- stronger failure handling;
- authentication and authorisation where required;
- auditability;
- security controls;
- observability;
- failure testing.

### Milestone 5 — Production Readiness

Strengthen:

- automated testing;
- CI/CD;
- containerisation;
- deployment;
- performance testing;
- cost measurement;
- operational documentation.

### Milestone 6 — Engineering Challenges

The working system will then face changing conditions rather than remaining a static demonstration.

These will include:

#### Change Request

A business requirement changes after implementation has begun.

#### Fault Injection

A dependency or assumption fails and the system must recover safely.

#### Scale Shock

The original workload increases substantially, requiring architectural reassessment.

The purpose is to evaluate whether the system can evolve rather than merely work under ideal conditions.

---

## Planned Repository Structure

```text
signaly/
│
├── README.md
├── LICENSE
├── .gitignore
├── .env.example
│
├── frontend/
├── backend/
│
├── data/
│   ├── README.md
│   ├── raw/
│   ├── processed/
│   └── synthetic/
│
├── evals/
│   ├── README.md
│   ├── datasets/
│   └── results/
│
├── tests/
│
├── infrastructure/
│
├── docs/
│   ├── BUSINESS_CASE.md
│   ├── REQUIREMENTS.md
│   ├── ARCHITECTURE.md
│   ├── AI_DESIGN.md
│   ├── EVALUATION.md
│   ├── THREAT_MODEL.md
│   ├── TESTING.md
│   ├── OBSERVABILITY.md
│   ├── COST_ANALYSIS.md
│   │
│   ├── adr/
│   │
│   └── incidents/
│
├── .github/
│   └── workflows/
│
└── docker-compose.yml
```

> The structure above represents the target repository organisation. Directories and documents will be introduced when they contain meaningful implementation or evidence rather than being created empty for appearance.

---

## Engineering Record

Important engineering decisions will be documented using the following reasoning pattern:

| Question | Purpose |
|---|---|
| What hurts? | Define the business problem |
| How bad is the baseline? | Establish evidence before AI |
| Why might AI help? | Define the hypothesis |
| Why this architecture? | Justify the design |
| What alternatives were considered? | Demonstrate trade-off analysis |
| Does it work? | Evaluate with evidence |
| Where does it fail? | Understand limitations |
| How can it be abused? | Evaluate security |
| What does it cost? | Evaluate economics |
| Would we ship it? | Make an evidence-based production decision |

A valid engineering conclusion may be that an AI approach **does not meet the required release threshold**.

---

## Documentation

Detailed engineering documentation will be maintained separately from this README so that the README remains the entry point rather than becoming the entire project specification.

Planned documentation includes:

- business case;
- requirements;
- architecture;
- AI design;
- Architecture Decision Records;
- data documentation;
- evaluation methodology;
- experiment results;
- testing strategy;
- threat model;
- observability strategy;
- cost analysis;
- incidents and failure analysis.

---

## Current Status

> **Milestone 0 — Product Foundation**

Signaly is currently at the beginning of its engineering lifecycle.

The immediate objective is to establish the business case, requirements, initial architecture, data strategy, evaluation methodology, and first architecture decisions before expanding the implementation.

---

## License

This project is licensed under the MIT License.
