---
doc-id: GOV-OQ
title: Open Questions Register
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Product Owner (accountable)
applies-to: all blueprint work
---

# Open Questions Register

Questions that materially affect the blueprint, with the accountable owner and what they block. Questions from the accepted Product Owner decision log keep their original identifiers in brackets. Nothing in this register is decided by assumption: unresolved items stay `OPEN` until an accountable human answers.

Priority: **B** = blocking for its work package; **I** = important, not blocking; **D** = defer to the phase noted.

## 1. Product Owner inputs (from SRC-001 §6)

| ID | Question | Owner | Needed by | Blocks | Priority | Status |
|---|---|---|---|---|---|---|
| OQ-001 | Registered legal entity name and tax identifiers (TIN, VAT registration) [PO-001A] | Product Owner | Week 1 | Financial architecture, invoicing, tax configuration (WP 0.4) | B | OPEN |
| OQ-002 | Named Finance Controller / pilot Finance representative [PO-001B] | Product Owner | Week 1 | Financial approvals, blueprint sign-off | B | OPEN |
| OQ-003 | Named Hotel Operations representative and hotel acceptance approver [PO-001C] | Product Owner | Week 1 | Workflow validation, UAT acceptance (WP 0.3) | B | OPEN |
| OQ-004 | Exact role of UBA and Wema Bank (operating, acquirer, transfer, settlement, API) [PAY-001] | Product Owner / Finance | Week 1 | Payments architecture (WP 0.7) | I | OPEN |
| OQ-005 | Are guest-facing online payment links required? [PAY-002] | Product Owner | Week 1 | Payments architecture scope | I | OPEN |
| OQ-006 | POS terminal/acquirer and settlement-report details [PAY-003] | Hotel Finance | Week 1 | POS clearing and reconciliation design | B | OPEN |
| OQ-007 | Bank statement formats and access/integration constraints [PAY-004] | Hotel Finance | Week 1–2 | Bank reconciliation design | I | OPEN |
| OQ-008 | Hotel business-day cutoff and expected close deadline in WAT [OPS-001] | Hotel Operations | Week 1 | Night-audit timing, staffing model (WP 0.3/0.4) | B | OPEN |
| OQ-009 | Confirm or replace the 99.5% service objective [REL-001] | Product Owner | Week 2 | Non-functional requirements (WP 0.7) | I | OPEN |
| OQ-010 | Approve cost and staffing consequences of 24×7 support and 1-hour RPO/RTO [REL-002] | Product Owner | Week 3 | Deployment/operations architecture (WP 0.7) | I | OPEN |

## 2. Business and policy questions

| ID | Question | Owner | Blocks | Priority | Status |
|---|---|---|---|---|---|
| OQ-011 | Which document is the statutory and customer-facing invoice: the hotel-generated folio invoice or the accounting system's tax invoice? | Finance Controller | Financial architecture (WP 0.4) | B | OPEN |
| OQ-012 | Deposit, refund, forfeiture, cancellation and no-show policy defaults, and who may override them | Finance / Product Owner | Folio and night-audit state machines (WP 0.3/0.4) | B | OPEN |
| OQ-013 | Corporate credit policy: default limits, approval authority, suspension and reinstatement rules | Finance Controller | AR and direct-bill design (WP 0.4) | B | OPEN |
| OQ-014 | Does the pilot operate F&B outlets inside SuiteFlow (restaurant/bar/room service), and if so which ones? External POS integration is deferred by MVP boundary, but outlet revenue and postings remain in scope if outlets exist | Hotel Operations | POS/F&B scope, capability priorities (WP 0.1 pass 2, WP 0.7) | B | OPEN |
| OQ-015 | Is there a restaurant/kitchen store inventory operation in the pilot, or is inventory control out of pilot scope? | Hotel Operations | Inventory capability priority | I | OPEN |
| OQ-016 | Revenue-management expectations at pilot: manual rate management only, or forecasting/recommendation features? | Product Owner | Rate and revenue capability priorities | I | OPEN |
| OQ-017 | Are group/event and conference business material at the pilot hotel (conference center in the property name)? Which group workflows must be pilot-ready? | Hotel Operations | Group/event capability priorities (WP 0.1 pass 2) | B | OPEN |
| OQ-018 | Channel/distribution requirement at pilot: direct-only, or OTA/channel-manager integration required? | Product Owner | Distribution capability priority | I | OPEN |
| OQ-019 | Identity capture requirements for guest registration (ID document scanning, retention, Nigeria regulatory expectations) | Hotel Operations / Security | Front-office workflows, privacy design (WP 0.3/0.5) | B | OPEN |
| OQ-020 | Door-lock integration expectations for the pilot | Hotel Operations | Integration scope | D (Phase 1 research first) | OPEN |
| OQ-021 | Service charge policy (rate, distribution, accounting treatment) | Finance Controller | Financial architecture (WP 0.4) | B | OPEN |
| OQ-022 | Maintenance/engineering scope at pilot: work orders, preventive maintenance, or out of pilot scope? | Hotel Operations | Maintenance capability priority, state machines | I | OPEN |
| OQ-023 | Which employee/user data is authoritative from HRMS, and is HRMS required at all beyond user identity? | Product Owner | Platform identity design (WP 0.2/0.5) | I | OPEN |
| OQ-024 | Guest data retention periods, especially ID documents and financial records | Security / Legal | Data model, privacy controls (WP 0.2/0.5) | I | OPEN |
| OQ-025 | Existing data to migrate: incumbent PMS, spreadsheets, or paper records; volume, quality and history depth | Hotel Operations / Product Owner | Migration architecture (WP 0.7), roadmap | B | OPEN |

## 3. Architecture and platform questions

| ID | Question | Owner | Blocks | Priority | Status |
|---|---|---|---|---|---|
| OQ-026 | Target cloud region, data residency and hosting constraints for guest and financial data | Security / Product Owner | Deployment architecture (WP 0.7) | I | OPEN |
| OQ-027 | When is a second property expected, and under what operating model (same entity, management contract, franchise)? | Product Owner | Multi-property/tenancy design depth (WP 0.2) | I | OPEN |
| OQ-028 | Licensing position for upstream open-source dependencies and commercial distribution | Legal counsel | Release and distribution architecture | I | OPEN |
| OQ-029 | Fiscalisation / e-invoicing requirements in Nigeria that would require integration with tax authority systems | Finance / Legal | Integration architecture, invoicing | I | OPEN |
| OQ-030 | Do payment gateway webhooks and bank interfaces provide idempotent, referenceable confirmations suitable for automated reconciliation, or is manual reconciliation the pilot baseline? | Platform / Finance | Payments and reconciliation design (WP 0.7) | I | OPEN |
| OQ-031 | AI capability priorities and acceptability for hotel staff at pilot; which AI assistance, if any, is desired at launch? | Product Owner | AI capability priorities (pass 2) | D (Phase 20 target) | OPEN |
| OQ-032 | Accessibility and language requirements for staff facing interfaces (English-only assumption?) | Product Owner | UX architecture (WP 0.6) | D (WP 0.6) | OPEN |
| OQ-034 | Housekeeping service standards: is formal supervisor inspection required before sale (all rooms or VIP/rush only), stayover service frequency, and does the property operate minibar/in-room consumption? | Hotel Operations | Housekeeping workflows and rules (BR-HSK-002/005) | I | OPEN |
| OQ-035 | Pricing policy details: child age bound for free stays, extra-person and sharing pricing, early check-in / late-checkout charges, early-departure rules | Product Owner / Hotel Operations | Rate and front-office rules (BR-RTM-005, BR-FO-004/005) | I | OPEN |
| OQ-036 | Comp and house-use policy: who may authorise, limits, and statistical treatment (are comps counted in occupancy? is house use excluded from ADR?) | Product Owner / Hotel Operations | Statistics and reporting rules (BR-RPT-003, BR-FOL-005) | I | OPEN |
| OQ-037 | Cash handling policy: house-bank size, cashier variance tolerance, payout limits, deposit-to-safe rules | Finance | Cashiering rules (BR-CSH-002/005) | I | OPEN |
| OQ-038 | Connectivity and power reality at the property: WAN links and redundancy, power stability/UPS coverage, appetite for on-site equipment. Directly determines the offline/degraded-mode design (a front desk that stops when the internet drops is not viable) | Product Owner / Platform | Offline strategy, deployment architecture (WP 0.7) | I | OPEN |
| OQ-033 | Named Technical Lead and Security/Privacy adviser for blueprint approval gates; no such named roles exist yet | Product Owner | Blueprint ACCEPTED status (charter §12) | I | OPEN |

## 4. Governance

Every question above is closed by recording: the answer, the answering authority, the date, and the affected documents. Closed questions remain in the register with status `CLOSED` and a link to the decision record; they are never deleted.

## 5. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial register seeded from SRC-001 §6 and blueprint analysis | PROPOSED |
