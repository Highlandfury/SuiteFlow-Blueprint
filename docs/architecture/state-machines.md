---
doc-id: ARCH-STATES
title: Target State Machines
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Domain Architect (drafted); Hotel Operations + Product Owner (approval)
applies-to: full enterprise target
depends-on: [ARCH-DOMAIN, ADR-003, ARCH-RULES]
---

# Target State Machines

## 1. Purpose and method

Every entity whose life changes over time is governed by exactly one state machine. A state change that is not a defined transition is a defect, not a shortcut. This document defines, for each machine: states, transitions, actor and authority, preconditions and validations, database and financial effects, events and notifications, and failure/exception handling.

**Transition attributes** (mandate §14): source, target, actor, permission, preconditions, validation, database effect, financial effect, notification, audit event, rollback, exception. Tables use a compact form; §2 states the rules that apply to **every** transition so tables only state specifics.

**Guarantees**

- A state is never written without an executed transition (no silent edits). State and transition record are written atomically.
- A transition is legal, permitted, precondition-satisfied and evidenced, or it does not happen.
- Rejected attempts are themselves events where they matter operationally (for example, a failed check-in gate) and are always observable.

## 2. Common transition contract (applies to every machine)

| Attribute | Rule |
|---|---|
| Actor | A named authenticated principal (human user or service identity). Bulk/system transitions run as explicit service identities. |
| Permission | Checked server-side against role + authority limits + property/company scope. Authority limits are effective-dated configuration. |
| Preconditions | All precondition checks run inside the transition transaction; a failing check aborts the transition with a deterministic, user-actionable error. |
| Validation | Domain invariants (`ARCH-DOMAIN` §invariants) are enforced; business rules (`ARCH-RULES`) are the source for calculations; resolved configuration versions are captured as evidence. |
| Database effect | State, transition record (actor, occurred-at, business date, reason where required, before/after, correlation) in the same transaction; outbox events written transactionally (ADR-001 §5.2). |
| Financial effect | Only through Guest Financials or Accounting Interface services. Financial effects carry business date, idempotency identity (for externally-triggered or repeatable commands) and posting links. |
| Notification | Configurable per property: operational (boards, tasks), financial (cashier/close alerts), guest (communications). Delivery failures never block the transition and are retried with evidence. |
| Audit event | Every transition emits an audit event with the transition record identity; immutable, append-only; includes reason for all override/authority transitions. |
| Rollback | By compensating transition only; no state history is edited or deleted. Errors before commit abort atomically. Post-commit discovery uses the machine's defined correction path (void, reversal, governed reopen). |
| Exception | Defined per machine below; exceptions never silently bypass a control — they either block, require authority, or produce a flagged condition visible to audit. |
| Concurrency | Mutable aggregates carry a version stamp; conflicting transitions fail deterministically and are retry-safe. Uniqueness constraints defend structural invariants (one active stay per room, no duplicate allocation). |

## 3. Core stays lifecycle

### SM-RESERVATION

| | |
|---|---|
| Entity | Reservation (aggregate root) |
| Lifetime | From creation to terminal state; retains history forever |
| States | `TENTATIVE` (hold awaiting guarantee), `CONFIRMED`, `IN_HOUSE` (derived: any room checked in), `COMPLETED` (all rooms departed), `CANCELLED`, `NO_SHOW`, `EXPIRED` (tentative hold lapsed) |

| # | From → To | Trigger | Actor | Authority / preconditions | Effects | Exceptions |
|---|---|---|---|---|---|---|
| 1 | — → CONFIRMED | Booking with guarantee or accepted non-guaranteed policy | Reservations agent, front desk | Availability + rate eligibility + restrictions satisfied + guest resolvable + guarantee policy met (INV-RSV-1…5, 8); property scope | Creates Reservation, rooms, guests, allocations, number, deposit schedule; `reservation.confirmed` | Partial availability: booking rejected with the failing date/type named. Duplicate guest warning is advisory, duplicate booking detection is blocking |
| 2 | — → TENTATIVE | Optional hold without guarantee (if property enables) | Agent | Property policy allows holds; expiry configured | Allocation held with expiry; `reservation.tentative` | Hotel may disable holds entirely (configuration) |
| 3 | TENTATIVE → CONFIRMED | Guarantee received / waiver approved | Agent / supervisor | Guarantee evidence recorded (INV-RSV-8); hold not expired | Allocation retained; `reservation.confirmed` | Waiver requires authority; recorded as override |
| 4 | TENTATIVE → EXPIRED | Hold expiry job | System | Expiry reached; no guarantee | Allocation released; `reservation.expired` | None (job resumable, idempotent) |
| 5 | CONFIRMED → CANCELLED | Cancellation | Agent (within policy) / supervisor (overrides) | Cancellation terms snapshot applied (INV-RSV-6); deposit disposition decided (BR-FOL-009) | Releases remaining allocations; resolves deposit (refund/forfeit/retain per policy with authority); `reservation.cancelled` | Penalty waivers require authority and reason; partial-stay cancellations split rooms as amendments first |
| 6 | CONFIRMED → NO_SHOW | Business-date no-show determination | Night audit / front desk with authority | Business date crossed; no check-in; guarantee terms applied | Charges no-show per terms (folio/AR path per BR); releases remaining allocations; resolves deposit; `reservation.no_show` | Disputed no-show is corrected by reinstate + reversal (governed), never by deletion |
| 7 | CONFIRMED → IN_HOUSE | First room checks in (derived transition recorded) | System | Any ReservationRoom enters CHECKED_IN | Status projection + event `reservation.in_house` | — |
| 8 | IN_HOUSE → COMPLETED | Last room checks out | System | All rooms checked out; folio outcomes per INV-FO-3 | `reservation.completed` | — |
| 9 | CANCELLED → CONFIRMED | Reinstatement | Supervisor with authority | Availability re-obtainable; policy allows; reason recorded | New allocations created; `reservation.reinstated` | Original cancellation evidence retained |
| 10 | any → any (amendment) | Dates, rooms, guests, rate, routing changes | Agent / supervisor per change type | Restrictions/availability re-validated; repricing deterministic | Amendment record + adjusted allocations; events as applicable | Repricing that increases value requires authority where policy sets limits |

### SM-RESERVATION-ROOM

| | |
|---|---|
| States | `PENDING` (unassigned), `ASSIGNED`, `CHECKED_IN`, `CHECKED_OUT`, `CANCELLED`, `NO_SHOW` |

| # | From → To | Trigger | Actor | Authority / preconditions | Effects | Exceptions |
|---|---|---|---|---|---|---|
| 1 | — → PENDING | Created with reservation | Agent | INV-RSV-1…3 | Allocation(s) for room-nights; `room.added` | — |
| 2 | PENDING → ASSIGNED | Room assignment | Agent / auto-assign | Room sellable, correct type, DND honoured, no conflicting stay (INV-RSV-7, INV-FO-1) | RoomAssignment record; `room.assigned` | Type mismatch / dirty room requires authority override with reason, recorded |
| 3 | ASSIGNED → PENDING | Unassign | Agent | Room not yet occupied | Assignment released; `room.unassigned` | Pre-arrival only; in-house uses room move (SM-ROOM-ASSIGNMENT) |
| 4 | PENDING/ASSIGNED → CHECKED_IN | Check-in | Front desk | Full gate set: readiness, identity/registration, credit, payment policy, arrival date, restrictions (BR-FO-001); creates Stay | Stay created (SM-STAY); room occupancy changes; folio group/windows created; `stay.checked_in` | Gate failures block; overrides require authority and are flagged to audit; policy failures are never advisory-only |
| 5 | CHECKED_IN → CHECKED_OUT | Checkout | Front desk | Folio invariant: settled or transferred (INV-FO-3); keys revoked | Stay ends; room released to housekeeping; `stay.checked_out` | Balance left in-house requires authority per policy and is reported as exception |
| 6 | PENDING/ASSIGNED → CANCELLED | Line cancellation | Agent / supervisor | Policy; allocations released | `room.cancelled` | — |
| 7 | PENDING/ASSIGNED → NO_SHOW | No-show determination | Night audit / desk | Guarantee terms | Charge per BR; allocation released | — |

### SM-ROOM (readiness and saleability)

Room has two independent state dimensions plus derived occupancy; a single "room status" field is prohibited (CAP-PM-008).

| Machine | States |
|---|---|
| Readiness (housekeeping) | `VACANT_DIRTY` → `CLEANING` → `CLEAN` → (`INSPECTED` where policy requires) → `READY`; plus `BLOCKED` (DND/refused-access), `OCCUPIED_DIRTY`, `OCCUPIED_CLEAN` |
| Saleability | `SELLABLE`, `OOO`, `OOS`, `HELD_FOR_BLOCK` |

| # | Machine | From → To | Trigger | Actor | Authority / preconditions | Effects & exceptions |
|---|---|---|---|---|---|---|
| 1 | Readiness | VACANT_DIRTY → CLEANING | Task start | Attendant | Assigned task exists (or supervisor assignment) | Task in progress; `room.readiness.changed` |
| 2 | Readiness | CLEANING → CLEAN | Task complete | Attendant | Completion evidence per policy | Awaiting inspection if policy requires; else READY path |
| 3 | Readiness | CLEAN → INSPECTED → READY | Inspection pass | Supervisor | Inspector ≠ cleaner where policy requires (self-inspection exception only with recorded authority) | Room becomes sellable input; `room.readiness.changed` |
| 4 | Readiness | CLEAN → VACANT_DIRTY | Inspection fail | Supervisor | Findings recorded | Task reopened; failure reason recorded; repeat-failure escalation |
| 5 | Readiness | any → BLOCKED | DND / access refused | Attendant / supervisor | Task evidence | Room not sellable until unblocked; visible on board; discrepancy check remains open |
| 6 | Saleability | SELLABLE → OOS | Short hold (maintenance or other) | Supervisor / maintenance | Reason, expected return, authority per policy | Inventory reduced (capacity, not allocation); `room.saleability.changed`; existing assignments reviewed |
| 7 | Saleability | SELLABLE/OOS → OOO | Out-of-order | Supervisor with authority | Reason, duration, impact check on committed reservations | Sellable capacity reduced; conflicts with assignments require relocation workflow; `room.saleability.changed` |
| 8 | Saleability | OOO/OOS → SELLABLE | Return to service | Supervisor | WorkOrder verified (INV-SVC-2); readiness READY/CLEAN per policy | Capacity restored; availability projection refreshed |
| 9 | Saleability | SELLABLE → HELD_FOR_BLOCK | Block hold | System (block) | Block allocation active | Never sold outside the block; release at cutoff |

### SM-ROOM-ASSIGNMENT

| | |
|---|---|
| States | `ACTIVE`, `MOVED` (superseded by another assignment), `RELEASED` (unassigned pre-arrival), `ENDED` (stay completed) |

| # | From → To | Trigger | Actor | Authority / preconditions | Effects | Exceptions |
|---|---|---|---|---|---|---|
| 1 | — → ACTIVE | Assign / move executes | Agent / supervisor | New room qualifies (type, readiness, DND, no conflict); stay/reservation state allows | Previous assignment (if any) → MOVED; audit `room.moved` with reason; occupancy updates atomically | Type/readiness override requires authority; do-not-move violation is blocked outright |
| 2 | ACTIVE → RELEASED | Unassign | Agent | Not occupied | Availability restored | — |
| 3 | ACTIVE → ENDED | Checkout | System | Stay ends | Readiness path triggered | — |

### SM-STAY

| | |
|---|---|
| States | `ACTIVE` (checked in), `DUE_OUT` (derived from business date), `CHECKED_OUT`, `VOIDED` (erroneous check-in cancelled before night audit), `RELOCATED` (guest walked to another property) |

| # | From → To | Trigger | Actor | Authority / preconditions | Effects | Exceptions |
|---|---|---|---|---|---|---|
| 1 | — → ACTIVE | Check-in transition (see SM-RESERVATION-ROOM #4) | Front desk | Full check-in gates (BR-FO-001) | Stay + folio group; occupancy; keys; `stay.checked_in` | — |
| 2 | ACTIVE → ACTIVE | Room move | Agent / supervisor | New room valid (SM-ROOM-ASSIGNMENT #1) | Assignment MOVED; folio unaffected; `stay.moved` | Moves across room types follow BR pricing rules |
| 3 | ACTIVE → ACTIVE | Extension | Agent | Availability for added nights; rate/credit re-check (BR-FO-003) | Allocation + nights added; repricing recorded; `stay.extended` | Extension beyond credit requires approval |
| 4 | ACTIVE → CHECKED_OUT | Checkout | Front desk | INV-FO-3 | Folio closed/transferred; room released; `stay.checked_out` | — |
| 5 | ACTIVE → VOIDED | Cancel erroneous check-in | Supervisor | Before night audit or with governed correction after; reason mandatory | Folio void path (INV-FOL-2), allocations restored, room returned; `stay.voided` | After night audit: governed remediation only (SM-NIGHT-AUDIT reopen path) |
| 6 | ACTIVE → RELOCATED | Guest walk | Supervisor + finance | Relocation record; destination confirmed; cost approval per policy | Stay ends with relocation evidence; cost posting per BR; `stay.relocated` | Repeated relocation is a revenue/quality report item |

## 4. Financial lifecycle

### SM-FOLIO

| | |
|---|---|
| Entity | Folio (aggregate root; one per responsibility container) |
| States | `OPEN`, `SETTLED` (balance zero), `TRANSFERRED` (balance moved to AR), `CLOSED` (locked after checkout/event end), `VOID` (opened in error, no financial effect), `CORRECTION_OPEN` (governed post-stay correction window; distinct from a business-day reopen) |

| # | From → To | Trigger | Actor | Authority / preconditions | Effects | Exceptions |
|---|---|---|---|---|---|---|
| 1 | — → OPEN | Stay/group/event/account activation | System | Owner entity active (stay checked in, group active, event opened) | Windows created per routing; `folio.opened` | — |
| 2 | OPEN → SETTLED | Full settlement | Front desk / cashier | INV-FOL-1 holds with zero balance; all payments in valid final state (no pending clearance unless policy allows zero-balance-credit) | `folio.settled` | Pending POS/cheque with zero balance blocks closure unless policy authorises "settled with clearance open" (flagged, tracked) |
| 3 | OPEN → TRANSFERRED | Direct-bill transfer | Supervisor / finance | Credit eligibility or approved exception (INV-FOL-9); responsibility documented | CityLedgerTransfer → Accounting Interface posting; balance removed from guest ledger responsibility; `folio.transferred` | Partial transfer allowed (per window/charge set); remainder stays OPEN |
| 4 | SETTLED/TRANSFERRED → CLOSED | Checkout or event completion | Front desk / system | INV-FO-3; no pending dispute on the folio | Folio locked; keys revoked; `folio.closed` | Folio with open DisputeCase closes only with flag recorded |
| 5 | CLOSED → CORRECTION_OPEN → OPEN | Post-stay correction | Finance with authority | Reason, authority, within policy window or on reopened accounting date; remediation evidence | Corrections as linked items; `folio.correction_opened` | Post-close corrections are never silent; they surface in audit reports |
| 6 | — → VOID | Erroneous folio creation | Supervisor | No financial items posted | Folio voided with reason; `folio.voided` | Folio with items must be settled/transferred/reopened, not voided |

### SM-FOLIO-ITEM

Items are immutable facts with a lifecycle expressed by linked records, never by mutation.

| | |
|---|---|
| States | `ACTIVE`, `REVERSED` (voided by a linked reversal), `ADJUSTED` (allowance applied referencing the original), `TRANSFERRED` (moved across windows/folios by linked transfer) |

| # | From → To | Trigger | Actor | Authority / preconditions | Effects | Exceptions |
|---|---|---|---|---|---|---|
| 1 | — → ACTIVE | Posting | Service / user per BR posting authority | Amount, classification, responsible party resolved, business date open (INV-FOL-3/4/5) | Item + evidence + `folio.item_posted` | Duplicate posting prevention by idempotency identity for automatic postings |
| 2 | ACTIVE → REVERSED | Void (error) / reversal | Supervisor / finance per limits | Within window and authority; reason mandatory; original not settled into closed books | Linked reversal item; `folio.item_reversed` | After day close: remediation path on reopened date; never edit original |
| 3 | ACTIVE → ADJUSTED | Allowance/comp | Supervisor with limits | Reason, authority per BR-FOL-004/006 | Linked adjustment item; `folio.item_adjusted` | Above limit escalates for approval (maker–checker) |
| 4 | ACTIVE → TRANSFERRED | Cross-folio transfer | Agent / supervisor | Target window/folio open, responsibility authorised | Linked transfer pair; `folio.item_transferred` | Transfers to AR for balances follow SM-FOLIO #3 |
| 5 | ACTIVE → (unchanged) | Tax recalculation request | — | Prohibited: computed tax is part of the original item family | A new correcting item is required | Attempts to edit tax in place are blocked by design |

### SM-PAYMENT

| | |
|---|---|
| Entity | Payment (aggregate root) |
| States | `RECEIVED`, `PENDING_CLEARANCE` (POS captured / cheque deposited / transfer awaiting verification), `CLEARED`, `REVERSED` (dishonour/decline), `VOIDED` (same-day recorded-in-error before session close), `CHARGEBACK` (card dispute after clearing) |

| # | From → To | Trigger | Actor | Authority / preconditions | Effects | Exceptions |
|---|---|---|---|---|---|---|
| 1 | — → RECEIVED | Payment taken | Cashier / front desk | Session open; method data captured; identity of payer recorded | Payment line on folio; `payment.received` | Cash is RECEIVED→CLEARED at session count; POS/cheque/transfer stay pending until evidence |
| 2 | RECEIVED → CLEARED | Cash counted / instant confirmation | Cashier session close / system | Count matches or variance handled | Clearing evidence; `payment.cleared` | Cash variance posts through session variance path |
| 3 | RECEIVED → PENDING_CLEARANCE | POS capture, cheque deposit, transfer capture | Cashier / system | Reference captured (terminal, cheque number, bank reference) | Awaiting acquirer/bank evidence; `payment.pending_clearance` | BR-PAY-006: authorization ≠ settlement |
| 4 | PENDING_CLEARANCE → CLEARED | Acquirer batch / bank confirmation match | Finance / system (statement ingestion) | Reference + amount match per BR-CSH-004 | `payment.cleared`; reconciliation link | Unmatched beyond window → reconciliation exception owned by finance |
| 5 | PENDING_CLEARANCE → REVERSED | Decline / dishonour / failed verification | Finance | Evidence recorded; authority per policy | Reversal + folio balance restoration + AR/guest exposure; `payment.reversed` | Cheque dishonour reopens receivable and may trigger credit hold (BR-ACC/CRP) |
| 6 | RECEIVED → VOIDED | Recorded in error | Cashier with supervisor | Before session close; reason mandatory | Payment voided with linked evidence; `payment.voided` | After close: reversal path only |
| 7 | CLEARED → CHARGEBACK | Card dispute received | Finance | Dispute evidence captured | DisputeCase opened; provisional reversal per acquirer rules; `payment.chargeback` | Dispute resolution (won/lost) posts the final linked adjustment |

### SM-REFUND

| | |
|---|---|
| States | `REQUESTED`, `APPROVED`, `PAID`, `REJECTED`, `CANCELLED` |

| # | From → To | Trigger | Actor | Authority / preconditions | Effects | Exceptions |
|---|---|---|---|---|---|---|
| 1 | — → REQUESTED | Refund request | Agent / finance | Original cleared payment exists (INV-FOL-8); amount ≤ un-refunded cleared funds | Request record with reason | Refund without original (e.g. goodwill) uses adjustment + finance path, not this machine |
| 2 | REQUESTED → APPROVED | Approval | Supervisor/finance per limits | Maker ≠ checker where policy requires; limits applied | Approval evidence; `refund.approved` | Above-limit escalations recorded |
| 3 | APPROVED → PAID | Execution | Finance / cashier | Method fidelity: same method where possible; bank details verified | Payment outflow evidence; `refund.paid`; accounting posting | Method substitution requires authority and fraud check |
| 4 | REQUESTED → REJECTED / CANCELLED | Decline / withdrawal | Approver / requester | Reason recorded | Evidence retained | — |

### SM-DEPOSIT

| | |
|---|---|
| Entity | Deposit (aggregate root; liability) |
| States | `RECEIVED`, `HELD`, `PARTIALLY_APPLIED`, `APPLIED`, `PARTIALLY_REFUNDED`, `REFUNDED`, `PARTIALLY_FORFEITED`, `FORFEITED`; conservation per INV-FOL-7 |

| # | From → To | Trigger | Actor | Authority / preconditions | Effects | Exceptions |
|---|---|---|---|---|---|---|
| 1 | — → RECEIVED → HELD | Deposit receipt and verification | Cashier / finance | Method state tracked per SM-PAYMENT; obligation (reservation/group) linked | Liability recorded; `deposit.received` | Unverified transfer held as pending until bank confirmation |
| 2 | HELD → PARTIALLY_APPLIED/APPLIED | Application to charges | Front desk / system at close | Charges exist; allocation to responsibility window per routing | Liability reduces; folio balance reduces; `deposit.applied` | Application to a different party than the depositor requires authority |
| 3 | HELD/partially applied → PARTIALLY_REFUNDED/REFUNDED | Refund on cancellation | Supervisor/finance per limits | Policy basis; cleared funds | Refund per SM-REFUND; `deposit.refunded` | — |
| 4 | HELD → PARTIALLY_FORFEITED/FORFEITED | Forfeiture per cancellation/no-show policy | Supervisor + finance | Policy basis, authority, reason; guest communication evidence | Liability reduces to zero; revenue/posting per BR-FOL-009; `deposit.forfeited` | Forfeiture is never automatic beyond policy limits; disputes route to DisputeCase/goodwill adjustment |
| 5 | HELD → HELD | Ageing review (unclaimed deposit) | Finance | Policy window; retention rules | Flagged for finance; escheatment/unclaimed-property handling per jurisdiction (Phase 1 research) | — |

### SM-CASHIER-SESSION

| | |
|---|---|
| States | `OPEN`, `CLOSING` (counting), `PENDING_REVIEW` (variance), `CLOSED` |

| # | From → To | Trigger | Actor | Authority / preconditions | Effects | Exceptions |
|---|---|---|---|---|---|---|
| 1 | — → OPEN | Session open | Cashier | One open session per cashier/till (unique constraint); float assigned and evidenced | Float recorded; `cashier.session_opened` | Concurrent open attempts fail deterministically |
| 2 | OPEN → OPEN | Receipts, payouts, drops | Cashier | Policy limits on payouts; drops evidenced | Payment lines, drop records; live totals | Void corrections per SM-PAYMENT #6 |
| 3 | OPEN → CLOSING | Close initiated | Cashier | All lines finalised | System produces expected totals by method | — |
| 4 | CLOSING → CLOSED | Count entered, variance within tolerance | Cashier | Float accounted; counts recorded | Session closed with evidence; `cashier.session_closed`; accounting variance posting if any | — |
| 5 | CLOSING → PENDING_REVIEW | Variance beyond tolerance | System | — | Session held open for review; alerts to supervisor/finance (BR-CSH-003); close-window alert per BR-REL-005 | Night audit cannot advance with sessions in PENDING_REVIEW unless policy authorises flagged continuation |
| 6 | PENDING_REVIEW → CLOSED | Variance approved | Supervisor/manager per limits | Reason; maker ≠ checker on own variance; accounting treatment selected | Variance posted per BR; review evidence; `cashier.variance_resolved` | Repeated variances escalate per BR-CSH-006 |
| 7 | any → any (correction) | Correlated correction after close | Finance | Governance: closed sessions immutable; corrections are new adjusting entries | Linked adjustment evidence | Direct edits to closed sessions are prohibited |

### SM-INVENTORY (allocation and hold lifecycle)

| | |
|---|---|
| Entity | InventoryAllocation (fact) |
| States | `ACTIVE`, `CONSUMED` (room-night occupied or charged), `RELEASED` (cancelled/hold expired/cutoff/early departure), `EXPIRED` (hold lapsed) |

| # | From → To | Trigger | Actor | Authority / preconditions | Effects | Exceptions |
|---|---|---|---|---|---|---|
| 1 | — → ACTIVE | Reservation/block/hold creation | System | Capacity available or overbooking policy allows (INV-PM-6) | Allocation per room-night; availability projection refreshed; `inventory.allocated` | Overbooking requires recorded authority; exposure reported nightly |
| 2 | ACTIVE → CONSUMED | Business date passes with stay active or no-show charged | Night audit | Stay in-house or no-show determination complete | Allocation frozen as consumed; `inventory.consumed` | Early departure releases remaining nights (next transition) |
| 3 | ACTIVE → RELEASED | Cancellation, amendment, cutoff release, early departure, unassign | Agent/supervisor/system | Owning entity authorises release (INV-PM-5) | Availability restored; `inventory.released` | Release of nights already consumed is impossible by construction |
| 4 | ACTIVE → EXPIRED | Hold/cutoff expiry job | System | Expiry reached | Same as release with expiry reason; idempotent job | Resumable; double-release prevented by state check + uniqueness |

## 5. Service, commercial and governance machines

### SM-HOUSEKEEPING-TASK

| | |
|---|---|
| States | `CREATED`, `ASSIGNED`, `IN_PROGRESS`, `PAUSED` (blocked/DND), `COMPLETED` (awaiting inspection where required), `INSPECTED_PASS`, `INSPECTED_FAIL`, `CANCELLED` |

| # | From → To | Trigger | Actor | Authority / preconditions | Effects | Exceptions |
|---|---|---|---|---|---|---|
| 1 | — → CREATED | Task generation (departure, stayover, deep, rush, request) | System | Source event (checkout, board cycle, schedule, request) | Task record; priority per policy; `hk.task_created` | Idempotent generation per room/date/type |
| 2 | CREATED → ASSIGNED | Assignment / rebalance | Supervisor | Attendant available; workload policy | Attendant notified; board updates; `hk.task_assigned` | Bulk assignment honours fairness policy; reassignment recorded |
| 3 | ASSIGNED → IN_PROGRESS → COMPLETED | Execution | Attendant | Sequence rules honoured (per policy); completion evidence | Room readiness transition (SM-ROOM #1–2); credits; `hk.task_completed` | Interruption (DND etc.) → PAUSED with reason; resume or reschedule |
| 4 | COMPLETED → INSPECTED_PASS | Inspection | Supervisor | Inspector ≠ cleaner where policy requires | Room READY path (SM-ROOM #3); `hk.inspection_passed` | Self-inspection only under recorded authority exception |
| 5 | COMPLETED → INSPECTED_FAIL | Inspection fail | Supervisor | Findings recorded | Reopens task; `hk.inspection_failed`; repeat-failure escalation | Chronic failures trigger training/quality case |
| 6 | any → CANCELLED | Task no longer needed (room sold OOO, guest extension with no-service) | Supervisor/SYSTEM | Reason recorded | Board updates; `hk.task_cancelled` | Cancellation after work started requires supervisor |

### SM-WORK-ORDER

| | |
|---|---|
| States | `REPORTED`, `TRIAGED`, `ASSIGNED`, `IN_PROGRESS`, `ON_HOLD` (parts/access), `COMPLETED`, `VERIFIED`, `CLOSED`, `CANCELLED` |

| # | From → To | Trigger | Actor | Authority / preconditions | Effects | Exceptions |
|---|---|---|---|---|---|---|
| 1 | — → REPORTED | Issue reported (staff, guest, inspection, PM schedule) | Any staff | Location/asset identifiable | Work order; SLA clock starts; `mnt.work_order_reported` | Guest-originated issues link an Incident where applicable |
| 2 | REPORTED → TRIAGED → ASSIGNED | Priority and trade assignment | Chief engineer / supervisor | Priority policy; skills match | Assignment; notifications; `mnt.work_order_assigned` | Safety-critical priority overrides queue ordering |
| 3 | ASSIGNED → IN_PROGRESS | Work starts | Technician | — | Attendance/effort evidence | — |
| 4 | IN_PROGRESS → ON_HOLD | Parts/access unavailable | Technician | Reason; expected resolution | SLA paused per policy with visibility | Repeated holds escalate to procurement/capital path |
| 5 | IN_PROGRESS → COMPLETED | Work complete | Technician | Completion evidence per policy (notes, parts, photos) | Room/asset condition input; `mnt.work_order_completed` | — |
| 6 | COMPLETED → VERIFIED → CLOSED | Verification (supervisor/requester) | Supervisor | Verification evidence; OOO return-to-service linkage (INV-SVC-2) | Room saleability restored where applicable; `mnt.work_order_verified` | Failed verification reopens work with findings |
| 7 | any → CANCELLED | Duplicate/no longer required | Supervisor | Reason | Evidence retained | — |

### SM-GROUP-BLOCK

| | |
|---|---|
| States | `TENTATIVE`, `DEFINITE`, `RELEASED` (cutoff executed), `ACTIVE` (event dates), `COMPLETED`, `CANCELLED` |

| # | From → To | Trigger | Actor | Authority / preconditions | Effects | Exceptions |
|---|---|---|---|---|---|---|
| 1 | — → TENTATIVE | Draft block offered | Sales | Space/rooms availability checked | Holds created per policy with expiry | Holds beyond policy limits require revenue authority |
| 2 | TENTATIVE → DEFINITE | Contract signed / terms agreed | Sales + finance | Rate agreement, deposit terms, cancellation/attrition terms documented; deposit schedule created | Block commitments; `group.definite` | Deposit receipt tracked per SM-DEPOSIT |
| 3 | DEFINITE → RELEASED | Cutoff passes | System (idempotent job) | Cutoff date reached | Unpicked inventory released with evidence; block remains for billing; `group.released` | Late release is a recorded exception for revenue review |
| 4 | RELEASED/DEFINITE → ACTIVE | First group room checks in on dates | System | Block dates reached | Projection only; `group.active` | — |
| 5 | ACTIVE → COMPLETED | Last group folio settled/transferred and event ended | System | Master folio closed (SM-FOLIO #4) | `group.completed` | Unsettled master blocks completion with exception flag |
| 6 | any pre-ACTIVE → CANCELLED | Cancellation | Sales + finance | Attrition/cancellation terms calculated (BR-GRP-003); deposit disposition | Inventory released; financial consequences posted; `group.cancelled` | Attrition disputes route through finance case, never by editing terms |
| 7 | DEFINITE → DEFINITE (amend) | Date/quantity/rate changes | Sales with authority | Re-check availability for increases; terms recorded | Amendment evidence; `group.amended` | Increases beyond availability require revenue approval |

### SM-CORPORATE-CONTRACT

Covers account lifecycle, credit profile and negotiated rate agreement as one governance machine with per-part states.

| | |
|---|---|
| States | Account: `ACTIVE`, `SUSPENDED` (credit hold), `CLOSED`; Credit profile: `OPEN`, `HOLD`, `BLOCKED`; Rate agreement: `DRAFT`, `ACTIVE`, `EXPIRED`, `TERMINATED` |

| # | From → To | Trigger | Actor | Authority / preconditions | Effects | Exceptions |
|---|---|---|---|---|---|---|
| 1 | — → ACTIVE (account) | Account approved | Sales + finance | Credit assessment for direct-bill intent; billing identity; tax details | Account usable in routing/rates; `account.activated` | Accounts without credit operate pre-payment only |
| 2 | ACTIVE → SUSPENDED / Credit OPEN → HOLD/BLOCKED | Exposure beyond limit, dishonour, overdue AR | Finance / system alert | BR-CRP-001: exposure calculation; notice to sales | New direct-bill transfers blocked; existing stays handled per policy (notification/collection); `account.suspended` | Suspension does not retroactively change posted charges; it governs future transfers |
| 3 | SUSPENDED → ACTIVE | Settlement/reinstatement | Finance | Balance or agreement restored; authority | Transfers re-enabled; `account.reinstated` | Repeated suspensions escalate commercial review |
| 4 | — → DRAFT → ACTIVE (rate agreement) | Negotiation → signature | Sales + revenue + finance | Rate basis, validity, eligibility rules; no conflicts with BAR floor policy | Negotiated rates resolve for eligible bookings; `rate_agreement.activated` | Retroactive activation requires revenue approval; past stays unchanged |
| 5 | ACTIVE → EXPIRED | Validity end | System | End date reached | Eligibility stops prospectively; `rate_agreement.expired` | Renewal is a new agreement version |
| 6 | ACTIVE → TERMINATED | Commercial decision | Sales + finance with authority | Reason; impact review of future bookings | Future bookings repriced per policy; `rate_agreement.terminated` | Booked stays honour terms per contract language (recorded decision) |

### SM-RATE

| | |
|---|---|
| States | Rate plan: `DRAFT`, `ACTIVE`, `SUSPENDED` (not sellable), `RETIRED`; Rate amount/version: `PENDING` (future), `ACTIVE` (in effect), `SUPERSEDED`, `CANCELLED` (never became effective) |

| # | From → To | Trigger | Actor | Authority / preconditions | Effects | Exceptions |
|---|---|---|---|---|---|---|
| 1 | — → DRAFT → ACTIVE | Plan creation and approval | Revenue / GM with authority | Terms, inclusions, cancellation policy, restrictions defined; tax eligibility consistent | Plan sellable per dates; `rate_plan.activated` | Activation requires rate change authority (BR-RTM-003) |
| 2 | ACTIVE → ACTIVE (future amount change) | Rate change | Revenue with authority | New version effective-dated; conflict/overlap validated | Future dates use new version; past untouched (INV-RTM-1); `rate_amount.changed` | Bulk changes preview impacted dates and require confirmation |
| 3 | PENDING → CANCELLED | Cancelled before effect | Revenue | Not yet effective | No effect; `rate_amount.cancelled` | Effective change cannot be cancelled, only superseded forward |
| 4 | ACTIVE → SUPERSEDED | Newer version effective | System | New version effective_from reached | Resolution switches prospectively; `rate_amount.superseded` | — |
| 5 | ACTIVE → SUSPENDED → ACTIVE | Stop selling temporarily | Revenue | Reason | New bookings blocked; existing honoured; `rate_plan.suspended` | Suspension does not alter posted history |
| 6 | ACTIVE → RETIRED | Plan end-of-life | Revenue + GM | No future dependency, or migration mapping recorded | Plan unavailable prospectively; `rate_plan.retired` | Retiring a plan with future bookings requires repricing decision recorded |

### SM-NIGHT-AUDIT (business day and close)

| | |
|---|---|
| States | Business day: `OPEN`, `CLOSING`, `CLOSED`, `REOPENED` (governed remediation window). Close run: `RUNNING`, `FAILED` (recoverable), `COMPLETED` |

| # | From → To | Trigger | Actor | Authority / preconditions | Effects | Exceptions |
|---|---|---|---|---|---|---|
| 1 | — → OPEN | First day open / advance | System | Prior day CLOSED or initial provisioning | Posting window open; `day.opened` | — |
| 2 | OPEN → CLOSING | Close initiated | Night auditor | Pre-close checklist passes or blocking exceptions resolved (BR-NAU-001): unposted charges, open cashier sessions, departure folios, discrepancies, posting queues | Close run starts; posting restricted to close processes; `day.closing` | Failed checklist blocks advancement unless each blocking item is resolved; authorized continue with flags only where policy allows and is reported to finance |
| 3 | CLOSING → CLOSED | Close completes | System | Nightly postings complete; control totals reconcile or exceptions recorded; close run COMPLETED | Revenue/tax posted to business date; control totals frozen; reports generated; next day opened; `day.closed` | Partial failure: close run FAILED → resumable; no partial day advancement; recovery per SM rule 4 |
| 4 | CLOSING → (FAILED, remain CLOSING) | Failure mid-close | System | — | Recovery checkpoint retained; alerts to on-call (BR-REL-005) | Re-run resumes from checkpoint; idempotency prevents double-posting (INV-NAU-2) |
| 5 | CLOSED → REOPENED | Reopen for correction | Finance controller with authority | Reason, authority, accounting impact assessment; within policy window | Remediation window; corrections dated to remediation rules, originals immutable; `day.reopened` | Reopen during a later open day allowed only when policy permits; concurrent-close conflicts are prevented by property-level lock |
| 6 | REOPENED → CLOSED | Remediation complete | Finance controller | Remediation evidence; control totals restated with version | New frozen totals version; `day.reclosed` | Restatement is versioned, never overwrites the original totals |
| 7 | any | Income audit certification | Income auditor | Independent verification per BR-INA (CAP-INA-007) | Certification record at day level; `audit.certified` | Certification is advisory-blocking per property policy; unresolved findings remain visible regardless |

## 6. Cross-machine consistency rules

1. **Atomic relationships.** Stay creation ↔ reservation-room check-in ↔ allocation consumption ↔ folio opening occur in one transaction or not at all.
2. **No orphan money.** Every financial transition references its source operational entity; every operational financial consequence emits posting intent (Accounting Interface) exactly once.
3. **No orphan inventory.** Allocation state changes are always driven by their owning entity (reservation, block, hold) and are idempotent.
4. **Time respects the business day.** Financial machines require an OPEN business date; close-time machines (night audit) own the transition of that date.
5. **Corrections compose.** Every machine's correction path composes with ADR-003: additive records, linked evidence, versioned restatements, never edits.
6. **Authority is data.** Every "requires authority" in this document resolves to effective-dated AuthorityLimit configuration, property-scoped, with maker–checker where stated.

## 7. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial state machine catalogue (18 machines) issued with WP 0.3 | PROPOSED |

