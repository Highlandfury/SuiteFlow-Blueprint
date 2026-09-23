---
doc-id: GOV-ANSWERS
title: Open Question Answers — Industry-Standard and Best-Practice Positions
status: PROPOSED
version: 0.3
date: 2026-09-23
owner: Principal Architect (drafted at Product Owner direction); Product Owner (adoption)
applies-to: resolution of the Open Questions Register (GOV-OQ)
depends-on: [GOV-OQ, GOV-CHARTER, GOV-INPUTS]
---

# Open Question Answers — Industry-Standard and Best-Practice Positions

## 1. Purpose and authority

On 23 September 2026 the Product Owner directed that the pending questions in the Open Questions Register (`open-questions.md`) be answered on the basis of **industry standard and best practice**, so that design work is no longer blocked by questions that common practice can settle.

This document records those answers. It does two things:

1. Where industry practice determines a concrete answer, it gives the answer in an adoptable form (**ANSWERED-D**).
2. Where only the hotel, a named person, a provider or professional advice can answer, it states the **evidence required** and a recommended interim default so design continues without inventing facts (**OPEN-FACT**, **OPEN-ADVICE**, **OPEN-APPROVAL**).

**Authority and governance.** This document does not fabricate facts, does not name people, and does not close questions that require an accountable human. On **23 September 2026 the Product Owner adopted all `ANSWERED-D` answers in full**; the 25 questions are `CLOSED` in the register with a decision-log entry (GOV-OQ §4), and the affected documents carry the marker `OQ-nnn (closed)`. The named-approver sign-offs for the affected document sets remain part of blueprint acceptance (charter §12). `OPEN-FACT` and `OPEN-ADVICE` questions remain open until the evidence or advice exists. All statements are PROPOSED and carry the confidence labels of charter §6: industry-practice statements are ASSUMED unless evidenced; jurisdiction-specific legal and tax statements are **UNVERIFIED** pending Phase 1 primary-source research and professional advice, and are design positions — not compliance claims.

## 2. How to read the answers

| Class | Meaning | Effect |
|---|---|---|
| **ANSWERED-D** | Industry standard or best practice determines a concrete answer | Usable now as the design baseline; formal closure at the acceptance gate or by explicit Product Owner adoption |
| **OPEN-FACT** | Only the hotel, a provider or a named person can supply the fact | Recommended interim default given; question stays open until the evidence is recorded |
| **OPEN-ADVICE** | Needs legal, tax or privacy confirmation | Recommended position given; remains open until adviser sign-off |
| **OPEN-APPROVAL** | Needs a cost or staffing decision by the Product Owner | Recommendation with a staffing/cost model given; cannot be granted by this document |

Each answer states: **Answer**, **Basis**, **Still needed**, **Affects**.

## 3. Summary of outcomes

25 questions were answered on an industry-standard basis and adopted by the Product Owner on 23 September 2026 (now `CLOSED` in the register); OQ-003 closed by the Product Owner's appointment as operations authority; 8 need facts (six deferred to the first-property deployment); 3 need professional advice; 1 needs the Product Owner's cost approval.

| ID | Class | Answer in one line |
|---|---|---|
| OQ-001 | OPEN-FACT | Legal identity cannot be invented: CAC/TIN/VAT documents required; identity is parameterised; no statutory document issues until recorded |
| OQ-002 | OPEN-FACT | Appoint a named Finance Controller + deputy; interim financial approvals stay with the Product Owner |
| OQ-003 | CLOSED | Product Owner acts as operations authority and acceptance approver until a real property is secured (appointment 23 Sep 2026) |
| OQ-004 | OPEN-FACT | One primary collections bank + acquirer chosen on evidence; virtual accounts for transfers; two-bank contingency acceptable |
| OQ-005 | ANSWERED-D | Yes at enterprise target; pilot keeps at most one provider path if prepayment is needed |
| OQ-006 | OPEN-FACT | Provide acquirer/terminal/settlement-report facts; design reconciles terminal → acquirer → bank daily from a clearing account |
| OQ-007 | OPEN-FACT | Request CSV/Excel/MT940/API statements from both banks; manual import fallback with dual review |
| OQ-008 | ANSWERED-D | 02:00 WAT cutoff; audit 02:00–03:30; certified close by 04:00; property-configurable |
| OQ-009 | ANSWERED-D | Keep 99.5% for the pilot (monthly, maintenance excluded); 99.9% as enterprise target |
| OQ-010 | OPEN-APPROVAL | Approve 24×7 with a sustainable rota (≥4 on-call engineers or a managed partner) and the warm-recovery cost |
| OQ-011 | ANSWERED-D | SuiteFlow's hotel document is the customer-facing invoice; statutory/fiscal document derives and links (ADR-008) |
| OQ-012 | ANSWERED-D | 48 h free cancellation; one night inside 48 h; no-show = first night + tax; deposits 1 night / 100% groups; FOM/GM overrides logged |
| OQ-013 | ANSWERED-D | Credit application + references; limit at onboarding; 30-day terms; suspension at 60 days overdue; Finance Controller reinstates |
| OQ-014 | ANSWERED-D | Restaurant and bar (room service if operated) run in SuiteFlow; external POS integration stays deferred |
| OQ-015 | ANSWERED-D | Light stock control at pilot (par levels, counts, wastage); full recipe costing in a later phase |
| OQ-016 | ANSWERED-D | Manual rate management at pilot; RMS/forecasting later |
| OQ-017 | ANSWERED-D | Groups pilot-critical: blocks, cutoffs, rooming lists, master folio/routing, deposits, group statements, basic conference space booking |
| OQ-018 | ANSWERED-D | Direct + corporate at pilot; manual OTA handling if unavoidable; channel manager later |
| OQ-019 | ANSWERED-D | Capture ID type/number for adult guests; scan only if legally required; never store NIN images; Class-A handling |
| OQ-020 | ANSWERED-D | Manual keys at pilot; lock-vendor research in Phase 1; integration in an enterprise phase |
| OQ-021 | ANSWERED-D | 10% F&B service charge as a staff-distribution liability, never revenue; tax treatment per adviser |
| OQ-022 | ANSWERED-D | Work orders + light preventive maintenance at pilot, coordinated with OOO rooms |
| OQ-023 | ANSWERED-D | HRMS/directory is the identity source; SuiteFlow consumes an identity projection only; no payroll |
| OQ-024 | OPEN-ADVICE | Recommended retention schedule (ID images 90 d; profiles 6 y; financial 7 y; AML 5 y; logs 12 m+; CCTV 30 d) — counsel to confirm |
| OQ-025 | OPEN-FACT | Provide existing-data inventory; migrate open bookings, guests, profiles, AR/deposits, rate plans; archive history; 2 dry runs |
| OQ-026 | ANSWERED-D | In-country (Nigeria) hosting with in-country backups; NDPA transfer controls for any offshore processing; verify providers |
| OQ-027 | ANSWERED-D | Multi-property is already the design; a second property is configuration, not re-architecture; timing is commercial |
| OQ-028 | OPEN-ADVICE | Adopt an OSS policy with SBOM/license scanning; copyleft review; counsel gate before distribution |
| OQ-029 | OPEN-ADVICE | Treat fiscalisation as adapter INT-014 deriving from the operative document; confirm current FIRS obligations with the adviser |
| OQ-030 | ANSWERED-D | Automate gateway webhooks (idempotent); bank confirmation manual/semi-automated until evidence supports more; reconciliation is the control of record |
| OQ-031 | ANSWERED-D | No AI at pilot; candidate priorities listed for enterprise phases; evaluation gates per capability |
| OQ-032 | ANSWERED-D | English-only + WCAG 2.2 AA at pilot; localisation later |
| OQ-033 | OPEN-FACT | Appoint Technical Lead and Security/Privacy adviser (with deputies); security sign-off stays pending until then |
| OQ-034 | ANSWERED-D | Inspect VIP/arrival-critical rooms plus ≥20% spot checks; daily stayover service; minibar only if operated, with posting and departure check |
| OQ-035 | ANSWERED-D | Children ≤5 free; 6–12 sharing at 50%; early/late check-in/out banded at 50%/full night; amounts configurable |
| OQ-036 | ANSWERED-D | Comps approved and reason-coded, target ≤2% of room nights, counted in occupancy at value with contra-revenue; house use excluded from occupancy/ADR |
| OQ-037 | ANSWERED-D | Float ₦100,000 default; zero tolerance with investigation above 0.5%; cash payouts ≤₦50,000 require FOM; daily banking; dual custody |
| OQ-038 | OPEN-FACT | Site survey required; dual-path WAN + UPS mandatory; degraded read-only mode + manual fallback; no full offline financial mode |

## 4. Answers

### 4.1 Product Owner inputs (OQ-001…OQ-010)

#### OQ-001 — Registered legal entity and tax identifiers

**Answer.** Not answerable from industry practice: the registered name, RC number, TIN, VAT registration and registered address are facts. Required evidence: CAC certificate of incorporation, FIRS TIN, VAT registration certificate, registered address, and bank mandate details. Design position: legal identity (name, address, RC, TIN, VAT) is effective-dated configuration used by every document and statutory report; no customer-facing or statutory document may be finalised or issued until the values are recorded, and the go-live gate blocks on them.

**Basis.** Statutory invoicing requires the supplier's legal name and tax particulars; configuration-driven entity identity is standard PMS practice.

**Still needed.** The certificates/documents from the first property's operating entity — **deferred to the First-Property Deployment Gate** (the reference pilot has no legal entity; documentation uses placeholder configuration).

**Affects.** ADR-008 document templates, financial architecture, invoicing and statutory reporting.

#### OQ-002 — Named Finance Controller / pilot Finance representative

**Answer.** The role is required: a named individual (and a named deputy) accountable for financial policy, approval of the financial architecture and authority limits, credit policy, write-offs/refunds above thresholds, close certification and reopen authority. Industry governance practice is a documented role charter plus a named holder — this document cannot name the person.

**Basis.** Segregation-of-duties and approval-authority practice (charter §12); standard finance governance in hotel operations.

**Still needed.** Appointment by the Product Owner, target Week 1. Until then the Product Owner holds interim financial approval and the blueprint's financial defaults remain in force.

**Affects.** Charter §12 approvals, role-and-authority matrix, ADR-005…008 approvals, business rules using `[OQ-002]`.

#### OQ-003 — Named Hotel Operations representative and acceptance approver

**Answer.** Under the synthetic reference pilot (PO decision, 23 Sep 2026) the **Product Owner acts as the operations authority and acceptance approver** until a real property is secured; real hotel staff validate at the first-property deployment. The role requires authority to speak for front office, housekeeping and F&B practice.

**Basis.** Standard UAT and operational-acceptance practice; the blueprint's operational-realism test (charter §10) is unenforceable without a named hotel counterpart.

**Resolved.** Appointment recorded 23 Sep 2026 (inputs register BR-PILOT-008; register decision log §4). Workflow validation now runs as role-played operational-realism tests; real-hotel validation is a First-Property Deployment Gate item.

**Affects.** Workflow/UAT gates, business-rules confirmation, `[OQ-003]` references.

#### OQ-004 — Exact role of UBA and Wema Bank

**Answer.** Both banks may serve, but best practice is a clear split: **one primary collections/operating account** per property for cash and card settlement, **one designated POS acquirer** selected on evidence (settlement timeliness, fees, terminal support, statement quality, API availability), transfers received into the operating account, and a secondary bank as contingency. Where offered, use **dedicated/virtual account references per payer** so transfer reconciliation is automatable. Roles are assigned after the Phase 1 evidence matrix; the interim model is both banks used manually with recorded references.

**Basis.** Treasury and reconciliation practice: minimise collection accounts, maximise referenceability, select acquirer competitively.

**Still needed.** Account structure (operating vs settlement), merchant IDs, whether either bank offers API or virtual accounts, statement formats — **deferred to the First-Property Deployment Gate**; the reference pilot uses the simulated manual provider path.

**Affects.** ADR-011, payments/banking interfaces (INT-002/003), reconciliation design.

#### OQ-005 — Guest-facing online payment links

**Answer.** At the enterprise target: yes — prepayment links are standard for direct and group bookings and reduce no-shows and cash handling. At pilot: keep within the accepted MVP boundary (at most one proven provider path if required; otherwise controlled references), because the provider-neutral adapter (ADR-011) lets a link provider attach later without redesign.

**Basis.** E-commerce/payments practice in hospitality; BR-MVP-005.

**Still needed.** Product Owner preference if prepayment is commercially important before pilot.

**Affects.** Payments scope, ADR-011, integration roadmap.

#### OQ-006 — POS terminal/acquirer and settlement-report details

**Answer.** Facts required: acquirer, merchant and terminal IDs with locations, settlement frequency (typically T+1), and a sample settlement report (batch totals, fees, net credit) plus how the bank credit is referenced. Design position: daily three-way reconciliation — terminal batch → acquirer settlement → bank credit — through a clearing account per acquirer, with an aged exception queue; manual batch reconciliation from terminal/acquirer reports is the accepted pilot baseline until interface evidence supports automation.

**Basis.** Standard card-clearing and income-audit control; BR-PAY-002/006.

**Still needed.** Acquirer/terminal/settlement facts — **deferred to the First-Property Deployment Gate**; the reference pilot reconciles synthetic settlement batches.

**Affects.** Payments architecture, income audit, cashiering, RSK-FIN-003.

#### OQ-007 — Bank statement formats and access

**Answer.** Request electronic statements from both banks, preferred in order: API → MT940 → CSV/Excel → PDF. Facts required: format samples, access method (portal/email/API), frequency, and which accounts are covered (operating and settlement). Design fallback: manual statement import with dual review. Standard control: daily reconciliation of bank movements to cashiering, POS clearing and transfer clearing with zero unexplained differences.

**Basis.** Bank reconciliation practice; NFR integrity requirements.

**Still needed.** Statement samples and access details — **deferred to the First-Property Deployment Gate**; the reference pilot uses synthetic statement files.

**Affects.** Bank reconciliation design, cashiering, income audit.

#### OQ-008 — Business-day cutoff and close deadline

**Answer.** Recommended pilot default (industry practice for a city hotel with F&B): business date rolls at **02:00 WAT**; outlets may post until 02:00; night audit runs **02:00–03:30**; the close is completed and certified by **04:00 WAT** (pilot default certification-before-advance per ADR-006); post-cutoff manual postings go to the next business date; financial-integrity and queue alerts page the on-call owner around the window (BR-REL-005). Property-configurable.

**Basis.** Standard night-audit timing after outlet close, before the breakfast operating day; accepted NFR P-6/P-7 window.

**Still needed.** Hotel Operations confirmation at workflow validation (they may choose an earlier/later window).

**Affects.** BR-NAU rules, NFR P-6/P-7, night-audit workflows, NFR OQ-008 dependency.

#### OQ-009 — Confirm or replace the 99.5% service objective

**Answer.** Keep **99.5%** (≈3 h 39 m/month) as the pilot objective, measured monthly and excluding pre-agreed maintenance windows, with the measurement source and maintenance policy documented. Set **99.9%** (≈43 m/month) as the enterprise target at multi-property scale. Availability design includes degraded-mode/manual fallback, because pure uptime without a fallback overstates usability. Repeated misses trigger reliability work before scope expansion (error-budget discipline).

**Basis.** SaaS availability practice; 99.9% is the common enterprise-SaaS standard, and 99.5% is a defensible single-property pilot with a manual fallback.

**Still needed.** Product Owner confirmation, target Week 2.

**Affects.** NFR A-1, deployment architecture, monitoring and alerting.

#### OQ-010 — Cost and staffing of 24×7 and 1-hour RPO/RTO

**Answer (recommendation, not approval).** Genuine 24×7 needs a sustainable model: at least **four on-call engineers** (two-deep, leave/sickness safe) or a **managed infrastructure/NOC partner** for a six-person core team, plus hotel-side L1 escalation contacts, monitoring/paging, runbooks, incident-command rules and on-call compensation. The 1-hour RPO/RTO requires continuous binlog/PITR replication and warm recovery capacity (BR-REL-002/003) — accept this cost as a launch gate. Recommend approval with a named rota, escalation matrix and budget line by Week 3.

**Basis.** Industry on-call staffing practice (a rota below four people concentrates operational risk); accepted requirements BR-REL-001…006.

**Still needed.** Product Owner cost/staffing approval (REL-002). This document cannot grant it.

**Affects.** Deployment/operations architecture, runbook catalogue, RSK-DEP-001/002.

### 4.2 Business and policy questions (OQ-011…OQ-025)

#### OQ-011 — Invoice authority

**Answer.** The customer-facing invoice/receipt is the **hotel-generated document from SuiteFlow** (folio invoice, pro forma, receipts, corporate statements) — universal hotel practice and the document the guest and corporate client receive. Where Nigerian VAT law or fiscalisation requires a statutory tax invoice or an accredited fiscal document, that document is **derived from and linked to** the SuiteFlow document (one supply, one operative document, same series reference) per ADR-008. Statutory particulars (legal name, TIN, VAT registration, address, buyer details where required, VAT rate/amount, series number) are configuration-driven and confirmed by the tax adviser.

**Basis.** Hotel front-office practice; VAT invoice particulars practice; ADR-008 architecture.

**Still needed.** Finance Controller confirmation and tax-adviser particulars (OQ-021/029).

**Affects.** ADR-008 (provisional → confirmable), financial architecture, document templates, reporting.

#### OQ-012 — Deposit, refund, forfeiture, cancellation and no-show policy

**Answer.** Recommended policy set (all property-configurable, effective-dated):
- **Standard transient:** free cancellation ≥48 h before arrival (before 12:00 local); one-night penalty inside 48 h; **no-show = first night + taxes**.
- **Non-guaranteed bookings:** released at 18:00 local on arrival day.
- **Deposits:** one night for standard transient; 100% or contract terms for groups, conference and peak dates; group cutoff dates per contract (typical 30–7 days).
- **Refunds:** to the original instrument, initiated at decision/checkout, typically within 5–10 business days; cash deposits refunded per cash-handling policy.
- **Forfeiture:** computed from the governing policy formula (BR-FOL-009), never operator judgement; dedicated cancellation/no-show revenue (ADR-007).
- **Overrides:** Front Office Manager may waive a first-night penalty within a limited band; General Manager/Finance Controller above it; every override is reason-coded and auditable.

**Basis.** Common hotel cancellation/no-show and deposit practice; ADR-005/007; BR-FOL-009/010/011.

**Still needed.** Hotel/Finance confirmation at workflow validation; amounts/bands are policy.

**Affects.** BR-FOL rules, folio/reservation state machines, role matrix, night audit.

#### OQ-013 — Corporate credit policy

**Answer.** Standard policy: credit application with trade references and bank confirmation; security/deposit for new or high-risk accounts; a limit set per account at onboarding (typical: one month of expected room + outlet spend, or a policy cap), reviewed annually and on breach; **30-day terms from statement** (statements issued on the 1st for the prior month); **automatic suspension at 60 days past due or limit breach** (no new direct-bill bookings while suspended; walk-in prepayment remains available); **reinstatement by the Finance Controller** after settlement; weekly aged-AR review with 90+ days escalated to collections/legal.

**Basis.** Trade-credit and AR management practice; RSK-FIN-003 controls.

**Still needed.** Finance Controller confirmation of limits, suspension thresholds and override authority.

**Affects.** AR/direct-bill design, corporate workflows, role matrix.

#### OQ-014 — F&B outlets inside SuiteFlow

**Answer.** Yes for this property profile: a ~200-room Lagos conference-centre hotel operates at least a **restaurant and bar** (and room service if the hotel runs it) in SuiteFlow; outlet revenue, checks and postings are in scope. External POS business integration stays deferred by the accepted MVP boundary, but nothing about outlets is out of scope. The hotel confirms the exact outlet list (restaurant, bar, room service, conference/banquet catering).

**Basis.** F&B is conventional in this property class; accepted MVP keeps outlet revenue in scope (BR-MVP-011 defers external POS integration only).

**Still needed.** Outlet list from Hotel Operations.

**Affects.** POS/F&B capability priorities, posting families, outlet workflows.

#### OQ-015 — Restaurant/kitchen store inventory

**Answer.** Light stock control at pilot: item master, receipts/issues, par levels, monthly counts, wastage/spoilage records and cost-of-goods visibility for F&B stores. Full recipe costing, production and multi-store purchasing are enterprise-phase. If no store is operated, inventory is switched off by configuration — no design change.

**Basis.** Progressive POS/inventory practice; keeps pilot operable without full stock management.

**Still needed.** Confirmation of whether a kitchen/store operation exists.

**Affects.** Inventory capability priority, POS workflows.

#### OQ-016 — Revenue-management expectations

**Answer.** Manual rate management at pilot: a small, governed rate-plan set (rack/BAR, corporate, group, package), seasonal and day-of-week pricing, and restrictions (min-stay, closed-to-arrival, stop-sell). Rate plans stay few enough to operate without a revenue manager. RMS/forecasting/automated recommendations are enterprise-phase capabilities; nothing at pilot depends on them.

**Basis.** Rate-management practice for a single independent property; roadmap phase model.

**Still needed.** Product Owner confirmation (expected to align).

**Affects.** Rate and revenue capability priorities, rate-plan configuration.

#### OQ-017 — Group/event and conference business

**Answer.** Pilot-critical: the property is a conference centre. Pilot-ready group workflows: group blocks with cutoff/release, rooming lists, group rates, deposit and billing instructions, **master folio with routing** (room/tax to master, incidentals to guest), group check-in/out, group statements — plus basic conference/function-space booking with event orders and charges to the master folio. Depth of banquet/catering (menus, per-person pricing) is confirmed with the hotel; the minimum is space booking with charges and deposits.

**Basis.** Conference-hotel operating practice; group/catering is a primary revenue stream in this property class.

**Still needed.** Hotel confirmation of the group/catering depth required for pilot.

**Affects.** GRP/SAL capability priorities, group workflows, folio routing rules.

#### OQ-018 — Channel/distribution requirement

**Answer.** Direct + corporate at pilot (website/phone/walk-in and negotiated corporate); if OTA volume must be served, handle it manually with strict rate/availability discipline and a documented operating procedure (RSK-INT-002). Channel-manager/OTA integration is a later enterprise phase; the inventory/rate boundary is designed so a channel manager attaches without redesign.

**Basis.** Distribution practice for independent Nigerian properties; accepted MVP boundaries.

**Still needed.** Product Owner confirmation.

**Affects.** Distribution capability priority, integration roadmap (INT boundary).

#### OQ-019 — Identity capture at guest registration

**Answer.** Capture **ID type and number for all adult guests** at registration, plus nationality/address — standard hotel registration and needed for watchlist/security checks. Scan/photo only where legally required or for foreign-guest reporting (confirm with counsel; Nigerian immigration expectations apply to foreign guests). **Never store NIN images** or unnecessary document images. If scanning is enabled: Class-A handling (encryption, masked display, read logging, retention per OQ-024). Identity capture is verified at check-in and does not by itself block a guest with a valid reservation unless policy says otherwise.

**Basis.** Hotel registration practice; NDPA principles of minimisation; security model Class-A.

**Still needed.** Legal confirmation on scanning/foreign-guest reporting; hotel confirmation.

**Affects.** BR-FO-001, security model, INT-011, retention schedule.

#### OQ-020 — Door-lock integration

**Answer.** Manual keys at pilot. Start lock-vendor research in Phase 1 (vendor APIs, RFID/BLE, audit trail, key issuing/return at the desk, PMS integration options); integration lands in an enterprise phase once a lock vendor and protocol are selected. Nothing in pilot scope depends on it; the key-issuance/return log applies regardless.

**Basis.** Progressive integration practice: don't couple the pilot to a lock vendor before selection; defer is standard.

**Still needed.** Phase 1 research; hotel preference recorded.

**Affects.** Integration scope (INT-010), front-office workflows.

#### OQ-021 — Service charge policy

**Answer.** Recommended default: **10% service charge on F&B** (commonly also conference/banquet; optional on other services), collected as a **staff-distribution liability — never revenue** — reconciled monthly and distributed under an approved staff policy (pro-rata to service staff, transparent schedule, staff communication). Whether service charge enters the VAT/consumption-tax base requires adviser confirmation; until then configuration keeps it separate from revenue and applies tax per adviser instruction. All rates effective-dated configuration.

**Basis.** Nigerian hotel service-charge practice; ADR-005 liability treatment; BR-FOL-010 context.

**Still needed.** Finance Controller policy; tax-adviser treatment.

**Affects.** Financial architecture, tax configuration, reporting, staff distribution process.

#### OQ-022 — Maintenance/engineering scope

**Answer.** Work orders + light preventive maintenance at pilot: light asset register (rooms, key equipment), work orders from guest/staff reports with priority and SLA, assignment and completion notes, **OOO/OOS coordination with housekeeping and front desk**, and recurring PM schedules for critical assets (generator, lifts, water, AC, kitchens). Full CMMS (spare parts, condition monitoring) is enterprise-phase.

**Basis.** Maintenance practice for a 200-room property; the OOO loop is the pilot's highest-value control.

**Still needed.** Hotel confirmation of PM assets and priorities.

**Affects.** MNT capability priority, maintenance workflows/state machines, UX maintenance board.

#### OQ-023 — HRMS authority

**Answer.** Identity-only: the **HRMS/directory is the system of record for employee identity**; SuiteFlow consumes a user identity projection (name, role, property/company scope, active status) and holds no employment/payroll data. If no HRMS exists at pilot, SuiteFlow user administration is authoritative until an identity source is connected (SSO later). Leaver deactivation must be immediate via the identity source or an admin process.

**Basis.** Identity-boundary practice; accepted MVP (HRMS identity boundary only, payroll deferred).

**Still needed.** Confirmation of whether an HRMS/IdP exists at the pilot.

**Affects.** Platform identity design, security model, HRM boundary.

#### OQ-024 — Guest data retention

**Answer (recommended schedule; legal finalisation required).**
| Category | Recommended retention |
|---|---|
| Guest ID images/scans | 90 days after departure, then deleted, unless flagged (fraud/security/legal hold) or legally required longer |
| ID type/number in guest profile | Profile active + 6 years after last stay (financial/legal linkage; shorter if counsel directs) |
| Folio, invoice, payment and GL records | 7 years |
| AML-relevant transaction records | 5 years after the transaction |
| Security/audit logs | 12 months minimum; financial-relevant audit trails aligned to 7 years |
| Marketing consent/withdrawal records | Consent life + 12 months as proof |
| CCTV (if any) | 30 days rolling |

Retention is per-category configuration from day one (data model already supports it); deletion is evidenced. NDPA 2023 principles govern — purpose limitation, storage limitation, data-subject rights, cross-border transfer rules.

**Basis.** Nigerian data-protection principles plus common financial/AML record-keeping expectations (tax 7 y, AML 5 y, security logs 12 m). **UNVERIFIED** until counsel confirms.

**Still needed.** Legal/counsel confirmation (Phase 1/2), before Class-A data goes live.

**Affects.** Data model, security model, privacy controls, reporting retention, AI prompt/log retention.

#### OQ-025 — Existing data to migrate

**Answer.** Cannot be assessed without an inventory. Industry-standard cutover scope: open reservations/future bookings, in-house and arriving guests, guest profiles (deduplicated by verified identity), AR open balances with ageing, deposit/advance balances, gift certificates/vouchers, negotiated rates and rate plans, rooms/assets/bedding configuration, and user/role setup. Historical folios migrate only if legally required; otherwise archive read-only outside the platform. Method: data-quality audit → mapping → at least two dry runs in staging → opening-balance reconciliation signed by hotel finance → rehearsal cutover with rollback → go/no-go. Never cut over on a business date with unreconciled balances.

**Basis.** Standard PMS migration methodology; RSK-DATA-001 controls.

**Still needed.** Inventory of incumbent systems/spreadsheets/paper, volumes, quality and history depth — **deferred to the First-Property Deployment Gate**; the reference pilot migrates synthetic opening data (guest profiles, reservations, deposits, AR balances) through the same tooling.

**Affects.** Migration architecture, roadmap Phase 9, cutover plan.

### 4.3 Architecture and platform questions (OQ-026…OQ-038)

#### OQ-026 — Cloud region, data residency, hosting

**Answer.** Host the pilot **in-country (Nigeria)**, or in a region assessed as providing adequate protection under NDPA 2023, with primary data and backups in-country where available. Any offshore processing (support tooling, AI, email/text vendors) must be inventoried and covered by lawful transfer mechanisms and processor agreements. Data residency is a Phase 1 vendor-selection criterion. Specific provider/region availability and NDPC adequacy positions remain **UNVERIFIED** until checked.

**Basis.** NDPA 2023 cross-border transfer framework; enterprise data-residency practice.

**Still needed.** Phase 1 verification of provider regions and NDPC positions.

**Affects.** Deployment architecture, security model, vendor selection.

#### OQ-027 — Second property timing and operating model

**Answer.** The design is already multi-property (tenant = deployment unit; properties as first-class partitions; ADR-002). Whatever the second-property model — same entity, management contract, franchise — onboarding it must be **configuration** (property record, chart-of-accounts mapping, tax/rate profiles, users and scope), not re-architecture. Timing is a commercial question for the Product Owner and is not a design blocker; the pilot remains a one-property tenant where the model is degenerate.

**Basis.** ADR-002; RSK-ARCH-002 control (single property is the trivial case, not a special case).

**Still needed.** None for design; Product Owner informs when commercially known.

**Affects.** Tenancy design, roadmap phasing.

#### OQ-028 — Licensing position for upstream dependencies

**Answer (policy recommendation; counsel confirms).** Maintain an OSS policy with: an SBOM and automated license scanning in CI; an allow-list for permissive licenses (MIT/BSD/Apache-2.0); explicit review of copyleft (GPL/LGPL/AGPL) obligations before any distribution or resale; repository boundary discipline; and attribution/NOTICE generation. Hosted-service use generally triggers distribution obligations only on distribution of the software, but the product's distribution intent must be clarified — **network-copyleft (AGPL) dependencies are the highest risk for a commercial SaaS**. Counsel sign-off remains the gate for external distribution.

**Basis.** Open-source compliance practice; RSK-LIC-001; repository license position pending.

**Still needed.** Legal counsel review (Phase 1/2) and a distribution-intent decision.

**Affects.** Release/distribution architecture, repository licensing, CI license gates.

#### OQ-029 — Fiscalisation / e-invoicing in Nigeria

**Answer.** Nigeria is moving toward mandatory e-invoicing/FIRS integration with phased rollout by taxpayer size; the obligations and thresholds applicable to this entity must be confirmed with the tax adviser. Design position: treat accredited fiscal integration as an **adapter boundary (INT-014)** that derives from and links to the SuiteFlow operative document (ADR-008); the document engine keeps series/particulars configurable for statutory formats. If the adviser confirms a current mandate at this entity's size, fiscalisation becomes a launch gate for invoicing flows.

**Basis.** FIRS e-invoicing direction (specifics **UNVERIFIED**); ADR-008 derived-document pattern.

**Still needed.** Tax-adviser ruling (Phase 1) and monitoring of FIRS mandates.

**Affects.** ADR-008, integrations (INT-014), document engine, tax configuration.

#### OQ-030 — Idempotent, referenceable payment confirmations

**Answer.** Payment gateways generally provide webhooks with unique references — automatable with idempotent processing (write-ahead provider intent, dedupe keys; ADR-010/011). Bank-transfer confirmation via statement/portal is less standardised: automate when the bank provides API or virtual-account references, otherwise use the manual verification workflow (accepted baseline, BR-MVP-005). The adapter carries capability flags (automated / semi-automated / manual) per provider. Reconciliation remains the control of record: no payment is final until matched to an approved bank record.

**Basis.** Payment-integration practice; provider-neutral adapter design (ADR-011).

**Still needed.** Provider evidence matrix (Phase 1/3) per bank/acquirer.

**Affects.** ADR-011, integration architecture, income audit.

#### OQ-031 — AI capability priorities and acceptability

**Answer.** **No AI capability is enabled at pilot** (governance-first: AI proposes, governed services decide — charter §15). Candidate enterprise-phase priorities, in likely value order: (1) night-audit/income-audit anomaly detection (read-only analysis); (2) document extraction (IDs, vendor invoices) with Class-A handling; (3) guest-communication drafting with human send; (4) revenue/forecast recommendations surfaced to humans. Each capability passes evaluation gates (accuracy, privacy, abuse/bias checks, data-scope tests) before enablement; priorities are set with the Product Owner at Phase 19–20 planning.

**Basis.** AI governance practice; WP 0.7 AI architecture.

**Still needed.** Product Owner priority-setting at the relevant phase (no pilot action).

**Affects.** AI capability roadmap, evaluation gates.

#### OQ-032 — Accessibility and language

**Answer.** English-only for pilot; **WCAG 2.2 AA for core operator flows** (already the target) — keyboard-first front desk, visible focus, screen-reader labels, contrast, scalable text. Multilingual/localisation and guest-facing accessibility are enterprise-phase decisions. Training materials in English.

**Basis.** Accessibility standard practice; UX architecture target.

**Still needed.** Product Owner confirmation (expected to align).

**Affects.** UX architecture, QA test obligations.

#### OQ-033 — Named Technical Lead and Security/Privacy adviser

**Answer.** Cannot be named by industry practice — the Product Owner appoints. Governance standard: each role has a named accountable person **and a named deputy**; the Technical Lead owns technical design approvals and release gates; the Security/Privacy adviser owns security/privacy sign-off and the NDPA compliance posture. Until appointed, technical approvals are delegated to the programme's maintaining architect; **security sign-off remains pending** and the blueprint cannot reach ACCEPTED without it (charter §12). Recommend appointment by Week 1–2.

**Basis.** Programme governance practice; charter §12 approval authorities.

**Still needed.** Two appointments by the Product Owner.

**Affects.** Blueprint acceptance gate, ADR approvals, security model.

#### OQ-034 — Housekeeping service standards

**Answer (defaults, configurable).** Supervisor inspection required for **VIP and arrival-critical rooms**, plus **spot checks on ≥20%** of other rooms (refines the current default); all rooms released only via task complete + inspection where required + no OOS/OOO (BR-HSK-002). Stayover **full service daily** (bed, bath, trash; linen change every 2–3 days per hotel standard or daily if the hotel's standard says so); turndown optional/off by default. **Minibar only if the property operates it**; when yes: consumption posts to the responsible folio window and a minibar check at departure blocks silent checkout within policy tolerance (BR-HSK-005). No sensor minibars at pilot.

**Basis.** Housekeeping practice for a ~200-room city/conference hotel; BR-HSK-002/005 defaults.

**Still needed.** Hotel confirmation of linen frequency, spot-check percentage and minibar operation.

**Affects.** BR-HSK-002/005, housekeeping workflows/state machines.

#### OQ-035 — Pricing policy details

**Answer (defaults; final amounts are hotel policy).** Children **≤5 free** when sharing (no extra bed); **6–12 at 50%** when sharing, plus any extra-bed charge; extra adult sharing by policy percentage/fee; single occupancy may take a plan-defined discount. **Early check-in:** before 06:00 = full night; 06:00–12:00 = 50% of the nightly rate (subject to availability). **Late checkout:** 12:00–18:00 = 50%; after 18:00 = full night. **Early departure:** no refund after check-in except per cancellation policy or contract terms. Deposit/guarantee required for early check-in and late checkout. All values effective-dated configuration, never hard-coded.

**Basis.** Common Nigerian/international front-office pricing bands; BR-RTM-005, BR-FO-004/005.

**Still needed.** Hotel confirmation of amounts and bands.

**Affects.** Rate rules, front-office rules, folio charges.

#### OQ-036 — Comp and house-use policy

**Answer.** **Comps:** require approval per the role matrix (recommend Front Office Manager within a monthly budget, General Manager above it) with a reason code (VIP, service recovery, marketing, owner); recommend a **≤2% of room nights** monthly target with breach alerts. Comps are valued at the applicable rate for statistics and post at value with a contra-revenue allowance (ADR-005/BR-FOL-005), so **occupancy and ADR include comps** and cost remains visible. **House use:** excluded from occupancy and ADR, tracked separately with approval and usage limits. Both appear in night-audit and monthly statistics.

**Basis.** USALI-consistent statistics practice and ADR-005 treatment; BR-RPT-003.

**Still needed.** Hotel confirmation of approval bands and the comp target.

**Affects.** BR-RPT-003, BR-FOL-005, reporting, role matrix.

#### OQ-037 — Cash handling policy

**Answer (recommended placements, configurable).** House-bank float per cashier, default **₦100,000**, verified at session start/end. **Zero variance tolerance**, with investigation and documented explanation required above **0.5% of session volume**. Cash payouts/refunds above **₦50,000** require Front Office Manager approval; above **₦150,000** require General Manager. **Daily banking** of cash next business morning with after-hours safe-drop; **dual custody** for safe access and deposit preparation. No IOUs, personal cheques or FX at the desk. Over/short posts to a dedicated account identifying operator and supervisor.

**Basis.** Cashiering control practice; BR-CSH-002/005 defaults.

**Still needed.** Finance confirmation of float, thresholds and tolerance.

**Affects.** BR-CSH rules, cashiering workflows, income audit.

#### OQ-038 — Connectivity and power reality

**Answer.** At the first property: the facts (ISP links, redundancy, power stability, UPS coverage, appetite for on-site equipment) must come from the hotel — a **site survey at the First-Property Deployment Gate** (PO decision, 23 Sep 2026; the synthetic reference pilot has no site) covering: ISP(s), link types/speeds and failover, generator/changeover behaviour, UPS coverage, and equipment-room conditions. The reference pilot runs on a hosted environment with the mandated controls assumed. Design requirements apply regardless: **dual-path WAN (wired + mobile failover)** and **UPS for desk/network equipment** are launch requirements (NFR A-7/A-8); degraded mode = read-only cache of operational essentials plus a scripted manual fallback with same-day reconciliation. **No full offline financial operation is offered** — a front desk that stops when the internet drops is a launch blocker (RSK-DEP-003).

**Basis.** Deployment architecture requirements; accepted availability/recovery baseline.

**Still needed.** Site survey results and cost consequences at the first property — **deferred to the First-Property Deployment Gate** (Product Owner/Platform).

**Affects.** Deployment architecture, NFR A-7/A-8, offline strategy, risk RSK-DEP-003.

## 5. Adoption record and next steps

**Adopted.** On 23 September 2026 the Product Owner adopted all `ANSWERED-D` answers in full and closed OQ-003 by appointment (Product Owner as acting operations authority). The register records the closures and decision log (GOV-OQ §4), and the affected documents now mark closed questions `OQ-nnn (closed)`; documents whose rules were refined by an adopted answer (business rules, NFR, capability map, target state, ADR-008, master index) carry a version increment, and marker-only updates are recorded in the commit history.

**Still open — and what closes them:**

1. **Facts** (`OPEN-FACT`, 8): programme-level — OQ-002 (Finance Controller) and OQ-033 (Technical Lead and Security/Privacy adviser); deferred to the First-Property Deployment Gate — OQ-001 (entity/tax documents), OQ-004 (bank roles), OQ-006 (acquirer/terminals/settlement reports), OQ-007 (statement formats), OQ-025 (existing data inventory), OQ-038 (site survey). Close when the evidence is recorded.
2. **Advice** (`OPEN-ADVICE`, 3): OQ-024 (retention schedule), OQ-028 (open-source licensing), OQ-029 (fiscalisation/e-invoicing). Close with Phase 1 professional sign-off; the interim positions above keep design moving.
3. **Approval** (`OPEN-APPROVAL`, 1): OQ-010 (24×7 staffing and recovery budget). Close with the Product Owner's approval.

The accepted business-requirement amendment for the synthetic reference pilot is recorded in the inputs register §3.1 (BR-PILOT-001/005/008/009/010). All adopted answers remain consistent with the amended pilot boundaries and the charter's standing constraints. Where an adopted answer keeps a dependency (for example OQ-019 legal confirmation on scanning, OQ-021 tax treatment), the dependency is stated in the answer and tracked in the risk register — the adopted position is what design uses meanwhile.

## 6. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial answer pack at Product Owner direction: 38 questions answered on industry-standard/best-practice basis (25 ANSWERED-D, 9 OPEN-FACT, 3 OPEN-ADVICE, 1 OPEN-APPROVAL) | PROPOSED |
| 0.2 | 2026-09-23 | All 25 ANSWERED-D answers adopted by the Product Owner; register closed (§4 decision log) and affected documents marked `OQ-nnn (closed)` | PROPOSED |
| 0.3 | 2026-09-23 | Synthetic reference pilot decision: OQ-003 closed by appointment; OQ-001/004/006/007/025/038 marked deferred to the First-Property Deployment Gate | PROPOSED |
