---
doc-id: BP-V1
title: SuiteFlow Target-State Blueprint v1.3
status: PROPOSED
version: 1.3
date: 2026-09-23
owner: Product Owner (acceptance); Principal Architect (maintainer)
applies-to: all SuiteFlow work
depends-on: [GOV-CHARTER, GOV-INPUTS]
---

# SuiteFlow Target-State Blueprint v1.3

## 1. What this is

The consolidated target-state blueprint: the complete specification of the SuiteFlow hotel platform as it should exist, designed from hospitality requirements, enterprise PMS capability benchmarks, accounting principles, security principles and real operating workflows — before and independently of the current implementation.

**Status: PROPOSED.** Nothing in this blueprint is yet ACCEPTED (charter §4). Acceptance requires the approvals and resolutions listed in §12. Until then, the blueprint is the working specification and the baseline against which the current implementation will be audited in Programme P2.

**How to use it:** start at the charter (governance, source-of-truth hierarchy, change control); use the capability map for scope and priorities; use the domain/state/rule documents for behaviour; use finance, security, UX, integration, reporting, NFR, QA, deployment and documentation architectures for their disciplines. Every artifact cites capability IDs; every requirement resolves to a test obligation.

## 2. Consolidated inventory

| Artifact class | Count | Location |
|---|---|---|
| Documents in this blueprint | 44 | `docs/` |
| Domains | 25 | capability map |
| Capabilities (`CAP-*`) | 283 | `product/capability-map.md` |
| Domain invariants (`INV-*`) | 80 | `architecture/domain-model.md` |
| Business rules (`BR-*`) | 101 across 15 domains | `architecture/business-rules.md` |
| State machines (`SM-*`) | 18 | `architecture/state-machines.md` |
| Workflows (`WF-*`) | 22 | `workflows/catalogue.md` |
| Screens | 16 | `ux/architecture.md` |
| Reports | 22 | `reporting/architecture.md` |
| Interfaces | 14 | `integrations/architecture.md` |
| Test obligations (`TO-*`) | 29 representative | `qa/strategy.md` |
| Architecture decisions (`ADR-*`) | 11 | `architecture/adr/` |
| Risks (`RSK-*`) | 33 | `00-governance/risk-register.md` |
| Open questions (`OQ-*`) | 38 (26 closed, 12 open) | `00-governance/open-questions.md` |
| Accepted requirements (`BR-PILOT/PAY/MVP/REL-*`) | 33 | `00-governance/inputs-register.md` |

## 3. Governance summary

- **Source of truth (charter §3):** approved business requirements → accepted blueprint → ADRs → rules → implementation specifications → code/database → tests → external research → assumptions. A lower level never silently overrides a higher one.
- **Change control (charter §7):** accepted content changes only with impact analysis and recorded approval; architectural changes require an ADR first.
- **Confidence labels (charter §6):** VERIFIED / INFERRED / ASSUMED / UNVERIFIED / CONFLICTING. Blueprint-wide defaults are PROPOSED and thus ASSUMED unless labelled otherwise.
- **Review protocol:** ten explicit passes per work package including two adversarial (ASTRA) passes; findings recorded in commit history and work-package reports.

## 4. Product definition at a glance

- **Vision:** an enterprise hotel operating platform running the commercial, operational and financial life of a hotel — deterministic, auditable, operationally realistic — first proven on a modelled 200-room Lagos property (synthetic reference pilot), designed for groups and chains without re-architecture.
- **Scope tiers:** enterprise target (all 283 capabilities) / pilot release — **204 candidate pilot capabilities** (`Yes`, provisional until the P0 scope freeze) / enterprise delivery 76 (`D10`–`D20`) / 3 pending evidence (OQ-007/024/029).
- **Pilot:** synthetic reference pilot — the modelled "Golfview profile" (200 rooms, WAT, NGN, conference property; cash/POS/transfer/cheque); 24-week horizon starting 23 Sep 2026, target reference release **10 Mar 2027**; pilot scope freezes at acceptance; 24×7 support, 1 h RPO/RTO; real deployment follows the First-Property Deployment Gate.
- **Deferred by accepted boundary:** payroll, advanced CRM, advanced analytics, external POS business integration, foreign currency, multi-property reporting — all retained in the enterprise target with phases.

## 5. Architecture at a glance

- **Topology (ADR-001):** modular monolith; four ownership layers (Platform, Operations Core, Control Plane, Accounting Authority); one owner per datum; adapters at every external boundary; a single financial posting gateway; module boundaries enforced by tests.
- **Tenancy (ADR-002):** tenant is the deployment unit; properties are first-class partitions; scope enforced platform-wide; optional dedicated deployment for hard-isolation customers.
- **Temporal (ADR-003):** four classified temporal strategies; applied-basis snapshots; corrections additive; historical documents reproducible as issued.
- **Identity (ADR-004):** opaque internal identity; separate human number series; namespaced external references; non-destructive merges.
- **Product binding:** no existing product inherits architectural authority; binding to roles (operations core, accounting, control plane) is a Programme P3/P4 evidence decision.

## 6. Financial model at a glance

- **Ownership (ADR-005):** SuiteFlow is the subledger of record (folios, payments, deposits, cashier); the Accounting Authority owns GL, AR, tax and period close.
- **Posting:** one daily aggregated run per property per business day, 16 posting families, item-level drill-down, idempotent, unmapped family blocks close.
- **Revenue:** recognised once, on the correct business day; settlements and deposits are balance-sheet movements; direct-bill transfer is zero-revenue; comps post at value with contra-revenue.
- **Close (ADR-006):** validation → postings → reconciliation → certification → advance; certification timing configurable with pilot default before advance; reopen governed and versioned; guest operations never freeze for financial degradation.
- **Deposits (ADR-007):** obligation-linked liabilities with conservation; forfeiture to dedicated cancellation/no-show revenue; tax points flagged UNVERIFIED pending advice.
- **Documents (ADR-008):** SuiteFlow issues guest/corporate documents; statutory documents derive and link one-to-one; ruled by OQ-011 (closed) — SuiteFlow issues the customer-facing document, statutory/fiscal documents derive from and link to it.
- **Reconciliation:** 13 daily checks (subledger, clearing, acquirer settlement, bank statement); zero unexplained difference; exceptions owned and aged.

## 7. Security at a glance

- **Principles:** deny by default; server-side enforcement; single enforcement point (ADR-009); effective-dated authority limits; payload-bound maker–checker; break-glass governed.
- **Data classes:** A restricted (ID documents, watchlist, bank details) / B confidential / C internal / D configuration, with masking, read-logging and export controls.
- **Scope:** property/company enforcement on every surface class including reports, exports, APIs and AI tools; scope leakage is a critical defect.
- **Roles:** 20-role catalogue with limit defaults (PROPOSED) and 8 separation-of-duties rules with explicit exception paths.

## 8. Experience, integration, reporting, operations

- **UX:** 16 screens specified with keyboard flows, validation/error semantics, permission-driven rendering, empty states, mobile behaviour and degradation rules; UI test obligations include keyboard-only core flows and permission-leak tests.
- **Integrations:** 14 interfaces with full failure models; transactional outbox and idempotency contract (ADR-010); provider-neutral payments with manual baseline (ADR-011).
- **Reporting:** read-model architecture with as-of semantics, 22-report catalogue, definitions governance, drill-down guarantee.
- **NFRs:** labelled performance, concurrency, availability, recovery (1 h RPO/RTO REQUIRED), integrity, observability, scalability and accessibility requirements.
- **Deployment:** versioned artefact promotion, dual-path connectivity and UPS as pilot requirements, warm recovery, worker duplicate-effect protection, release management and runbook catalogue.
- **Documentation:** 21-document operational set with owners; docs-as-code; release-gated currency.
- **AI:** governance-first; proposals only; no AI enabled at pilot by default; evaluation gates per capability (D19).

## 9. Delivery plan

Pilot D1–D9 (foundation → property/rates → guests/reservations → front office → housekeeping/maintenance → folio/cashiering → close/finance → reporting/hardening → synthetic migration/role-played UAT/reference release), the First-Property Deployment Gate, and enterprise D10–D20, each with objective, capabilities, inputs, dependencies, risks, tests and exit criteria: `product/roadmap.md` v1.1.

## 10. Acceptance model

| Level | Acceptance means |
|---|---|
| Capability | Behaviour per state machines, rules and invariants; test obligations pass; documentation updated |
| Phase | Exit criteria met; evidence pack signed; no open S0/S1; approvers per charter §12 |
| Pilot (reference) | Golden-day financial acceptance on synthetic data; security test pack; timed restore drill; role-played UAT accepted by the Product Owner (acting operations authority) and finance; runbooks exercised; 24×7 rota live; reference-release go/no-go approval |
| First property | First-Property Deployment Gate closed: entity/bank/site/migration facts, hotel-staff UAT and acceptance, training, on-site readiness review |
| Blueprint | This document ACCEPTED with the sign-offs in §12 |

## 11. Open questions and risks

- **Open questions:** 38 in the register (`00-governance/open-questions.md`); **26 are closed** (25 by Product Owner adoption of the industry-standard answers on 23 Sep 2026, plus OQ-003 by the Product Owner acting as operations authority; decision log in the register §4). The 12 open items: **8 facts** — OQ-002 and OQ-033 now, OQ-001/004/006/007/025/038 deferred to the First-Property Deployment Gate — **3 professional-advice** items (OQ-024/028/029) and **one cost approval** (OQ-010). The non-deferred blockers are **OQ-002/033** (Finance Controller; Technical Lead and Security adviser).
- **Risks:** 33 in the register (`00-governance/risk-register.md`); highest: blueprint drift (RSK-GOV-001), financial/tax correctness (RSK-FIN-001/004), scope leakage (RSK-SEC-001), tenancy migration (RSK-MIG-002), connectivity/power (RSK-DEP-003), reference-vs-reality gap (RSK-PROD-003), operational adoption (RSK-OPS-001).
- **Review record:** acceptance-readiness review (`00-governance/review-findings-2026-09-23.md`, GOV-REVIEW) — 45 findings (3 critical, 13 high) from the finance, technical and security passes; resolution belongs to the pre-acceptance fix pass.
- **Adopted answers and interim defaults:** the closed questions' answers are in force and marked `OQ-nnn (closed)` in affected documents; open questions carry interim defaults, clearly marked, until their evidence, advice or approval exists. The synthetic reference pilot amendment (no real property; role-played acceptance) is recorded in the inputs register §3.1.

## 12. Programme P0 exit gate — what remains for ACCEPTED status

| Requirement | Approver | Status |
|---|---|---|
| Product vision, scope, capability priorities (incl. pilot de-scope decision) | Product Owner | Pending |
| Domain model, state machines, workflows, business rules (incl. adopted `[OQ]` answers) | Product Owner (acting operations authority, OQ-003 closed) | Pending |
| Financial architecture, posting/close/deposit/invoice rules | Finance Controller (OQ-002) + tax adviser (OQ-029) | Pending — invoice/deposit/service-charge answers adopted (OQ-011/012/021 closed) |
| Security model, role matrix, authority-limit defaults | Security/Privacy adviser (OQ-033) + Product Owner | Pending |
| UX, integration, reporting, NFR, QA, deployment, documentation, AI architectures | Technical Lead (OQ-033) + Product Owner | Pending |
| All 11 ADRs confirmed as ACCEPTED or revised | Respective approvers per ADR | Pending |
| Open questions closed or defaults formally adopted | Product Owner | 26 closed (adoption + OQ-003 appointment); 12 remain — 8 facts (6 deferred to the first property), 3 advice, 1 approval |
| Pilot capability set frozen (candidate pilot scope accepted or de-scoped) | Product Owner | Pending — freeze at Programme P0 acceptance (TEC-08) |

When the above complete, the blueprint is promoted to **ACCEPTED (current version, v1.3)** and becomes level 2 of the source-of-truth hierarchy — the baseline for Programme P2's audit, Programme P3's gap matrix and all implementation commitments.

## 13. What happens next

1. **Programme P1 — External research:** OPERA Cloud public-doc benchmark review, Frappe/ERPNext/Kamra/Frappe Payments/CRM/HRMS capability verification, Nigerian tax/legal primary sources, provider evidence matrices. Outputs: research register with cited sources; blueprint claims upgraded from ASSUMED to VERIFIED where evidence supports.
2. **Programme P2 — Current-state audit:** evidence-based classification of the implementation (implemented-and-verified / partial / defective / architecturally wrong / duplicated / missing) against this blueprint.
3. **Programme P3 — Gap matrix:** every capability mapped with action and priority.
4. **Programme P4 — Transition architecture:** per-domain keep/extend/refactor/wrap/replace rulings, product binding for roles, migration strategy.
5. **D1–D20:** delivery per the roadmap, with governed proof spikes permitted earlier for the riskiest mechanisms.

## 14. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 1.0 | 2026-09-23 | Consolidated blueprint issued at WP 0.8: inventory, architecture summaries, acceptance model, exit gate, next steps | PROPOSED |
| 1.1 | 2026-09-23 | Industry-standard answers adopted (25 questions closed, register v0.3): inventory, ADR-008 summary, §11 and §12 updated | PROPOSED |
| 1.2 | 2026-09-23 | Synthetic reference pilot recorded (OQ-003 closed; property-dependent items deferred): pilot definition, delivery plan, acceptance model (First-Property Deployment Gate) and §11/§12 updated | PROPOSED |
| 1.3 | 2026-09-23 | P1: candidate pilot scope (204/76/3), dated horizon, P0 scope-freeze gate row; review references updated (TEC-04/05/08, FIN/SEC P1) | PROPOSED |
