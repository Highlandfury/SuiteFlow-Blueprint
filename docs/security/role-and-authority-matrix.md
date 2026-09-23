---
doc-id: SEC-ROLES
title: Role and Authority Matrix
status: PROPOSED
version: 0.2
date: 2026-09-23
owner: Security Engineer (drafted); Product Owner + Finance Controller (approval; OQ-002 open; OQ-003 closed — acting operations authority)
applies-to: full enterprise target; pilot roles
depends-on: [SEC-MODEL, ADR-009, PROD-PERSONAS]
---

# Role and Authority Matrix

## 1. Purpose

This document turns the personas (`PROD-PERSONAS`) into a **system role catalogue**, per-role permission summaries, **authority-limit defaults** and **separation-of-duties rules**. It is the implementation source for ADR-009 and the security model.

All numeric limits are **PROPOSED defaults pending sign-off** (OQ-002 Finance Controller appointment; OQ-012 and OQ-013 policies closed by adoption 23 Sep 2026); they are expressed in NGN and are property-configurable, effective-dated configuration (BR-PLT-001). Nothing here is hard-coded.

## 2. Role catalogue

| Role | Scope | Purpose in one line |
|---|---|---|
| General Manager | Property | Approves within top bands; owns property performance; no routine operational entry |
| Duty Manager / Front Office Supervisor | Property | Runs the shift; approves overrides, adjustments and refunds within bands |
| Front Desk Agent | Property | Arrivals, in-house service, departures, folios, payments within limits |
| Reservations Agent | Property | Creates and amends bookings; no financial approvals |
| Revenue Manager | Property (rates) | Owns rate plans, restrictions and inventory policy; no financial approvals |
| Housekeeping Attendant | Assigned tasks | Executes cleaning tasks and reports room truth |
| Housekeeping Supervisor | Property | Assigns and inspects; approves readiness and discrepancies resolution |
| Maintenance Technician | Assigned work | Executes work orders; no saleability authority |
| Chief Engineer | Property | Triages and verifies maintenance; requests OOO with supervisor oversight |
| Night Auditor | Property | Executes close; resolves within limits; never self-approves exceptions |
| Income Auditor | Property (audit) | Independently verifies and certifies the day; no operational entry |
| Cashier (Front Office Cashier) | Own sessions | Handles money within sessions; owns variance explanation |
| Finance Controller / Accountant | Property / company | Owns financial configuration, AR, deposits governance, remediation, reporting |
| Sales & Events Coordinator | Property (sales) | Manages accounts, groups and events within commercial limits |
| F&B Outlet Manager | Outlet(s) | Runs outlet operations; posts to rooms; stock control within limits |
| System Administrator | Platform (technical) | User provisioning, configuration, integrations; **no business authority** |
| External Auditor | Contract scope, read-only | Reads complete evidence; time-boxed; no writes |
| Group/Chain Executive *(future)* | Group scope, read-only + policy | Portfolio reporting; no property financial entry |
| Service identities *(machine)* | Interface scope | Batch, integration and worker jobs; explicit per-job scopes |
| AI assistant *(machine)* | Invoker scope + tool allow-list | Read tools and proposal flows only |

## 3. Permission summaries

Legend: **R** read, **C** create, **U** update within state machine, **A** approve (within band), **—** none. "Own" = own records/sessions only.

| Permission area | GM | DM/FO Sup | FD Agent | Res Agent | Revenue | HSK Att | HSK Sup | Maint | Chief Eng | Night Aud | Income Aud | Cashier | Finance Ctrl | Sales | Outlet Mgr | Sys Admin | Ext Auditor |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Reservations | R | R/U | R/C/U | R/C/U | R | — | — | — | — | R | R | — | R | R/C/U | — | — | R |
| Availability & restrictions | R/A* | R | R | R | R/C/U/A | — | — | — | — | R | R | — | R | R | — | — | R |
| Rates | R | R | R | R | R/C/U/A | — | — | — | — | R | R | — | R/A | R | — | — | R |
| Check-in / stay / checkout | R | R/A | R/C/U | R | — | — | — | — | — | R | R | R | R | — | — | — | R |
| Room assignment & moves | R | R/A | R/C/U | R/C | — | R | R/C/U | R | R | R | R | — | — | — | — | — | R |
| Guest profiles | R | R | R/C/U | R/C/U | R | — | — | — | — | R | R | R | R | R/C/U | R | — | R |
| Class-A guest data | R | R* | R* | R* | — | — | — | — | — | R* | R* | R* | R* | R* | — | — | — |
| Traces, requests, incidents | R | R/A | R/C/U | R/C | — | R/U | R/C/U | R/U | R/U | R | R | — | — | R | R | — | R |
| Housekeeping tasks | R | R | R | — | — | R/U own | R/C/U/A | R | R | R | R | — | — | — | — | — | R |
| Maintenance work orders | R | R | R/C | — | — | R/C | R/C/U | R/U own | R/C/U/A | R | R | — | R | — | R/C | — | R |
| Folios & charges | R | R/A | R/C/U | R | — | — | — | — | — | R/U | R | R/C/U | R/A | R | R/C | — | R |
| Adjustments / comps | R | A (band) | R | — | — | — | — | — | — | R | R | R | R/A | R | R (band) | — | R |
| Payments | R | R | R/C/U | — | — | — | — | — | — | R | R | R/C/U | R | — | R/C/U | — | R |
| Refunds | R/A* | A (band) | R | — | — | — | — | — | — | R | R | R (request) | R/A | — | — | — | R |
| Deposits | R | R/A | R/C/U | R/C | — | — | — | — | — | R | R | R/C/U | R/A | — | — | — | R |
| Cashier sessions | R | R/A | R (own) | — | — | — | — | — | — | R | R | C/U own | R/A | — | C/U own | — | R |
| Night audit / close | R | R | — | — | — | — | — | — | — | R/C/U | R | — | R/A* | — | — | — | R |
| Income audit & certification | R | R | — | — | — | — | — | — | — | R | R/C/U/A | — | R | — | — | — | R |
| Corporate credit & AR | R | R | R | R | — | — | — | — | — | R | R | — | R/C/U/A | R/C | R | — | R |
| Groups & events | R | R | R | R | R | — | R | — | — | R | R | — | R | R/C/U/A | R | — | R |
| Outlets | R | R | R | — | — | — | — | — | — | R | R | R | R | — | R/C/U/A | — | R |
| Reports | R | R | R | R | R | — | R | — | R | R | R | R | R | R | R | R (config) | R |
| Exports (class A/B) | A* | — | — | — | — | — | — | — | — | — | — | — | A | — | — | — | — |
| User provisioning | — | — | — | — | — | — | — | — | — | — | — | — | R | — | — | R/C/U/A | — |
| Configuration (non-financial) | R/A | R | — | — | R (rates) | — | — | — | R (asset) | — | — | — | R (financial) | — | — | R/C/U/A | R |
| Configuration (financial) | R/A* | — | — | — | — | — | — | — | — | — | R | — | R/C/U/A | — | — | R (execution) | R |

\* Requires second factor and is flagged in audit. Sys Admin executes configuration changes but never approves them (ADR-009 §8; personas §2.15).

**Privileged grants (SEC-04 resolution).** User provisioning and role assignment run on the maker–checker mechanism: requester, approver and beneficiary are distinct identities. Finance roles additionally require Finance Controller approval; security/administrator roles and break-glass require the Security/Privacy Adviser (interim: Product Owner while OQ-033 is open). The quarterly privileged-access review is performed by a party independent of the administrator who executed the grants.

## 4. Authority-limit defaults (PROPOSED — NGN)

| Action | Front Desk Agent | Supervisor / DM | GM | Finance Controller | Dual control required above |
|---|---|---|---|---|---|
| Adjustment / allowance per item | — | ≤ 20,000 | ≤ 100,000 | Unlimited with reason + report | 100,000 |
| Comp room per instance | — | ≤ 1 room-night, 20,000 value | Unlimited, monthly report | — | Unlimited GM items quarterly review |
| Rate discount (off BAR) | — | ≤ 10% | ≤ 25% | — | > 25% (GM + Revenue) |
| Refund | — | ≤ 20,000 | ≤ 100,000 | ≤ 500,000 | 500,000 (FC + GM) |
| Deposit forfeiture | — | Policy formula only | Policy formula + deviations | Unlimited with evidence | Deviation from formula (FC) |
| Cashier variance approval | — | ≤ 5,000 | ≤ 20,000 | Unlimited with reason | 20,000 (FC) |
| Credit limit override / direct-bill exception | — | — | ≤ 100,000 exposure | Unlimited per policy | 100,000 (FC + GM) |
| AR write-off | — | — | ≤ 50,000 | ≤ 250,000 | 250,000 (FC + GM) |
| Day reopen | — | — | — | Required authority (reason + impact) | — |
| Period reopen | — | — | Co-approval | Co-approval | FC + GM |
| Break-glass grant | — | — | Request only | Request only | Two-person grant: requester ≠ grantor ≠ beneficiary; no self-grant; independent 24h review by the alert recipient (Technical Lead; interim Product Owner while OQ-033 open) |

These bands align with the pilot's ~200-room operation and are expected to be re-scoped with the Finance Controller (OQ-002). They are configuration, not code.

## 5. Separation of duties

| # | Separation | Rule | Exception path |
|---|---|---|---|
| 1 | Cash receiver ≠ variance approver | A cashier cannot approve their own session variance | Supervisor/FC approval; repeated use escalated |
| 2 | Requester ≠ approver | Refund, adjustment, comp and write-off requests require a different approver | FC may approve with GM notification where no second approver is on duty; flagged |
| 3 | Income auditor ≠ operator of the day | The income auditor must not have performed operational or cashier duties for the day being certified | None at pilot scale except recorded FC approval |
| 4 | Night auditor ≠ self-approved exceptions | Night auditor executes close but cannot approve their own exceptions | DM/FC wake-up path per BR-CSH/variance policy |
| 5 | Technical admin ≠ business authority | System administrators have no financial/operational approval rights | None |
| 6 | Rate changer ≠ over-band approver | Discounts/rate overrides above bands require Revenue/GM | Recorded approval |
| 7 | Booking agent ≠ refund approver | Agents cannot approve refunds on their own bookings | Supervisor/FC |
| 8 | Role grantor ≠ role beneficiary | No user may grant themselves a role or limit change; privileged grants require maker–checker with requester ≠ approver ≠ beneficiary | Finance roles: FC approval; security/admin and break-glass: Security/Privacy Adviser (interim: Product Owner); independent review |

Exception principle: where staffing makes strict separation impossible (small night team), the exception must be **explicitly configured, recorded, flagged in audit and reviewed monthly** — never silent (BR-PLT-004).

## 6. Provisioning, review and deprovisioning

- Joiner: access granted only on HR/manager authorisation, with role assignment recorded; MFA enrolment for privileged roles before first use.
- Mover: role changes audited; old permissions expire immediately on reassignment.
- Leaver: access disabled same day (CAP-HRM-002); sessions invalidated; credentials revoked/rotated.
- Review: quarterly access review by FC (finance roles) and Security/Admin (all privileged roles); dormant accounts disabled.
- Emergency access: break-glass per SEC-MODEL §9; post-review mandatory.

## 7. Open items

| Item | Owner |
|---|---|
| OQ-002 Finance Controller named (approves bands) | Product Owner |
| OQ-003 closed — acting operations authority validates operational roles | Product Owner |
| OQ-012 (closed) deposit/refund/cancellation policy (defines forfeiture formula) | Product Owner / Finance |
| OQ-013 (closed) corporate credit policy (limits and suspension) | Finance |
| OQ-033 Security adviser named | Product Owner |

## 8. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial role and authority matrix issued with WP 0.5; all limits PROPOSED defaults | PROPOSED |
| 0.2 | 2026-09-23 | P0 resolutions: privileged grants on maker–checker with independent review (SEC-04, §3/§5); break-glass two-person grant and named reviewer (SEC-02, §4) | PROPOSED |