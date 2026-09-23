---
doc-id: ARCH-RULES
title: Target Business Rules
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Hospitality Domain Architect (drafted); Finance Controller + Hotel Operations (approval; roles open)
applies-to: full enterprise target; values marked [OQ-nnn] pending product decisions
depends-on: [ARCH-DOMAIN, ARCH-STATES, PROD-CAPMAP]
---

# Target Business Rules

## 1. Purpose and rules of engagement

This document is the authoritative catalogue of **deterministic business rules**: statements that must produce the same result for the same inputs, every time, and whose exceptions are explicit and authorised.

Rules here are requirements for the target. Where a number or policy depends on an open Product Owner or hotel decision, the rule is marked `[OQ-nnn]` and carries a stated default for design purposes. No rule may be implemented from a guess; `[OQ-nnn]` markers must be resolved or the stated default accepted before the rule is ACCEPTED.

Rule format: `BR-<DOMAIN>-NNN`. Rules are cross-referenced by capabilities, state machines and invariants from the other architecture documents.

## 2. Availability and inventory (AVL)

| ID | Rule | Basis / dependencies |
|---|---|---|
| BR-AVL-001 | Availability for a date and room type = sellable capacity (physical rooms of that type minus active OOO windows) − active commercial allocations (reservations, blocks, holds). Derived, never edited. | INV-PM-4; CAP-AVL-001/002 |
| BR-AVL-002 | A booking that would make any night's availability negative is rejected unless an active OverbookingPolicy permits it; permitted oversell is reported nightly as exposure. | INV-PM-6; CAP-AVL-005 |
| BR-AVL-003 | OOO removes a room from both capacity and occupancy statistics; OOS removes sellability but remains in capacity and available-room statistics. | Statistical definitions (BR-RPT-002/003) |
| BR-AVL-004 | Restrictions resolve by explicit precedence: stop sell > closed to arrival/departure > min/max LOS > advance purchase. Most restrictive wins; resolution is reproducible and recorded. | INV-RTM-3; CAP-AVL-004 |
| BR-AVL-005 | Block inventory releases automatically at cutoff; late extension requires revenue authority and is reported. | INV-GRP-2; CAP-GRP-002 |
| BR-AVL-006 | Every allocation state change records its owning entity and reason; allocations are never deleted, only released/expired/consumed. | INV-PM-5; CAP-AVL-009 |

## 3. Rates (RTM)

| ID | Rule | Basis / dependencies |
|---|---|---|
| BR-RTM-001 | A booking resolves exactly one rate plan eligible for the room type, dates and guest/account eligibility; ineligibility is blocking, override requires authority. | INV-RSV-4; CAP-RSV-015 |
| BR-RTM-002 | Rate resolution precedence for eligible plans: negotiated rate (valid agreement) > package/promo (conditions met) > BAR. Ties resolve by lowest price for the guest unless flag `prefer_margin` is set by revenue policy. | CAP-RTM-004/006 |
| BR-RTM-003 | Rate and restriction changes require rate-change authority; changes are effective-dated and never alter past dates or posted items. Bulk changes preview affected dates and occupancy before confirmation. | INV-RTM-1/5; CAP-RTM-011/012 |
| BR-RTM-004 | Derived rates recompute from base rate and rule; recomputation only affects dates not yet posted; floors/ceilings clamp results; negative results are invalid. | INV-RTM-4; CAP-RTM-003 |
| BR-RTM-005 | Occupancy pricing: extra adult/child, sharing and single-occupancy rules are per rate plan; children at or below the property's child age bound [OQ-035] are free unless the plan states otherwise. | CAP-RTM-007 |
| BR-RTM-006 | LOS pricing applies when the stay length meets the plan's thresholds; the applicable LOS price recomputes when stay dates change and is recorded at booking. | CAP-RTM-008 |
| BR-RTM-007 | Every charge captures the applied rate basis (plan version, amount, rule) as evidence at posting time. | INV-RTM-2; ADR-003 |
| BR-RTM-008 | "Best rate" claims (BAR guarantees) are only made where the property configures them; the system never invents a rate comparison at booking time. | Scope discipline |

## 4. Front office (FO)

| ID | Rule | Basis / dependencies |
|---|---|---|
| BR-FO-001 | Check-in requires all of: reservation confirmed for the arrival date (or walk-in creation), room assigned and readiness acceptable, identity/registration captured per policy [OQ-019], credit/payment position acceptable per policy, and no blocking alert. Failures block; overrides require the specific authority and are audit-flagged. | INV-FO-2; CAP-FO-002/004 |
| BR-FO-002 | Checkout requires the folio invariant: balance zero, transferred, or explicitly authorised to remain with reason and owner. Keys/access revoke on checkout. | INV-FO-3; BR-FOL-001; CAP-FO-008 |
| BR-FO-003 | A stay extension requires availability for added nights, re-resolved rate, and credit re-check; failure blocks or requires supervisor authority with recorded reason. | CAP-FO-010 |
| BR-FO-004 | Early check-in and late checkout are priced per configured policy [OQ-035] and require the same authority as a rate override when free of charge. | CAP-FO-009 |
| BR-FO-005 | Early departure applies the rate plan's early-departure rule; where no rule exists, remaining nights are cancelled without penalty unless the plan or contract states otherwise. [OQ-035] | CAP-FO-019 |
| BR-FO-006 | Relocation (walking) requires a relocation record, destination confirmation, and cost approval per policy; the guest's original terms are honoured and the cost is posted to the relocation account, not silently spread. | CAP-FO-018 |
| BR-FO-007 | A room move changes the assigned room, not the contract; pricing consequences follow the room-type change rules and are recorded as amendments. | INV-RSV-9 |

## 5. Folio and financial rules (FOL)

| ID | Rule | Basis / dependencies |
|---|---|---|
| BR-FOL-001 | Folio balance identity holds at all times: `Σ charges + tax + transfers in − payments − deposits applied − adjustments − refunds − transfers out`. Any violation is a defect that blocks close. | INV-FOL-1 |
| BR-FOL-002 | Every posted item carries: business date (open), currency, source entity, actor, revenue classification, and (where computed) applied-basis snapshot. | INV-FOL-3/5 |
| BR-FOL-003 | Routing precedence for a charge: explicit instruction for the charge type > reservation/account default > stay default > guest window. The resolved rule version is evidence on the item. | INV-FOL-4; CAP-FOL-005 |
| BR-FOL-004 | Adjustments (allowances) require a reason and authority by amount band; bands are effective-dated configuration. Above the top band requires maker–checker. | CAP-FOL-006 |
| BR-FOL-005 | Comps and house use require authority, are flagged as non-revenue, and are reported daily; they never silently reduce availability accounting. | CAP-FO-017 |
| BR-FOL-006 | Voids are permitted only before the business day closes (or through the governed reopen path). Voids preserve the original item and create a linked reversal. | INV-FOL-2; SM-FOLIO-ITEM |
| BR-FOL-007 | Transfers between folios/windows require an open target container, a responsible party, and produce a linked transfer pair. | SM-FOLIO-ITEM #4 |
| BR-FOL-008 | Deposits are liabilities: applied only against charges, refunded only from cleared funds, and never recognised as revenue before application, forfeiture or expiry per policy. | INV-FOL-7; CAP-ACC-006 |
| BR-FOL-009 | Forfeiture requires the governing cancellation/no-show policy basis, authority per limits, and recorded guest communication; partial forfeiture proportions follow the policy calculation, not the operator's judgement. [OQ-012] | CAP-RSV-004/005 |
| BR-FOL-010 | No-show charging: guaranteed reservations charge the first night plus tax (default) unless the guarantee terms state otherwise; non-guaranteed no-shows charge nothing; deposit disposition follows BR-FOL-009. [OQ-012] | CAP-RSV-005 |
| BR-FOL-011 | Cancellation penalty windows and percentages follow the resolved policy captured at booking plus any subsequently-effective statutory rule; the applied basis is recorded. [OQ-012] | INV-RSV-6 |
| BR-FOL-012 | Refunds never exceed cleared, un-refunded funds; method fidelity applies (same method where possible); bank-detail changes for refunds require enhanced verification. | INV-FOL-8; CAP-FOL-009 |
| BR-FOL-013 | Direct-bill transfer requires credit eligibility per BR-CRP-001 or a recorded authorised exception; each transfer produces exactly one AR document (idempotent). | INV-FOL-9; CAP-CRP-005 |
| BR-FOL-014 | A folio closes only when settled or transferred; pending-clearance payments with zero balance close with a tracked clearance flag per policy, never as silent settled money. | SM-FOLIO #2 |
| BR-FOL-015 | Positing to a folio owned by a different property is prohibited except through governed inter-property agreements (future phase) with explicit evidence. | ADR-002; scope |

## 6. Cashiering (CSH)

| ID | Rule | Basis / dependencies |
|---|---|---|
| BR-CSH-001 | One open session per cashier and till; opening requires an evidenced float. | SM-CASHIER-SESSION #1 |
| BR-CSH-002 | Session close requires counts by method and produces a variance; variance tolerance is configured [OQ-037 default: zero tolerance with review above 0.5% of session volume]. | CAP-CSH-005/006 |
| BR-CSH-003 | A variance beyond tolerance requires independent review (maker ≠ checker) with reason and selected accounting treatment before close completes. | INV-FOL-10 |
| BR-CSH-004 | Cash clears at count; POS clears against acquirer batch; transfer clears against bank confirmation; cheque clears against bank value confirmation. No other state counts as settled. | BR-PAY-006; CAP-CSH-010 |
| BR-CSH-005 | Drops are evidenced and reconciled; cash-in-transit is tracked to bank deposit. | CAP-CSH-004 |
| BR-CSH-006 | Repeated variance patterns (per person or till, per policy window) escalate to finance review and are reported monthly. | CAP-CSH-009 |
| BR-CSH-007 | Closed sessions are immutable; corrections are new adjusting entries on the current day with links to the original session. | ADR-003 |

## 7. Credit and receivables (CRP)

| ID | Rule | Basis / dependencies |
|---|---|---|
| BR-CRP-001 | Exposure = unbilled in-house direct-bill exposure + open AR + unapplied deposits owed to the account's credit position... specifically: exposure check = direct-bill exposure + overdue AR; transfer allowed only if limit not breached. | CAP-CRP-004 |
| BR-CRP-002 | A credit limit breach blocks new direct-bill transfers automatically; existing stays are handled per policy with sales/finance notification on the same day. | SM-CORPORATE-CONTRACT #2 |
| BR-CRP-003 | Suspension and reinstatement are recorded decisions with reasons; suspension never rewrites posted charges. | CAP-CRP-004 |
| BR-CRP-004 | Commission accrues per agreement only; payable commissions require settlement/cleared payment status, and reversals follow the payment state. | INV-GRP-7; CAP-CRP-006 |
| BR-CRP-005 | AR ageing buckets and due dates follow agreed terms; overdue thresholds trigger collection states and credit review. | CAP-ACC-004 |
| BR-CRP-006 | Write-offs require finance authority per limits and are reported; write-offs never delete the receivable history. | CAP-ACC-004 |

## 8. Groups and events (GRP)

| ID | Rule | Basis / dependencies |
|---|---|---|
| BR-GRP-001 | Pickup is computed from reservations linked to block lines; over-allocation beyond block quantity requires revenue authority. | INV-GRP-1 |
| BR-GRP-002 | Block inventory releases at cutoff automatically; extensions require revenue authority and are reported. | BR-AVL-005 |
| BR-GRP-003 | Attrition/cancellation charges are computed from the contracted formula against actual pickup; the calculation evidence is recorded and challengeable, never recalculated informally. [OQ-017] | INV-GRP-1/4; CAP-GRP-008 |
| BR-GRP-004 | Block rates apply only to linked reservations; eligibility and rate evidence are stored per booking. | INV-GRP-3 |
| BR-GRP-005 | A master account must exist before group charges route to it; group charges without a master account are blocked. | INV-GRP-4 |
| BR-GRP-006 | Function space is exclusive by time block; double-booking is prohibited; holds follow allocation discipline. | INV-GRP-6 |
| BR-GRP-007 | Group deposits and their application follow deposit rules (BR-FOL-008/009) at the group level; partial application is normal and tracked. | CAP-GRP-007 |

## 9. Housekeeping and maintenance (HSK/MNT)

| ID | Rule | Basis / dependencies |
|---|---|---|
| BR-HSK-001 | Task generation is deterministic from state: departure → departure clean; occupied non-departure → stayover per service policy; OOO return → readiness task; requests → priority task. | CAP-HSK-002 |
| BR-HSK-002 | A room is released to sale only through: task completed + inspection passed where the property requires inspection [OQ-034 default: inspection required for VIP/arrival-critical rooms only] + no active OOS/OOO. | INV-SVC-1 |
| BR-HSK-003 | Self-inspection (cleaner = inspector) is permitted only under a recorded property policy with authority exception per room type; exceptions are reported. | INV-SVC-1 |
| BR-HSK-004 | Discrepancies between physical occupancy and system state are resolved before the business day closes; unresolved discrepancies appear on the close checklist. | INV-SVC-4; BR-NAU-001 |
| BR-HSK-005 | Minibar/consumption capture posts to the responsible folio window; unposted consumption flagged at departure blocks silent checkout tolerance per policy. [OQ-014] | CAP-HSK-008 |
| BR-MNT-001 | OOO created by maintenance immediately reduces sellable capacity; return to service requires verified work order. | INV-SVC-2 |
| BR-MNT-002 | Priority and SLA classes are configuration with objective clocks; safety-critical work overrides queue order. | CAP-MNT-002 |
| BR-MNT-003 | Preventive maintenance schedules generate tasks with the same evidence requirements as reactive work. | CAP-MNT-005 |
| BR-MNT-004 | Parts and materials consumption posts to inventory/accounting; maintenance cost is attributable to asset/room. | CAP-MNT-006 |

## 10. Night audit and income audit (NAU/INA)

| ID | Rule | Basis / dependencies |
|---|---|---|
| BR-NAU-001 | Close cannot advance while any blocking exception is unresolved: unposted charges, open cashier sessions in OPEN/CLOSING/PENDING_REVIEW (unless policy permits flagged continuation), pending departures, unresolved discrepancies, failed posting queues, open deposit liabilities requiring disposition. | INV-NAU-3; CAP-NAU-002 |
| BR-NAU-002 | All postings are dated to the open business date; posting to a closed date is rejected; governed remediation runs only through a reopened day. | INV-NAU-1 |
| BR-NAU-003 | Nightly runs (room/package charges, tax, recurring charges) are idempotent per (property, business date, run type): re-execution posts no duplicates. | INV-NAU-2 |
| BR-NAU-004 | Day advance is atomic with close completion; partial advancement is impossible. Close failures leave the day CLOSING and resumable from checkpoint. | SM-NIGHT-AUDIT #4 |
| BR-NAU-005 | Reopen requires finance-controller authority [OQ-002 role] with reason and impact assessment; reclosed totals are versioned, originals preserved. | INV-NAU-4/5 |
| BR-NAU-006 | Day reports are generated from frozen totals at close; later restatements produce new report versions, never silent replacements. | CAP-NAU-010 |
| BR-NAU-007 | Close-window alerts (approaching cutoff, failed runs, pending reviews) page the on-call owner per support policy. | BR-REL-005 |
| BR-INA-001 | Income audit certifies the day independently of operators; certification uses full-population controls (revenue, taxes, cash, settlements, adjustments, deposits) with sampling only as amplification, never as a substitute for control totals. | CAP-INA-002/007 |
| BR-INA-002 | Adjustment/void/comp reporting is produced daily per user and amount band; repeated patterns escalate regardless of individual authorisation. | CAP-INA-003 |
| BR-INA-003 | Audit findings carry owner and deadline; unresolved findings remain visible to management and are not closed by day advancement. | INV-INA-2 |
| BR-INA-004 | Certification status (pass/hold) is recorded with findings; a held day is reported to finance even if operations must proceed per policy. | CAP-INA-006 |
| BR-INA-005 | Deposit movements (receipts, applications, refunds, forfeitures) are verified daily against the conservation invariant and authority evidence. | CAP-INA-005; INV-FOL-7 |

## 11. Accounting interface (ACC)

| ID | Rule | Basis / dependencies |
|---|---|---|
| BR-ACC-001 | Revenue is recognised once, on the approved business day, to the mapped accounts; settlement movements are balance-sheet only. | DP-CTX-003 (reserved for WP 0.4); CAP-ACC-002 |
| BR-ACC-002 | Every posting carries a durable idempotency identity; retries resolve to the original outcome; duplicate posting is a P0 defect. | INV-ACC-1; CAP-INT-007 |
| BR-ACC-003 | Uncertain external outcomes (timeout after submission) are resolved by reconciliation, never by blind re-submission. | INV-ACC-2 |
| BR-ACC-004 | Control accounts (guest ledger, deposits, AR, tax, cash-in-transit) reconcile daily; unreconciled differences block financial close reporting, not operational operation. | CAP-ACC-009 |
| BR-ACC-005 | Reversals and corrections are additive linked documents; posted journals are never edited. | INV-ACC-4 |
| BR-ACC-006 | Tax is calculated from the effective rule for each business date; tax liabilities are never netted against revenue in reporting. | INV-FOL-5; CAP-ACC-005 |
| BR-ACC-007 | Period close freezes the period; reopen follows governance akin to day reopen (BR-NAU-005) with stronger authority. | CAP-ACC-010 |

## 12. Integration and platform (INT/PLT)

| ID | Rule | Basis / dependencies |
|---|---|---|
| BR-INT-001 | Every inbound external effect is deduplicated by namespaced reference; duplicates are absorbed idempotently with evidence. | ADR-004; CAP-PLT-010 |
| BR-INT-002 | Every outbound integration retries with bounded backoff, dead-letters after the policy limit, and surfaces failures to operations — never fails silently. | CAP-INT-001 |
| BR-INT-003 | Integration failures reconcile: volume and value totals are compared per interface per day; unexplained differences are owned exceptions. | CAP-ACC-009 |
| BR-INT-004 | Provider-specific data never leaks into domain rules; adapters translate. | ADR-001 |
| BR-PLT-001 | Authority limits are configuration with effective dates; changes require the authority they govern (nobody widens their own limit). | CAP-PLT-002; charter SoD |
| BR-PLT-002 | Property/company scope is enforced on every access path including reports, exports and AI tools; a missing scope is a bug of the highest severity. | ADR-002; CAP-PLT-003 |
| BR-PLT-003 | Break-glass access is time-boxed, alerts security and finance, and is reviewed within 24 hours. | CAP-PLT-015 |
| BR-PLT-004 | No principal may approve their own exception where maker–checker is configured; emergency single-person paths exist only as configured, flagged and reviewed. | SoD (personas §4) |
| BR-PLT-005 | Configuration changes capture actor, reason, effective date and version; transactions capture the resolved version they used. | ADR-003 |

## 13. Reporting and statistics (RPT)

| ID | Rule | Basis / dependencies |
|---|---|---|
| BR-RPT-001 | Report figures reconcile to source transactions and drill down; a report that cannot reconcile is a defect. | CAP-RPT-012 |
| BR-RPT-002 | Available room nights for a date = sellable capacity (all rooms minus OOO; OOS rooms included). | BR-AVL-003 |
| BR-RPT-003 | Occupied room nights = rooms with an active stay on the night, excluding house use, including comps; [OQ-036 default; to be confirmed by hotel]. | Comp/house policy |
| BR-RPT-004 | Occupancy % = occupied room nights ÷ available room nights for the period. | — |
| BR-RPT-005 | ADR = room revenue ÷ occupied room nights (comps included at zero revenue; ADR effect stated wherever reported). | — |
| BR-RPT-006 | RevPAR = room revenue ÷ available room nights. | — |
| BR-RPT-007 | Total revenue per available room (TRevPAR) uses total operating revenue including F&B; definition applied consistently. | POS domains |
| BR-RPT-008 | Statistical definitions are versioned configuration; changing a definition versions reports and never silently restates history. | ADR-003; CAP-RPT-003 |

## 14. Rules awaiting decisions

The following rules contain `[OQ-nnn]` defaults that must be confirmed before ACCEPTED: BR-RTM-005/BR-FO-004/BR-FO-005 (child age, early/late pricing, early departure; OQ-035), BR-FO-001 (identity capture; OQ-019), BR-FOL-009/010/011 (deposit, no-show, cancellation; OQ-012), BR-CSH-002 (variance tolerance; OQ-037), BR-GRP-003 (attrition; OQ-017), BR-HSK-002 (inspection policy; OQ-034), BR-HSK-005 (minibar; OQ-014), BR-RPT-003 (comp/house; OQ-036). Each default is chosen to match common hotel practice and Nigerian-market pilot expectations; all remain PROPOSED.

## 15. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial business-rules catalogue (15 domains, 101 rules) issued with WP 0.3 | PROPOSED |