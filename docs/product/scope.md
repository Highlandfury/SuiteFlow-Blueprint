---
doc-id: PROD-SCOPE
title: Product Scope
status: PROPOSED
version: 0.2
date: 2026-09-23
owner: Product Owner (accountable)
applies-to: full enterprise target
depends-on: [PROD-VISION, GOV-INPUTS]
---

# Product Scope

## 1. How to read this document

Three scope tiers are distinguished, and they must never be confused:

| Tier | Meaning |
|---|---|
| **T — Target** | In the enterprise product end-state. The blueprint designs all of it. |
| **P — Pilot release** | Required for the synthetic reference pilot release and the first-property deployment. Subset of Target. |
| **D — Deferred** | In the Target, deliberately not in the Pilot release. Has a phase in the roadmap. |

The accepted Product Owner MVP boundaries (BR-MVP-001…013) are the authority for what may be deferred from the Pilot release. The Product Owner direction of 23 September 2026 (enterprise target, pilot-first delivery) is the authority for designing T fully.

Nothing in tier D may be silently removed from tier T. Removal requires change control (charter §7).

## 2. Domain scope

| Domain | Tier | Pilot-release note |
|---|---|---|
| Property & inventory management (properties, buildings, floors, rooms, room types, features, OOO/OOS, pseudo rooms) | T = P | Required in full for one property |
| Reservations (creation, modification, cancellation, no-show, walk-in, waitlist, sharing, splitting, merging, multi-room, multi-guest, packages, fixed charges, traces, alerts, preferences) | T = P | Pilot executes the property's real reservation practice; group/event depth per OQ-017 closed (pilot-critical) |
| Availability & inventory control (availability calculation, restrictions, stop sell, overbooking policy, allotments) | T = P | Allotment depth may be limited to group blocks at pilot |
| Rates (rate plans, BAR, derived rates, packages, negotiated rates, restrictions, effectivity, rate history) | T; P for manual rate management | Automated revenue management is D (OQ-016 closed) |
| Front office (arrivals, check-in, in-house, moves, extensions, early/late, registration, requests, wake-up/messages, incidents) | T = P | Core operational requirement |
| Guest profiles & privacy (profiles, contacts, preferences, relationships, duplicates, merge, consent, retention) | T = P | Full lifecycle required for data quality and privacy |
| Loyalty | T; D at pilot unless a program exists | Provider-neutral design; no program accepted for pilot (BR-MVP-007 context) |
| Groups & blocks (group booking, block, rooming list, pickup, master account, group billing, deposits) | T; P (OQ-017 closed: groups pilot-critical) |
| Corporate & travel trade (accounts, negotiated rates, contracts, billing, credit limits, AR origination, commissions) | T = P | Corporate direct bill is in accepted MVP scope |
| Sales & events (leads, opportunities, contracts, events, banquets, function space) | T; D at pilot unless conference business is material | OQ-017 (closed); conference centre suggests relevance |
| Housekeeping (room board, assignments, clean/inspect, priority rooms, discrepancies, productivity) | T = P | Required in full |
| Maintenance & engineering (work orders, assignment, priority, preventive maintenance, return to inventory) | T; P (OQ-022 closed: work orders + light PM) |
| Folio & billing (folios, windows, routing, transfers, adjustments, charges, invoices, settlement) | T = P | Required in full |
| Cashiering (till, session, receipts, payouts, drops, variance, closing, approval) | T = P | Required in full, all four accepted payment methods |
| Payments & settlement (cash, POS, transfer, cheque lifecycles; deposits; refunds; gateway adapters) | T = P | Cash/POS/transfer/cheque required; one proven gateway path only if required (BR-MVP-005) |
| Night audit (business date, pre-close validation, posting, reconciliation, close, rollover, recovery, reopen controls) | T = P | Required in full |
| Income audit (independent verification, exception management, audit roles) | T = P | Required in full |
| Accounting integration (revenue posting, tax, AR, GL, reconciliation) | T = P | Required in full; ownership design in WP 0.4 |
| POS / F&B (outlets, menus, tables, orders, room posting, outlet settlement) | T; P only if outlets operate in SuiteFlow | BR-MVP-011 defers *external POS business integration*; OQ-014 closed: restaurant and bar run in SuiteFlow at pilot |
| Purchasing & inventory (items, stores, suppliers, receiving, consumption, costing) | T; D at pilot except outlet/store reconciliation if outlets operate | OQ-015 (closed) |
| Reporting & BI (operational, financial, revenue, audit, management, dashboards, scheduled reports) | T; P for operational/financial reporting | Advanced analytics D (BR-MVP-010) |
| CRM (segmentation, campaigns, consent, communication history) | T; P for minimal consent/withdrawal/suppression projection | BR-MVP-006 |
| HR (employees, roles, departments, schedules, attendance, commissions) | T; P only for user/employee identity boundary | BR-MVP-007; payroll D (BR-MVP-008) |
| Multi-property & chain (central profiles, group reporting, central reservations, per-property accounting) | T; D at pilot | BR-MVP-013 |
| Integrations (OTAs, channel managers, booking engines, payment providers, POS, accounting, CRM, messaging, locks, ID scanners, kiosks, BI, revenue management, government systems) | T; P for the accepted payment/banking scope and any proven provider path | Channel manager and lock integrations deferred (OQ-018, OQ-020 closed); fiscalisation depends on OQ-029 |
| Platform (authn/authz, RBAC, tenancy, configuration, audit, events, notifications, monitoring, backup/recovery) | T = P | Required in full |
| AI assistance | T; D at pilot unless separately approved | OQ-031 (closed) |

## 3. Explicit non-goals (permanent)

- Consumer-facing OTA marketplace operation.
- General-purpose ERP functionality beyond hospitality-operations needs.
- Storage of card PAN/CVV.
- Autonomous AI authority over price, availability, tax, balances, payments, inventory or permissions.
- Cryptographic or fiscal certification claims without an accredited provider where local law requires one (to be verified in Phase 1).

## 4. Confirmed constraints

| Constraint | Value | Source |
|---|---|---|
| Reference property (modelled) | "Golfview profile": 200-room NGN/WAT conference property, GRA Ikeja, Lagos — synthetic reference pilot, no contracted site | BR-PILOT-001 (amended 23 Sep 2026) |
| Pilot scope | One property, ~200 rooms | BR-PILOT-002 |
| Timezone | Africa/Lagos (WAT, UTC+1); business date always hotel-local | BR-PILOT-003 |
| Currency | NGN only at pilot; foreign currency deferred | BR-PILOT-004, BR-MVP-012 |
| Payment methods | Cash, POS terminal, bank transfer, cheque | BR-PAY-001…004 |
| Delivery horizon | 24 weeks to the pilot-ready reference release (synthetic), acceptance-gated; first-property deployment follows the First-Property Deployment Gate | BR-PILOT-005 (amended 23 Sep 2026) |
| Support | 24×7 | BR-REL-001 |
| RPO / RTO | 1 hour / 1 hour | BR-REL-002/003 |
| Service objective | 99.5% pilot; 99.9% enterprise target (OQ-009 closed) | BR-REL-004 |

## 5. Regulatory and statutory context (to verify in Phase 1)

The following are **UNVERIFIED** planning assumptions that Phase 1 research and tax counsel must confirm before financial configuration is finalised:

- VAT treatment of accommodation, F&B and service charge at applicable rates, and the tax point for each.
- Lagos State consumption/levy obligations on hotel accommodation and restaurant services, if applicable to this property.
- Withholding tax obligations on corporate settlements and commissions.
- Fiscalisation / e-invoicing expectations, if any, and whether an accredited provider is required (OQ-029).
- Data protection obligations for guest personal data and identity documents (Nigeria Data Protection Act 2023 and related regulation) — retention, lawful basis, breach handling (OQ-024).
- Statutory guest-registration requirements for identity capture (OQ-019 closed).
- Employment-related requirements if any AI or scheduling features touch staff evaluation (not planned at pilot).

No financial or tax rule enters the blueprint as VERIFIED without a cited primary source.

## 6. Assumptions (ASSUMED)

**Pilot model (PO decision, 23 Sep 2026):** the pilot is a **synthetic reference pilot**. The operating assumptions below describe the modelled reference property and its scripted operation; they are validated through role-played scenarios, and real-hotel validation is part of the First-Property Deployment Gate. The Product Owner acts as operations authority and acceptance approver until a real property is secured (OQ-003 closed).

1. The reference property operates a 24-hour front desk with a night audit function; these are modelled in the scripted golden day.
2. Internet connectivity may be interrupted; the system must degrade gracefully without corrupting financial state.
3. Finance staff are on-site and can perform daily cashier and close duties; income audit independence can be staffed.
4. The reference pilot accepts that financial configuration (taxes, service charge, routing) is subject to Finance sign-off rather than ad-hoc change; the real hotel adopts the signed configuration at deployment.
5. Existing operational data (if any) will be made available for migration assessment (OQ-025).
6. Hotel outlets (restaurant/bar/conference) exist; they run inside SuiteFlow at pilot (OQ-014 closed).
7. No guest-facing self-service is required at pilot.
8. Staff read English; local-language support is not required at pilot (OQ-032 closed: English-only adopted; localisation later).

## 7. Dependencies

- **Hotel participation**: acting operations authority (Product Owner; OQ-003 closed), Finance representative (OQ-002), business-day cutoff (OQ-008 closed); real hotel staff at the First-Property Deployment Gate.
- **Banking/payment context**: roles of UBA/Wema (OQ-004), POS/acquirer settlement reports (OQ-006), statement formats (OQ-007).
- **Regulatory clarity**: tax and data-protection positions (section 5).
- **Implementation ecosystem**: the existing SuiteFlow implementation and its external dependencies are the subject of Phase 2/3; their current capabilities do not constrain this scope.
- **Infrastructure**: hosting capable of the 1-hour RPO/RTO targets and 24×7 support model.

## 8. Ownership questions deliberately left open

This document defines scope, not ownership. The question "which system owns which domain in the target architecture" — including whether existing products (hotel operations application, accounting application, control plane) satisfy their candidate roles — is answered in WP 0.2 (domain ownership) and WP 0.4 (financial ownership) with explicit ADR outcomes. The accepted MVP boundary naming existing products is treated as intent, not architecture.

## 9. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial scope issued with WP 0.1 pass 1 | PROPOSED |
| 0.2 | 2026-09-23 | Synthetic reference pilot recorded (PO decision): pilot/reference scope tiers, property and horizon amended; operational assumptions marked as modelled | PROPOSED |
