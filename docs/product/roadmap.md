---
doc-id: PROD-ROADMAP
title: Product Roadmap
status: PROPOSED
version: 1.3
date: 2026-09-23
owner: Product Owner (accountable)
applies-to: full enterprise target; pilot-first delivery
depends-on: [PROD-CAPMAP, PROD-SCOPE, QA-STRATEGY, ARCH-NFR, DEP-ARCH, GOV-RISK]
supersedes: PROD-ROADMAP 0.1 (skeleton)
---

# Product Roadmap

## 1. Purpose

The delivery plan from target blueprint to the pilot-ready reference release and beyond. D1–D9 deliver the **synthetic reference pilot** (the modelled "Golfview profile"); D10–D20 deliver the enterprise target without re-architecting what the pilot builds. Deployment to a real property is a separate gated step after D9 (First-Property Deployment Gate). Every phase is expressed as capabilities, with dependencies, risks, tests and **exit criteria** (mandate §61). Work that cannot cite a capability does not belong in a phase until the capability map changes through change control.

The roadmap follows the programme path: this blueprint (Programme P0) → external research (Programme P1) → current-state audit (Programme P2) → gap matrix (Programme P3) → transition architecture (Programme P4) → these delivery phases. Delivery phases only begin with ACCEPTED capabilities and a transition architecture ruling for each affected domain.

**Clock and scope freeze (PO decision, 23 Sep 2026 — TEC-04).** The 24-week horizon starts **23 Sep 2026** and ends at the pilot-ready reference release on **10 Mar 2027**; it includes Programme P0 acceptance (by Week 3), Programme P1–P4, and Delivery D1–D9. Indicative envelope: P1–P2 Weeks 1–8; P3–P4 to Week 12 (platform-domain rulings released early to unblock D1 by ~Week 6); D9 exit by Week 24. The pilot capability set **freezes at Programme P0 acceptance**; additions require change control with a Product Owner-approved trade-out, and any date change is a Product Owner decision recorded against BR-PILOT-005.

## 2. Cross-phase rules

1. **Gates are earned.** Every phase exit requires: its tests passing, no open S0/S1 defects, documentation updated, ADR conflicts resolved or recorded, and the named approvers' sign-off.
2. **Each phase leaves the system operable.** No phase ends in a state where the hotel could not run a day's business on what exists.
3. **Integrity chains complete before breadth.** Financial and inventory integrity chains must work end-to-end before adjacent capability expansion.
4. **Evidence packs.** Each phase produces an evidence pack: test results, reconciliation proofs, drill records, sign-offs, known issues.
5. **Approval authorities** per charter §12; financial gates additionally require Finance; security gates require the Security/Privacy adviser (OQ-033).

## 3. Pilot phases (1–9)

### D1 — Foundation, tenancy and control substrate

| Aspect | Detail |
|---|---|
| Objective | One property, one tenant, enforced scopes; identities, permissions, audit, events and business-date foundations operating |
| Capabilities | CAP-PLT-001…009, 011…014; CAP-PM-001…004, 013; CAP-HRM-001/002; CAP-NAU-001; CAP-ACC-001 |
| Inputs | Accepted blueprint; environment ready; OQ-026 (closed; hosting), OQ-028 (licensing) for topology only |
| Deliverables | Tenant/property/company configuration; authentication with MFA for privileged roles; RBAC with authority limits; append-only audit; outbox/event substrate; effective-dated configuration; numbering series; business-date scaffolding |
| Dependencies | Programme P0 accepted (WP 0.8); transition architecture rulings for platform domain (Programme P4) |
| Risks | RSK-ARCH-001 (ownership ambiguity), RSK-SEC-001 (scope leak), RSK-MIG-002 (tenancy migration) |
| Tests | Permission/scope pack (TO-SEC-001/002); architecture tests (module boundaries, permission declarations, scope injection); audit completeness; configuration effective-dating |
| Exit criteria | Cross-scope denial proven per surface class; self-approval denial proven; audit append-only verified; business-date assertions enforced; no open S0/S1; platform evidence pack signed |

### D2 — Property, rates and inventory definition

| Aspect | Detail |
|---|---|
| Objective | The property's sellable existence: rooms, types, features, zones, rate plans, restrictions, effective-dated pricing |
| Capabilities | CAP-PM-005…012; CAP-RTM-001…012; CAP-AVL-001/002/003/004; CAP-PLT-014 |
| Inputs | OQ-021 (closed; tax/service charge) before tax configuration is final; reference property inventory data (synthetic) |
| Deliverables | Room inventory and numbering; types/features/pools; OOO/OOS control; rate plans, amounts, derives, restrictions; capacity and availability calculation; tax/localisation configuration framework |
| Dependencies | D1 |
| Risks | RSK-FIN-004 (tax correctness), RSK-PROD-002 (hotel reality mismatch) |
| Tests | TO-RTM-001/002 (history immutability, past-date edit impossibility); derived-rate worked examples; restriction precedence reproducibility; availability rebuild/reconciliation; OOO capacity effect (TO-AVL-002) |
| Exit criteria | Rate determinism proven with worked examples; rate history immutable; availability rebuilt and reconciled to zero difference; restrictions reproducible; hotel inventory loaded and accepted by operations representative |

### D3 — Guests and reservations

| Aspect | Detail |
|---|---|
| Objective | The full commercial booking lifecycle with privacy-aware guest records |
| Capabilities | CAP-GST-001…010, 012; CAP-RSV-001…023; CAP-AVL-007/009 |
| Inputs | OQ-012 (closed; cancellation/no-show/deposit policy), OQ-024 (retention) for class-A handling |
| Deliverables | Guest profiles with duplicates/merge/consent; reservation lifecycle with amendments, guarantees, deposits, routing instructions, traces; booking concurrency discipline; waitlist; shared/split/join structures |
| Dependencies | D1–D2 |
| Risks | RSK-GST (if any), RSK-FIN-001 (deposit/cancellation correctness), RSK-DOM-001 |
| Tests | State machine legality suite (SM-RESERVATION/RESERVATION-ROOM); double-sell race (TO-AVL-001); deposit conservation (TO-FOL-004 partial); merge non-destructiveness (INV-GST-1/2); cancellation policy snapshot; guest validation with hotel |
| Exit criteria | Reservation lifecycle tests pass including negatives; concurrency double-booking prevented deterministically; deposit scheduling and cancellation policy snapshot proven; duplicate detection quality accepted; hotel walkthrough signs workflows |

### D4 — Front office

| Aspect | Detail |
|---|---|
| Objective | Governed arrival, stay and departure with service operations |
| Capabilities | CAP-FO-001…010, 012…019 |
| Inputs | OQ-019 (closed; ID capture) for registration evidence; D3 guest data |
| Deliverables | Arrivals and readiness; check-in gates with overrides; registration evidence; in-house console; room moves; extensions; checkout invariant; early/late; service recovery; incidents; shift handover; relocation; house-use/comps; minimal readiness/release state model consumed at check-in (inspection and discrepancy depth completed in D5) |
| Dependencies | D1–D3 |
| Risks | RSK-OPS-001 (adoption), RSK-OPS-002 (business-date discipline) |
| Tests | TO-FO-001 (gates block, overrides audited); TO-FO-002 (checkout invariant); registration immutability; move/do-not-move; extension availability/rate/credit; override audit trail; shift handover completeness |
| Exit criteria | Every check-in gate blocks when failing and every override is authorized and audited; checkout invariant holds under fault injection; front desk performs arrival-to-departure with no parallel paper process for core steps (pilot acceptance criterion) |

### D5 — Housekeeping and maintenance

| Aspect | Detail |
|---|---|
| Objective | Rooms cleaned, inspected and released on real state; maintenance tracked with evidence |
| Capabilities | CAP-HSK-001…012; CAP-MNT-001…008 (pilot depth per OQ-022 (closed)); CAP-PM-009/010 |
| Inputs | OQ-014 (closed; F&B/minibar), OQ-022 (closed; maintenance scope), OQ-034 (closed; inspection/service standards) |
| Deliverables | Room board; task generation/assignment; attendant console (mobile); inspection workflow; discrepancy resolution; productivity; work orders with SLA and evidence; OOO return-to-service verification |
| Dependencies | D4 exchange (front office ↔ housekeeping states) |
| Risks | RSK-OPS-001 (device/rota reality), RSK-DOM-001 |
| Tests | Readiness gate non-bypass; discrepancy daily reconciliation; OOO affects availability; work-order verification before return to service; inspection policy enforcement; mobile offline-tolerant capture |
| Exit criteria | Readiness cannot be bypassed outside recorded policy; discrepancies resolved daily with close-checklist visibility; maintenance return-to-service verified; housekeeping supervisor accepts board workflows |

### D6 — Folio, cashiering and settlement

| Aspect | Detail |
|---|---|
| Objective | Every charge, payment, deposit, adjustment and refund correct, evidenced and reproducible |
| Capabilities | CAP-FOL-001…013; CAP-CSH-001…010; POS/guest communication as scoped by OQ-014 (closed); CAP-INT-004/005 (payments/banking as configured) |
| Inputs | OQ-006 (POS/acquirer details), OQ-011 (closed; invoice form), OQ-012 (closed), OQ-021 (closed) |
| Deliverables | Folios and windows; binding windows to responsibility; charges/taxes; adjustments with authority; payments by method with true states; deposits lifecycle; refunds; cashier sessions and variance; receipts/invoices per OQ-011 (closed) |
| Dependencies | D4–D5; ADR-007/008 confirmed |
| Risks | RSK-FIN-001/003, RSK-SEC-002 |
| Tests | TO-FOL-001…005; TO-CSH-001; payment state transitions; no PAN/CVV storage verified; concurrency on folio posting; settlement matching (manual provider baseline) |
| Exit criteria | Folio balance identity holds under concurrent posting and crash injection; deposit conservation proven; refunds bounded by cleared funds with authority; cashier variance maker–checker enforced; no card data stored (verified) |

### D7 — Night audit, income audit and finance close

| Aspect | Detail |
|---|---|
| Objective | The day closes once, provably, recoverably; revenue and tax post correctly; books reconcile |
| Capabilities | CAP-NAU-002…010; CAP-INA-001…008; CAP-ACC-001…012; CAP-INT-007 |
| Inputs | OQ-002 (Finance Controller), OQ-008 (closed; close window), OQ-011 (closed), OQ-021 (closed); ADR-005/006 confirmed |
| Deliverables | Business-date gate; close checklist and runs; control totals; exception management; certification; posting families with mapping; reconciliation suite; day reports |
| Dependencies | D6; accounting interface binding (role contract, product per Programme P4 transition) |
| Risks | RSK-FIN-001/002/004, RSK-OPS-002 |
| Tests | Golden day end-to-end; TO-NAU-001/002; TO-ACC-001…004; close failure recovery; mapping gap blocks close; zero-revenue direct bill; tax reconciliation |
| Exit criteria | Golden-day fixture passes including negative cases; close is idempotent and resumable; control accounts reconcile to zero on the golden day; restatements versioned; finance signs the close contract |

### D8 — Reporting and operational hardening

| Aspect | Detail |
|---|---|
| Objective | Management, finance and audit run on system reports with drill-down reconciliation |
| Capabilities | CAP-RPT-001…003, 006…008, 011, 012; CAP-PLT-008/010/011; CAP-AVL-008 if applicable |
| Inputs | D7 closed data; real volumes for performance |
| Deliverables | Report catalogue (pilot set); dashboards; drill-down; scheduled delivery; exports with provenance; close-window alerting; observability dashboards |
| Dependencies | D7; NFR budgets |
| Risks | RSK-PERF-001, RSK-SCALE-001 (later) |
| Tests | Report acceptance obligations; scope non-leak on reports/exports; performance budgets P-1…P-9 at production-scale synthetic volume; alert paging (TO-OPS-001) |
| Exit criteria | Every pilot report reconciles and drills down; scope tests pass; performance budgets met at 200-room scale; close-window alerts page correctly; management accepts dashboards |

### D9 — Synthetic migration, role-played UAT and reference release

| Aspect | Detail |
|---|---|
| Objective | Release the pilot-ready reference build with rehearsal-proven migration, rollback and recovery on synthetic data; prepare the First-Property Deployment Gate |
| Capabilities | CAP-PLT-012/016; migration tooling exercised on synthetic guests, reservations, in-house stays, folios, deposits and opening balances; CAP-HSK-010 as migration control |
| Inputs | OQ-010 (support cost); synthetic opening-data set; all prior phases (OQ-025 deferred to the first property) |
| Deliverables | Migration tooling and dry-runs on synthetic data; opening-balance reconciliation; cutover and rollback plans; role-played UAT scripts and execution; runbooks; 24×7 rota; First-Property Deployment Gate checklist; reference-release go/no-go decision |
| Dependencies | D1–D8; hosted reference environment (site connectivity is a first-property item, OQ-038 deferred) |
| Risks | RSK-MIG-001/002, RSK-DEP-001/002/003, RSK-LIC-001, RSK-PROD-003 |
| Tests | Migration dry-runs with reconciliation on synthetic volumes; TO-REL-001/002 (timed restore, worker safety); TO-SEC-001…003 (security pack); scripted UAT with role-played personas (Product Owner as acting operations authority; finance review); degradation drills; manual fallback rehearsal |
| Exit criteria | Synthetic dry-run migration reconciles opening balances, in-house guests and deposits to zero difference; timed restore meets 1 h RTO with post-restore reconciliation; security test pack passed; role-played UAT executed with evidence and accepted by the Product Owner (acting operations authority) and finance; incident/breach tabletop exercised; runbooks exercised; 24×7 rota live; rollback exercised; **reference release approved by Product Owner, Finance, QA and SRE** |

### First-Property Deployment Gate (after D9)

Deployment to a real property is a separate gated step. The gate closes the items deferred from the reference pilot; a real deployment may not start until each item is closed and its evidence recorded:

| Gate item | Source |
|---|---|
| Legal entity and tax identifiers recorded; statutory document templates finalised | OQ-001 |
| Bank roles, POS acquirer/terminals, settlement reports and statement access verified | OQ-004/006/007 |
| Site survey completed — links/failover, power/generator, UPS, equipment room; connectivity design confirmed | OQ-038 |
| Existing-data inventory assessed; migration dry-runs on real data reconcile | OQ-025 |
| Hotel-staff UAT and acceptance executed with real roles; training completed | BR-PILOT-010; OQ-003 |
| Financial configuration (taxes, service charge, routing) signed by the Finance Controller | OQ-002 |
| Operational-readiness review: runbooks, rota, escalation, rollback rehearsal on site | RSK-DEP-001/002 |
| Tax-adviser sign-off: VAT rate and tax points, forfeiture tax treatment, service-charge tax base, fiscalisation/e-invoicing (OQ-029), statutory template particulars | FIN-13; ADR-007/008 |
| Authority-limit defaults and the consolidated refund/payout/waiver table signed by the Finance Controller | FIN-13; SEC-ROLES §4 |

## 4. Enterprise delivery phases (D10–D20)

Sequenced by dependency and value; re-orderable by commercial opportunity without changing content.

| Delivery phase | Theme | Capability scope | Exit criteria |
|---|---|---|---|
| 10 | Groups, corporate, sales, events | CAP-GRP-*; CAP-CRP-006…010; CAP-SAL-*; CAP-AVL-005/006/010 | Group commitment→revenue chain proven end-to-end (block→pickup→master→AR); attrition evidence |
| 11 | Outlets, inventory, F&B depth | CAP-POS-*; CAP-INV-*; CAP-MNT-004/005/006; CAP-HSK-006/011/012; CAP-INA-008; CAP-ACC-007 | Theoretical vs actual consumption reconciles; outlet revenue reconciles to settlement |
| 12 | CRM, communication, loyalty | CAP-CRM-001…005/007; CAP-GST-011; CAP-INT-008 | Consent-respecting lifecycle automation with suppression evidence |
| 13 | Distribution and channels | CAP-INT-002/003; CAP-AVL-008; CAP-RTM-013 (advisory); CAP-HRM-004/005 | Channel parity with no oversell under latency/retry; reconciliation green |
| 14 | Multi-property and chain | CAP-PM-003; CAP-CRP-008; CAP-BI-002/003; CAP-HRM-003; CAP-SAL-010 | Second property onboarded by configuration; consolidation reconciles per property |
| 15 | BI and advanced analytics | CAP-BI-001/004/005; CAP-RPT-004/005/009/010 | Analytical model reconciles to closed books; self-service scope-governed |
| 16 | Integration expansion | CAP-INT-006/010/011/012/013; CAP-BI-006 | Each adapter passes contract/failure/reconciliation packs |
| 17 | Security, compliance, audit hardening | CAP-PLT-015; CAP-INA-008; regulatory obligations from Programme P1 research | Break-glass review discipline; independent audit readiness |
| 18 | Performance, reliability, scale | Cross-cutting | Proven scale targets; failure-mode exercises; capacity model |
| 19 | AI capabilities | CAP-AI-001…010 | Governance layer enforced; evaluation suites pass; human authority preserved |
| 20 | Continuous governance and optimisation | Cross-cutting | Blueprint/current-state gap reviews on cadence; debt and risk registers current |

## 5. Governed proof spikes (optional accelerator)

Before or during delivery phases, throwaway spikes may prove the riskiest mechanisms without committing delivery scope (charter §4): tenancy/scope enforcement, posting idempotency, close recovery, connectivity/degradation behaviour. Spikes produce evidence, not product code; any spike code is discarded or rewritten under the normal ticket process.

## 6. Path to first implementation code

Programme P0 (this blueprint) → Programme P1 research → P2 audit → P3 gap → P4 transition → delivery phases D1–D20, executed as small, tested tickets under Definition of Ready/Done. Proof spikes may run earlier with Product Owner approval.

## 7. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Skeleton roadmap issued with WP 0.1 | SUPERSEDED |
| 1.0 | 2026-09-23 | Full roadmap with per-phase objectives, capabilities, inputs, dependencies, risks, tests and exit criteria; proof-spike provision; path to code | PROPOSED |
| 1.1 | 2026-09-23 | Synthetic reference pilot: D9 renamed and rescoped (synthetic migration, role-played UAT, reference release); First-Property Deployment Gate added; property-dependent inputs deferred | PROPOSED |
| 1.2 | 2026-09-23 | P1: clock and freeze paragraph; First-Property Gate rows for tax-adviser sign-off and FC-signed limits; D9 tabletop criterion; phase naming aligned (TEC-04/05, FIN-13) | PROPOSED |
| 1.3 | 2026-09-23 | P2 sequencing: CAP-AVL-003 moved to D2 (TEC-06); D4/D5 circular dependency fixed (TEC-07) | PROPOSED |
