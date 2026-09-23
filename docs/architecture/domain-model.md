---
doc-id: ARCH-DOMAIN
title: Target Domain Model
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Domain Architect (drafted); Product Owner (approval)
applies-to: full enterprise target
depends-on: [ARCH-TARGET, ADR-001, ADR-002, PROD-CAPMAP]
---

# Target Domain Model

## 1. Method and notation

This document defines the domain in **bounded contexts** aligned to the module map (`target-state.md` §3.2). For each context it lists entities, aggregate roots (AR), value objects where material, ownership, lifecycle references and invariants.

Rules applied throughout:

1. **One owner per entity** (ADR-001). Other contexts reference an entity only by stable identity (ADR-004) — never by copying it or writing to it.
2. **Aggregates are transaction boundaries.** A state change inside an aggregate is atomic; cross-aggregate effects are coordinated by application services and reconciled by the Control Plane.
3. **History is part of the model, not an afterthought.** Immutable transaction records, effective-dated configuration and transition evidence are first-class (ADR-003).
4. **Financial responsibility is explicit.** No charge exists without a responsible party decision recorded at posting time (§5).
5. **Terminology is normative** per the glossary. Entity names here are the canonical names used by all later artifacts.

State machines, transition permissions and validation detail per aggregate are defined in WP 0.3; this document defines the shapes and invariants they must satisfy.

## 2. Context relationship map

```text
Property & Inventory ──allocations──► Availability (derived)
        ▲                                ▲
        │ rooms/type                     │ restrictions/rates
        │                          Rates ─┘
        │                                │
Reservations ──creates on arrival──► Front Office (Stay) ──consumes──► Service Operations
        │                                │
        │ guests                         │ folio events
        ▼                                ▼
Guests & Privacy ─────────────► Guest Financials ──posting──► Accounting Interface ──► Accounting Authority
        ▲                                ▲
        │ accounts/blocks                │ outlet tickets
Groups, Corporate & Sales ─────────────► Outlets & Inventory
        │
        └── read by ──► Reporting & Analytics (read models)
Platform serves every context; Integrations adapt every external system; AI reads and proposes only.
Night Audit & Income Audit govern the daily close over Guest Financials and Accounting Interface.
```

## 3. Contexts

### 3.1 Property & Inventory

The physical sellable estate and the authoritative consumption of inventory.

| Entity / aggregate | Kind | Purpose and notes |
|---|---|---|
| Property | Entity | Operational home of everything; timezone, currency, operating calendar |
| LegalEntity | Entity | Reporting/tax entity; linked to properties as configured |
| PropertyGroup | Entity | Hierarchy for configuration and consolidated reporting (E14) |
| Building / Floor / Zone | Entity | Physical structure; housekeeping zones and pools drive assignment |
| RoomType | Entity | Sellable product definition (capacity, bedding, default occupancy) |
| Room | Entity | Physical room; current room number is an attribute, not identity (ADR-004) |
| RoomTypeAssignment | Effective-dated link | A room may change type over time (renovation, reclassification) |
| RoomFeature / feature assignment | Entity | Structured matching attributes |
| RoomPool | Entity | Assignment grouping (CAP-PM-012) |
| **InventoryAllocation** | **AR (fact)** | Authoritative commercial inventory consumption: one record per room-night consumed by a reservation, block or hold, with source reference and release reference. Physical unavailability (OOO/OOS) is not an allocation; it reduces sellable capacity |
| OOOOSWindow | Entity | Out-of-order/out-of-service period with reason, approval and return-to-service evidence |
| OverbookingPolicy | Configuration | Bounded, governed oversell allowance |
| CapacityCalendar | Derived | Sellable capacity per date = physical rooms minus active OOO/OOS windows; feeds the availability projection |

**Invariants**
- `INV-PM-1` A room belongs to exactly one property and has exactly one active room type assignment at a time.
- `INV-PM-2` Room numbers are unique within a property among active rooms; renumbering preserves identity.
- `INV-PM-3` OOO/OOS windows for one room never overlap; each has an owner, reason and closure evidence.
- `INV-PM-4` Availability per date = sellable capacity (physical rooms minus active OOO/OOS windows) − active commercial allocations; it is derived and never directly edited.
- `INV-PM-5` An allocation is released only by its owner (`release_ref`); released allocations remain as history.
- `INV-PM-6` Availability may not become negative unless an active OverbookingPolicy permits it; each such night is an auditable exposure.
- `INV-PM-7` Every OOO/OOS change immediately affects sellable inventory; return to sale requires the policy-defined verification (CAP-PM-010).

### 3.2 Rates

Pricing as effective-dated configuration with a snapshot discipline that preserves history.

| Entity / aggregate | Kind | Purpose and notes |
|---|---|---|
| RatePlan (AR) | Entity | Commercial product: inclusions, restrictions, cancellation policy, market segment |
| RateVersion | Effective-dated record | Versioned terms of a rate plan; never edited in place (ADR-003) |
| RateAmount | Effective-dated record | Per date × room type × plan amount, manual or derived |
| DeriveRule | Configuration | Base plan, adjustment policy, floor/ceiling, rounding |
| RateRestriction | Effective-dated record | Stop sell, CTA, CTD, min/max LOS, minimum advance |
| NegotiatedRateAgreement | Entity | Account eligibility, validity, rate basis (CAP-RTM-004) |
| PackageDefinition / Inclusion | Entity | Components and their posting treatment (CAP-RSV-016) |
| PromoCode / DiscountRule | Entity | Discount authority and stacking rules |
| TaxRule / ServiceChargeRule | Effective-dated record | Jurisdiction rules with effective dates (final ownership per WP 0.4) |

**Invariants**
- `INV-RTM-1` Rate amounts and restrictions are effective-dated; no historical change may alter a past date's terms.
- `INV-RTM-2` Every folio charge carries a snapshot of the applied amount basis and tax rule version used (CAP-FOL-003, ADR-003).
- `INV-RTM-3` Restriction conflicts resolve by explicit precedence (most restrictive wins); the resolution is reproducible.
- `INV-RTM-4` Derived rates recompute from their base and rules; recomputation never rewrites posted history.
- `INV-RTM-5` Rate changes carry actor, reason and authority (CAP-RTM-011).
- `INV-RTM-6` Channel-published rates (ADR-002/INT-002) are projections of this context, never an independent source.

### 3.3 Reservations

The commercial promise: what was sold, to whom, at what price and under which guarantee.

| Entity / aggregate | Kind | Purpose and notes |
|---|---|---|
| **Reservation** | **AR** | Commercial contract: number, source, account links, currency, lifecycle state |
| ReservationRoom | Entity | One room line: room type, plan, occupancy, dates, guarantee, assignment, cancellation-terms snapshot |
| ReservationGuest | Entity | Person + role (primary/accompanying) per room; supports multiple guests per room |
| Guarantee | Entity | Instrument securing the booking (deposit, card, corporate, agent voucher) |
| DepositSchedule | Entity | Expected deposit amounts and due dates (links to Guest Financials deposits) |
| RoutingInstruction | Entity | Charge category → responsible party (materialises in Guest Financials) |
| Trace / Alert / Request | Entity | Operational obligations created at booking |
| ReservationAmendment | Immutable record | What changed, who changed it, authority, before/after |
| WaitlistEntry | Entity | Demand not yet confirmed (CAP-RSV-006) |
| SharedReservationLink | Entity | Two or more reservations sharing one room with separate responsibility (CAP-RSV-011) |
| SplitStayLink | Entity | Stay segments across rooms/types with billing continuity (CAP-RSV-012) |
| CancellationRecord / NoShowRecord | Entity | Policy application evidence (CAP-RSV-004/005) |

**Invariants**
- `INV-RSV-1` A Reservation has at least one ReservationRoom; each room has exactly one primary guest.
- `INV-RSV-2` Arrival < departure; dates are business dates in the property timezone.
- `INV-RSV-3` Booking requires an allocation for every room-night (CAP-AVL-001), created transactionally with the reservation.
- `INV-RSV-4` Rate plans eligible for the room type and dates must be used; exceptions require recorded authority.
- `INV-RSV-5` Restrictions are satisfied at booking, amendment and arrival; overrides are authorised and evidenced.
- `INV-RSV-6` Cancellation and no-show consequences use the terms snapshot captured at booking, plus any governing policy in force at the event; the applied basis is recorded.
- `INV-RSV-7` Room assignment targets only sellable, appropriately-typed rooms; "Do Not Move" is honoured by automation.
- `INV-RSV-8` Guarantee state and deposit schedule are consistent: a guaranteed reservation has an instrument or a recorded authorised exception.
- `INV-RSV-9` All lifecycle transitions are legal, permitted and evidenced (CAP-RSV-022; transitions in WP 0.3).

### 3.4 Front Office (Stay)

The operational occupancy: who is physically in the hotel, in which room, until when.

| Entity / aggregate | Kind | Purpose and notes |
|---|---|---|
| **Stay** | **AR** | Actual occupancy created at check-in from a reservation room; ends at checkout |
| RoomAssignment | Entity + history | Room ↔ stay/reservation association with reason and period |
| RoomOccupancyState | Derived | Occupied/vacant/due-out projection from stays |
| RegistrationAcceptance | Immutable evidence | Terms accepted at check-in, by whom, with identity-capture reference |
| GuestRequest / ServiceTask | Entity | Requests with owners and deadlines (CAP-FO-012) |
| Incident / Complaint | Entity | Operational events with service-recovery linkage (CAP-FO-014) |
| ServiceRecovery | Entity | Compensation decision with authority, linking to a folio adjustment (CAP-FO-015) |
| WakeUpCall / Message | Entity | Scheduled guest services |
| KeyIssuance | Entity | Access credential grants, validity and revocation (CAP-FO-011) |
| RelocationRecord | Entity | Walking a guest: cost, destination, approvals, reporting (CAP-FO-018) |
| EarlyDepartureRecord / ExtensionRecord | Entity | Stay-duration changes with pricing consequences (CAP-FO-010/019) |

**Invariants**
- `INV-FO-1` At most one active Stay exists per room at any time.
- `INV-FO-2` A Stay originates only from a checked-in ReservationRoom; check-in enforces identity, credit, readiness and policy gates (CAP-FO-002/004).
- `INV-FO-3` Checkout requires the folio invariant to hold: balance settled, transferred or explicitly authorised to remain (INV-FOL-1).
- `INV-FO-4` Room release (becomes vacant-dirty) occurs only after checkout; release triggers housekeeping (CAP-HSK-002).
- `INV-FO-5` A room may be sold only when readiness and saleability allow, or under a recorded authorised override.
- `INV-FO-6` Key/access credentials are valid only while the stay is active; checkout revokes access.
- `INV-FO-7` Registration acceptance is immutable evidence; corrections are new records referencing the original.

### 3.5 Guests & Privacy

One person, one history — achieved lawfully.

| Entity / aggregate | Kind | Purpose and notes |
|---|---|---|
| **GuestProfile** | **AR** | Canonical person: names, contacts, documents (restricted), preferences, relationships, watchlist status (restricted), membership links |
| GuestDocument | Entity (restricted) | Identity documents with retention and access rules (CAP-GST-002/009) |
| GuestRelationship | Entity | Family/companion/corporate links (CAP-GST-004) |
| GuestAliasIdentity | Immutable record | Merge provenance: source identities folded into a survivor (CAP-GST-006) |
| DuplicateCandidate / ReviewRecord | Entity | Scored duplicate suggestions and decisions (CAP-GST-005) |
| ConsentRecord | Immutable record | Purpose, version, evidence, effective period (CAP-GST-008) |
| RetentionRule / ErasureRecord | Configuration + evidence | Retention application and lawful erasure with legal-hold checks (CAP-GST-009) |
| Membership | Entity | Provider-neutral loyalty (CAP-GST-011) |

**Invariants**
- `INV-GST-1` A profile merge is non-destructive: the survivor is explicit, aliases are retained, and every reference resolves through alias mapping.
- `INV-GST-2` Merges and reverse-merge evidence are immutable; no guest record is hard-deleted while financial or legal retention applies.
- `INV-GST-3` Sensitive fields (documents, watchlist) have dedicated access permissions; ordinary operational roles see only what their role requires.
- `INV-GST-4` Processing beyond contract/legal necessity requires a recorded consent version; withdrawal is honoured prospectively.
- `INV-GST-5` Erasure respects legal hold and financial retention; erasure itself is evidenced.
- `INV-GST-6` Profile history figures reconcile to reservations and folios; no independently invented totals.

### 3.6 Groups, Corporate & Sales

Commercial relationships that shape rates, inventory and responsibility.

| Entity / aggregate | Kind | Purpose and notes |
|---|---|---|
| **Account** | **AR** | Commercial party: corporate, agent, source partner; roles and billing identity |
| CreditProfile | Entity | Terms, limit and derived exposure (CAP-CRP-004) |
| BillingInstruction | Entity | Default routing/responsibility for the account (CAP-CRP-003) |
| CommissionAgreement | Entity | Agent commission terms and lifecycle (CAP-CRP-006) |
| AccountContact / SalesActivity | Entity | Relationship history (CAP-CRP-009) |
| GroupAccount | Entity | Group as a commercial entity |
| **GroupBlock** | **AR** | Inventory hold + rate agreement + cutoff + deposit terms + master account intent (CAP-GRP-002) |
| BlockLine | Entity | Room type, quantity, rate, release rules |
| PickupLink | Entity | Reservation ↔ block line association for pickup tracking (CAP-GRP-004) |
| RoomingList / RoomingListEntry | Entity | Named guests → rooms → reservations (CAP-GRP-003) |
| Event / FunctionSpace / EventBooking | Entity | Space inventory, bookings, catering/billing requirements (CAP-GRP-011/012) |
| Lead / Opportunity / SalesContract | Entity | Pipeline and won business (CAP-SAL-001/005) |

**Invariants**
- `INV-GRP-1` Pickup may not exceed block quantity except under a recorded authorised over-allocation.
- `INV-GRP-2` Block inventory is released automatically at cutoff and the release is evidenced.
- `INV-GRP-3` Block rates apply only to reservations linked through PickupLink; deviations require authority.
- `INV-GRP-4` A group master account exists before group charges are routed to it; responsibility is never implicit.
- `INV-GRP-5` Account exposure (AR + unbilled exposure) is checked before direct-bill transfers; breaches require approval per policy.
- `INV-GRP-6` Function spaces cannot be double-booked; space holds follow the same allocation discipline as rooms.
- `INV-GRP-7` Commission calculations reference an agreement; no agreement, no commission.

### 3.7 Service Operations

Getting rooms ready and keeping the asset working — with evidence.

| Entity / aggregate | Kind | Purpose and notes |
|---|---|---|
| **HousekeepingTask** | **AR** | Room work item: type, priority, state, assignment, credits, completion evidence (CAP-HSK-002…005) |
| Inspection | Entity | Supervisor verification: pass/fail, findings, examiner (CAP-HSK-009) |
| RoomDiscrepancy | Entity | Physical vs system truth conflict with resolution evidence (CAP-HSK-010) |
| **WorkOrder** | **AR** | Maintenance work: source, priority, SLA, trade assignment, parts, completion evidence (CAP-MNT-001…003) |
| Asset / PMSchedule / PMTask | Entity | Equipment register and preventive maintenance (CAP-MNT-004/005) |
| MinibarConsumption | Entity | In-room consumption posted to folio (CAP-HSK-008) |
| LostAndFoundItem | Entity | Custody and lawful return/disposal (CAP-HSK-012) |

**Invariants**
- `INV-SVC-1` A room is released to sale only through the readiness path (task complete + inspection per policy) or an authorised override.
- `INV-SVC-2` OOO periods created by work orders immediately affect inventory; return to service requires WorkOrder verification (CAP-MNT-008).
- `INV-SVC-3` Task and work-order transitions are legal, assigned and audited; closure requires evidence per policy.
- `INV-SVC-4` Discrepancies are resolved daily; an unresolved discrepancy is visible to the close checklist (CAP-NAU-002).

### 3.8 Guest Financials

Where hospitality becomes accounting. Every naira owed, paid, held or refunded is represented here with an explicit responsible party and an immutable trail.

| Entity / aggregate | Kind | Purpose and notes |
|---|---|---|
| **Folio** | **AR** | Account container: type (guest, group master, event, house, corporate), owner reference, currency, lifecycle state (open → settled → closed; transferred where applicable) |
| BillingWindow | Entity | Numbered responsibility account within a stay/group (1…N); each window names its responsible party (CAP-FOL-002) |
| **FolioItem** | **Immutable record** | One financial line; typed family: Charge, Tax, Payment, DepositApplication, Adjustment, Transfer, Refund, VoidReversal. Every item carries business date, currency, source actor, authority where applicable, revenue classification and a snapshot of applied pricing/tax basis |
| ShareAllocation | Entity | Splits a charge across multiple responsible parties for shared rooms (CAP-RSV-011, CAP-FOL-002) |
| ChargeResponsibilityDecision | Immutable evidence | The resolved responsible party for a charge at posting time, including the routing rule version used |
| **Payment** | **AR** | Settlement lifecycle per method: received → pending clearance → cleared / reversed / chargeback; method-specific data (POS batch, transfer reference, cheque clearing) modelled faithfully (BR-PAY-002…004) |
| **Deposit** | **AR** | Liability lifecycle: received → held → applied (partial or full) → refunded (partial or full) / forfeited; linked to reservation/group obligation (CAP-FOL-008) |
| **Refund** | **AR** | Return of cleared funds; method fidelity, authority limits, link to original payment (CAP-FOL-009) |
| **CashierSession** | **AR** | Float, transactions, drops, counted close, variance, independent review (CAP-CSH-001…007) |
| CityLedgerTransfer | Entity | Governed transfer of a settled balance to a corporate/agent AR account; creates the AR document via Accounting Interface (CAP-CRP-005) |
| DisputeCase | Entity | Chargeback/dispute lifecycle: evidence, representment, outcome, financial posting |
| FolioGroup | Entity | The set of windows/folios belonging to one stay, group or event for consolidated presentation and invariant checking |

**Invariants**
- `INV-FOL-1` Folio balance equals Σ charge and tax items + transfers in − Σ payments − deposits applied − adjustments − refunds − transfers out. This identity holds at all times, including mid-transaction and after recovery.
- `INV-FOL-2` Folio items are immutable. Corrections, allowances and voids create new linked items; nothing is edited or deleted.
- `INV-FOL-3` Every item belongs to a business date that was open when the item was created (CAP-NAU-001).
- `INV-FOL-4` Every charge has exactly one resolved responsible party, evidenced at posting (routing version, share rule or explicit instruction).
- `INV-FOL-5` Tax is computed from the effective-dated rule snapshot for the item's business date; tax lines are inseparable from their parent charge for reconciliation purposes.
- `INV-FOL-6` Payment settlement state reflects reality: a POS authorization is not a settlement; a cheque is not cleared until the bank confirms (BR-PAY-005/006).
- `INV-FOL-7` Deposit conservation: received = applied + refunded + forfeited + remaining, at every point in time (CAP-ACC-006).
- `INV-FOL-8` A refund never exceeds cleared, un-refunded funds, and requires authority per policy; forfeiture requires policy basis and approval.
- `INV-FOL-9` A CityLedgerTransfer requires credit eligibility or a recorded authorised exception, and produces exactly one AR document (idempotent).
- `INV-FOL-10` A CashierSession closes only with a counted total; a variance closes only under the configured review authority (CAP-CSH-005/006).
- `INV-FOL-11` One currency per folio; no mixed-currency arithmetic (foreign currency deferred, BR-MVP-012).
- `INV-FOL-12` Shared and split responsibility is modelled through windows and share allocations, never by duplicating charges across folios.

### 3.9 Night Audit & Income Audit

The daily close and its independent verification.

| Entity / aggregate | Kind | Purpose and notes |
|---|---|---|
| **BusinessDay** | **AR** | Per property: business date, state (Open → Closing → Closed; Reopened under authority), posting window |
| CloseRun | Entity | One execution attempt of the close: steps, results, recovery checkpoint, idempotency identity |
| PostingRun | Entity | Nightly charge/tax generation; idempotent per (property, business date, run type) |
| ExceptionCase | Entity | Control failures or unresolved items: owner, severity, blocking flag, resolution evidence (CAP-NAU-006) |
| ControlTotal | Entity | Expected vs actual totals per control (charges, payments, taxes, deposits, cash) |
| AuditCertification | Entity | Income audit pass/hold decision with findings and certification evidence (CAP-INA-007) |

**Invariants**
- `INV-NAU-1` No financial item exists outside an open business date except governed remediation on a reopened accounting date.
- `INV-NAU-2` A close is idempotent and resumable: re-execution never posts a second time (CAP-NAU-008).
- `INV-NAU-3` The business date advances only when required controls pass or blocking exceptions are resolved; each resolution is evidenced.
- `INV-NAU-4` Reopening a closed day requires authority, reason and immutable evidence; reopening never rewrites history, it creates remediation records (CAP-NAU-009).
- `INV-NAU-5` Control totals for a closed day are frozen and reconcilable forever.
- `INV-INA-1` Income audit certification is performed by a role separated from the operations it verifies, using the configured exception path where staffing demands it.
- `INV-INA-2` Audit findings have owners, deadlines and closure evidence; unresolved findings remain visible to management regardless of close (CAP-INA-006).

### 3.10 Accounting Interface

The governed bridge to the books; not an accounting system.

| Entity / aggregate | Kind | Purpose and notes |
|---|---|---|
| **PostingRecord** | **AR** | One intended financial effect: source event reference, idempotency identity, classification, amount, status, attempts |
| AccountingDocumentLink | Immutable record | External accounting document identity, checksum, status, reversal links |
| ReconciliationCase | Entity | Interface/period differences with resolution evidence (CAP-ACC-009) |
| ControlAccountSnapshot | Entity | Per day/property: expected vs booked balances for each control account |

**Invariants**
- `INV-ACC-1` At most one committed posting exists per source event per accounting dimension; retries reuse the posting identity.
- `INV-ACC-2` A posting that is uncertain (external success/local failure or inverse) is resolved by reconciliation, never by blind re-posting.
- `INV-ACC-3` Closed periods reconcile to zero difference, or carry a recorded, owned exception.
- `INV-ACC-4` Reversals are new documents linked to the original; posted history is never edited.
- `INV-ACC-5` The interface holds no independent balance of record; it holds evidence and linkage (ownership matrix, `target-state.md` §4).

### 3.11 Outlets & Inventory

Outlet sales and stock as governed operational facts.

| Entity / aggregate | Kind | Purpose and notes |
|---|---|---|
| Outlet / MenuItem / PriceList | Entity / effective-dated | Outlet definitions and pricing (CAP-POS-001/002) |
| Table | Entity | Physical table inventory (CAP-POS-003) |
| **OutletTicket** | **AR** | Order/check lifecycle: lines, modifiers, discounts, settlement, room posting (CAP-POS-003/004) |
| Settlement | Entity | Outlet money movement with method fidelity (CAP-POS-007) |
| StockItem / Recipe / StockLocation | Entity | Items, consumption recipes, stores (CAP-INV-001/002) |
| **StockMovement** | **Immutable record** | Receipts, issues, transfers, consumption, adjustments — the stock ledger facts |
| StockCount (AR) / VarianceResolution | Entity | Physical counts, variance with authority (CAP-INV-007) |
| Supplier / PurchaseOrder / GoodsReceipt | Entity | Procurement chain (CAP-INV-003/004) |

**Invariants**
- `INV-POS-1` Ticket totals equal line totals less authorised discounts; settlement state reflects reality.
- `INV-POS-2` Room posting validates the stay, responsibility routing and credit position (CAP-POS-008).
- `INV-INV-1` Stock movements are immutable; corrections are compensating movements.
- `INV-INV-2` Count variance resolution requires authority and a reason; unresolved variance blocks the outlet's reconciliation report.
- `INV-INV-3` Theoretical consumption derives from recipes against actual sales; the comparison is reproducible.

### 3.12 Platform

The substrate every other context depends on.

| Entity / aggregate | Kind | Purpose and notes |
|---|---|---|
| Principal (User / ServiceIdentity) | Entity | Staff users and machine identities |
| Role / Permission / ScopeGrant | Configuration | RBAC with property/company scope (CAP-PLT-002/003) |
| AuthorityLimit | Effective-dated configuration | Numeric limits per role/principal (e.g. refund, discount, reopen) |
| ConfigurationItem | Effective-dated, scoped | Policies, timings, limits, tax mappings, numbered series references |
| NumberSeries | Entity | Configured human-facing numbering (ADR-004) |
| **AuditEvent** | **Immutable record** | Who/what/when/where, before/after, reason, correlation (CAP-PLT-006) |
| OutboxEvent / InboxEvent | Immutable record | Transactional events out; deduplication ledger in |
| Notification / AlertRule | Entity | Escalation and delivery evidence |
| ImportJob / ExportJob / MigrationRecord | Entity | Bulk movement and migration evidence (CAP-PLT-016) |
| BreakGlassGrant | Entity | Time-boxed emergency access with mandatory review (CAP-PLT-015) |

**Invariants**
- `INV-PLT-1` Every governed action has an authenticated principal, an authorization decision and an audit event.
- `INV-PLT-2` Every access path enforces tenant, property and company scope (ADR-002).
- `INV-PLT-3` Configuration changes are effective-dated, authorised and audited; resolved versions are captured in the evidence of the transactions they govern.
- `INV-PLT-4` Audit and outbox records are append-only; no principal may edit or delete them.
- `INV-PLT-5` Break-glass access is time-boxed, highly visible and reviewed; it grants no standing authority.

## 4. Domain events (representative catalogue)

Events are facts about completed transactions, written to the outbox in the same transaction as the state change (ADR-001 §5.2). Full contracts and versioning in WP 0.7.

| Event | Publisher | Primary consumers |
|---|---|---|
| `reservation.confirmed`, `reservation.amended`, `reservation.cancelled`, `reservation.no_show` | Reservations | Reporting, CRM projection, channels, night audit |
| `inventory.allocated`, `inventory.released` | Property & Inventory | Availability, channels, revenue reporting |
| `stay.checked_in`, `stay.checked_out`, `stay.moved`, `stay.extended` | Front Office | Housekeeping, folios, keys, reporting |
| `room.readiness.changed`, `room.saleability.changed` | Service Operations / Inventory | Front office, availability |
| `folio.item_posted`, `folio.closed`, `folio.transferred` | Guest Financials | Accounting Interface, income audit, reporting |
| `payment.received`, `payment.cleared`, `payment.reversed`, `refund.issued` | Guest Financials | Cashiering, accounting, reconciliation |
| `deposit.received`, `deposit.applied`, `deposit.refunded`, `deposit.forfeited` | Guest Financials | Accounting, reporting |
| `cashier.session.closed` | Guest Financials | Income audit, accounting |
| `day.closing`, `day.closed`, `day.reopened` | Night Audit | Accounting, reporting, integrations |
| `audit.certified`, `audit.exception_raised` | Income Audit | Management, finance, audit |
| `posting.committed`, `posting.failed`, `reconciliation.exception` | Accounting Interface | Finance, monitoring |
| `account.credit_changed`, `block.released` | Corporate/Groups | Front office, checkout, reporting |
| `guest.merged`, `consent.changed` | Guests & Privacy | CRM projection, reporting |

Naming: `<context>.<past_tense_fact>`; payloads carry stable identities, property scope, business date and correlation — never balances (consumers query or receive postings).

## 5. Financial responsibility model — worked scenarios

The mandate requires these realities to be modelled explicitly. Each scenario below shows the entities involved and the invariants that bind them.

### 5.1 Multi-room family booking

One Reservation with three ReservationRooms, two ReservationGuests each (one primary per room), payer = one guest.
- Each room-night creates an InventoryAllocation (INV-RSV-3); one routing instruction assigns all room charges to the payer's BillingWindow; incidentals default to each room's own window.
- Check-in creates three Stays; folio group contains three folios plus the payer's window arrangement.
- Charging is per room with INV-FOL-4 responsibility evidence; the payer sees one consolidated statement.

### 5.2 Shared room with split responsibility

Two guests share one room; room charge split 50/50; one pays cash, the other's company is billed.
- SharedReservationLink connects their reservations; ShareAllocation on the room charge produces two charge lines with separate responsibility evidence (INV-FOL-12 — no duplicated charges).
- One folio window settles by cash payment; the other is transferred by CityLedgerTransfer to the company AR after a CreditProfile check (INV-FOL-9).

### 5.3 Corporate direct bill with agent commission

Company pays room + tax; guest pays incidentals; agent earns commission.
- BillingInstruction at the Account sets default routing (CAP-CRP-003); the routing rule version is captured per charge (INV-FOL-4).
- At checkout the room+tax balance transfers to the company AR (one AccountingDocumentLink), incidentals settle at the desk.
- CommissionAgreement drives a commission calculation at settlement; commission posts as its own financial event (INV-GRP-7).

### 5.4 Group block, rooming list and master account

40-room block with contracted rate, cutoff and deposit; banquet billed to the master account.
- GroupBlock holds inventory (INV-GRP-1/2); PickupLinks track conversion; leftover releases at cutoff with evidence.
- RoomingList maps named guests to reservation rooms; group rate applies only to linked reservations (INV-GRP-3).
- Group charges route to the master folio's windows; the group Deposit applies to the master balance (deposit conservation, INV-FOL-7).

### 5.5 Deposit, cancellation and no-show

Deposit received at booking; cancellation inside the penalty window; partial forfeiture.
- Deposit lifecycle: received → held → partially forfeited (policy basis + authority) → remainder refunded with method fidelity (INV-FOL-8).
- No-show: the reservation transitions on the business date; guarantee terms snapshot governs the charge; remaining deposit liabilities resolve the same day (CAP-RSV-005).

### 5.6 Partial payments, cashiering and disputes

Guest prepays by transfer; pays the remainder by POS; later disputes the POS charge.
- Payment AR tracks each method's true state: transfer pending verification → cleared; POS received → pending settlement → cleared against acquirer batch (INV-FOL-6).
- CashierSession bundles the POS receipt and closes with counted total (INV-FOL-10); the daily close reconciles to the acquirer batch (CAP-CSH-010, CAP-INA-004).
- DisputeCase manages representment; a loss posts a linked reversal (never an edit) via Accounting Interface (INV-ACC-4).

### 5.7 Rate history and mid-year changes

Rates amended effective 1 October; bookings exist across the change.
- RateAmount/RateVersion effective-dating (INV-RTM-1); past folio items retain their applied-basis snapshot (INV-RTM-2); new bookings use the new version; derived rates recompute only forward (INV-RTM-4).

### 5.8 Property-specific configuration change mid-stay

Service charge or tax rule changes during a guest's stay.
- Each night's charge computes against the rule effective on that business date; the snapshot is stored per item (INV-FOL-5, ADR-003). The statement shows the period-wise application without retroactive recomputation.

## 6. Deferred domain detail

- Loyalty program mechanics, CRM campaign entities, sales pipeline depth and BI models are target capabilities whose detailed models arrive with their enterprise phases (Ph12, Ph10, Ph15).
- Payment provider token models and bank statement entities are defined with the integration architecture (WP 0.7) and financial architecture (WP 0.4).
- Tax and statutory document entities are finalised in WP 0.4 (OQ-011, OQ-021).

## 7. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial target domain model issued with WP 0.2 pass 2 | PROPOSED |
