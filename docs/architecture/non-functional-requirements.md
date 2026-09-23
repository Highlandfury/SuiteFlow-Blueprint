---
doc-id: ARCH-NFR
title: Non-Functional Requirements
status: PROPOSED
version: 0.2
date: 2026-09-23
owner: Principal Architect + SRE (drafted); Product Owner (approval)
applies-to: pilot and enterprise target; values labelled REQUIRED / TARGET / ASPIRATIONAL / TBD
depends-on: [GOV-INPUTS, ADR-006, FIN-ARCH, SEC-MODEL, UX-ARCH]
---

# Non-Functional Requirements

## 1. Purpose and labelling

Measurable requirements for performance, concurrency, availability, recovery, integrity, observability, scalability, security, auditability, maintainability, upgradeability, operability, accessibility, compatibility and localisation.

Labels (mandate §20): **REQUIRED** (contractual or confirmed), **TARGET** (engineering objective, to be validated with evidence), **ASPIRATIONAL** (direction, not committed), **TBD** (needs input; owner named). No number here is invented as fact; targets are planning values to be proven under production-scale synthetic data and UAT.

## 2. Performance

| # | Requirement | Value | Label | Basis |
|---|---|---|---|---|
| P-1 | Room rack interactive render (200 rooms) | < 1 s p95 | TARGET | UX budget; validate at 200-room scale |
| P-2 | Queue navigation and list updates | < 300 ms p95 | TARGET | UX budget |
| P-3 | Global search (guest/reservation/folio) | < 1 s p95 | TARGET | UX budget |
| P-4 | Tape chart (90 days × 10 room types, bookings+bocks) | < 2 s p95 | TARGET | UX budget |
| P-5 | Command latency (check-in confirm, payment post) | < 2 s p95 | TARGET | Counter pace |
| P-6 | Nightly posting run (200 rooms, full folio volume) | < 15 min | TARGET | Close window (OQ-008 closed: 02:00–04:00 default) |
| P-7 | Total close including reconciliation | within configured close window | REQUIRED (OQ-008 closed) | BR-NAU-001 |
| P-8 | Daily report pack generation | < 60 s | TARGET | Morning operations |
| P-9 | Large historical reports | < 5 min | TARGET | Management use |
| P-10 | API p95: queries / commands | 500 ms / 1 s | TARGET | Integration contracts (WP 0.7) |

## 3. Concurrency and correctness

| # | Requirement | Value | Label |
|---|---|---|---|
| C-1 | Concurrent staff sessions at 200 rooms | 50 simultaneous | TARGET (validate in Phase 2/7) |
| C-2 | Peak concurrent check-ins | 15 without lost updates | TARGET |
| C-3 | Inventory double-booking under race | Deterministically prevented (unique constraints + transactional checks) | REQUIRED |
| C-4 | Folio balance identity under concurrent posting | Holds at every observable moment | REQUIRED (INV-FOL-1) |
| C-5 | Duplicate effects under retry/restart | None (idempotent) | REQUIRED (ADR-010) |

## 4. Availability and recovery

| # | Requirement | Value | Label | Basis |
|---|---|---|---|---|
| A-1 | Service availability | 99.5% pilot; 99.9% enterprise target (OQ-009 closed) | TARGET | BR-REL-004 |
| A-2 | RPO | 60 minutes | REQUIRED | BR-REL-002 |
| A-3 | RTO | 60 minutes | REQUIRED | BR-REL-003 |
| A-4 | Restore drill cadence | Pre-release and quarterly, timed | REQUIRED | BR-REL-002/003 |
| A-5 | Post-restore financial reconciliation | Within the hour after service restoration | REQUIRED | BR-REL-007 |
| A-6 | Worker duplicate-effect protection after restore | Proven by drill | REQUIRED | BR-REL-007 |
| A-7 | Connectivity redundancy | Dual-path WAN (wired + mobile failover) with UPS for desk equipment | REQUIRED for pilot viability | OQ-038; degraded-mode design |
| A-8 | Degraded mode | Non-financial captures queue and reconcile; financial actions blocked with clear UI | REQUIRED | UX-ARCH §12 |
| A-9 | Guest operations during financial degradation | Continue; only financial advancement is held | REQUIRED | ADR-006 §7 |

## 5. Financial integrity

| # | Requirement | Value | Label |
|---|---|---|---|
| F-1 | Revenue recognised once per business day | Zero duplicate/missing postings | REQUIRED |
| F-2 | Daily control-account reconciliation | Zero unexplained difference | REQUIRED |
| F-3 | Mapping completeness | Unmapped family blocks close | REQUIRED |
| F-4 | Deposit conservation | Holds at every observable moment | REQUIRED |
| F-5 | Close idempotency | No double posting across retries | REQUIRED |
| F-6 | Audit trail completeness for governed transitions | 100% of transitions evidenced | REQUIRED |

## 6. Observability and operability

| # | Requirement | Value | Label |
|---|---|---|---|
| O-1 | Outbox lag alert | < 5 min | TARGET |
| O-2 | Close failure paging | Within 5 min of failure, 24×7 | REQUIRED | 
| O-3 | Integration health surfaces per interface | Health, lag, dead letters, attempts | REQUIRED |
| O-4 | Correlation identity across operational → posting → reconciliation | End-to-end traceable | REQUIRED |
| O-5 | Runbooks per operational failure (close, integration, recovery, auth outage) | Maintained and exercised | REQUIRED |
| O-6 | Log retention (security/audit/financial) | Per policy, minimum 12 months (OQ-024 to confirm) | TBD |

## 7. Scalability

| # | Requirement | Value | Label |
|---|---|---|---|
| S-1 | Pilot scale | One property, 200 rooms, 1–3M folio items/year | REQUIRED |
| S-2 | Small group | 2–10 properties on the same tenant model | TARGET (Ph14) |
| S-3 | Chain | 50+ properties, consolidated reporting | ASPIRATIONAL |

## 8. Security and auditability

Per SEC-MODEL: deny by default; server-side enforcement on all surfaces; MFA for privileged roles; class-A handling; append-only audit; break-glass governance; scope-leak defects classified critical. Security NFRs are REQUIRED; their acceptance evidence is the security test pack (WP 0.7 QA strategy).

## 9. Maintainability and upgradeability

| # | Requirement | Value | Label |
|---|---|---|---|
| M-1 | Architecture tests (module boundaries, permissions declarations, scope injection) | Passing in CI | REQUIRED |
| M-2 | Automated migration framework; restartable migrations | Every release | REQUIRED |
| M-3 | Upgrade without bespoke forks; rehearsed rollback | Pre-release evidence | REQUIRED |
| M-4 | Test coverage of financial and permission logic | Defined by QA strategy gates | REQUIRED |
| M-5 | Documentation currency (no undocumented behaviour) | Release gate | REQUIRED |

## 10. Accessibility, compatibility, localisation

| # | Requirement | Value | Label |
|---|---|---|---|
| X-1 | Accessibility | WCAG 2.2 AA for core flows | TARGET (OQ-032 closed scope) |
| X-2 | Browsers | Current evergreen Chrome/Edge/Firefox/Safari; tablet Safari/Chrome | REQUIRED |
| X-3 | Minimum viewport | 1366×768 desktop; tablet/phone responsive per UX-ARCH §10 | REQUIRED |
| X-4 | Language | English at pilot; localisation architecture ready | REQUIRED / ASPIRATIONAL |
| X-5 | Print fidelity | Folio/receipt/registration/day pack series-correct | REQUIRED |

## 11. Open items

| Item | Owner | Effect |
|---|---|---|
| OQ-008 (closed) close window | Hotel Ops | P-6/P-7 operability |
| OQ-009 (closed) availability objective | Product Owner | A-1 |
| OQ-010 support cost/staffing approval | Product Owner | O-2, 24×7 rota |
| OQ-024 retention periods | Security/Legal | O-6, retention design |
| OQ-026 (closed) hosting/region | Product Owner | Deployment topology, residency |
| OQ-032 (closed) accessibility/language scope | Product Owner | X-1, X-4 |
| OQ-038 connectivity/power | Product Owner | A-7/A-8 design |

## 12. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial NFRs issued with WP 0.7 | PROPOSED |
| 0.2 | 2026-09-23 | Adopted answers applied: OQ-008 close window and OQ-009 availability (99.5% pilot, 99.9% enterprise target) marked closed | PROPOSED |