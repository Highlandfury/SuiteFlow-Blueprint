---
doc-id: AI-ARCH
title: AI Governance and Assistance Architecture
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Principal Architect + Security Engineer (drafted); Product Owner (approval; OQ-031 open)
applies-to: enterprise target (Phase 19); pilot has no AI capabilities enabled by default
depends-on: [ADR-009, SEC-MODEL, ARCH-DOMAIN]
---

# AI Governance and Assistance Architecture

## 1. Purpose and principles

AI in SuiteFlow is an operational amplifier under governance, architected now so it can be added later without redesigning security or authority. Principles (mandate §56):

1. **AI proposes; governed services dispose.** No AI output is authoritative for price, availability, tax, balance, payment amount, inventory, room assignment or permission.
2. **Same doors, same locks.** Any state change executes through the same authorized domain services as a human action, with the same validation, limits and audit.
3. **Content is untrusted.** Guest notes, documents, messages and provider payloads may contain adversarial instructions; AI treats all retrieved content as data, never as instructions.
4. **Least data.** Prompts carry the minimum data required; class-A data enters prompts only where the use case is approved and scoped.
5. **Audited.** Tool calls, proposals, approvals and executions are logged with the invoking principal.
6. **Evaluated before enabled.** Each AI capability passes an evaluation suite before it is switched on for any property.
7. **Cost-capped.** Usage budgets and rate limits per tenant/capability; runaway loops are contained.

## 2. Architecture

```text
User (or authorized service) 
   │ invokes assistant (scoped to their principal)
   ▼
Assistant runtime (model abstraction; no direct DB access)
   ├── Read tools      → scoped query tools (property/company scope enforced; class rules applied)
   ├── Proposal tools  → propose action (payment adjustment, rate change, task creation…) → maker–checker / authority flow
   └── Explanation tools → summaries over data the principal may read
   ▼
Governed services (domain) — decide, validate, execute, audit
   ▼
Audit + evaluation telemetry
```

- **Model abstraction**: the platform defines model-agnostic interfaces so providers/models can change without touching domain code; no customer data is used for model training without explicit contractual consent.
- **Tool gateway**: the only path from AI to platform data or effects; tools declare permissions and scope; the invoking principal's authority is the ceiling, never the assistant's.
- **Proposal records**: non-authoritative, expire, carry the intended payload hash; execution re-validates preconditions (ADR-009 §4).
- **Evaluation harness**: scenario suites per capability (accuracy, refusal, injection resistance, scope safety) with recorded results and change control.

## 3. Data protection in AI use

| Class | Default in AI | Exception |
|---|---|---|
| A (identity docs, watchlist, bank details) | Excluded | Approved, scoped use case with masking and audit (none planned at pilot) |
| B (guest PII, folios) | Allowed for the invoking principal's scope with minimisation | Class-A-adjacent fields redacted by tool design |
| C (operational) | Allowed within scope | — |
| D (config) | Allowed | — |

Prompt and response logging follows security retention rules; logs redact class-A fields; guest consent for AI processing beyond operational necessity is not required for staff-assist use where processing is within the existing lawful basis, but marketing/CRM AI use must respect consent projection (CAP-CRM-003).

## 4. Capability catalogue and gates

| Capability | Allowed inputs/tools | Authority | Enablement gate |
|---|---|---|---|
| AI-001 Front desk assistant | Scoped read tools (reservation, room, folio context), glossary | Explanation only | Evaluation: scope safety, refusal on out-of-scope |
| AI-002 Reservation assistant | Reservation reads, rate/availability reads | Proposals (create/amend → standard validation and authority) | Pricing-authority tests; no authoritative quotes outside tool output |
| AI-003 Management reporting assistant | Report/read models, definitions | Explanation with drill links | Definition-version awareness; no invented numbers |
| AI-004 Housekeeping assistant | Task reads, board summaries | Proposals (task prioritisation suggestions) | Supervisors remain deciders |
| AI-005 Maintenance assistant | Work-order/asset reads | Proposals (triage suggestion) | — |
| AI-006 Document extraction (IDs, invoices, vouchers) | Class-A handling per OQ-019/024 | Proposals (pre-fill) | Extraction accuracy and privacy tests |
| AI-007 Anomaly detection (financial/operational) | Posting/close/audit data within scope | Alerts for humans | False-positive/negative evaluation; no automatic enforcement |
| AI-008 Shift-handover summaries | Transition/evidence reads | Draft text | Accuracy review; no financial claims without drill links |
| AI-009 Guest communication drafting | Guest context within scope, templates, policy | Draft only | Brand/policy compliance review |
| AI-010 Governance layer | — (platform capability) | Enforces tool scope, budgets, audit | Mandatory before any other AI capability |

Pilot position: no AI capability is enabled by default; OQ-031 sets priorities if any pilot AI is approved.

## 5. Failure modes and controls

| Failure | Control |
|---|---|
| Hallucinated figures | Numbers come only from tool outputs with drill links; explanations without source are blocked by design; evaluation tests |
| Prompt injection via content | Content treated as data; instruction separation; tool allow-lists; injection-resistance evaluation |
| Scope leak through aggregation | Tools enforce principal scope; aggregation tested per surface class (SEC-MODEL §6) |
| Authority overreach | Proposals only; maker–checker and limits at execution; AI identities have no approval rights |
| Data exfiltration via prompts | Data minimisation; logging/redaction; provider terms prohibit training on customer data without consent |
| Cost/loop abuse | Budgets, rate limits, kill switch per capability and tenant |
| Model/provider change regression | Model abstraction + evaluation re-run on change (change control) |

## 6. Governance

- **Enablement**: capability-by-capability, property-by-property configuration; each enablement requires the evaluation gate and Product Owner approval.
- **Change control**: prompt/tool changes are versioned; material changes re-run evaluations.
- **Incident handling**: AI incidents (wrong output acted upon, leak, overreach) follow the same severity model; S0/S1 stop-the-line applies; root-cause includes prompt/tool design.
- **Transparency**: users know when a suggestion is AI-generated; proposals show their inputs and confidence basis.
- **Human accountability**: every execution has a human or governed service principal accountable for it.

## 7. Open items

| Item | Owner |
|---|---|
| OQ-031 AI priorities for pilot/roadmap | Product Owner |
| Model/provider selection and data-processing terms | Security + product (WP 0.8/Ph19) |
| OQ-024 retention (prompt/log retention) | Security/Legal |
| Evaluation suite content per capability | QA + product |

## 8. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial AI governance architecture issued with WP 0.7 | PROPOSED |