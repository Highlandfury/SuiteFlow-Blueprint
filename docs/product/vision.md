---
doc-id: PROD-VISION
title: Product Vision
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Product Owner (accountable)
applies-to: full enterprise target
depends-on: [GOV-CHARTER, GOV-INPUTS]
---

# Product Vision

## 1. Product statement

**SuiteFlow is an enterprise hotel operating platform: it runs the commercial, operational and financial life of a hotel, from the moment a room is offered for sale to the moment the day's revenue is banked and audited.**

SuiteFlow is not a booking calendar with invoicing attached, and not an accounting system with rooms bolted on. It is an operating platform: it encodes how a hotel actually works — availability, rate, reservation, arrival, stay, departure, charge, settlement, close, audit, reporting — with the discipline an enterprise demands: deterministic calculations, server-enforced control, complete audit trails, and financial records that reconcile to the naira.

The first delivery is a single 200-room property in Lagos. The product that is designed and built is the enterprise platform; the pilot is its first proving ground, not its boundary.

## 2. The problem

Independent and group hotels in Nigeria and comparable markets operate on a patchwork of paper, spreadsheets, disconnected POS terminals, bank SMS messages and on-premise legacy systems. The consequences are familiar and expensive:

- **Revenue leakage** — unposted charges, unclosed cashier shifts, unreconciled card and transfer settlements, comps and voids without authority.
- **Financial opacity** — the general manager cannot see occupancy, rate, revenue or receivables without a manual end-of-day ritual; the finance controller cannot trust the ledger without rechecking the operational system.
- **Operational friction** — reservations, front desk, housekeeping and maintenance coordinate by phone call and memory; room status is stale; VIP and allergy information travels on sticky notes.
- **No defensible audit trail** — when money is missing, no one can reconstruct who did what, when, and with what authority.
- **Commercial rigidity** — rates, packages, groups and corporate contracts live in the manager's head; corporate credit and AR are tracked on invoices in a drawer.

Enterprise PMS platforms exist, but they are priced, provisioned and operated for large chains. The market gap is an operationally serious platform that a mid-size, professionally-run independent property can adopt without an enterprise IT department — and that a growing group can standardise on across properties without re-implementation.

## 3. Target customers

### 3.1 Initial market (design centre)

| Dimension | Target |
|---|---|
| Segment | Independent upscale/business hotels and suites, conference hotels, city hotels |
| Size | 40–400 rooms; multi-outlet (restaurant, bar, conference/banquet) common |
| Markets | Nigeria first (regulatory, payment and banking reality designed in), then comparable West African markets |
| Operation | Owner-managed or professionally managed; finance function on-site; 24-hour front desk |
| Existing IT | Low-to-moderate maturity; unreliable internet acceptable (graceful degradation required) |

### 3.2 Expansion direction

| Stage | Customer | Added platform capability |
|---|---|---|
| 1 | Single property | Full PMS operation as delivered to the pilot |
| 2 | Small group (2–10 properties, one entity or manager) | Central profiles, cross-property reporting, group rate and corporate account configuration, per-property accounting |
| 3 | Chain / management company | Property groups, multiple legal entities, central reservations distribution, chain analytics, standardised configuration management |

Single-property customers must not be burdened with chain complexity they do not use; chain customers must not require re-implementation to be onboarded. That balance is a first-class design requirement, not a future patch.

## 4. Product principles

1. **Operational realism first.** Every workflow is designed as the hotel performs it, not as a database diagram suggests. If a workflow cannot be explained to a front desk agent in plain language, it is wrong.
2. **Financial integrity is non-negotiable.** Revenue is recognised once, on the correct business day, to the correct account, with the correct tax. Every balance is reconcilable. Money is never created or destroyed by a software defect.
3. **Server decides, UI proposes.** Availability, rate, tax, balance and permission decisions are computed and enforced server-side. Screens present; they never determine.
4. **Business date is the spine.** Every posting, report and control links to the hotel's operating day, not to a server timestamp.
5. **Everyone sees only what they should.** Property, company and role scope are enforced for every read and write — including reports, exports and AI tools.
6. **Nothing happens without evidence.** Sensitive actions carry actor, authority, reason, before/after state and correlation identifiers.
7. **Configure, don't customise.** Property-level behaviour (policies, taxes, timings, routing, numbering) is configuration with effective dates, not per-hotel code.
8. **Provider-neutral boundaries.** OTAs, payment acquirers, banks, door locks, channel managers and fiscal systems sit behind adapters. No provider's quirks leak into the domain.
9. **AI proposes; governed services dispose.** AI may summarise, explain, search, classify, draft and recommend. It never determines price, availability, tax, balance, payment, inventory, assignment or permission.
10. **The target survives the present.** Current implementations are evidence, not authority. The design is measured against hospitality, accounting, security and operational truth.

## 5. Capability pillars

The full capability universe with identifiers, priorities and ownership is defined in `capability-map.md` (WP 0.1 pass 2). The pillars are:

1. **Property and inventory** — properties, buildings, rooms, room types, features, OOO/OOS, pseudo rooms, pools.
2. **Reservations and availability** — lifecycle, amendments, guarantees, waitlist, blocks, allotments, restrictions, overbooking policy.
3. **Rates and revenue controls** — rate plans, derives, packages, negotiated rates, restrictions, rate history, effectivity.
4. **Front office** — arrivals, check-in, in-house service, moves, extensions, checkout, registration evidence, requests, traces.
5. **Guest and CRM foundation** — privacy-aware profiles, preferences, relationships, consent, duplicates, loyalty hooks.
6. **Groups, corporate and sales** — blocks, rooming lists, master accounts, negotiated terms, credit and AR origination, events.
7. **Housekeeping and maintenance** — room board, tasks, inspections, discrepancies, work orders, preventive maintenance.
8. **Folio, cashiering and settlement** — folios, windows, routing, transfers, adjustments, payments for cash/POS/transfer/cheque, deposits, refunds, cashier sessions.
9. **Night audit, income audit and accounting integration** — business-day close, reconciliation, revenue and tax posting, AR transfer, accounting reconciliation.
10. **POS / F&B** — outlet sales, room posting, outlet settlement.
11. **Reporting and analytics** — operational, financial, revenue, audit reporting; dashboards; forecasts.
12. **Platform** — tenancy, identities, RBAC, configuration, audit, notifications, events, integration, observability, recovery.
13. **AI assistance** — governed assistants for front desk, reservations, housekeeping, maintenance, finance and management.

## 6. Enterprise direction: what "enterprise" means here

- **Single property is the easy case, not the only case.** Tenancy, numbering, configuration and reporting are designed so a second property is a configuration and data event, not a re-architecture.
- **Financial consolidation without financial ambiguity.** Each property closes its own day and keeps its own books; group reporting consolidates; control accounts reconcile per property and per entity.
- **Upgradeability.** The platform is delivered as a versioned application with migrations; customer data and configuration survive upgrades without bespoke forks.
- **Operability.** The system is observable, recoverable to a defined point and time, and supportable at 24×7 for a production hotel.
- **Governance.** Architecture decisions, requirement traceability and change control exist as artefacts, not as tribal memory.

## 7. AI positioning

AI in SuiteFlow is an operational amplifier under governance:

- **Advisory by default** — summarise the day, explain a balance, find a reservation, draft a guest message, classify a maintenance request, flag an anomaly.
- **Governed when acting** — any state change passes through the same authorized domain services a human would use, with the same validation and audit.
- **Never authoritative** — no AI output defines a price, an availability count, a tax, a payment amount, an accounting total, or a permission.

AI capability priorities are governed by OQ-031 closed (no AI at pilot) and are architected in WP 0.7, not assumed into pilot scope.

## 8. Benchmark posture

Publicly documented enterprise PMS capabilities (including Oracle Hospitality OPERA Cloud documentation) are used as a **functional benchmark**: to understand which problems serious hotel operations must solve, which workflows and controls are expected, and what data and reporting they imply. Benchmark material is used to derive requirements only. No proprietary implementation, source code, internal design or protected asset is copied, and no benchmark capability enters the blueprint without being re-justified against SuiteFlow's own principles and the pilot's operational reality.

## 9. Non-goals

- SuiteFlow is not an online travel agency and will not operate a consumer marketplace.
- SuiteFlow is not a general-purpose ERP; accounting depth beyond hospitality-operations needs is integrated, not re-implemented.
- SuiteFlow will not store card PAN/CVV data. Card processing is handled by acquirers under their own compliance regimes.
- SuiteFlow will not replace human authority: credit approvals, comps, refunds, forfeitures and overrides are human decisions with recorded accountability.
- SuiteFlow is not a revenue-management algorithm vendor; recommendations may be supported, but pricing authority remains human (see OQ-016 (closed) for pilot expectations).

## 10. Success measures

### Pilot (first property)

| Measure | Target |
|---|---|
| Financial integrity | Night audit closes with zero unreconciled differences on the golden-day acceptance suite; every control account reconciles daily |
| Cash control | 100% of cashier sessions closed with counted cash and resolved variances within policy |
| Settlement control | ≥95% of POS batches matched to acquirer settlement within the agreed clearance window; all exceptions visible and owned |
| Operational adoption | Front desk performs arrivals, moves, extensions and checkout end-to-end in SuiteFlow with no parallel paper process for those steps |
| Reliability | 99.5% availability objective (OQ-009 closed) with proven 1-hour RPO/RTO drill |
| Reporting | Daily operating and financial reports produced from the system, not from spreadsheets |

### Enterprise (multi-property)

| Measure | Target |
|---|---|
| Onboarding effort | A second property onboards by configuration and data (no code change) |
| Consolidation | Group reporting consolidates closed property data with per-property reconciliation intact |
| Upgrade safety | Version upgrades apply without bespoke modification and with a rehearsed rollback |

## 11. Uncertainty

Target market sizing, the problem statement in section 2, competitor capability claims and Nigerian regulatory specifics are **ASSUMED/UNVERIFIED** planning judgments drawn from the programme's operating context, pending Phase 1 research. They describe a real market gap as understood today; they are not yet backed by cited market evidence.

Success-measure thresholds in section 10 are **PROPOSED planning targets** except where they restate a confirmed baseline requirement (RPO/RTO, 24×7 support). They require Product Owner confirmation before they become ACCEPTED.

## 12. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial vision issued with WP 0.1 pass 1 | PROPOSED |
