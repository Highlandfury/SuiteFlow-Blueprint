---
doc-id: WF-CATALOGUE
title: Workflow Catalogue
status: PROPOSED
version: 0.2
date: 2026-09-23
owner: Hospitality Domain Architect (drafted); operational validation by the acting operations authority (OQ-003 closed)
applies-to: full enterprise target; pilot workflows marked
depends-on: [ARCH-STATES, ARCH-RULES, PROD-PERSONAS]
---

# Workflow Catalogue

## 1. Purpose and method

A **workflow** is how work actually moves between people and the system across time. State machines describe entity lifecycles; workflows describe human operations, including the handovers, failure modes and controls between shifts.

Each workflow below states: trigger, actors, steps (human and system), controls, failure modes, financial consequence, and linked capabilities/state machines/rules. Workflows marked **[P]** are pilot-release scope.

The **operational realism test** (charter §10) is applied to every workflow: who performs it, when, what they see, what they need, what can go wrong, who overrides, what is audited, what happens financially, what happens afterwards.

Detailed screen-level operational design (swim-lanes, information hierarchy, keyboard flows) is produced with the UX architecture (WP 0.6) against these workflows; they are the acceptance source for it.

## 2. Reservations

### WF-RSV-001 — Create a reservation **[P]**

- **Trigger:** enquiry by phone, email, walk-in or channel.
- **Actors:** reservations agent (or front desk); duty manager for overrides.
- **Steps:** identify guest (search/deduplicate/create) → check availability for dates/party → present eligible rates (BR-RTM-001/002) → capture guarantee/deposit (BR-FOL-008) → confirm terms and capture acceptance evidence → system allocates inventory transactionally (BR-AVL-001) → confirmation issued (CAP-RSV-020).
- **Controls:** eligibility and restriction enforcement (BR-AVL-004); duplicate-guest warning; deposit schedule correctness; concession limits for discounts (BR-FOL-004).
- **Failure modes:** availability race (retry with clear message); rate ineligibility; duplicate guest created under pressure (mitigated by search-first UX, review queue).
- **Financial consequence:** none yet except deposit receipt (liability, BR-FOL-008).
- **Links:** CAP-RSV-002/014/015; SM-RESERVATION #1; SM-DEPOSIT #1.

### WF-RSV-002 — Modify or cancel a reservation **[P]**

- **Trigger:** guest change request.
- **Actors:** agent; supervisor for penalty waivers.
- **Steps:** retrieve reservation → propose change with repricing preview → validate availability/restrictions → capture acceptance → apply amendment (recorded evidence) → if cancellation: apply policy snapshot, dispose of deposit per BR-FOL-009, release allocations → issue revised confirmation/cancellation evidence.
- **Controls:** repricing determinism (BR-RTM-006/007); penalty waivers require authority with reason; release is evidenced (BR-AVL-006).
- **Failure modes:** modified dates unavailable (partial amendment via split stay or waitlist); disputed terms (policy snapshot is the arbiter).
- **Financial consequence:** deposit refund/forfeit per policy; penalty posting where applicable.
- **Links:** CAP-RSV-003/004; SM-RESERVATION #5/#10; SM-DEPOSIT #3/4.

### WF-RSV-003 — No-show processing **[P]**

- **Trigger:** business-date cutoff passes without arrival.
- **Actors:** night audit (system-assisted); duty manager for disputes.
- **Steps:** system lists unresolved arrivals → operator concludes each with reason → no-show determination applies guarantee terms (BR-FOL-010) → charge posted or released → deposit disposition → allocations released → report produced.
- **Controls:** guarantee evidence checked; disputed determinations require manager authority; reinstate path exists (SM #9) with reversal, not deletion.
- **Failure modes:** late arrivals after determination (reinstate + reversal); guest with valid late-arrival note (documented).
- **Links:** CAP-RSV-005; SM-RESERVATION #6; SM-INVENTORY #2/3.

### WF-RSV-004 — Group booking: block to master account **[P — OQ-017 (closed)]**

- **Trigger:** group/event enquiry with contracted terms.
- **Actors:** sales, revenue (rates/inventory), finance (credit/deposit), front desk at arrival, night audit for billing.
- **Steps:** hold space/rooms (TENTATIVE) → contract terms recorded (rate agreement, cutoff, attrition/deposit) → DEFINITE with deposit schedule → rooming list exchanged → reservations created/linked → cutoff releases leftover (BR-GRP-002) → arrivals check in under block rate → master account receives routed charges (BR-GRP-005) → final bill settled/transferred → block completed with pickup/wash report.
- **Controls:** pickup vs block (BR-GRP-001); attrition calculation evidence (BR-GRP-003); credit exposure at transfer (BR-CRP-001); master folio invariant.
- **Failure modes:** rooming list late (walk-in-style creation at desk); attrition dispute (calculation evidence is the arbiter); credit breach at checkout (escalation, not silent transfer).
- **Links:** CAP-GRP-001…008; SM-GROUP-BLOCK; SM-CORPORATE-CONTRACT #4; SM-FOLIO #3.

## 3. Front office

### WF-FO-001 — Arrival and check-in **[P]**

- **Trigger:** guest arrives (or pre-registration completed earlier).
- **Actors:** front desk agent; supervisor for overrides; housekeeping notified via readiness states.
- **Steps:** locate reservation → verify readiness and assignment (BR-FO-001) → capture identity/registration per policy → verify credit/payment position → confirm rate/charges briefly and capture any acceptance → create stay, open folio group, issue keys → handover to guest services.
- **Controls:** gate set blocking with authority-based overrides (all flagged); registration evidence immutable; key validity tied to stay.
- **Failure modes:** room not ready (queue/hold with service recovery); declined payment (alternative guarantee, supervisor decision); walk (BR-FO-006).
- **Financial consequence:** folio open; deposit application possible; no revenue yet.
- **Links:** CAP-FO-002/003/004/011; SM-STAY #1; BR-FO-001.

### WF-FO-002 — In-house service request **[P]**

- **Trigger:** guest request (front desk, phone, messaging).
- **Actors:** agent → department (housekeeping/maintenance/F&B) → back to guest with closure.
- **Steps:** capture request with category, priority, deadline → route to owning department → department executes and evidences → closure confirmed to guest and record.
- **Controls:** SLA visibility; ageing report; requests never silently closed.
- **Failure modes:** inter-department handover loss (single record with owner + reminders).
- **Links:** CAP-FO-012; SM-HOUSEKEEPING-TASK; SM-WORK-ORDER #1.

### WF-FO-003 — Room move **[P]**

- **Trigger:** guest preference, maintenance issue, operational need.
- **Actors:** agent/supervisor; housekeeping; engineering if OOO-related.
- **Steps:** check target room suitability → confirm availability and readiness → create move with reason → update keys/folio → if OOO-driven, coordinate repair and return-to-service.
- **Controls:** do-not-move honouring; type-change pricing rules (BR-FO-007); OOO conflicts surfaced before assignment.
- **Financial consequence:** possible rate adjustment or compensation (BR-FOL-004).
- **Links:** CAP-RSV-009; SM-ROOM-ASSIGNMENT #1; SM-STAY #2.

### WF-FO-004 — Checkout and settlement **[P]**

- **Trigger:** guest departure (or express checkout).
- **Actors:** front desk; cashier; finance for transfers.
- **Steps:** verify consumption posted (minibar/laundry/F&B) → present folio → settle or transfer per responsibility (BR-FOL-013/014) → issue invoice/receipt per OQ-011 (closed) → checkout transition; keys revoked; room release to housekeeping → discrepancy check if physical state differs.
- **Controls:** folio invariant blocking (BR-FO-002); pending-clearance flagging; credit check at transfer; direct-bill requires eligibility or approval.
- **Failure modes:** disputed charges (adjustment path with authority); transfer declines (alternative settlement documented); late minibar post (governed correction).
- **Financial consequence:** settlement/transfer; revenue cleared to books at close.
- **Links:** CAP-FO-008; CAP-FOL-012; SM-FOLIO #2/3/4; SM-STAY #4.

### WF-FO-005 — Early departure and stay extension **[P]**

- **Trigger:** guest changes stay duration.
- **Actors:** agent; supervisor for exceptions.
- **Steps:** extension: availability + rate + credit re-check (BR-FO-003) → amend, reprice, reissue → early departure: apply plan rule (BR-FO-005) → release remaining nights (with evidence) → adjust routing/expectations.
- **Controls:** authority for penalty waivers; housekeeping and revenue visibility.
- **Links:** CAP-FO-010/019; SM-STAY #3.

### WF-FO-006 — Shift handover **[P]**

- **Trigger:** shift change.
- **Actors:** outgoing and incoming duty staff.
- **Steps:** system produces handover pack (arrivals pending, VIPs, unrest, credit flags, open discrepancies, unclosed sessions, maintenance holds, VIP/guest promises) → outgoing reviews and annotates → incoming acknowledges → unresolved items remain owned and visible.
- **Controls:** acknowledgement recorded; critical items cannot be dropped from the pack.
- **Links:** CAP-FO-016; CAP-NAU-010.

## 4. Housekeeping and maintenance

### WF-HK-001 — Daily room assignment and cleaning **[P]**

- **Trigger:** board cycle (start of shift) and state changes during the day.
- **Actors:** supervisor (assign, balance), attendants (execute), inspectors (verify).
- **Steps:** supervisor reviews board → assignments balanced by credits/priority → attendant works task list on mobile → updates room state with evidence → rush/VIP prioritised → supervisor inspects where required → rooms released READY (BR-HSK-002) → productivity captured.
- **Controls:** readiness gate (broken only by authorised override); DND handling; discrepancy reporting.
- **Failure modes:** no-show attendant (rebalance); blocked room (PAUSED with reason); chronic inspection failures (quality case).
- **Links:** CAP-HSK-001…005/009; SM-HOUSEKEEPING-TASK; SM-ROOM.

### WF-HK-002 — Room discrepancy resolution **[P]**

- **Trigger:** housekeeping reports occupancy/condition differing from system.
- **Actors:** attendant → supervisor → front office → security if needed.
- **Steps:** discrepancy recorded → immediate triage (is someone in the room?) → front office reconciles records → resolution recorded with evidence → unresolved items on close checklist (BR-HSK-004).
- **Controls:** no silent resolution; security involvement for unauthorised occupancy.
- **Links:** CAP-HSK-010; SM-ROOM; BR-NAU-001.

### WF-MT-001 — Work order to verified completion **[P — OQ-022 (closed)]**

- **Trigger:** fault report (guest, staff, inspection, PM schedule).
- **Actors:** reporter, chief engineer (triage/assign), technician, supervisor (verify).
- **Steps:** report captured → triaged with priority/SLA → assigned to trade → work executed with parts/evidence → verified → closed → room saleability restored where applicable (BR-MNT-001).
- **Controls:** safety-critical override; hold reasons visible; verification separation where staffing allows.
- **Failure modes:** parts unavailable (ON_HOLD with expected resolution); repeat failure (asset history escalation, capital review).
- **Links:** CAP-MNT-001…003/007/008; SM-WORK-ORDER; SM-ROOM #7/8.

## 5. Finance

### WF-FIN-001 — Cashier session **[P]**

- **Trigger:** shift start/end of a cash-handling role.
- **Actors:** cashier, supervisor/finance reviewer.
- **Steps:** open with evidenced float → take payments (cash/POS/transfer/cheque) with method-faithful recording → payouts within limits → drops as needed → close: count by method → variance computed → within tolerance: close; beyond: PENDING_REVIEW → independent approval with reason and accounting treatment → session closed.
- **Controls:** one open session per till; maker ≠ checker on variance; repeated-variance escalation (BR-CSH-003/006).
- **Links:** CAP-CSH-001…007; SM-CASHIER-SESSION; BR-CSH-001…007.

### WF-FIN-002 — Payment settlement and reconciliation **[P]**

- **Trigger:** POS batch, bank statement, cheque presentation.
- **Actors:** finance (reconciliation), front desk (corrections).
- **Steps:** capture provider evidence (batch/statement) → match against payments (amount, reference, date) → matched items move to CLEARED → unmatched become reconciliation cases with owners → dishonours reverse with folio/AR consequences → daily reconciliation report.
- **Controls:** no manual "mark cleared" without evidence; unmatched ageing thresholds; reversal authority.
- **Links:** CAP-CSH-010; CAP-ACC-008/009; SM-PAYMENT #4/5; BR-CSH-004.

### WF-FIN-003 — Deposit, refund and forfeiture **[P]**

- **Trigger:** reservation guarantee, cancellation, no-show, dispute resolution.
- **Actors:** front desk (receipt/apply), supervisor (refund/forfeit within limits), finance (above limits, bank details).
- **Steps:** deposit receipt verified → held as liability → applied at close or checkout → refund request → approval per limits → payment with method fidelity and verification → forfeiture only with policy basis, authority and communication evidence.
- **Controls:** deposit conservation (BR-FOL-008/009); refund limits; bank-change fraud checks (BR-FOL-012).
- **Links:** CAP-FOL-008/009; SM-DEPOSIT; SM-REFUND; WF-FIN-005.

### WF-FIN-004 — Direct bill and receivables **[P]**

- **Trigger:** corporate/agent booking or checkout transfer.
- **Actors:** finance, sales, front desk.
- **Steps:** account with billing instructions exists → routing applies at posting → exposure checked at transfer (BR-CRP-001) → transfer creates AR document (idempotent) → AR ageing and statements → collections/dunning per terms → write-off only with authority (BR-CRP-006).
- **Controls:** credit limits block automatically; overdue thresholds trigger holds; transfer without eligibility requires recorded approval.
- **Links:** CAP-CRP-004/005; CAP-ACC-004; SM-FOLIO #3; SM-CORPORATE-CONTRACT #2.

### WF-FIN-005 — Guest refund / goodwill adjustment **[P]**

- **Trigger:** complaint resolution, billing dispute, service failure.
- **Actors:** front desk/supervisor (limits), finance (above limits).
- **Steps:** identify original payment/charge → choose instrument (refund for money back; adjustment for charge reduction) → authority check → execute with reason → guest communication → reporting (BR-INA-002).
- **Controls:** refunds ≤ cleared funds; maker–checker above top band; patterns reviewed monthly.
- **Links:** CAP-FOL-009; SM-REFUND.

## 6. Night audit

### WF-NA-001 — Night audit and close **[P]**

- **Trigger:** end of operating day (cutoff configured; OQ-008 (closed)).
- **Actors:** night auditor (execution), supervisor/manager (exception approvals), on-call (failures), income auditor (next morning).
- **Steps:** pre-close checklist (unposted charges, open sessions, pending departures, discrepancies, queues) → resolve or escalate each blocker (BR-NAU-001; ADR-006 §3) → initiate close → nightly postings run idempotently (BR-NAU-003) → control totals reconcile per ADR-006 §3 (unexplained differences block; only policy-defined cases with owner and due date may remain) → day CLOSED, next day OPENED atomically → day reports generated and distributed → handover notes for morning.
- **Controls:** blocking checklist; idempotent resumable runs; control-total reconciliation; alerts around the close window (BR-REL-005).
- **Failure modes:** mid-close failure (recoverable resume, no partial advancement); control mismatch (exception with owner, day may not falsely certify); power/network loss (checkpointed recovery).
- **Links:** CAP-NAU-001…010; SM-NIGHT-AUDIT; BR-NAU-001…006.

### WF-NA-002 — Close failure recovery **[P]**

- **Trigger:** failed close run (system or control failure).
- **Actors:** night auditor + on-call engineer; finance notified.
- **Steps:** alert raised → auditor retries resume → repeated failure escalates to engineering with correlation evidence → once resolved, close resumes from checkpoint without duplicate postings → incident noted for review.
- **Controls:** idempotency proof; no manual patching of financial data; post-incident review for financial impact.
- **Links:** INV-NAU-2; SM-NIGHT-AUDIT #4; BR-INT-002.

### WF-NA-003 — Day reopen and remediation **[P]**

- **Trigger:** discovered error after close (missing posting, wrong charge, discrepancy).
- **Actors:** finance controller (authority), income auditor (evidence), operational staff (correction execution).
- **Steps:** request with reason and impact assessment → approval (BR-NAU-005) → day REOPENED → corrections posted on the remediation date per policy → control totals restated (versioned) → day RECLOSED → audit report updated with restatement note.
- **Controls:** authority; additive corrections only; restatement versioning; report distribution.
- **Links:** CAP-NAU-009; ADR-003; SM-NIGHT-AUDIT #5/6.

### WF-NA-004 — Income audit certification **[P]**

- **Trigger:** business day closed.
- **Actors:** income auditor; finance controller (escalation).
- **Steps:** verify revenue vs operational sources → verify cash and settlement reconciliation → review adjustments/voids/comps by user and band → verify deposit movements and taxes → record findings with owners → certify pass/hold (BR-INA-001) → management/finance distribution.
- **Controls:** full-population controls (BR-INA-001); pattern escalation (BR-INA-002); findings survive day advancement.
- **Failure modes:** held day (reported to finance; corrective path set); recurring findings (control review).
- **Links:** CAP-INA-001…008; SM-NIGHT-AUDIT #7.

## 7. Coverage note

This catalogue covers the pilot-critical workflows and the main enterprise patterns. Additional workflows (outlet operations, sales pipeline, channel distribution, CRM, purchasing) are added when their phases begin (D10+), under the same format and realism test. Screen-level design for every workflow above is produced in WP 0.6.

## 8. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial workflow catalogue (22 workflows) issued with WP 0.3 | PROPOSED |
| 0.2 | 2026-09-23 | FIN-04 alignment: WF-NA-001 close steps reference the canonical blocking table (ADR-006 §3) | PROPOSED |