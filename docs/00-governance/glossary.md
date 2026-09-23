---
doc-id: GOV-GLOSSARY
title: SuiteFlow Glossary
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Domain Architecture (drafted); Product Owner (approval)
applies-to: all blueprint work
---

# SuiteFlow Glossary

Terms are defined as **SuiteFlow uses them**, which may differ from a specific vendor's usage. Where hospitality practice varies, the definition states the practice the blueprint targets. This glossary is normative for terminology inside the blueprint; documents must use these terms with these meanings.

## 1. Property and inventory

| Term | Definition |
|---|---|
| Property | A single hotel operation with its own rooms, calendar, rates, staff and financial reporting. The unit of operational tenancy. |
| Legal entity | The incorporated entity that contracts, invoices and is taxed. One legal entity may operate several properties; one property may involve several entities (e.g. owner and operator). |
| Property group | A named collection of properties managed or reported together (brand, cluster, ownership group). |
| Building / Wing / Floor | Physical hierarchy used to locate rooms and organise housekeeping and engineering work. |
| Room type | Sellable category of room (e.g. Deluxe King). Carries capacity, default occupancy and feature set. |
| Room | A uniquely numbered physical sellable space. May be taken out of sale (OOO/OOS) without changing the room type. |
| Sellable room type | The room type actually offered for sale, which may combine physical room types (e.g. Run of House). |
| Feature / attribute | Non-price characteristic used for matching guests to rooms (view, bed, accessibility, floor preference, smoking policy). |
| Pseudo room | A non-physical inventory record used for auxiliary sales or routing (e.g. parking, day-use packages, "house account" targets). Never occupied. |
| Day use | A stay with same-day arrival and departure, not occupying the room overnight. |
| House use | Rooms occupied by hotel staff or management for operational reasons; not revenue-producing. |
| Comp room | A room provided without charge; still recognised operationally and reported as a comp with an approval trail. |
| Physical inventory | Count of rooms that physically exist. |
| Sellable inventory | Physical inventory minus rooms taken out of sale (OOO/OOS, maintenance holds), plus overbooking allowance policy. |
| Overbooking | Deliberate sale beyond physical inventory under a configured policy; a target-state capability with control and reporting, not a defect. |
| OOO (Out of Order) | Room removed from inventory; nights are not counted as available for availability or occupancy statistics. |
| OOS (Out of Service) | Room temporarily not sellable but still counted as available inventory; used for light maintenance and short holds. |
| Room condition | Physical condition reported by housekeeping or engineering (clean, dirty, inspected, damaged, etc.), distinct from sale status. |
| Room status | Sale state of a room (vacant clean, vacant dirty, occupied, blocked, OOO/OOS, due out, etc.). |
| Discrepancy | Conflict between housekeeping-reported occupancy/condition and front-office/system state that requires resolution. |
| Allotment | Pre-agreed inventory held for a partner (group, corporate, agent, OTA) with release rules. |
| Block | Reserved inventory held for a group or event, with cutoff dates, pickup expectations and rate agreement. |
| Pickup | Rooms actually taken from a block compared with the block's committed quantity. |
| Waitlist | Requested stays that cannot be confirmed against current availability, managed for release when inventory opens. |
| Walk-in | A guest arriving without a prior reservation. |
| Walk | Moving a guest with a confirmed reservation to another property because the original cannot honour it; carries policy, cost and reporting consequences. |
| No-show | A guaranteed or non-guaranteed reservation that does not arrive by the property's defined cutoff. |
| Stop sell | A restriction that closes inventory for sale for a period or room type. |
| Restriction | Any control limiting sale: minimum/maximum length of stay, closed to arrival (CTA), closed to departure (CTD), minimum advance booking, stop sell. |

## 2. Reservations and rates

| Term | Definition |
|---|---|
| Reservation | A commitment to provide accommodation and services to a guest for a period under agreed commercial terms. The commercial contract of a stay. |
| Reservation room | One room line within a reservation; a reservation may contain several rooms. Each has its own room type, rate, occupancy and assignment lifecycle. |
| Reservation guest | A person attached to a reservation room. One reservation room has one primary guest and any number of accompanying guests. |
| Share (shared reservation) | Two or more reservations sharing one physical room; used for billing separation between co-occupants. |
| Split reservation | A stay split across room types or rooms over its duration (e.g. first night in a standard, remainder in a suite). |
| Join / merge | Operations that link or combine records (e.g. joining separate reservations for one guest) while preserving financial history. |
| Stay | The operational record of a guest actually occupying a room from check-in to check-out. Distinct from the reservation, which may exist without a stay (no-show, cancellation). |
| Arrival / Departure | Scheduled or actual calendar dates of stay. |
| Housekeeping status notation | Industry shorthand such as VC (vacant clean), VD (vacant dirty), OC (occupied clean), OD (occupied dirty), OOO, OOS, DND (do not disturb). |
| Guarantee | The commitment that secures a reservation (deposit, credit card, corporate guarantee, travel agent voucher). Determines no-show and cancellation consequences. |
| Deposit | Money received before or during the stay that reduces the guest's outstanding balance. Held as a liability until applied, refunded or forfeited. |
| Cancellation policy | The rule set determining refundability, forfeiture and fees when a reservation is cancelled within its penalty window. |
| Rate plan | The commercial product: a price basis plus inclusions, restrictions and cancellation terms (e.g. Bed & Breakfast, Advance Purchase). |
| BAR (Best Available Rate) | The reference public rate for a date and room type from which many other rates are derived. |
| Derived rate | A rate calculated from a base rate by a defined rule (percentage, fixed offset, floor/ceiling). |
| Negotiated rate | A rate contractually agreed with a corporate account or agent, with validity dates and production commitments. |
| Package | A rate plan including additional elements (meals, transfers, laundry, parking) whose value may be posted separately. |
| Fixed charge | A charge posted automatically on a defined schedule as part of a package or rate plan. |
| Routing instruction | A rule determining which folio or billing window receives which charge, based on charge type, date, quantity or responsibility. |
| Traces | Follow-up tasks attached to reservations, guests or rooms with a due date (e.g. "call guest at 18:00", "VIP amenity"). |
| Alerts | Prominent operational flags displayed at check-in or during service (e.g. allergies, blacklist, VIP, credit hold). |
| VIP | Guest classification triggering defined service levels and amenity standards. |
| Registration card | The document or acceptance record capturing stay-specific terms accepted at check-in, with the guest's evidence of acceptance. |
| Pre-arrival | Operational phase before arrival: confirmation, deposit collection, assignment, pre-registration, preference fulfilment. |
| Cancellation / Modification | Lifecycle events that change or terminate a reservation; each has policy consequences and audit requirements. |
| Room move | Transfer of an in-house guest or an upcoming reservation to a different room. |
| Upgrade / Downgrade | Change of room type relative to the reservation, with a price and policy consequence. |

## 3. Front office and housekeeping

| Term | Definition |
|---|---|
| Front desk | The operating desk and console from which arrivals, departures, in-house service and cashiering are handled. |
| Room rack | At-a-glance grid of rooms and their operational status, with guest and readiness information. |
| Tape chart (availability chart) | Time-based grid of inventory across dates, showing reservations, blocks and availability by room type. |
| Front Desk room state | Sale/occupancy state managed by front office (vacant, occupied, due out, blocked). |
| Housekeeping room state | Cleaning/readiness state managed by housekeeping (dirty, in progress, clean, inspected). |
| Room assignment | Binding a reservation room to a physical room. |
| Do Not Move | Instruction preventing automatic or unauthorised room movement for a reservation or room. |
| Check-in | The governed transition that creates the stay, accepts the registration terms, sets up billing and grants access. |
| Check-out | The governed transition that settles or transfers the balance, ends the stay, releases the room and triggers housekeeping. |
| Early check-in / Late check-out | Arrival before or departure after standard times, with policy and charging consequences. |
| Extension | Continuation of an in-house stay beyond its scheduled departure, subject to availability and rate. |
| In-house | Guest currently occupying a room. |
| Due out | Occupied room scheduled to depart on the current business date. |
| Room queue | Ordered list of rooms available for assignment, with attributes and suitability ranking. |
| Housekeeping task | A unit of work for cleaning, turndown, deep clean or inspection, associated with a room and date. |
| Stayover clean | Housekeeping service for an occupied room that is not departing. |
| Departure clean | Full cleaning after a guest departs. |
| Deep clean | Scheduled intensive cleaning that may require taking the room out of service. |
| Rush room | Room needed immediately; carries priority. |
| Inspection | Supervisor verification that a room meets standard before it is returned to sale. |
| Productivity | Housekeeping workload measure (credits, rooms per attendant, minutes per task). |
| Maintenance work order | A governed request to repair or service an asset, with priority, assignment and completion evidence. |
| Preventive maintenance | Scheduled maintenance that protects assets and prevents failure. |
| Return to inventory | Controlled process of taking a room out of OOO/OOS and restoring it to sale. |

## 4. Folio, cashiering, night audit and accounting

| Term | Definition |
|---|---|
| Folio | The guest's running account of charges, taxes, payments, deposits and adjustments for a stay. |
| Billing window | A numbered account attached to a reservation or stay that receives routed charges (e.g. Window 1 = guest, Window 2 = company). |
| Master folio | The primary folio for a group or event, receiving charges designated to the group's account. |
| Split folio | Separate accounts for one stay, dividing responsibility (e.g. room vs incidental charges). |
| Share folio | A folio structure supporting shared rooms with separate billing responsibilities. |
| Routing | The act of directing a charge to a specific account or billing window per configured instruction. |
| Transfer (charge transfer) | Moving an existing charge between folios or windows with an audit trail. |
| Charge | An amount owed for a delivered good or service (room, F&B, laundry, minibar, etc.). |
| Allowance / Adjustment | A reduction or correction applied to a charge or balance with reason and authority. |
| Void | Removal of an erroneous transaction before settlement, preserving the original record and reason. |
| Payment | Money received (cash, POS, transfer, cheque) that reduces the balance. |
| Settlement | The process by which a payment is confirmed as final (cash counted, POS matched to acquirer settlement, transfer verified, cheque cleared). |
| Deposit application | Use of a previously received deposit to settle charges. |
| Refund | Return of money to a payer or other approved recipient, with approval authority. |
| Forfeiture | Approved retention of a deposit following cancellation or no-show, converting liability to revenue. |
| Cashier session | A bounded shift during which an identified cashier is responsible for receipts and payouts. |
| House bank | The float of cash assigned to a cashier at session open. |
| Drop | A transfer of cash out of a cashier session (to the vault/safe) evidenced and counted. |
| Over/short | The variance between recorded cash transactions and counted cash at session close. |
| Cashier reconciliation | The governed comparison and approval that closes a cashier session. |
| Guest ledger | The subledger of amounts owed by, or held for, in-house and arriving guests. |
| City ledger / Guest ledger transfer | Transfer of a settled folio balance to a corporate or agent receivable (direct bill). |
| Direct bill | Credit arrangement where a corporate account or agent is invoiced after checkout instead of the guest settling. |
| Accounts receivable (AR) | Amounts owed to the hotel by corporates, agents and other credit customers. |
| Credit limit | Maximum exposure allowed for a credit account before further charges are blocked or require approval. |
| Business date | The hotel's operating date, which may differ from the calendar date until night audit completes. All financial posting is dated to the business date. |
| Night audit | The governed end-of-day process: validation, posting of nightly charges and taxes, revenue and balance reconciliation, business-date advancement, and rollover to the next operating day. |
| End of day (EOD) | The operational close executing the night audit sequence. |
| Income audit | Independent verification that revenue, settlements, taxes and adjustments for the business date are complete and correctly stated before or after close, depending on the configured control model. |
| Trial balance | Night-audit report reconciling charges, payments, taxes and balances across the guest ledger for the business day. |
| Revenue | Earned income from operations (rooms, F&B, other) recognised to the correct business date and account. |
| Posting | The act of recording a financial consequence in the correct ledger with the correct date, account, tax treatment and references. |
| General ledger (GL) | The accounting book of record for all financial transactions. |
| Control account | A GL account whose balance must reconcile with a defined subledger total (e.g. guest ledger, AR). |
| Reconciliation | The disciplined comparison of two independent records to prove agreement, with exception handling for differences. |
| Revenue recognition | The accounting determination of when revenue is earned; in this design tied to the approved business day, not to cash receipt. |
| Tax | Statutory charges on sales (e.g. VAT, consumption tax) collected on behalf of authorities, with correct rates, basis and filing periods. |
| Service charge | A charge for service, with distinct accounting and distribution rules from tax. |
| Pro forma invoice | A non-statutory preview of charges for a guest or corporate account. |
| Fiscal year | The legal accounting year used for reporting and closing. |
| Journal | An accounting entry structure (debit/credit lines) created by a posting process. |
| Close (financial) | The point at which a period's accounting becomes immutable except through a governed remediation record. |

## 5. Groups, corporate and sales

| Term | Definition |
|---|---|
| Group | A set of reservations and/or rooms managed together for billing, inventory and service purposes. |
| Group booking | The commercial agreement for a group: dates, rooms, rates, deposits, billing instructions. |
| Rooming list | The list of named guests and room requirements belonging to a group. |
| Group block | Inventory and rate agreement held for a group; see Block. |
| Master account (group) | The folio receiving charges designated to the group. |
| Corporate account | A company with negotiated terms, billing arrangements and credit status. |
| Travel agent | An intermediary with commission and billing arrangements; distinct from a corporate account. |
| Source of business | Classification of where a booking originated (direct, corporate, agent, OTA, walk-in), used in reporting and commission. |
| Commission | Remuneration owed to an agent or channel for a booking, with its own accounting lifecycle. |
| Lead / Opportunity | A sales-stage prospect; converted to an account, contract or booking when won. |
| Sales contract | The agreement governing negotiated rates, allotments and terms for a corporate or group customer. |
| Event / Banquet | A function requiring space, catering and services, with its own account, schedule and billing. |
| Function space | Sellable non-room venue (meeting room, banquet hall) with capacity and setup attributes. |

## 6. Platforms, integration and controls

| Term | Definition |
|---|---|
| Tenant | The isolation boundary for a customer's data and configuration. In the pilot, one property per tenant. |
| Property scope | Authorization boundary restricting a user, session or API call to specific properties. |
| Company scope | Authorization boundary restricting financial visibility and actions to specific legal entities. |
| RBAC | Role-based access control: permissions assigned to roles, roles assigned to users, enforced server-side. |
| SoD (separation of duties) | Control preventing one person from performing conflicting actions (e.g. receive cash and approve its reconciliation) without an authorized, audited exception. |
| Maker–checker | Control requiring one person to propose and another to approve a sensitive action. |
| Idempotency | Property that repeating the same request produces the same outcome, never a duplicate effect. |
| Outbox | Durable record of events written in the same transaction as the state change, guaranteeing they are published. |
| Adapter | Boundary component that translates between SuiteFlow domain interfaces and an external provider's protocol, keeping provider specifics out of domain logic. |
| Webhook | Provider-initiated HTTP callback; treated as untrusted input requiring authentication, deduplication and validation. |
| Reconciliation (integration) | Comparison of local and external records for an interface, with exception queue and resolution evidence. |
| PITR | Point-in-time recovery of a database using continuous logs. |
| RPO / RTO | Recovery point objective (acceptable data loss) and recovery time objective (acceptable service interruption). Both are 1 hour for the pilot. |
| Nightly batch | Scheduled job executing nightly processing (postings, reporting, interfaces). |
| Business-date lock | Control preventing posting to a closed or not-yet-open business date. |
| Pseudonymisation | Replacing identifying data with references so records can be processed without exposing identity. |
| Consent | Purpose-specific, revocable permission from a guest for defined processing (e.g. marketing) with recorded evidence. |
| Audit event | Immutable record of who did what, when, to which record, with before/after state, reason and authority. |
| Governed action | A state-changing operation that must pass through domain validation, authorization and audit — as opposed to a raw data write. |
| AI action | Any AI-initiated proposal or execution; proposals are advisory, execution passes through the same governed services as human actions. |
| Source of truth | The single authoritative system of record for a class of data or decision; other copies are derived and must reconcile. |
| Derived data | Data computed from a source of truth, rebuildable and never authoritative. |
| Golden day | A deterministic, fully reconciled hotel operating day used as an acceptance fixture across revenue, settlement and close. |
| UAT | User acceptance testing with hotel and finance participants against agreed scenarios. |

## 7. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial glossary issued with WP 0.1 pass 1 | PROPOSED |
