---
doc-id: QA-STRATEGY
title: Target QA and Acceptance Architecture
status: PROPOSED
version: 0.2
date: 2026-09-23
owner: QA Architect (drafted); Product Owner (approval)
applies-to: full enterprise target; pilot acceptance
depends-on: [ARCH-STATES, ARCH-RULES, FIN-ARCH, SEC-MODEL, ARCH-NFR, INT-ARCH]
---

# Target QA and Acceptance Architecture

## 1. Purpose

This document defines how SuiteFlow proves it works: test levels, data strategy, traceability, gates, severity, release evidence, regression, UAT and acceptance. It is the quality contract for implementation tickets (Definition of Done) and for pilot go/no-go.

## 2. Test levels

| Level | Scope | Examples | Blocking for |
|---|---|---|---|
| Unit | Pure logic: rate derivation, tax computation, deposit conservation formulas, state transition legality tables | BR-RTM-004, INV-FOL-7 | Merge |
| Module/integration | Module boundaries: reservation ↔ inventory ↔ folio; posting service; outbox | INV-RSV-3, INV-ACC-1 | Merge |
| API | Contract behaviour, versioning, errors, idempotency | API specs (WP 0.7) | Merge / release |
| Workflow/state machine | Every transition in ARCH-STATES: legality, permissions, evidence, effects | All SM-* | Release |
| Data & migration | Migration restart, backfill, reconciliation, no-loss | ADR-003 class mapping | Release / go-live |
| Permission & scope | Negative tests per surface class; self-approval denial; masking; audit of sensitive reads | SEC-MODEL §13 | Release |
| Financial | Golden day end-to-end; posting families; control accounts; deposits; refunds; close/reopen | BR-FOL/ACC/NAU suites | Release |
| Concurrency | Races: double assignment, double payment, double posting, simultaneous close | INV-*, C-3/C-4/C-5 | Release |
| Failure injection | Crash between commit and publish; worker kill; provider timeout/uncertain; partial batch; restore | ADR-010/011 models | Release |
| UI | Key flows (keyboard-only, permission-rendering, empty/error states, responsive) | UX-ARCH §30 | Release |
| End-to-end | Guest day and group day scripts across screens and roles | WF-* | Release / UAT |
| Performance | Production-scale synthetic volume; budgets in NFR §2 | NFR P-1…P-10 | Release / go-live |
| Security | Scope leakage, authz bypass, injection, secrets, webhook forgery, AI tool abuse | SEC-MODEL §13 | Release / go-live |
| Regression | Full suites re-run on every change to shared logic | See §7 | Every release |
| UAT | Scripted role-played scenarios on synthetic data (Product Owner as acting operations authority, OQ-003 closed; finance review); real-role UAT deferred to the first property | §8 | Reference release / First-Property Deployment Gate |

## 3. Test data strategy

- **Golden day fixture**: deterministic 200-room NGN/WAT synthetic operating day with known control totals, negative cases and expected reconciliation outcomes — the financial acceptance anchor (extends the existing implementation's concept as a target requirement).
- **Production-scale synthetic volume**: 1–3M folio items/year equivalent for performance and close tests.
- **No production personal data in test**; where production-derived data is required, it is masked/pseudonymised with class-A fields removed.
- Test data generation is scripted and versioned so failures are reproducible.

## 4. Traceability — test obligation registry

Every invariant, business rule and state machine produces test obligations `TO-<DOMAIN>-NNN`, recorded in the implementation test plan and traced back to the blueprint. Representative obligations established by this blueprint:

| ID | Obligation | Source |
|---|---|---|
| TO-AVL-001 | Double-sell attempt fails deterministically under simulated race | INV-PM-4/6, C-3 |
| TO-AVL-002 | Overbooking exposure report matches permitted oversells nightly | BR-AVL-002 |
| TO-RTM-001 | Derived rate recompute never alters posted history | INV-RTM-4, ADR-003 |
| TO-RTM-002 | Past-date rate edit is impossible; forward version required | BR-RTM-003 |
| TO-FO-001 | Every check-in gate blocks when failing; override requires authority and is audited | BR-FO-001 |
| TO-FO-002 | Checkout refuses unbalanced/untended folios per invariant | BR-FO-002, INV-FOL-1 |
| TO-FOL-001 | Folio balance identity holds under concurrent posting and crash injection | INV-FOL-1 |
| TO-FOL-002 | Corrections are additive; original items immutable | INV-FOL-2 |
| TO-FOL-003 | Tax snapshots match effective rules per business date | INV-FOL-5 |
| TO-FOL-004 | Deposit conservation holds through partial apply/refund/forfeit | INV-FOL-7 |
| TO-FOL-005 | Refund cannot exceed cleared, un-refunded funds | INV-FOL-8 |
| TO-CSH-001 | One open session per till; variance beyond tolerance requires independent approval | BR-CSH-001/003 |
| TO-NAU-001 | Close is idempotent and resumable; no partial advancement | INV-NAU-2, ADR-006 |
| TO-NAU-002 | Reopen creates versioned restatement; originals preserved | INV-NAU-4/5 |
| TO-ACC-001 | Exactly one posting per source event per dimension under retry | INV-ACC-1 |
| TO-ACC-002 | Direct-bill transfer posts zero revenue lines | ADR-005 §5 |
| TO-ACC-003 | Mapping gap blocks close | ADR-005 §7; FIN-ARCH §3 |
| TO-ACC-004 | Control accounts reconcile to zero on golden day | FIN-ARCH §10 |
| TO-SEC-001 | Cross-scope denial per surface class (command/query/report/export/API/webhook/AI tool) | SEC-MODEL §6 |
| TO-SEC-002 | Self-approval denial across maker–checker catalogue | SEC-ROLES §5 |
| TO-SEC-003 | Class-A masking and read logging; export approval | SEC-MODEL §5/§10 |
| TO-INT-001 | Duplicate delivery produces one effect; replay safe | ADR-010 |
| TO-INT-002 | Provider timeout resolved by status/reconciliation, no blind resubmission | ADR-010 §4 |
| TO-INT-003 | Acquirer batch reconciliation catches injected mismatch | INT-ARCH §6 |
| TO-REL-001 | Timed restore within 1 h RTO; post-restore reconciliation passes | BR-REL-002/003 |
| TO-REL-002 | Restored workers do not duplicate external/accounting effects | BR-REL-007 |
| TO-UX-001 | Keyboard-only completion of check-in/payment/move/checkout/close | UX-ARCH §30 |
| TO-UX-002 | Permission UI cannot leak through counts/search/errors | UX-ARCH §30 |
| TO-OPS-001 | Close failure pages on-call within 5 min and runbook resolves | NFR O-2, WF-NA-002 |

## 5. Gates

| Gate | Required evidence |
|---|---|
| Merge | Unit + module + API tests; architecture tests; no new unmapped posting families |
| Release candidate | Full regression suites; financial suite; permission/scope pack; failure injection; UI key flows |
| Reference release | Golden day acceptance on synthetic data; performance at scale; security test pack; timed restore drill; synthetic migration reconciliation; role-played UAT accepted by the Product Owner (acting operations authority) and finance; documentation updated; runbooks exercised |
| First property | First-Property Deployment Gate: real-data migration reconciliation; hotel-staff UAT and acceptance; training completed; on-site readiness review |
| Post-release | Monitored stabilization; defect triage; no S0/S1 open from previous release |

## 6. Defect severity

| Severity | Definition | Response |
|---|---|---|
| S0 | Financial integrity violation, data loss, scope leakage, security breach | Stop-the-line; fix before any release; root-cause review |
| S1 | Core workflow blocked (check-in, payment, close) with no workaround | Hotfix path; daily escalation until resolved |
| S2 | Degraded function with workaround; incorrect non-financial data | Next release; tracked |
| S3 | Cosmetic, wording, minor UX | Backlog |

## 7. Regression suites

- **State machine suite**: every transition legal/illegal cases.
- **Financial suite**: golden day, postings, control accounts, deposits, refunds, close/reopen.
- **Permission suite**: scope, limits, maker–checker, masking.
- **Close suite**: idempotency, recovery, certification, restatement.
- **Integration suite**: contracts, failure scenarios, reconciliation injections.
- **UX suite**: key flows, keyboard, empty/error states.
- **Recovery suite**: restore drill evidence recency.

Any change to shared logic (authorization, posting, close, inventory) re-runs the full affected suites regardless of apparent locality.

## 8. UAT and acceptance

- **UAT scripts** derived from the workflow catalogue, executed by real roles (agent, supervisor, housekeeping, night audit, income audit, finance) on a UAT environment with production-like synthetic data.
- **Financial acceptance**: finance controller executes close, reconciliation review, refund/forfeit flows and signs the golden-day result.
- **Operational acceptance**: the acting operations authority (Product Owner; OQ-003 closed) signs workflow and service-quality acceptance at the reference release; hotel staff sign at the First-Property Deployment Gate.
- **Go/no-go** considers: open S0/S1, golden-day result, restore drill recency, permission pack, security pack, runbook readiness, 24×7 rota (OQ-010), documentation completeness, and unresolved blocking open questions.

## 9. Environments

| Environment | Purpose | Data |
|---|---|---|
| Development | Build and unit/module tests | Synthetic |
| Test/CI | Automated suites, performance | Synthetic at scale |
| UAT | Scripted acceptance, migration rehearsal | Production-like synthetic (masked where derived) |
| Production | Live operation | Real |

Promotion is by versioned artefact only; no environment-specific code branches; configuration is data (ADR-001/002).

## 10. Open items

| Item | Owner | Effect |
|---|---|---|
| OQ-002 named acceptance participant (finance); OQ-003 closed (acting operations authority) | Product Owner | UAT script owners and sign-off |
| OQ-008 (closed) close window | Hotel Ops | Close performance gate |
| OQ-009 (closed) availability objective | Product Owner | Availability test definition |
| OQ-024 retention | Security/Legal | Test data retention |
| OQ-038 connectivity | Product Owner | Degradation drills design |

## 11. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial QA and acceptance architecture issued with WP 0.7 | PROPOSED |
| 0.2 | 2026-09-23 | Synthetic reference pilot: UAT reframed as scripted role-played acceptance; release gates updated (reference release; First-Property Deployment Gate) | PROPOSED |