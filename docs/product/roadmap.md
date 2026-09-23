---
doc-id: PROD-ROADMAP
title: Product Roadmap (Skeleton)
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Product Owner (accountable)
applies-to: full enterprise target; pilot-first delivery
depends-on: [PROD-CAPMAP, PROD-SCOPE, GOV-RISK]
---

# Product Roadmap (Skeleton)

## 1. Purpose and status

This is the **skeleton** roadmap: phases, objectives, capability scope and exit-criteria themes. It exists now so that capability priorities have a delivery shape and the pilot is not designed in isolation.

The full roadmap — with per-phase deliverables, dependencies, risks, test obligations, acceptance criteria and cutover detail per the phase-exit standard — is produced in **WP 0.8** once the target architecture (WP 0.2–0.7) is defined. This document will then be versioned to 1.0.

## 2. Roadmap principles

1. **Pilot-first, target-complete.** Phases 1–9 deliver the Golfview pilot. The target remains the whole capability map; enterprise phases deliver the rest without re-architecting what the pilot built.
2. **Capability traceability.** Every phase is expressed as capability IDs. If work cannot cite a capability, it does not belong in a phase until the map changes through change control.
3. **Integrity before breadth.** The financial and inventory integrity chains (§7 of the capability map) must be complete end-to-end before adjacent capabilities expand. A hotel with half a folio system has less than no folio system.
4. **Gates are earned, not scheduled.** The 24-week horizon is a target gated by finance, audit, security, recovery and hotel acceptance. A phase that fails its exit evidence holds the programme; it does not pass informally.
5. **Each phase leaves the system operable.** No phase may end in a state where the hotel could not run a day's business on what exists.

## 3. Pilot phases (1–9)

### Phase 1 — Foundation, tenancy and control substrate

| Aspect | Content |
|---|---|
| Capabilities | CAP-PLT-001…009, 011…014; CAP-PM-001…004, 013; CAP-HRM-001, 002; CAP-NAU-001; CAP-ACC-001 (mapping skeleton) |
| Objective | One property, one tenant, one set of enforced scopes; identities, permissions, audit, events and business-date foundations in place |
| Exit themes | Negative tests for unauthorized and cross-scope access; audit completeness on governed actions; configuration effective-dating; recovery design accepted against 1h RPO/RTO |
| Dependencies | OQ-026 (hosting/region), OQ-028 (licensing) for production topology only |

### Phase 2 — Property, rates and inventory definition

| Aspect | Content |
|---|---|
| Capabilities | CAP-PM-005…012; CAP-RTM-001…012; CAP-AVL-001, 002, 004; CAP-PLT-014 (tax/localisation configuration) |
| Objective | The hotel's product existence: rooms, types, features, zones, rate plans, restrictions, effective-dated pricing |
| Exit themes | Deterministic rate calculation proven with worked examples (occupancy, children, LOS, restrictions); rate history immutable; availability calculation rebuildable and reconciled |
| Dependencies | OQ-021 (tax/service charge) before tax configuration is final; hotel room data collection |

### Phase 3 — Guests and reservations

| Aspect | Content |
|---|---|
| Capabilities | CAP-GST-001…010, 012; CAP-RSV-001…022; CAP-AVL-003, 007, 009 |
| Objective | The full commercial booking lifecycle with privacy-aware guest records |
| Exit themes | Reservation state machine with legality tests; concurrency-safe inventory (double-booking attempts must fail deterministically); deposit and guarantee scheduling correctness; duplicate detection quality controls |
| Dependencies | OQ-012 (cancellation/no-show/deposit policy), OQ-024 (retention) |

### Phase 4 — Front office

| Aspect | Content |
|---|---|
| Capabilities | CAP-FO-001…010, 012…016 |
| Objective | Governed arrival, stay and departure: check-in gates, in-house management, moves, extensions, checkout, service recovery |
| Exit themes | Check-in blocked by policy/credit/readiness with authorized, audited overrides; checkout invariant (balance settled or transferred) proven under failure injection; shift handover evidence |
| Dependencies | OQ-019 (ID capture) for registration evidence design; Phase 5 readiness integration |

### Phase 5 — Housekeeping and maintenance

| Aspect | Content |
|---|---|
| Capabilities | CAP-HSK-001…012 (008 only if outlets in scope, OQ-014); CAP-MNT-001…008 (pilot depth per OQ-022); CAP-PM-009, 010 |
| Objective | Rooms are cleaned, inspected and released on real state; maintenance is tracked with evidence |
| Exit themes | Readiness gate cannot be bypassed; discrepancy workflow reconciles front office and housekeeping daily; OOO/OOS instantly affects availability |
| Dependencies | OQ-014, OQ-022 |

### Phase 6 — Folio, cashiering and settlement

| Aspect | Content |
|---|---|
| Capabilities | CAP-FOL-001…013; CAP-CSH-001…010; CAP-POS if in pilot scope (OQ-014); CAP-CRM-003/004/006, CAP-INT-009 (guest communication) |
| Objective | Every charge, payment, deposit, adjustment and refund is correct, evidenced and reproducible on demand |
| Exit themes | Folio balance invariant under concurrent posting and failure injection; cashier variance workflow with independent review; POS/cheque/transfer true state models; no PAN/CVV storage verified |
| Dependencies | OQ-006 (POS/acquirer details), OQ-011 (invoice form), OQ-021 |

### Phase 7 — Night audit, income audit and finance close

| Aspect | Content |
|---|---|
| Capabilities | CAP-NAU-002…010; CAP-INA-001…007; CAP-ACC-001…012; CAP-INT-007 |
| Objective | The day closes once, provably, recoverably; revenue and tax post correctly; books reconcile |
| Exit themes | Golden-day fixture passes end-to-end including negative cases; close recovery is idempotent (no double posting); control accounts reconcile to zero difference; reopen/backdated controls proven |
| Dependencies | OQ-002 (Finance representative), OQ-008 (cutoff), OQ-011, OQ-021 |

### Phase 8 — Reporting and operational hardening

| Aspect | Content |
|---|---|
| Capabilities | CAP-RPT-001…003, 006…008, 011, 012; CAP-PLT-008, 010, 011; CAP-AVL-008 if applicable |
| Objective | Management, finance and audit run on system reports with drill-down reconciliation |
| Exit themes | Every report reconciles to source and drills down; scheduled close-window alerts page correctly; performance within operating windows at 200-room volumes |
| Dependencies | Phase 7 complete; real hotel data volumes available |

### Phase 9 — Migration, UAT and production cutover

| Aspect | Content |
|---|---|
| Capabilities | CAP-PLT-012, 016; CAP-HSK-010 (discrepancy as migration control); migration of guest, reservation, folio and opening-balance data |
| Objective | Operate the hotel in production with rehearsal-proven cutover, rollback and recovery |
| Exit themes | Dry-run migration reconciles (opening balances, in-house guests, deposits); timed restore with post-restore reconciliation; hotel acceptance signed; 24×7 support rota live; rollback exercised |
| Dependencies | OQ-025 (existing data), REL-002 (support cost), all prior phases |

## 4. Enterprise phases (10+)

Phases are sequenced by dependency and value. They may be re-ordered by commercial opportunity without changing their content.

| Phase | Theme | Primary capability scope | Exit themes |
|---|---|---|---|
| 10 | Groups, corporate, sales and events | CAP-GRP-*, CAP-CRP-006…010, CAP-SAL-*, CAP-AVL-005/006/010 | Group commitment-to-revenue chain (block → pickup → master account → AR) proven end-to-end |
| 11 | Outlets, inventory and F&B depth | CAP-POS-* (external POS via INT-006), CAP-INV-*, CAP-MNT-004/005/006, CAP-HSK-006/011/012, CAP-INA-008, CAP-ACC-007 | Theoretical vs actual consumption reconciles; outlet revenue reconciles to settlement |
| 12 | CRM, communication and loyalty | CAP-CRM-001…005/007, CAP-GST-011, CAP-INT-008 | Consent-respecting lifecycle automation with suppression evidence |
| 13 | Distribution and channels | CAP-INT-002/003, CAP-AVL-008, CAP-RTM-013 advisory, CAP-HRM-004/005 | Channel inventory parity with no oversell under latency and retry |
| 14 | Multi-property and chain | CAP-PM-003, CAP-CRP-008, CAP-BI-002/003, CAP-HRM-003, CAP-SAL-010 | Second property onboarded by configuration; consolidated reporting reconciles per property |
| 15 | BI and advanced analytics | CAP-BI-001/004/005, CAP-RPT-004/005/009/010 | Analytical model reconciles to closed books; self-service governed by scope |
| 16 | Integration expansion | CAP-INT-006/010/011/012/013, CAP-BI-006 | Each adapter holds contract, idempotency, failure and reconciliation evidence |
| 17 | Security, compliance and audit hardening | CAP-PLT-015, CAP-INA-008, compliance obligations from Phase 1 research | Break-glass review discipline; independent audit readiness |
| 18 | Performance, reliability and scale | Cross-cutting; no new capabilities | Proven scale targets; failure-mode exercises; capacity model |
| 19 | AI capabilities | CAP-AI-001…010 | Governance layer enforced (AI cannot exceed scoped tools); evaluation suite passes; human authority preserved |
| 20 | Continuous governance and optimisation | Cross-cutting | Blueprint/current-state gap reviews on cadence; debt and risk registers current |

## 5. What happens to the current implementation

Nothing in this roadmap presumes the current implementation is discarded or retained. The transition architecture (Phase 4 of the programme) will map every capability to a per-domain action — keep, extend, refactor, wrap, replace, migrate — against this map and the current-state audit. Until then, the implementation continues being evidence, not plan.

## 6. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Skeleton roadmap issued with WP 0.1 pass 2; full version in WP 0.8 | PROPOSED |