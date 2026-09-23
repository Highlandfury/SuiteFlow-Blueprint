---
doc-id: GOV-OQ
title: Open Questions Register
status: PROPOSED
version: 0.7
date: 2026-09-23
owner: Product Owner (accountable)
applies-to: all blueprint work
---

# Open Questions Register

Questions that materially affect the blueprint, with the accountable owner and what they block. Questions from the accepted Product Owner decision log keep their original identifiers in brackets. Nothing in this register is decided by assumption: unresolved items stay `OPEN` until an accountable human answers.

On 23 September 2026 the Product Owner directed that the pending questions be answered on an industry-standard/best-practice basis, and adopted all industry-standard answers. The answers are recorded in `industry-standard-answers.md` (GOV-ANSWERS): **26 questions are `CLOSED`** (25 by adoption on 23 Sep 2026, plus OQ-003 by the Product Owner acting as operations authority; decision log §4), **8 remain `OPEN-FACT`** (hotel/provider facts; several deferred to the first-property deployment), **3 remain `OPEN-ADVICE`** (professional confirmation) and **1 remains `OPEN-APPROVAL`** (cost/staffing). On the same date the Product Owner redefined the pilot as a **synthetic reference pilot** (no real property); property-dependent questions are deferred to the First-Property Deployment Gate (inputs register §3.1).

Priority: **B** = blocking for its work package; **I** = important, not blocking; **D** = defer to the phase noted.

Resolution statuses:

| Status | Meaning |
|---|---|
| `OPEN` | Not yet answered or classified |
| `ANSWERED-D` | Industry-standard/best-practice answer recorded in GOV-ANSWERS §4; effective as the design baseline; closed at the acceptance gate by the named approver (charter §12) or by explicit Product Owner adoption |
| `CLOSED` | Answer recorded and adopted (decision log §4); affected documents marked `OQ-nnn (closed)` |
| `OPEN-FACT` | Requires a hotel/provider/named-person fact; GOV-ANSWERS states the required evidence and an interim default |
| `INTERIM ACTING` | Acting role-assistant capacity recorded under delegated Product Owner direction (GOV-APPOINT); human countersignature and appointment remain pending — never a human sign-off |
| `OPEN-ADVICE` | Requires legal/tax/privacy confirmation; GOV-ANSWERS states the recommended position |
| `OPEN-APPROVAL` | Requires a Product Owner cost/staffing decision; GOV-ANSWERS states the recommended model |
| `APPROVED-IN-PRINCIPLE` | Model approved under delegated Product Owner direction (23 Sep 2026); contract signature / spend remains the real Product Owner's execution step |

## 1. Product Owner inputs (from SRC-001 §6)

| ID | Question | Owner | Needed by | Blocks | Priority | Status |
|---|---|---|---|---|---|---|
| OQ-001 | Registered legal entity name and tax identifiers (TIN, VAT registration) [PO-001A] | Product Owner | First property | Financial architecture, invoicing, tax configuration (WP 0.4) | B | OPEN-FACT |
| OQ-002 | Named Finance Controller / pilot Finance representative [PO-001B] | Product Owner | Week 1 | Financial approvals, blueprint sign-off | B | INTERIM ACTING (countersigned 23 Sep 2026; GOV-APPOINT); human appointment pending |
| OQ-003 | Named Hotel Operations representative and hotel acceptance approver [PO-001C] | Product Owner | Week 1 | Workflow validation, UAT acceptance (WP 0.3) | B | CLOSED |
| OQ-004 | Exact role of UBA and Wema Bank (operating, acquirer, transfer, settlement, API) [PAY-001] | Product Owner / Finance | First property | Payments architecture (WP 0.7) | I | OPEN-FACT |
| OQ-005 | Are guest-facing online payment links required? [PAY-002] | Product Owner | Week 1 | Payments architecture scope | I | CLOSED |
| OQ-006 | POS terminal/acquirer and settlement-report details [PAY-003] | Hotel Finance | First property | POS clearing and reconciliation design | B | OPEN-FACT |
| OQ-007 | Bank statement formats and access/integration constraints [PAY-004] | Hotel Finance | First property | Bank reconciliation design | I | OPEN-FACT |
| OQ-008 | Hotel business-day cutoff and expected close deadline in WAT [OPS-001] | Hotel Operations | Week 1 | Night-audit timing, staffing model (WP 0.3/0.4) | B | CLOSED |
| OQ-009 | Confirm or replace the 99.5% service objective [REL-001] | Product Owner | Week 2 | Non-functional requirements (WP 0.7) | I | CLOSED |
| OQ-010 | Approve cost and staffing consequences of 24×7 support and 1-hour RPO/RTO [REL-002] | Product Owner | Week 3 | Deployment/operations architecture (WP 0.7) | I | APPROVED-IN-PRINCIPLE (delegated; real-PO signature pending) |

*Deferrals (PO decision, 23 Sep 2026): OQ-001, OQ-004, OQ-006 and OQ-007 are deferred to the First-Property Deployment Gate; the synthetic reference pilot uses recorded placeholders and simulated providers.*

## 2. Business and policy questions

| ID | Question | Owner | Blocks | Priority | Status |
|---|---|---|---|---|---|
| OQ-011 | Which document is the statutory and customer-facing invoice: the hotel-generated folio invoice or the accounting system's tax invoice? | Finance Controller | Financial architecture (WP 0.4) | B | CLOSED |
| OQ-012 | Deposit, refund, forfeiture, cancellation and no-show policy defaults, and who may override them | Finance / Product Owner | Folio and night-audit state machines (WP 0.3/0.4) | B | CLOSED |
| OQ-013 | Corporate credit policy: default limits, approval authority, suspension and reinstatement rules | Finance Controller | AR and direct-bill design (WP 0.4) | B | CLOSED |
| OQ-014 | Does the pilot operate F&B outlets inside SuiteFlow (restaurant/bar/room service), and if so which ones? External POS integration is deferred by MVP boundary, but outlet revenue and postings remain in scope if outlets exist | Hotel Operations | POS/F&B scope, capability priorities (WP 0.1 pass 2, WP 0.7) | B | CLOSED |
| OQ-015 | Is there a restaurant/kitchen store inventory operation in the pilot, or is inventory control out of pilot scope? | Hotel Operations | Inventory capability priority | I | CLOSED |
| OQ-016 | Revenue-management expectations at pilot: manual rate management only, or forecasting/recommendation features? | Product Owner | Rate and revenue capability priorities | I | CLOSED |
| OQ-017 | Are group/event and conference business material at the pilot hotel (conference center in the property name)? Which group workflows must be pilot-ready? | Hotel Operations | Group/event capability priorities (WP 0.1 pass 2) | B | CLOSED |
| OQ-018 | Channel/distribution requirement at pilot: direct-only, or OTA/channel-manager integration required? | Product Owner | Distribution capability priority | I | CLOSED |
| OQ-019 | Identity capture requirements for guest registration (ID document scanning, retention, Nigeria regulatory expectations) | Hotel Operations / Security | Front-office workflows, privacy design (WP 0.3/0.5) | B | CLOSED |
| OQ-020 | Door-lock integration expectations for the pilot | Hotel Operations | Integration scope | D (Programme P1 research first) | CLOSED |
| OQ-021 | Service charge policy (rate, distribution, accounting treatment) | Finance Controller | Financial architecture (WP 0.4) | B | CLOSED |
| OQ-022 | Maintenance/engineering scope at pilot: work orders, preventive maintenance, or out of pilot scope? | Hotel Operations | Maintenance capability priority, state machines | I | CLOSED |
| OQ-023 | Which employee/user data is authoritative from HRMS, and is HRMS required at all beyond user identity? | Product Owner | Platform identity design (WP 0.2/0.5) | I | CLOSED |
| OQ-024 | Guest data retention periods, especially ID documents and financial records | Security / Legal | Data model, privacy controls (WP 0.2/0.5) | I | OPEN-ADVICE |
| OQ-025 | Existing data to migrate: incumbent PMS, spreadsheets, or paper records; volume, quality and history depth | Hotel Operations / Product Owner | Migration architecture (WP 0.7), roadmap | B | OPEN-FACT |

*Deferral (PO decision, 23 Sep 2026): OQ-025 is deferred to the First-Property Deployment Gate; the reference pilot uses synthetic data only.*

## 3. Architecture and platform questions

| ID | Question | Owner | Blocks | Priority | Status |
|---|---|---|---|---|---|
| OQ-026 | Target cloud region, data residency and hosting constraints for guest and financial data | Security / Product Owner | Deployment architecture (WP 0.7) | I | CLOSED |
| OQ-027 | When is a second property expected, and under what operating model (same entity, management contract, franchise)? | Product Owner | Multi-property/tenancy design depth (WP 0.2) | I | CLOSED |
| OQ-028 | Licensing position for upstream open-source dependencies and commercial distribution | Legal counsel | Release and distribution architecture | I | OPEN-ADVICE |
| OQ-029 | Fiscalisation / e-invoicing requirements in Nigeria that would require integration with tax authority systems | Finance / Legal | Integration architecture, invoicing | I | OPEN-ADVICE |
| OQ-030 | Do payment gateway webhooks and bank interfaces provide idempotent, referenceable confirmations suitable for automated reconciliation, or is manual reconciliation the pilot baseline? | Platform / Finance | Payments and reconciliation design (WP 0.7) | I | CLOSED |
| OQ-031 | AI capability priorities and acceptability for hotel staff at pilot; which AI assistance, if any, is desired at launch? | Product Owner | AI capability priorities (pass 2) | D (Programme P20 target) | CLOSED |
| OQ-032 | Accessibility and language requirements for staff facing interfaces (English-only assumption?) | Product Owner | UX architecture (WP 0.6) | D (WP 0.6) | CLOSED |
| OQ-034 | Housekeeping service standards: is formal supervisor inspection required before sale (all rooms or VIP/rush only), stayover service frequency, and does the property operate minibar/in-room consumption? | Hotel Operations | Housekeeping workflows and rules (BR-HSK-002/005) | I | CLOSED |
| OQ-035 | Pricing policy details: child age bound for free stays, extra-person and sharing pricing, early check-in / late-checkout charges, early-departure rules | Product Owner / Hotel Operations | Rate and front-office rules (BR-RTM-005, BR-FO-004/005) | I | CLOSED |
| OQ-036 | Comp and house-use policy: who may authorise, limits, and statistical treatment (are comps counted in occupancy? is house use excluded from ADR?) | Product Owner / Hotel Operations | Statistics and reporting rules (BR-RPT-003, BR-FOL-005) | I | CLOSED |
| OQ-037 | Cash handling policy: house-bank size, cashier variance tolerance, payout limits, deposit-to-safe rules | Finance | Cashiering rules (BR-CSH-002/005) | I | CLOSED |
| OQ-038 | Connectivity and power reality at the property: WAN links and redundancy, power stability/UPS coverage, appetite for on-site equipment. Directly determines the offline/degraded-mode design (a front desk that stops when the internet drops is not viable) | Product Owner / Platform | Offline strategy, deployment architecture (WP 0.7) | I | OPEN-FACT |
| OQ-033 | Named Technical Lead and Security/Privacy adviser for blueprint approval gates; no such named roles exist yet | Product Owner | Blueprint ACCEPTED status (charter §12) | I | INTERIM ACTING (countersigned 23 Sep 2026; GOV-APPOINT); human appointments pending |

*Deferral (PO decision, 23 Sep 2026): OQ-038 is deferred to the First-Property Deployment Gate; the reference pilot has no site and runs on a hosted environment with the mandated connectivity/UPS controls assumed at deployment.*

## 4. Decision log — adopted answers and closures (23 September 2026)

The Product Owner adopted all `ANSWERED-D` answers in full on 23 September 2026 and took the operations-authority appointment for OQ-003. Each question below is closed by recording its adopted answer or appointment; the full answer text, rationale and evidence requirements are in `industry-standard-answers.md` (GOV-ANSWERS). In the affected documents, a closed question is marked `OQ-nnn (closed)`; open questions keep their `OPEN-FACT` / `OPEN-ADVICE` / `OPEN-APPROVAL` status until their evidence, advice or approval exists.

| ID | Adopted answer (summary) | Answer ref | Authority | Date | Affected documents |
|---|---|---|---|---|---|
| OQ-005 | Online payment links: enterprise target yes; pilot at most one provider path if required | GOV-ANSWERS §4.1 | Product Owner | 2026-09-23 | Payments/integration scope |
| OQ-008 | Business date rolls 02:00 WAT; audit 02:00–03:30; certified close by 04:00; configurable | GOV-ANSWERS §4.1 | Product Owner | 2026-09-23 | BR-NAU rules; NFR P-6/P-7; night-audit workflows; ADR-006 |
| OQ-009 | 99.5% pilot availability (maintenance excluded); 99.9% enterprise target | GOV-ANSWERS §4.1 | Product Owner | 2026-09-23 | NFR A-1; deployment architecture |
| OQ-011 | SuiteFlow hotel document is the customer-facing invoice; statutory/fiscal document derives and links (ADR-008) | GOV-ANSWERS §4.2 | Product Owner | 2026-09-23 | ADR-008; financial architecture; document templates; reporting |
| OQ-012 | 48 h free cancellation; one-night penalty inside 48 h; no-show = first night + tax; deposits 1 night / 100% groups; refunds to original; logged overrides | GOV-ANSWERS §4.2 | Product Owner | 2026-09-23 | BR-FOL-009/010/011; reservation/folio state machines; role matrix |
| OQ-013 | Credit application + references; limit at onboarding; 30-day terms; suspension at 60 days overdue; Finance Controller reinstates | GOV-ANSWERS §4.2 | Product Owner | 2026-09-23 | AR/direct-bill design; corporate workflows |
| OQ-014 | Restaurant and bar (room service if operated) in SuiteFlow; external POS integration stays deferred | GOV-ANSWERS §4.2 | Product Owner | 2026-09-23 | POS/F&B scope; posting families |
| OQ-015 | Light stock control at pilot; full recipe costing later | GOV-ANSWERS §4.2 | Product Owner | 2026-09-23 | Inventory capability priority |
| OQ-016 | Manual rate management at pilot; RMS/forecasting later | GOV-ANSWERS §4.2 | Product Owner | 2026-09-23 | Rate/revenue priorities |
| OQ-017 | Groups pilot-critical: blocks, cutoffs, rooming lists, master folio/routing, deposits, group statements, basic conference space booking | GOV-ANSWERS §4.2 | Product Owner | 2026-09-23 | GRP/SAL priorities; group workflows |
| OQ-018 | Direct + corporate at pilot; manual OTA handling if unavoidable; channel manager later | GOV-ANSWERS §4.2 | Product Owner | 2026-09-23 | Distribution priority |
| OQ-019 | ID type/number for all adults; scan only if legally required; never NIN images; Class-A handling | GOV-ANSWERS §4.2 | Product Owner | 2026-09-23 | BR-FO-001; security model; INT-011 |
| OQ-020 | Manual keys at pilot; lock research Programme P1; integration later | GOV-ANSWERS §4.2 | Product Owner | 2026-09-23 | INT-010; front-office workflows |
| OQ-021 | 10% F&B service charge as staff-distribution liability, never revenue; tax treatment per adviser | GOV-ANSWERS §4.2 | Product Owner | 2026-09-23 | Financial architecture; tax configuration; reporting |
| OQ-022 | Work orders + light preventive maintenance at pilot with OOO coordination | GOV-ANSWERS §4.2 | Product Owner | 2026-09-23 | MNT priority; maintenance workflows |
| OQ-023 | HRMS/directory owns employee identity; SuiteFlow consumes an identity projection; no payroll | GOV-ANSWERS §4.2 | Product Owner | 2026-09-23 | Identity design; security model |
| OQ-026 | In-country (Nigeria) hosting preferred; NDPA transfer controls for any offshore processing | GOV-ANSWERS §4.3 | Product Owner | 2026-09-23 | Deployment architecture; security model; vendor selection |
| OQ-027 | Multi-property is configuration, not re-architecture; timing is commercial | GOV-ANSWERS §4.3 | Product Owner | 2026-09-23 | Tenancy design |
| OQ-030 | Automate gateway webhooks; bank confirmation manual/semi-automated until evidence supports more; reconciliation is the control of record | GOV-ANSWERS §4.3 | Product Owner | 2026-09-23 | ADR-011; integration architecture; income audit |
| OQ-031 | No AI at pilot; candidate priorities for enterprise phases | GOV-ANSWERS §4.3 | Product Owner | 2026-09-23 | AI architecture/roadmap |
| OQ-032 | English-only + WCAG 2.2 AA core flows at pilot; localisation later | GOV-ANSWERS §4.3 | Product Owner | 2026-09-23 | UX architecture; QA strategy |
| OQ-034 | VIP/arrival-critical inspection + ≥20% spot checks; daily stayover service; minibar only if operated, with posting and departure check | GOV-ANSWERS §4.3 | Product Owner | 2026-09-23 | BR-HSK-002/005; housekeeping workflows |
| OQ-035 | Children ≤5 free; 6–12 sharing at 50%; early/late check-in/out banded; amounts configurable | GOV-ANSWERS §4.3 | Product Owner | 2026-09-23 | BR-RTM-005; BR-FO-004/005 |
| OQ-036 | Comps approved/reason-coded, ≤2% target, counted in occupancy at value; house use excluded from occupancy/ADR | GOV-ANSWERS §4.3 | Product Owner | 2026-09-23 | BR-RPT-003; BR-FOL-005; reporting |
| OQ-037 | Float ₦100,000 default; zero tolerance with investigation >0.5%; payouts ≤₦50,000 FOM; daily banking; dual custody | GOV-ANSWERS §4.3 | Product Owner | 2026-09-23 | BR-CSH-002/005; cashiering; income audit |
| OQ-003 | Hotel Operations representative and acceptance approver: Product Owner acts as interim operations authority until a real property is secured; real hotel staff validate at first-property deployment | Inputs register §3.1 (BR-PILOT-008) | Product Owner (appointment) | 2026-09-23 | Personas/workflow validation; UAT acceptance gates; charter §12 |

## 5. Governance

Every question above is closed by recording: the answer, the answering authority, the date, and the affected documents. Closed questions remain in the register with status `CLOSED` and a link to the decision record; they are never deleted.

For the industry-standard answers of 23 September 2026 (GOV-ANSWERS): an `ANSWERED-D` item closes when (a) the Product Owner formally adopts it, or (b) the named approver per charter §12 signs the affected document set; the decision record then cites the adopted answer and the `[OQ-nnn]` markers in the affected documents are updated through change control. `OPEN-FACT` items close when the required evidence is recorded; `OPEN-ADVICE` items when the adviser's position is minuted; `OPEN-APPROVAL` when the Product Owner approves the cost/staffing model. The 26 items in §4 were closed by formal Product Owner adoption or appointment on 23 September 2026; the named-approver sign-offs for the affected document sets remain part of blueprint acceptance (charter §12). Adopted answers that retain an advice dependency (OQ-019 legal confirmation, OQ-021 tax treatment, OQ-024/028/029) or an evidence dependency (OQ-004/006/007 providers, OQ-001 entity, OQ-002 appointment, OQ-025 data inventory, OQ-038 site survey) are noted in GOV-ANSWERS; the property-dependent evidence items are deferred to the First-Property Deployment Gate.

## 6. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial register seeded from SRC-001 §6 and blueprint analysis | PROPOSED |
| 0.2 | 2026-09-23 | Answers classified against GOV-ANSWERS: 25 ANSWERED-D, 9 OPEN-FACT, 3 OPEN-ADVICE, 1 OPEN-APPROVAL; resolution-status legend added | PROPOSED |
| 0.3 | 2026-09-23 | 25 questions closed by Product Owner adoption of the industry-standard answers; decision log added; statuses flipped to CLOSED | PROPOSED |
| 0.4 | 2026-09-23 | Synthetic reference pilot: OQ-003 closed (Product Owner acting operations authority); OQ-001/004/006/007/025/038 deferred to the First-Property Deployment Gate | PROPOSED |
| 0.5 | 2026-09-23 | OQ-010 approved in principle under delegated PO direction; OQ-002/OQ-033 interim arrangements recorded with expiry at the P0 sitting | PROPOSED |
| 0.6 | 2026-09-23 | OQ-002/OQ-033 interim acting capacities recorded (role assistants; countersignature pending); INTERIM ACTING status added | PROPOSED |
| 0.7 | 2026-09-23 | Interim instruments countersigned by recorded Product Owner direction (A-01 complete); human appointments remain pending | PROPOSED |
