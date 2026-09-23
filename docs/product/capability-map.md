---
doc-id: PROD-CAPMAP
title: Enterprise Capability Map
status: PROPOSED
version: 0.2
date: 2026-09-23
owner: Product Owner (accountable); Domain Architecture (maintainer)
applies-to: full enterprise target
depends-on: [PROD-VISION, PROD-SCOPE, PROD-PERSONAS, GOV-CHARTER]
---

# Enterprise Capability Map

## 1. Purpose

This map is the authoritative inventory of everything SuiteFlow must be able to do in the enterprise target. It is the traceability root: business rules, workflows, state machines, domain objects, APIs, screens, permissions, tests and documentation must all cite a capability ID from this map. A capability that is not in this map cannot be committed to the product until the map is amended through change control.

The map is deliberately implementation-free. It says **what must be possible and why**, never how or where it is coded.

## 2. How to read the map

Each capability carries seven attributes:

| Attribute | Meaning |
|---|---|
| **ID** | Stable identifier `CAP-<DOMAIN>-NNN`. Never reused, never renumbered. |
| **Capability** | The outcome the product must deliver. |
| **Business problem** | Why it exists. If this cannot be stated plainly, the capability is not understood. |
| **Owner** | Target functional owner in the architecture (see legend below). |
| **Pri** | Priority P0–P4 (mandate §48). |
| **Pilot** | `Yes` in pilot release; `Pend OQ-nnn` awaiting a product decision; `E<n>` targeted in enterprise phase n. |
| **Depends on** | Capabilities materially required first. |

### 2.1 Owner legend

| Code | Target owner | Scope of ownership |
|---|---|---|
| **CORE** | Hotel Operations Core | Operational source records: properties, rooms, reservations, stays, folios, housekeeping tasks |
| **CTRL** | SuiteFlow Control Plane | Governed state changes, policy decisions, business date, validation, orchestration, reconciliation, audit events |
| **ACC** | Accounting Authority | General ledger, receivables, tax documents, statutory books and period close |
| **PLAT** | Platform Services | Identity, authorization, tenancy, configuration, events, notifications, observability, recovery |
| **EXT** | External Provider | Capability delivered by a third party (bank, acquirer, channel, messaging, lock, fiscal) |
| **ADP** | SuiteFlow Adapter | SuiteFlow-owned boundary that translates between the platform and external providers |

Mapping these functional owners onto concrete components — including whether current products (the hotel operations application, the accounting application, the control plane) satisfy their candidate roles — is decided in **WP 0.2** with explicit ADR outcomes. Nothing in this map presumes the current implementation.

### 2.2 Priority legend

| Priority | Meaning |
|---|---|
| **P0** | The system cannot function without it. |
| **P1** | Required for serious production operation. |
| **P2** | Important enterprise capability. |
| **P3** | Advanced capability. |
| **P4** | Optional or future capability. |

### 2.3 Pilot legend

`Yes` — in pilot release scope. `Pend OQ-nnn` — pilot inclusion depends on the named open question (hotel operating reality). `Ph<n>` — targeted in enterprise phase n of the roadmap (phases 10+ run after the pilot; see `roadmap.md`). Pilot status never lowers target priority; a P1 enterprise capability can be deferred from the pilot by accepted MVP boundary.

## 3. Summary

<!-- COUNTS -->

**Total: 283 capabilities across 25 domains.**

### 3.1 Priority distribution

| Priority | Count | Meaning in this map |
|---|---:|---|
| P0 | 57 | The system cannot function without it. |
| P1 | 131 | Required for serious production operation. |
| P2 | 64 | Important enterprise capability. |
| P3 | 25 | Advanced capability. |
| P4 | 6 | Optional or future capability. |

### 3.2 Target owner distribution

| Owner | Count |
|---|---:|
| CORE — Hotel Operations Core | 135 |
| CTRL — Control Plane | 89 |
| PLAT — Platform Services | 28 |
| ADP — SuiteFlow Adapter | 18 |
| ACC — Accounting Authority | 13 |

### 3.3 Pilot and phase distribution

| Scope | Count |
|---|---:|
| Pilot release (`Yes`) | 160 |
| Resolved hotel-reality decision (`OQ-0nn (closed)`) | 48 |
| Pending hotel-reality decision (`Pend OQ-0nn`) | 3 |
| Enterprise phase (after pilot) | 72 |

### 3.4 Scope caution

Of the 160 pilot-flagged capabilities, 156 are P0 or P1. That is a large commitment against a 24-week horizon and is a live risk (RSK-ARCH-003, RSK-PROD-001). This map does not shrink the pilot silently; de-scoping is a Product Owner decision, recorded explicitly against this map when the pilot release scope is frozen. The honest reading of this summary is: **the target is complete; the pilot is still to be negotiated.** The 48 resolved hotel-reality decisions (adopted 23 Sep 2026) fold into that freeze review; their scope effect is stated in `00-governance/industry-standard-answers.md`, and they do not silently enlarge the pilot.

## 4. Domain index

| Domain | Code | Section | Capabilities | Mandate pillar |
|---|---|---|---|---|
| Property & inventory management | PM | §5.1 | 13 | Property Management |
| Reservations | RSV | §5.2 | 23 | Reservations |
| Availability & inventory control | AVL | §5.3 | 10 | Availability |
| Rates & revenue management | RTM | §5.4 | 13 | Rates |
| Front office | FO | §5.5 | 19 | Front Office |
| Guest profiles & privacy | GST | §5.6 | 12 | Guest |
| Groups & events | GRP | §5.7 | 12 | Groups |
| Corporate & travel trade | CRP | §5.8 | 10 | Corporate |
| Housekeeping | HSK | §5.9 | 12 | Housekeeping |
| Maintenance & engineering | MNT | §5.10 | 8 | Maintenance |
| Folio & billing | FOL | §5.11 | 13 | Folio |
| Cashiering | CSH | §5.12 | 10 | Cashiering |
| Night audit & business date | NAU | §5.13 | 10 | Night Audit |
| Income audit | INA | §5.14 | 8 | Night Audit / Auditability |
| Accounting & finance integration | ACC | §5.15 | 12 | Accounting |
| Outlets & F&B | POS | §5.16 | 10 | POS / F&B |
| Inventory & purchasing | INV | §5.17 | 8 | Inventory / Purchasing |
| Sales & catering | SAL | §5.18 | 10 | Sales |
| Reporting | RPT | §5.19 | 12 | Reporting |
| Analytics & BI | BI | §5.20 | 6 | BI |
| CRM & communication | CRM | §5.21 | 7 | CRM |
| Workforce & HR boundary | HRM | §5.22 | 5 | HR |
| Integrations | INT | §5.23 | 14 | Integrations |
| Platform & administration | PLT | §5.24 | 16 | Platform |
| AI assistance | AI | §5.25 | 10 | AI |

## 5. Capability catalogue

### 5.1 PM — Property & inventory management

The physical and commercial definition of what a hotel sells. Everything operational, statistical and financial derives from this domain, so its records must be effective-dated, auditable and never silently mutated.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-PM-001 | Property master record (identity, timezone, currency, contact, operating calendar) | Every operation, statistic and posting needs one authoritative property definition | PLAT | P0 | Yes | — |
| CAP-PM-002 | Legal entity mapping (ownership, tax identity, reporting entity) | Hotels trade, tax and report through legal entities; the system must know which | PLAT | P0 | Yes | CAP-PM-001 |
| CAP-PM-003 | Property groups and multi-property hierarchy | Groups and chains need an explicit organisational structure for configuration and reporting | PLAT | P2 | Ph14 | CAP-PM-001 |
| CAP-PM-004 | Buildings, floors, wings and housekeeping zones | Housekeeping, maintenance and service delivery need physical structure | CORE | P0 | Yes | CAP-PM-001 |
| CAP-PM-005 | Room type catalogue (capacity, bedding, default occupancy, classification) | Selling and pricing require stable, unambiguous product definitions | CORE | P0 | Yes | CAP-PM-004 |
| CAP-PM-006 | Room inventory and numbering | Physical sellable units need identity, location and controlled number series | CORE | P0 | Yes | CAP-PM-004 |
| CAP-PM-007 | Room features and attributes | Guest preference matching and assignment quality require structured attributes | CORE | P0 | Yes | CAP-PM-006 |
| CAP-PM-008 | Room operational status model (front office and housekeeping states, condition) | Two departments maintain room truth; unmanaged divergence causes sold-dirty rooms and false occupancy | CORE | P0 | Yes | CAP-PM-006 |
| CAP-PM-009 | Out-of-order and out-of-service control with maintenance holds | Removing inventory for repair must be governed and reflected in availability immediately | CTRL | P0 | Yes | CAP-PM-006, CAP-MNT-007 |
| CAP-PM-010 | Return-to-inventory governance | A repaired room must not be sold before verification and release | CTRL | P1 | Yes | CAP-PM-009, CAP-MNT-008 |
| CAP-PM-011 | Pseudo rooms and auxiliary sellable items | Parking, day use and house accounts need sellable identities without physical rooms | CORE | P2 | Ph11 | CAP-PM-005 |
| CAP-PM-012 | Room pools and assignment groups | Operational assignment quality improves when rooms can be grouped by suitability | CORE | P1 | Yes | CAP-PM-004 |
| CAP-PM-013 | Property configuration and feature flags | Behaviour varies by property (policies, timings, controls) without code changes | PLAT | P0 | Yes | CAP-PM-001 |

### 5.2 RSV — Reservations

The commercial contract of hospitality. Reservations must handle real party shapes (multiple rooms, shared occupancy, changing plans) while preserving determinism and financial traceability.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-RSV-001 | Reservation search and lookup | Agents must find a booking instantly by name, number, room, date, company or channel | CORE | P0 | Yes | CAP-GST-001 |
| CAP-RSV-002 | Reservation creation for multi-room and multi-guest parties | Real bookings involve several rooms and accompanying guests, not one row per party | CORE | P0 | Yes | CAP-AVL-001, CAP-RTM-001, CAP-GST-002 |
| CAP-RSV-003 | Reservation modification with amendment history | Changes must be traceable, price-correct and reversible in evidence | CORE | P0 | Yes | CAP-RSV-002 |
| CAP-RSV-004 | Cancellation with deterministic policy application | Refunds, penalties and forfeitures must follow configured policy, not negotiation | CTRL | P1 | Yes | CAP-RSV-014, CAP-FOL-008 |
| CAP-RSV-005 | No-show determination and processing | Guaranteed revenue must be secured and inventory released consistently | CTRL | P1 | Yes | CAP-RSV-014, CAP-NAU-001 |
| CAP-RSV-006 | Waitlist management | Unfulfillable demand should convert automatically when inventory opens | CORE | P2 | Ph10 | CAP-AVL-001 |
| CAP-RSV-007 | Walk-in and same-day booking | The front desk must create compliant bookings at the counter | CORE | P0 | Yes | CAP-RSV-002 |
| CAP-RSV-008 | Room assignment and pre-assignment | Early, intelligent assignment raises service quality and operational speed | CORE | P0 | Yes | CAP-AVL-001, CAP-PM-012 |
| CAP-RSV-009 | Room moves for pre-arrival and in-house stays | Moves must preserve rate, billing and stay history integrity | CORE | P0 | Yes | CAP-FO-004, CAP-FOL-004 |
| CAP-RSV-010 | Upgrades and downgrades with pricing rules | Room-type changes must price and bill correctly and consistently | CTRL | P1 | Yes | CAP-RTM-001 |
| CAP-RSV-011 | Shared reservations (co-occupants with separate billing) | Co-occupants often pay separately; one folio cannot represent the contract | CORE | P2 | Ph10 | CAP-FOL-002 |
| CAP-RSV-012 | Split stays across room types or rooms | Stays may legitimately change product mid-stay without breaking billing continuity | CORE | P2 | Ph10 | CAP-RSV-009, CAP-FOL-004 |
| CAP-RSV-013 | Merge, join and linked reservations | Related bookings (family, crew, agent) must be manageable as a set | CORE | P2 | Ph10 | CAP-RSV-002 |
| CAP-RSV-014 | Guarantee and deposit scheduling | Securing a booking requires an explicit, enforceable guarantee instrument | CTRL | P1 | Yes | CAP-FOL-008, CAP-FOL-010 |
| CAP-RSV-015 | Rate selection and negotiated-rate resolution | The right rate must apply automatically, defensibly and by eligibility | CORE | P0 | Yes | CAP-RTM-004 |
| CAP-RSV-016 | Packages and fixed charges | Bundled offers must have their components posted correctly across the stay | CORE | P1 | Yes | CAP-RTM-005, CAP-FOL-003 |
| CAP-RSV-017 | Routing instructions and charge responsibility | Charges must reach the correct payer without manual intervention late at night | CTRL | P1 | Yes | CAP-FOL-005, CAP-CRP-003 |
| CAP-RSV-018 | Traces, alerts and reservation notes | Operational follow-ups and warnings must survive shift change and reach the right desk | CORE | P0 | Yes | CAP-RSV-002 |
| CAP-RSV-019 | Preference capture at booking and promise tracking | Service promises made during booking must reach operations and be fulfilled | CORE | P1 | Yes | CAP-GST-003 |
| CAP-RSV-020 | Confirmation and pre-arrival communication | Guests need accurate terms, totals and arrival instructions | CORE | P1 | Yes | CAP-CRM-006 |
| CAP-RSV-021 | Registration capture and pre-registration | Arrival terms should be accepted once, with evidence, before the queue forms | CTRL | P1 | Yes | CAP-FO-003 |
| CAP-RSV-022 | Reservation lifecycle state machine and transition audit | Every reservation state change must be legal, authorized, evidenced and reversible in evidence | CTRL | P0 | Yes | CAP-PLT-006, CAP-PLT-007 |
| CAP-RSV-023 | Day-use bookings | Same-day accommodation is legitimate revenue and must not consume overnight inventory | CORE | P2 | Yes | CAP-AVL-001 |

### 5.3 AVL — Availability & inventory control

The commercial inventory engine. Availability is a promise; the system must never oversell by accident, only by explicit policy.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-AVL-001 | Availability calculation by date and room type | Overselling and underselling both destroy value; the number must be deterministic | CORE | P0 | Yes | CAP-PM-006, CAP-RSV-002 |
| CAP-AVL-002 | Sellable versus physical inventory | Availability must reflect rooms taken out of service and holds | CORE | P0 | Yes | CAP-PM-009 |
| CAP-AVL-003 | Inventory rebuild and reconciliation | Derived counts must be rebuildable and provably consistent with source records | CTRL | P1 | Yes | CAP-AVL-001, CAP-PLT-006 |
| CAP-AVL-004 | Restrictions and stop sell | Revenue controls (stop sell, closed to arrival/departure, LOS rules) must be deterministic | CORE | P0 | Yes | CAP-RTM-009 |
| CAP-AVL-005 | Overbooking policy and limits | Deliberate oversell must be bounded, governed and reportable, never accidental | CORE | P2 | Ph10 | CAP-AVL-001 |
| CAP-AVL-006 | Allotments and partner inventory | Contracted inventory must be protected and released on schedule | CORE | P2 | Ph13 | CAP-GRP-002, CAP-CRP-002 |
| CAP-AVL-007 | Holds, blocks and group inventory | Group commitments must reserve inventory with explicit cutoffs and ownership | CORE | P1 | OQ-017 (closed) | CAP-GRP-002 |
| CAP-AVL-008 | Channel availability and ARI publication | Channels must always see the currently approved sellable inventory | ADP | P2 | Ph13 | CAP-INT-002 |
| CAP-AVL-009 | Inventory change audit | Every inventory change must be attributable to a person, policy or process | CTRL | P1 | Yes | CAP-PLT-006 |
| CAP-AVL-010 | Function-space availability | Conference and banquet space is sellable inventory with its own competition | CORE | P2 | OQ-017 (closed) | CAP-GRP-012 |

### 5.4 RTM — Rates & revenue management

Pricing is the highest-leverage commercial decision in a hotel. The system must make correct rates easy and wrong rates hard.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-RTM-001 | Rate plan catalogue (inclusions, restrictions, cancellation terms) | A rate is a commercial product, not a number; terms must be explicit | CORE | P0 | Yes | CAP-PM-005 |
| CAP-RTM-002 | BAR setup and rate calendar | Daily pricing across a year must be manageable at scale | CORE | P0 | Yes | CAP-RTM-001 |
| CAP-RTM-003 | Derived rates (rules, floors, ceilings) | Managed rate ladders must update safely without manual error | CORE | P1 | Yes | CAP-RTM-002 |
| CAP-RTM-004 | Negotiated and corporate rate agreements | Contracted rates must apply automatically to eligible bookings for their validity | CORE | P1 | Yes | CAP-CRP-002 |
| CAP-RTM-005 | Packages and inclusion pricing | Bundle components must be valued, restricted and posted correctly | CORE | P1 | Yes | CAP-RTM-001 |
| CAP-RTM-006 | Promotions and discount structures | Discounts must be controlled, authorized and reportable to their source | CTRL | P1 | Yes | CAP-RTM-001 |
| CAP-RTM-007 | Occupancy-based pricing (extra person, children, sharing) | Pricing must handle party composition deterministically | CORE | P0 | Yes | CAP-RTM-001 |
| CAP-RTM-008 | Length-of-stay pricing | LOS-based rates must calculate consistently at booking and at posting | CORE | P1 | Yes | CAP-RTM-001 |
| CAP-RTM-009 | Minimum/maximum stay and CTA/CTD enforcement | Restrictions must be enforced at booking, at amendment and in channels | CORE | P0 | Yes | CAP-AVL-004 |
| CAP-RTM-010 | Rate effectivity and history | Historical stays must retain the rate that actually applied | CORE | P0 | Yes | CAP-RTM-001 |
| CAP-RTM-011 | Rate change authority and audit | Price changes are high-consequence and must be attributable and reversible in evidence | CTRL | P1 | Yes | CAP-PLT-006 |
| CAP-RTM-012 | Rate management workspace with bulk operations | Revenue staff need speed without risking unintended mass changes | CTRL | P2 | Ph10 | CAP-RTM-002 |
| CAP-RTM-013 | Forecast and recommendation inputs (advisory only) | Pricing decisions benefit from pace and forecast; a human still decides | CORE | P3 | Ph19 | CAP-RPT-005, CAP-AI-003 |

### 5.5 FO — Front office

The operational heart of the hotel: arrival, stay and departure must be fast, controlled and complete.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-FO-001 | Arrivals list and readiness view | The day starts with knowing who is arriving and whether rooms will be ready | CORE | P0 | Yes | CAP-RSV-008, CAP-HSK-009 |
| CAP-FO-002 | Governed check-in | The moment identity, room, rate, credit and terms must all be right at once | CTRL | P0 | Yes | CAP-RSV-021, CAP-FOL-002, CAP-GST-007 |
| CAP-FO-003 | Registration evidence and identity capture | Legal registration and acceptance records must be captured once and retained lawfully | CTRL | P0 | OQ-019 (closed) | CAP-PLT-006 |
| CAP-FO-004 | Check-in policy gates with authorized overrides | Readiness, credit and policy must block bad check-ins with recorded exceptions | CTRL | P0 | Yes | CAP-FO-002, CAP-PLT-002 |
| CAP-FO-005 | In-house management console | Staff must know exactly who is in the building for service and safety | CORE | P0 | Yes | CAP-FO-002 |
| CAP-FO-006 | Room rack and operational board | Front desk needs an at-a-glance, always-current operational picture | CORE | P0 | Yes | CAP-PM-008, CAP-HSK-001 |
| CAP-FO-007 | Departures list and due-out management | Checkout peaks and late departures must be managed proactively | CORE | P0 | Yes | CAP-FO-005 |
| CAP-FO-008 | Governed check-out | Balances must be settled or transferred correctly before room release | CTRL | P0 | Yes | CAP-FOL-012, CAP-CSH-002 |
| CAP-FO-009 | Early check-in and late checkout | Extra time is a priced, policy-controlled concession, not a favour | CTRL | P1 | Yes | CAP-RTM-007 |
| CAP-FO-010 | Stay extensions | Extending a stay must re-check availability, rate and credit immediately | CORE | P0 | Yes | CAP-AVL-001, CAP-RSV-003 |
| CAP-FO-011 | Key/access issuance and lock integration | Physical access must follow authorized stay state and be revoked at checkout | ADP | P2 | OQ-020 (closed) | CAP-INT-010 |
| CAP-FO-012 | Guest requests and service tasks | Requests must have owners, deadlines and closure evidence | CORE | P1 | Yes | CAP-PLT-008 |
| CAP-FO-013 | Wake-up calls and guest messages | Classic guest services must be reliable, scheduled and recorded | CORE | P1 | Yes | CAP-PLT-008 |
| CAP-FO-014 | Incident and guest complaint management | Complaints are operational data with financial and reputational consequences | CORE | P1 | Yes | CAP-RSV-018 |
| CAP-FO-015 | Service recovery and compensation workflow | Making things right requires authority limits, record and accounting | CTRL | P1 | Yes | CAP-FOL-006, CAP-PLT-002 |
| CAP-FO-016 | Front office shift handover and audit trail | 24-hour operations depend on continuity between shifts | CTRL | P1 | Yes | CAP-PLT-006 |
| CAP-FO-017 | House use and comp room accommodation | Staff stays and complimentary rooms are real inventory and real cost; they need approval and accounting | CORE | P1 | Yes | CAP-FOL-006, CAP-RTM-011 |
| CAP-FO-018 | Guest relocation (walking) with cost recovery | When the hotel cannot honour a booking it must relocate the guest lawfully, at controlled cost | CTRL | P2 | Yes | CAP-AVL-001, CAP-FOL-009 |
| CAP-FO-019 | Early departure handling | Departing before the reserved date changes revenue and policy consequences deterministically | CTRL | P1 | Yes | CAP-FO-008, CAP-RTM-001 |

### 5.6 GST — Guest profiles & privacy

Hospitality is remembering people. Privacy is remembering them lawfully. This domain must serve both goals without compromise.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-GST-001 | Guest profile (golden record) | One guest, one history: duplicates and fragmentation destroy service and analytics | CORE | P0 | Yes | CAP-PM-001 |
| CAP-GST-002 | Contacts, addresses and documents | Multiple contact channels and identity documents must be structured, current and scoped | CORE | P0 | Yes | CAP-GST-001 |
| CAP-GST-003 | Preferences and accessibility needs | Remembering guests is the essence of hospitality and accessibility compliance | CORE | P1 | Yes | CAP-GST-001 |
| CAP-GST-004 | Guest relationships and company links | Families, companions, executives and corporate links change service and billing | CORE | P1 | Yes | CAP-GST-001 |
| CAP-GST-005 | Duplicate detection | Profiles must be quietly deduplicated before they corrupt credit and history | CORE | P1 | Yes | CAP-GST-001 |
| CAP-GST-006 | Governed profile merge with reversal evidence | Bad merges are destructive; merges must be controlled and reconstructable | CTRL | P1 | Yes | CAP-GST-005 |
| CAP-GST-007 | Watchlist and security flags | Duty of care and fraud prevention require discreet blocking and alerting | CORE | P1 | Yes | CAP-GST-001 |
| CAP-GST-008 | Consent and marketing preferences | Privacy law and guest trust require purpose-specific, versioned consent | CTRL | P1 | Yes | CAP-GST-001 |
| CAP-GST-009 | Data retention and erasure | Legal retention and privacy rights must be reconcilable and provable | CTRL | P1 | Pend OQ-024 | CAP-GST-008, CAP-PLT-006 |
| CAP-GST-010 | Guest history and production metrics | Lifetime value and history must reconcile to financial records | CORE | P1 | Yes | CAP-RPT-003 |
| CAP-GST-011 | Loyalty membership (provider-neutral) | Recognition programs need a real domain, not a free-text field | CORE | P2 | Ph12 | CAP-GST-001 |
| CAP-GST-012 | Guest 360 workspace | Service staff need one consolidated view with role-scoped access to sensitive fields | CORE | P1 | Yes | CAP-GST-001, CAP-PLT-002 |

### 5.7 GRP — Groups & events

Group and event business is often the profit centre of a full-service property, and the failure mode is commercial: inventory, rates and billing silently diverge between sales promises and operations.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-GRP-001 | Group account and group booking | A group is a commercial entity with its own terms, not a set of unrelated bookings | CORE | P1 | OQ-017 (closed) | CAP-CRP-001 |
| CAP-GRP-002 | Room blocks and inventory holds | Committed inventory must be protected, released on cutoff and visible to revenue | CORE | P1 | OQ-017 (closed) | CAP-AVL-007 |
| CAP-GRP-003 | Rooming list management | Named guests must replace anonymous occupancy before arrival day | CORE | P1 | OQ-017 (closed) | CAP-GRP-001, CAP-GST-001 |
| CAP-GRP-004 | Pickup tracking and wash reporting | Commitments versus actuals drive deposit, attrition and future sales decisions | CORE | P1 | OQ-017 (closed) | CAP-GRP-002 |
| CAP-GRP-005 | Group rates and contract terms | Contracted pricing must apply to eligible stays without dispute | CORE | P1 | OQ-017 (closed) | CAP-RTM-004 |
| CAP-GRP-006 | Master account and group billing | Group charges must consolidate to the correct payer, not to a random room | CTRL | P1 | OQ-017 (closed) | CAP-FOL-002, CAP-FOL-005 |
| CAP-GRP-007 | Group deposits and guarantees | Large commitments need secured financial consequences | CTRL | P1 | OQ-017 (closed) | CAP-FOL-008 |
| CAP-GRP-008 | Group cancellation and attrition calculation | Attrition and cancellation terms must be calculated from data, not argued | CTRL | P2 | Ph10 | CAP-GRP-004, CAP-GRP-005 |
| CAP-GRP-009 | Group adjustments with authority limits | Group goodwill has financial consequences and needs approval discipline | CTRL | P1 | OQ-017 (closed) | CAP-FOL-006 |
| CAP-GRP-010 | Group activity and communication timeline | Sales, operations and finance must share one group history | CORE | P2 | Ph10 | CAP-GRP-001 |
| CAP-GRP-011 | Event and banquet management | Functions combine space, catering, schedule and billing on their own account | CORE | P2 | Ph10 | CAP-GRP-012, CAP-POS-001 |
| CAP-GRP-012 | Function-space inventory and event calendar | Space is sellable inventory and must not be double-booked | CORE | P2 | OQ-017 (closed) | CAP-AVL-010 |

### 5.8 CRP — Corporate & travel trade

Credit and contracts turn rooms into receivables. The failure modes are financial (unsecured exposure) and commercial (rates applied to the wrong customer).

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-CRP-001 | Corporate and agency account master | Corporate customers need a single account with creditworthy identity and history | CORE | P1 | Yes | CAP-PM-001 |
| CAP-CRP-002 | Negotiated rate agreements with validity | Contracted terms must apply only while valid and only to eligible bookings | CORE | P1 | Yes | CAP-RTM-004 |
| CAP-CRP-003 | Billing instructions and default routing | Corporate billing expectations must translate into deterministic charge routing | CTRL | P1 | Yes | CAP-FOL-005 |
| CAP-CRP-004 | Credit limits, holds and exposure monitoring | Unsecured exposure must be bounded before it becomes bad debt | CTRL | P1 | Yes | CAP-ACC-004 |
| CAP-CRP-005 | Direct-bill origination and AR transfer | Settling to a corporate account must be a governed financial event, not a button | CTRL | P1 | Yes | CAP-FOL-012, CAP-ACC-004 |
| CAP-CRP-006 | Travel-agent commission lifecycle | Agent commissions must be calculated, approved, paid and reported | CORE | P2 | Ph10 | CAP-ACC-004 |
| CAP-CRP-007 | Account production and review | Account value must be visible to justify terms and review them | CORE | P2 | Ph10 | CAP-RPT-003 |
| CAP-CRP-008 | Account hierarchy (global/local links) | Chains and groups negotiate centrally and bill locally | CORE | P3 | Ph14 | CAP-CRP-001 |
| CAP-CRP-009 | Account contacts and sales activities | Commercial relationships must outlive individual salespeople | CORE | P2 | Ph10 | CAP-SAL-002 |
| CAP-CRP-010 | Corporate statements and reporting | Corporate customers need invoices and statements that match their records | ACC | P2 | OQ-011 (closed) | CAP-ACC-011 |

### 5.9 HSK — Housekeeping

Rooms are the inventory; readiness is the product. Housekeeping quality and honesty about room state directly determine what can be sold.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-HSK-001 | Real-time room board | Supervisors need live truth about every room, not stale list calls | CORE | P1 | Yes | CAP-PM-008 |
| CAP-HSK-002 | Automatic task generation (departure, stayover, deep, rush) | Work must appear from actual hotel state, not from a manual list | CORE | P1 | Yes | CAP-FO-008, CAP-PM-004 |
| CAP-HSK-003 | Attendant assignment and workload balancing | Fair, efficient distribution determines whether rooms are ready on time | CORE | P1 | Yes | CAP-HSK-002, CAP-PM-012 |
| CAP-HSK-004 | Attendant console (mobile-first) | Room attendants work on their feet; software must fit the job | CORE | P1 | Yes | CAP-HSK-003 |
| CAP-HSK-005 | Stayover and departure cleaning workflows | Standard sequences with status transitions prevent skipped steps | CORE | P1 | Yes | CAP-HSK-002 |
| CAP-HSK-006 | Deep-clean programs | Periodic intensive work must be scheduled without surprising the desk | CORE | P2 | Ph11 | CAP-HSK-005, CAP-PM-009 |
| CAP-HSK-007 | Priority, rush and VIP room handling | High-value arrivals must not be left waiting behind routine work | CORE | P1 | Yes | CAP-HSK-002, CAP-GST-003 |
| CAP-HSK-008 | Minibar and in-room consumption capture | In-room sales must be posted before checkout, every time | CORE | P1 | OQ-014 (closed) | CAP-FOL-003 |
| CAP-HSK-009 | Inspection, readiness approval and release to sale | Untrusted readiness destroys the front desk's ability to sell with confidence | CTRL | P1 | Yes | CAP-HSK-005, CAP-PM-010 |
| CAP-HSK-010 | Room discrepancy resolution | Housekeeping and front office must reconcile physical vs system truth daily | CTRL | P1 | Yes | CAP-PM-008, CAP-NAU-002 |
| CAP-HSK-011 | Productivity, workload and standard reporting | Labour cost and quality need measurement without surveillance excess | CORE | P2 | Ph11 | CAP-HSK-003 |
| CAP-HSK-012 | Lost and found tracking | Guest property must be traceable, returnable and lawfully handled | CORE | P2 | Ph11 | CAP-GST-001 |

### 5.10 MNT — Maintenance & engineering

Assets fail. The difference between a hotel that repairs and one that degrades is whether maintenance is a workflow with evidence or a phone call.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-MNT-001 | Work-order intake from any source | Issues reported by staff or guests must become tracked work, not lost messages | CORE | P1 | OQ-022 (closed) | CAP-FO-014, CAP-HSK-004 |
| CAP-MNT-002 | Assignment, priority and SLA tracking | Work must reach the right trade with a defined urgency | CORE | P1 | OQ-022 (closed) | CAP-MNT-001 |
| CAP-MNT-003 | Work execution and completion evidence | Completion claims need evidence (notes, parts, photos) for accountability | CORE | P1 | OQ-022 (closed) | CAP-MNT-002 |
| CAP-MNT-004 | Asset and equipment register | Preventive maintenance and capital planning require knowing what exists | CORE | P2 | Ph11 | CAP-PM-004 |
| CAP-MNT-005 | Preventive maintenance schedules | Failure prevention is cheaper than repair and lost inventory | CORE | P2 | Ph11 | CAP-MNT-004 |
| CAP-MNT-006 | Parts, materials and cost capture | Maintenance cost must reach the accounts, not vanish into purchases | CORE | P2 | Ph11 | CAP-INV-005, CAP-ACC-001 |
| CAP-MNT-007 | OOO/OOS request and hold integration | Maintenance holds must instantly affect sellable inventory | CTRL | P1 | OQ-022 (closed) | CAP-PM-009 |
| CAP-MNT-008 | Verification and return-to-service sign-off | A room must pass verification before it is sold again | CTRL | P1 | OQ-022 (closed) | CAP-MNT-003, CAP-PM-010 |

### 5.11 FOL — Folio & billing

The financial heart of a stay. Every naira a guest owes or has paid must live in a folio that can be explained, transferred and reconciled.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-FOL-001 | Folio creation and lifecycle | Every stay, group and event needs accounts that open, close and never lose history | CORE | P0 | Yes | CAP-FO-002 |
| CAP-FOL-002 | Billing windows (multiple accounts per stay) | Guests, companies and groups often split responsibility within one stay | CORE | P1 | Yes | CAP-FOL-001 |
| CAP-FOL-003 | Charge posting (rooms, F&B, services, misc) | Charges must land on the right account, date and revenue classification, once | CORE | P0 | Yes | CAP-FOL-001, CAP-ACC-001 |
| CAP-FOL-004 | Charge transfer and folio moves | Charges often land in the wrong place first; moving them must be safe and evidenced | CTRL | P1 | Yes | CAP-FOL-003 |
| CAP-FOL-005 | Routing and split responsibility rules | Standing instructions must route charges automatically and predictably | CTRL | P1 | Yes | CAP-FOL-002 |
| CAP-FOL-006 | Adjustments, allowances and voids with authority | Reductions are where leakage hides; they must require reason and authority | CTRL | P1 | Yes | CAP-PLT-002, CAP-PLT-006 |
| CAP-FOL-007 | Payment capture for all accepted methods | Cash, POS, transfer and cheque must each be recorded with their true state | CORE | P0 | Yes | CAP-CSH-003 |
| CAP-FOL-008 | Deposits and prepayment application | Money held before arrival is a liability until correctly applied | CTRL | P1 | Yes | CAP-ACC-006 |
| CAP-FOL-009 | Refunds and reversals | Returning money requires authority, method fidelity and a complete trail | CTRL | P1 | Yes | CAP-FOL-007, CAP-CSH-006 |
| CAP-FOL-010 | Tax and service-charge calculation | Statutory and policy charges must be calculated deterministically and dated correctly | CTRL | P0 | OQ-021 (closed) | CAP-PLT-014, CAP-ACC-005 |
| CAP-FOL-011 | Folio invoice and pro forma documents | Guests and corporates need accurate documents in the required legal form | CORE | P1 | OQ-011 (closed) | CAP-ACC-011 |
| CAP-FOL-012 | Folio balance integrity and reconciliation | The balance must always equal charges minus payments minus adjustments, provably | CTRL | P0 | Yes | CAP-FOL-003, CAP-FOL-007 |
| CAP-FOL-013 | Folio lock, checkout close and post-stay correction path | Closed accounts must be immutable except through a governed correction workflow | CTRL | P1 | Yes | CAP-FO-008, CAP-NAU-009 |

### 5.12 CSH — Cashiering

Cash is the easiest place to lose money and the hardest to reconstruct afterwards. Cashiering must make the honest path the fast path.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-CSH-001 | Till and house-bank assignment | Every cashier needs a defined float and a known starting point | CORE | P1 | Yes | CAP-CSH-002 |
| CAP-CSH-002 | Cashier session open and close | Accountability must be bounded in time and reviewable | CTRL | P1 | Yes | CAP-PLT-002 |
| CAP-CSH-003 | Receipts and payouts | All money movement needs immediate, correct recording at the counter | CORE | P0 | Yes | CAP-FOL-007 |
| CAP-CSH-004 | Drops and safe transfers | Cash must leave the desk under evidence before it becomes a risk | CORE | P1 | Yes | CAP-CSH-003 |
| CAP-CSH-005 | Count and variance declaration | Closure requires a counted reality, not an assumed one | CTRL | P1 | Yes | CAP-CSH-004 |
| CAP-CSH-006 | Supervisor review and variance approval | Variances require independent review with limits and reasons | CTRL | P1 | Yes | CAP-CSH-005, CAP-PLT-002 |
| CAP-CSH-007 | Cashier reconciliation and accounting | Cash differences must reach the accounts with the correct treatment | CTRL | P1 | Yes | CAP-CSH-006, CAP-ACC-008 |
| CAP-CSH-008 | Multi-cashier, multi-terminal and shift operations | Peak periods need parallel cashiers without merged accountability | CORE | P1 | Yes | CAP-CSH-002 |
| CAP-CSH-009 | Cashier audit, history and reporting | Repeated variances and patterns must be visible to management and audit | CTRL | P1 | Yes | CAP-CSH-006, CAP-RPT-008 |
| CAP-CSH-010 | POS terminal and acquirer reference capture | Card takings are not settled until matched to the acquirer's record | CORE | P1 | Yes | CAP-ACC-008, CAP-INT-005 |

### 5.13 NAU — Night audit & business date

The daily close is where a hotel's operating truth becomes its financial truth. Done well, it is boring. Done badly, it is where trust dies.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-NAU-001 | Business date model, lock and posting discipline | Financial postings must belong to the hotel's operating day, not a server clock | CTRL | P0 | Yes | CAP-PM-001 |
| CAP-NAU-002 | Pre-close validation checklist | Missing checkouts, open sessions and unposted charges must be caught before close | CTRL | P1 | Yes | CAP-FO-007, CAP-CSH-002, CAP-HSK-010 |
| CAP-NAU-003 | Nightly charge and tax posting run | Room and package revenue for the operating day must post once, correctly, every night | CTRL | P0 | Yes | CAP-NAU-001, CAP-RSV-016, CAP-FOL-010 |
| CAP-NAU-004 | Revenue and payment reconciliation | The day's revenue must agree across operational and financial views | CTRL | P1 | Yes | CAP-NAU-003, CAP-ACC-009 |
| CAP-NAU-005 | Balance and control-total checks | The close must prove itself (charges, payments, taxes, deposits) before advancing | CTRL | P1 | Yes | CAP-FOL-012 |
| CAP-NAU-006 | Exception queue and resolution tracking | Unresolved defects must block or qualify close with named ownership | CTRL | P1 | Yes | CAP-NAU-002, CAP-INA-006 |
| CAP-NAU-007 | Close execution, rollover and day reports | The operating day must turn over atomically and produce the management pack | CTRL | P0 | Yes | CAP-NAU-005 |
| CAP-NAU-008 | Close recovery and idempotency | A failed close must resume safely without double-posting | CTRL | P1 | Yes | CAP-PLT-007, CAP-NAU-007 |
| CAP-NAU-009 | Reopen and backdated-posting controls | Legitimate corrections must be possible; illegitimate history edits must not | CTRL | P1 | Yes | CAP-NAU-007, CAP-PLT-006 |
| CAP-NAU-010 | Day-end reports and handover pack | Management, finance and audit need the closed day's evidence as a unit | CTRL | P1 | Yes | CAP-NAU-007, CAP-RPT-002 |

### 5.14 INA — Income audit

Independent verification is the control that makes every other control credible. It must be designed as a workspace, not an afterthought report.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-INA-001 | Income-audit workspace around the closed day | The auditor needs one place to verify the whole day against source records | CTRL | P1 | Yes | CAP-NAU-007 |
| CAP-INA-002 | Revenue verification against operational sources | System totals must be proven against reservations, folios and outlets | CTRL | P1 | Yes | CAP-INA-001, CAP-ACC-002 |
| CAP-INA-003 | Adjustment, void, discount and comp audit | Where money is given away is where audit attention belongs | CTRL | P1 | Yes | CAP-FOL-006, CAP-RTM-006 |
| CAP-INA-004 | Cash and settlement audit | Cash and card takings must reconcile to counts and acquirer records | CTRL | P1 | Yes | CAP-CSH-007, CAP-CSH-010 |
| CAP-INA-005 | Deposit, refund and forfeiture audit | Guest money held and returned needs independent scrutiny | CTRL | P1 | Yes | CAP-FOL-008, CAP-FOL-009 |
| CAP-INA-006 | Exception register with ownership and follow-up | Findings must have owners, deadlines and closure evidence, not just be printed | CTRL | P1 | Yes | CAP-NAU-006 |
| CAP-INA-007 | Day certification (pass/hold) | A day must be independently certifiable before it is relied upon | CTRL | P1 | Yes | CAP-INA-002 |
| CAP-INA-008 | Audit reporting and trend analysis | Repeated findings must escalate to management, not repeat silently | CTRL | P2 | Ph11 | CAP-INA-006, CAP-RPT-008 |

### 5.15 ACC — Accounting & finance integration

The platform's financial output must be complete, correctly classified and reconcilable to the books, with a single recognition of revenue and no orphan transactions.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-ACC-001 | Financial mapping and transaction codes | Every operational event must translate into deterministic accounting classification | CTRL | P0 | Yes | CAP-PM-013 |
| CAP-ACC-002 | Revenue recognition and GL posting | Revenue must be recognised once, on the correct business day, to the correct accounts | ACC | P0 | Yes | CAP-ACC-001, CAP-NAU-003 |
| CAP-ACC-003 | Guest-ledger control account | The folio subledger must reconcile to a control account in the books, always | ACC | P1 | Yes | CAP-FOL-012, CAP-ACC-002 |
| CAP-ACC-004 | City ledger / AR accounting | Corporate and agent receivables need ageing, statements and credit control | ACC | P1 | Yes | CAP-CRP-005 |
| CAP-ACC-005 | Tax accounting and filing support | Collected taxes are liabilities to authorities, not income | ACC | P1 | OQ-021 (closed) | CAP-FOL-010 |
| CAP-ACC-006 | Deposit liability accounting | Money held before service is a liability until earned, refunded or forfeited | ACC | P1 | Yes | CAP-FOL-008, CAP-RSV-014 |
| CAP-ACC-007 | Service-charge accounting and distribution | Service charges have distinct rules from revenue and tax | ACC | P2 | OQ-021 (closed) | CAP-FOL-010 |
| CAP-ACC-008 | Payment clearing and bank accounting | Cash, card and transfer takings must each clear to the bank correctly | ACC | P1 | Yes | CAP-CSH-007, CAP-CSH-010 |
| CAP-ACC-009 | Reconciliation suite (control accounts and interfaces) | Unreconciled differences are how financial systems fail silently | ACC | P1 | Yes | CAP-ACC-002, CAP-ACC-008 |
| CAP-ACC-010 | Period close and financial calendar | Books must close on a defined calendar with controls and reopen discipline | ACC | P1 | Yes | CAP-ACC-009 |
| CAP-ACC-011 | Statutory and management reporting | Finance must produce required statements from reconciled data | ACC | P1 | OQ-011 (closed) | CAP-ACC-009 |
| CAP-ACC-012 | Accounting-system integration contract | Operational and accounting systems must exchange documents with identity and reconciliation | CTRL | P0 | Yes | CAP-PLT-007, CAP-ACC-002 |

### 5.16 POS — Outlets & F&B

Restaurant and bar revenue is fast, high-volume and easy to lose. Outlet operations must be as controlled as the front desk.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-POS-001 | Outlet configuration (restaurants, bars, room service as applicable) | Each outlet has its own pricing, taxes and settlement behaviour | CORE | P1 | OQ-014 (closed) | CAP-PM-013 |
| CAP-POS-002 | Menus, products and prices | Outlet pricing must be maintained centrally with effective dates | CORE | P1 | OQ-014 (closed) | CAP-POS-001 |
| CAP-POS-003 | Tables and order management | Table service needs a live order model tied to physical tables | CORE | P1 | OQ-014 (closed) | CAP-POS-001 |
| CAP-POS-004 | Bill/check management and splitting | Real tables split bills; the system must follow | CORE | P1 | OQ-014 (closed) | CAP-POS-003 |
| CAP-POS-005 | Discounts and comps with authority | Outlet giveaways are a leakage point and need the same discipline as front office | CTRL | P1 | OQ-014 (closed) | CAP-PLT-002 |
| CAP-POS-006 | Outlet tax handling | F&B tax treatment may differ from accommodation and must be configured | CTRL | P1 | OQ-014 (closed) | CAP-PLT-014 |
| CAP-POS-007 | Outlet payment capture and settlement | Outlet takings must reconcile to cashiers and acquirer records | CORE | P1 | OQ-014 (closed) | CAP-FOL-007, CAP-CSH-003 |
| CAP-POS-008 | Room posting with validation | Posting to a room must verify the stay and respect credit controls | CTRL | P1 | OQ-014 (closed) | CAP-FO-005, CAP-CRP-004 |
| CAP-POS-009 | Outlet cashiering and shift close | Outlet cash needs its own session discipline | CTRL | P1 | OQ-014 (closed) | CAP-CSH-002 |
| CAP-POS-010 | Outlet revenue and reconciliation reporting | Outlet revenue must reconcile to orders, postings and cash | CORE | P1 | OQ-014 (closed) | CAP-RPT-002, CAP-ACC-009 |

### 5.17 INV — Inventory & purchasing

What leaves the store must match what is sold and consumed. Without this chain, food and beverage cost is a guess.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-INV-001 | Item and recipe master | Consumption can only be deducted from sales if recipes are known | CORE | P2 | OQ-015 (closed) | CAP-POS-002 |
| CAP-INV-002 | Stores and warehouses | Physical storage locations must exist as inventory boundaries | CORE | P2 | OQ-015 (closed) | CAP-PM-001 |
| CAP-INV-003 | Suppliers and purchasing | Controlled ordering prevents uncontrolled spending | CORE | P2 | OQ-015 (closed) | CAP-INV-002 |
| CAP-INV-004 | Receiving and supplier invoice capture | Goods must be received against orders before they are paid | CORE | P2 | OQ-015 (closed) | CAP-INV-003 |
| CAP-INV-005 | Transfers and issues between stores and outlets | Movement between locations must be recorded or counts will never reconcile | CORE | P2 | OQ-015 (closed) | CAP-INV-002 |
| CAP-INV-006 | Consumption and recipe deduction | Theoretical consumption must be derived from actual sales | CORE | P2 | OQ-015 (closed) | CAP-INV-001, CAP-POS-003 |
| CAP-INV-007 | Stock counts and variance control | Physical counts are the proof; variance needs authority and explanation | CTRL | P2 | OQ-015 (closed) | CAP-INV-005 |
| CAP-INV-008 | Costing and inventory valuation integration | Stock value must reach the accounts correctly | ACC | P2 | Ph11 | CAP-INV-004, CAP-ACC-001 |

### 5.18 SAL — Sales & catering

Revenue is won before the guest arrives. Sales must convert promises into operational and financial commitments that survive handover.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-SAL-001 | Lead and opportunity management | Enquiries must be tracked from first contact to conversion or loss | CORE | P2 | Ph10 | CAP-CRP-001 |
| CAP-SAL-002 | Sales accounts and contacts | Commercial relationships need structure beyond a salesperson's inbox | CORE | P2 | Ph10 | CAP-CRP-001 |
| CAP-SAL-003 | Sales activities and task management | Follow-ups must be scheduled, visible and accountable | CORE | P2 | Ph10 | CAP-SAL-001 |
| CAP-SAL-004 | Proposal and quotation preparation | Proposals must price from real availability and rate rules | CORE | P2 | Ph10 | CAP-AVL-001, CAP-RTM-004 |
| CAP-SAL-005 | Sales contract lifecycle | Won business needs contract terms that operations and finance can execute | CORE | P2 | Ph10 | CAP-SAL-004 |
| CAP-SAL-006 | Event calendar and coordination | Events involve multiple departments on one timeline | CORE | P2 | Ph10 | CAP-GRP-011 |
| CAP-SAL-007 | Block creation and conversion to booking | A won event must become protected inventory and reservations without re-entry | CORE | P2 | Ph10 | CAP-GRP-002, CAP-RSV-002 |
| CAP-SAL-008 | Commission tracking and agreement terms | Intermediary terms must be honoured and controlled | CORE | P2 | Ph10 | CAP-CRP-006 |
| CAP-SAL-009 | Pipeline and production reporting | Sales effort and conversion need honest measurement | CORE | P2 | Ph10 | CAP-RPT-003 |
| CAP-SAL-010 | Targets, quotas and performance management | Commercial objectives must be trackable against reality | CORE | P3 | Ph14 | CAP-SAL-009 |

### 5.19 RPT — Reporting

Reporting is how the hotel steers. It must reconcile to the same numbers the accountants see, and every figure must be drillable to its source.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-RPT-001 | Operational reports (arrivals, in-house, departures, cancels, no-shows) | The daily operation runs on lists that must always be current | CORE | P0 | Yes | CAP-FO-001, CAP-FO-007 |
| CAP-RPT-002 | Financial reports (day close, revenue, payments, taxes, trial balance) | Finance must close and explain the day from the system, not from spreadsheets | ACC | P1 | Yes | CAP-NAU-007 |
| CAP-RPT-003 | Revenue and performance metrics (occupancy, ADR, RevPAR, segmentation) | Managers need agreed definitions, not five competing spreadsheets | CORE | P1 | Yes | CAP-PM-002, CAP-ACC-002 |
| CAP-RPT-004 | Forecast and budget comparison | Planning requires actual-versus-plan visibility | CORE | P2 | Ph15 | CAP-RPT-003 |
| CAP-RPT-005 | Pace and pickup reporting | Commercial decisions depend on how demand is building, not only on final numbers | CORE | P2 | Ph15 | CAP-RSV-002 |
| CAP-RPT-006 | Housekeeping and maintenance reports | Room readiness and asset workload need evidence-based management | CORE | P1 | Yes | CAP-HSK-011, CAP-MNT-002 |
| CAP-RPT-007 | Management dashboards | The GM should open one screen, not assemble one | CORE | P1 | Yes | CAP-RPT-002, CAP-RPT-003 |
| CAP-RPT-008 | Audit and exception reports | Controls are only real if exceptions resurface until resolved | CTRL | P1 | Yes | CAP-INA-006, CAP-CSH-009 |
| CAP-RPT-009 | Custom report building | Hotels need their own views without vendor involvement | CORE | P2 | Ph15 | CAP-PLT-009 |
| CAP-RPT-010 | Scheduled report delivery | Daily packs must arrive by themselves, on time, to the right inbox | CORE | P2 | Ph15 | CAP-PLT-008 |
| CAP-RPT-011 | Exports and data extracts | Finance, auditors and owners need controlled raw data | CORE | P1 | Yes | CAP-PLT-002 |
| CAP-RPT-012 | Report reconciliation and drill-down guarantee | Every summary figure must reconcile to and open its underlying transactions | CTRL | P1 | Yes | CAP-PLT-006, CAP-ACC-009 |

### 5.20 BI — Analytics & BI

Beyond daily reporting: understanding trends, comparisons and portfolios. Deferred from pilot, but the data model must not make it impossible.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-BI-001 | Analytical data model and warehouse | Analysis must not run against the operational database | PLAT | P3 | Ph15 | CAP-PLT-007 |
| CAP-BI-002 | Multi-property comparison | Groups need apples-to-apples property comparison | PLAT | P3 | Ph14 | CAP-PM-003, CAP-BI-001 |
| CAP-BI-003 | Chain consolidation reporting | Ownership and management reporting need consolidated views | PLAT | P3 | Ph14 | CAP-BI-002, CAP-ACC-010 |
| CAP-BI-004 | Self-service analytics | Managers must answer their own questions safely | PLAT | P3 | Ph15 | CAP-BI-001 |
| CAP-BI-005 | Historical trend and seasonality analysis | Pricing and planning need history, not recollection | PLAT | P3 | Ph15 | CAP-BI-001 |
| CAP-BI-006 | External BI tool integration | Real portfolios already run BI tools | ADP | P4 | Ph16 | CAP-INT-001 |

### 5.21 CRM & communication

Guest relationships extend beyond the stay. SuiteFlow supports marketing and communication under strict consent discipline.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-CRM-001 | Guest segmentation | Relevant communication requires structured segments, not manual lists | CORE | P3 | Ph12 | CAP-GST-001 |
| CAP-CRM-002 | Campaign management | Marketing activity must be traceable to consent and results | CORE | P3 | Ph12 | CAP-CRM-001, CAP-CRM-003 |
| CAP-CRM-003 | Consent, preference and suppression projection | Marketing systems must respect the guest's actual choices, not stale copies | CTRL | P2 | OQ-014 (closed) | CAP-GST-008 |
| CAP-CRM-004 | Communication history | Staff must see what the guest was told and when | CORE | P2 | OQ-014 (closed) | CAP-GST-001 |
| CAP-CRM-005 | Lifecycle and stay-based triggers | Birthday, post-stay and win-back moments must happen automatically and lawfully | CORE | P3 | Ph12 | CAP-CRM-003 |
| CAP-CRM-006 | Guest messaging channels (email, SMS, WhatsApp) | Guests communicate through channels the hotel must meet them on | ADP | P2 | Yes | CAP-INT-009 |
| CAP-CRM-007 | Marketing performance reporting | Marketing spend must be defensible | CORE | P3 | Ph12 | CAP-CRM-002 |

### 5.22 HRM — Workforce & HR boundary

SuiteFlow is not an HR system. It must, however, link the people who act in the hotel to the identities and permissions they hold.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-HRM-001 | Employee records boundary | Staff identity must come from one authoritative employment source | PLAT | P1 | Yes | CAP-PM-001 |
| CAP-HRM-002 | User identity and role linkage | System access must follow employment state (joiners, movers, leavers) | PLAT | P1 | Yes | CAP-HRM-001, CAP-PLT-001 |
| CAP-HRM-003 | Department and position structure | Approval chains and reporting need organisational context | PLAT | P2 | Ph14 | CAP-HRM-001 |
| CAP-HRM-004 | Staff scheduling | Rota planning affects 24×7 control coverage | CORE | P3 | Ph13 | CAP-HRM-003 |
| CAP-HRM-005 | Commission and payroll boundary | Payroll is out of scope; commission inputs must still reach it | ADP | P3 | Ph13 | CAP-CRP-006 |

### 5.23 INT — Integrations

No hotel is an island. Every external connection is a reliability, security and reconciliation problem before it is a feature.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-INT-001 | Adapter framework with contracts, retries, idempotency and reconciliation | Each integration cannot reinvent reliability; failures must be visible and recoverable | ADP | P1 | Yes | CAP-PLT-007 |
| CAP-INT-002 | Channel manager / OTA distribution | Third-party channels are a primary demand source and a primary oversell risk | ADP | P2 | Ph13 | CAP-INT-001, CAP-AVL-008 |
| CAP-INT-003 | Booking engine / web direct | Direct demand avoids commission and captures guest data | ADP | P3 | Ph13 | CAP-INT-001 |
| CAP-INT-004 | Payment gateway / acquirer integration | Card acceptance must be integrateable without storing card data | ADP | P2 | OQ-005 (closed) | CAP-INT-001 |
| CAP-INT-005 | Banking and statement ingestion | Settlement and reconciliation need bank records in usable form | ADP | P2 | Pend OQ-007 | CAP-INT-001 |
| CAP-INT-006 | POS system integration | Outlets may run external POS platforms | ADP | P2 | Ph11 | CAP-INT-001, CAP-POS-008 |
| CAP-INT-007 | Accounting system integration | Operational-to-accounting exchange must be governed and reconcilable | CTRL | P0 | Yes | CAP-INT-001, CAP-ACC-012 |
| CAP-INT-008 | CRM system integration | Marketing systems need consented, current guest data | ADP | P3 | Ph12 | CAP-INT-001, CAP-CRM-003 |
| CAP-INT-009 | Email, SMS and WhatsApp providers | Guest communication needs reliable providers with delivery evidence | ADP | P2 | Yes | CAP-INT-001 |
| CAP-INT-010 | Door-lock systems | Physical access must follow stay state and checkout | ADP | P3 | OQ-020 (closed) | CAP-INT-001, CAP-FO-011 |
| CAP-INT-011 | ID scanners and document capture devices | Front desk speed and accuracy benefit from device capture | ADP | P3 | OQ-019 (closed) | CAP-INT-001, CAP-FO-003 |
| CAP-INT-012 | Kiosks and self-service | Guest self-service reduces queue pressure where the property wants it | ADP | P4 | Ph16 | CAP-INT-001, CAP-FO-002 |
| CAP-INT-013 | Revenue management systems | Dedicated RMS platforms need rate, restriction and pickup interfaces | ADP | P4 | Ph16 | CAP-INT-001, CAP-RTM-013 |
| CAP-INT-014 | Government and fiscal systems | Statutory reporting or fiscalisation may be mandatory | ADP | P2 | Pend OQ-029 | CAP-INT-001 |

### 5.24 PLT — Platform & administration

The foundation every capability stands on. Platform failures are total failures, so this domain is held to the highest engineering standard.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-PLT-001 | Authentication (staff, API clients, service identities) | Only known, authenticated principals may act | PLAT | P0 | Yes | — |
| CAP-PLT-002 | Authorization, RBAC and authority limits | Permissions must be explicit, least-privilege and enforced server-side | PLAT | P0 | Yes | CAP-PLT-001 |
| CAP-PLT-003 | Property and company scope enforcement | Data must never leak across properties or entities | PLAT | P0 | Yes | CAP-PLT-002 |
| CAP-PLT-004 | Effective-dated configuration management | Hotel behaviour changes over time and must remain explainable | PLAT | P0 | Yes | CAP-PM-013 |
| CAP-PLT-005 | Numbering and document series | Reservations, folios, invoices and vouchers need unbroken, controllable series | PLAT | P1 | Yes | CAP-PM-013 |
| CAP-PLT-006 | Unified audit trail | Accountability requires one complete, immutable record of who did what | PLAT | P0 | Yes | CAP-PLT-001 |
| CAP-PLT-007 | Event bus, transactional outbox and idempotency | Distributed effects must be reliable, ordered and never duplicated | PLAT | P0 | Yes | CAP-PLT-006 |
| CAP-PLT-008 | Notifications, alerts and escalation | Operational and financial alerts must reach the right person in time | PLAT | P1 | Yes | CAP-PLT-007 |
| CAP-PLT-009 | API management, versioning and documentation | Internal and external consumers need stable, documented interfaces | PLAT | P1 | Yes | CAP-PLT-002 |
| CAP-PLT-010 | Webhooks and external callbacks | Providers call in; those calls must be authenticated, deduplicated and validated | PLAT | P1 | Yes | CAP-PLT-007 |
| CAP-PLT-011 | Observability (logs, metrics, traces, health) | Silent failure is the worst failure in a hotel | PLAT | P1 | Yes | CAP-PLT-007 |
| CAP-PLT-012 | Backup, restore and point-in-time recovery | 1-hour RPO/RTO demands continuous, proven recovery | PLAT | P1 | Yes | CAP-PLT-011 |
| CAP-PLT-013 | Upgrade and migration framework | The platform must evolve without forking or data loss | PLAT | P1 | Yes | CAP-PLT-012 |
| CAP-PLT-014 | Localization, tax and calendar configuration | Tax rules, fiscal calendars and formats vary by jurisdiction | PLAT | P1 | OQ-021 (closed) | CAP-PLT-004 |
| CAP-PLT-015 | Break-glass and emergency access controls | Emergencies need access that is possible, visible and reviewed | PLAT | P2 | Ph17 | CAP-PLT-002, CAP-PLT-006 |
| CAP-PLT-016 | Data import and export tooling | Onboarding, migration and exit all need controlled bulk data movement | PLAT | P2 | Ph9 | CAP-PLT-006 |

### 5.25 AI — AI assistance

AI is architected as a governed assistant layer from the start: useful, bounded and auditable. Every AI capability below is advisory unless routed through an authorized domain service.

| ID | Capability | Business problem | Owner | Pri | Pilot | Depends on |
|---|---|---|---|---|---|---|
| CAP-AI-001 | Front desk assistant | Agents ask operational questions in natural language instead of navigating screens | CTRL | P3 | Ph19 | CAP-PLT-002, CAP-PLT-009 |
| CAP-AI-002 | Reservation assistant | Booking enquiry handling needs speed without pricing authority errors | CTRL | P3 | Ph19 | CAP-RSV-001, CAP-RTM-004 |
| CAP-AI-003 | Management reporting assistant | Managers want explanations of numbers they can trust and drill into | CTRL | P3 | Ph19 | CAP-RPT-012, CAP-BI-001 |
| CAP-AI-004 | Housekeeping assistant | Supervisors need workload and priority guidance in the flow of work | CTRL | P4 | Ph19 | CAP-HSK-003 |
| CAP-AI-005 | Maintenance assistant | Fault triage and history lookup speed up repairs | CTRL | P4 | Ph19 | CAP-MNT-003 |
| CAP-AI-006 | Document extraction (IDs, invoices, vouchers) | Manual typing is slow and error-prone at the desk and in finance | CTRL | P3 | Ph19 | CAP-FO-003, CAP-INV-004 |
| CAP-AI-007 | Financial and operational anomaly detection | Fraud and error surface through patterns, not individual reviews | CTRL | P3 | Ph19 | CAP-INA-006, CAP-ACC-009 |
| CAP-AI-008 | Operational summaries and shift handover drafting | Shift change loses context; summaries restore it | CTRL | P3 | Ph19 | CAP-FO-016, CAP-PLT-006 |
| CAP-AI-009 | Guest communication drafting | Consistent, on-brand, policy-aware replies save time | CTRL | P4 | Ph19 | CAP-CRM-006 |
| CAP-AI-010 | AI governance and tool authorization layer | Without governance, AI becomes an unmonitored privileged user | CTRL | P3 | Ph19 | CAP-PLT-002, CAP-PLT-006 |

## 6. Notable additions beyond the mandate's initial list

The mandate's capability universe (its §11) was the starting point. The following were added because real hotel operations and enterprise practice require them, or because the original list split a single operational reality into disconnected pieces:

| Addition | Rationale |
|---|---|
| Service recovery and compensation workflow (CAP-FO-015) | Complaint resolution routinely involves money; without authority limits and accounting it becomes untracked leakage |
| Key/access issuance workflow (CAP-FO-011) | Door-lock integration answers "how does the lock speak", not "who may hold a key and when must it die" |
| Lost and found (CAP-HSK-012) | A universal hotel operation with legal and guest-relations consequences |
| Attrition calculation (CAP-GRP-008) | Group contract enforcement is a calculation, not a negotiation |
| Agent commission lifecycle (CAP-CRP-006) | Agent relationships are financial contracts, not just rate agreements |
| Function-space inventory (CAP-GRP-012) | Conference space is sellable inventory with double-booking risk |
| Day certification (CAP-INA-007) | A day must be independently certifiable, distinct from merely being closed |
| Report reconciliation and drill-down guarantee (CAP-RPT-012) | Enterprise reporting fails when summaries cannot be traced to transactions |
| Break-glass access (CAP-PLT-015) | Emergency access is inevitable; unmanaged emergency access is a breach |
| Data import/export tooling (CAP-PLT-016) | Onboarding, migration and customer exit need first-class tooling |
| Guest 360 workspace (CAP-GST-012) | A named operational surface (per the mandate's UI list) needs a capability behind it |
| AI governance layer (CAP-AI-010) | AI capabilities require an authorization and audit substrate before any assistant is safe |

## 7. Cross-domain integrity chains

Some capabilities only deliver their value as a connected chain. These chains are the acceptance focus of the roadmap:

| Chain | Path | Guarantee |
|---|---|---|
| Financial integrity | CAP-RSV-014 → CAP-FOL-008 → CAP-NAU-003 → CAP-INA-002 → CAP-ACC-002/003/009 | Revenue recognised once on the right business day; every control account reconciles |
| Inventory truth | CAP-PM-006/008/009 → CAP-HSK-009 → CAP-AVL-002 → CAP-FO-006 | A room is never sold as ready when it is not |
| Identity and privacy | CAP-GST-001/005/006/008/009 → CAP-FO-003 → CAP-PLT-002/006 | One guest, lawful data, scoped access, evidenced consent |
| Cash to bank | CAP-CSH-003 → CAP-CSH-005/006 → CAP-CSH-010 → CAP-ACC-008 | Counted cash and matched card batches become bank truth |
| Close integrity | CAP-NAU-002/005/007/008 → CAP-INA-007 → CAP-ACC-010 | The day closes once, provably, recoverably |
| Audit chain | CAP-PLT-006/007 → every CTRL transition → CAP-INA-006 | Every consequential action leaves reconstructable evidence |

## 8. Pilot dependencies in this map

Capabilities marked `OQ-0nn (closed)` had a hotel-reality dependency resolved by the adopted answers (23 Sep 2026); capabilities marked `Pend OQ-0nn` still depend on the three open items below. All are fully designed in the target regardless of the pilot answer:

| Question | Capabilities affected |
|---|---|
| OQ-011 (closed; statutory invoice form) | CAP-FOL-011, CAP-ACC-011, CAP-CRP-010 |
| OQ-012 (closed; deposit/cancellation/no-show policy) | CAP-RSV-004, CAP-RSV-005, CAP-FOL-008 |
| OQ-014 (closed; F&B outlets at pilot) | CAP-POS-001…010, CAP-HSK-008, CAP-CRM-003/004 |
| OQ-015 (closed; store inventory at pilot) | CAP-INV-001…008 |
| OQ-017 (closed; group/event business at pilot) | CAP-GRP-001…012, CAP-AVL-007/010 |
| OQ-019 (closed; ID capture requirements) | CAP-FO-003, CAP-INT-011, CAP-GST-009 |
| OQ-020 (closed; door locks at pilot) | CAP-FO-011, CAP-INT-010 |
| OQ-021 (closed; service charge/tax policy) | CAP-FOL-010, CAP-ACC-005/007, CAP-PLT-014 |
| OQ-022 (closed; maintenance at pilot) | CAP-MNT-001…008, CAP-PM-009/010 |
| OQ-024 (retention periods) | CAP-GST-009 |
| OQ-005 (closed: payment links later) / OQ-007, OQ-029 (open: bank and fiscal interfaces) | CAP-INT-004/005/014 |

## 9. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial capability map issued with WP 0.1 pass 2 | PROPOSED |
| 0.2 | 2026-09-23 | Hotel-reality decisions reclassified after adoption of the industry-standard answers: 48 resolved (`OQ-0nn (closed)`), 3 pending (OQ-007/024/029) | PROPOSED |
