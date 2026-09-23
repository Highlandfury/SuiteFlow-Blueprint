---
doc-id: PROD-PERSONAS
title: Personas and Role Model
status: PROPOSED
version: 0.4
date: 2026-09-23
owner: Product Owner (accountable); operational validation with the acting operations authority (OQ-003 closed)
applies-to: full enterprise target; pilot detail
depends-on: [PROD-SCOPE]
---

# Personas and Role Model

## 1. How to read this document

Three different things are often called "roles" and must stay separate:

| Concept | Definition | Lives in |
|---|---|---|
| **Persona** | A human job function with goals, pressures and a working context | This document |
| **System role** | A named permission bundle assigned to a user (e.g. Front Desk Agent, Night Auditor) | Security model (WP 0.5) |
| **Authority** | The right to take a specific consequential action (approve a refund, release a room, close the day) under a configured limit | Security model + business rules |

Personas inform workflow design. System roles implement least privilege. Authority is explicit, configurable and auditable — never implied by seniority in conversation, and never granted merely because someone is a system administrator.

All personas below are **ASSUMED** from hospitality practice and the accepted pilot profile. They are validated by the acting operations authority (Product Owner; OQ-003 closed) through role-played workflow scenarios before the reference release; real-hotel validation and on-site walkthroughs are First-Property Deployment Gate items.

## 2. Persona catalogue

Catalogue scope: hotel operating roles (2.1–2.17) plus platform governance roles (2.18 Technical Lead, 2.19 Security/Privacy Adviser). Governance personas are programme/platform roles, not property operations; their role-based reference-pilot accounts follow §4.8.

### 2.1 Front Desk Agent

| Aspect | Detail |
|---|---|
| Goal | Move guests through arrival, stay and departure quickly and correctly |
| Key activities | Arrivals, check-in, room assignment, guest requests, folio enquiries, payments on account, messages, checkout, extensions, walk-ins |
| Decisions | Room choice within assignment rules; payment method handling; escalating credit or identity concerns |
| Needs | Fast search, clear room availability, guest context (VIP, preferences, alerts), unambiguous totals, payment status, error messages that state the fix |
| Pressures | Queues at check-in/checkout peaks; interruptions; language variety; shift handover |
| Must never be able to | Change rates beyond granted limits, post unmotivated adjustments, waive charges, reopen closed business dates, approve their own cashier variance |
| Surfaces | Front desk console, room rack, reservation view, folio, cashier session |

### 2.2 Front Office Supervisor / Duty Manager

| Aspect | Detail |
|---|---|
| Goal | Keep the shift operating within policy; resolve exceptions with recorded authority |
| Key activities | Overrides and approvals (rate, comps within limits, early check-in, late checkout), room moves, guest complaint resolution, credit holds, escalation |
| Decisions | Overrides within delegated limits; escalation beyond them |
| Needs | Exception queues, override history, in-house overview, staff activity visibility |
| Must never be able to | Approve their own requests where maker–checker applies; approve above configured limits; alter closed financial records |
| Surfaces | Supervisor dashboard, override approval screens, occupancy and house status views |

### 2.3 Reservations Agent

| Aspect | Detail |
|---|---|
| Goal | Convert enquiries into accurate, profitable, correctly-rated reservations |
| Key activities | Availability and rate enquiry, reservation creation/modification/cancellation, deposits, confirmations, waitlist, group and corporate booking intake |
| Decisions | Rate selection within policy; deposit requirements; waitlist management |
| Needs | Clear availability and restrictions, rate explanations, guest history, accurate cancellation policies, duplicate guest detection |
| Must never be able to | Override restrictions without authority; remove restrictions silently; create reservations outside property scope |
| Surfaces | Reservation console, tape chart, guest profile, rate calendar |

### 2.4 Revenue Manager

| Aspect | Detail |
|---|---|
| Goal | Sell the right room at the right price; protect availability for higher-value demand |
| Key activities | Rate plan and restriction management, overbooking policy, block and allotment decisions, pace/pickup analysis, forecast review |
| Decisions | Rates, restrictions, stop-sell, overbooking levels within policy |
| Needs | Availability and pace at a glance, restriction calendar, forecast and pickup reporting, rate history |
| Must never be able to | Change historical rates or past stay revenue; bypass the change trail on rate and restriction changes |
| Surfaces | Rate management, restriction calendar, pick-up and pace reports |

At the pilot, this function may be performed by the GM or a remote group resource; the system roles and authority limits still apply.

### 2.5 Housekeeping Attendant

| Aspect | Detail |
|---|---|
| Goal | Clean assigned rooms to standard and report reality accurately |
| Key activities | View assignments, start/finish cleans, report room status and condition, report maintenance issues, mark rooms ready, record minibar consumption if applicable |
| Decisions | Sequence within assignment; flagging discrepancies |
| Needs | Simple, fast, mobile-friendly task list; minimal taps; offline tolerance; clear priority (rush, VIP); unambiguous status verbs |
| Must never be able to | Mark a room clean-inspected unless authorized; alter guest folios; change room assignment |
| Surfaces | Housekeeping tablet/phone view, room board, maintenance request capture |

### 2.6 Housekeeping Supervisor

| Aspect | Detail |
|---|---|
| Goal | Get rooms ready on time with verified quality; balance workload |
| Key activities | Assign and rebalance tasks, inspect rooms, approve readiness, manage priority and rush rooms, handle discrepancies, track productivity |
| Decisions | Inspect/pass/fail, reassign work, escalate maintenance, priority ordering |
| Needs | Board with real-time status, discrepancy queue, inspection workflow, workload view, exception history |
| Must never be able to | Pass an inspection for a room they cleaned themselves where policy forbids it; hide discrepancies without resolution |
| Surfaces | Supervisor board, inspection forms, discrepancy resolution |

### 2.7 Maintenance Technician / Chief Engineer

| Aspect | Detail |
|---|---|
| Goal | Fix issues quickly; protect the asset; know what is degrading |
| Key activities | Receive and accept work orders, record work, request parts, close with evidence, perform preventive maintenance |
| Decisions | Work priority sequence; escalating out-of-scope damage |
| Needs | Mobile work orders with location/room context, photos, parts and cost references, history of repeat faults |
| Must never be able to | Return a room to sale directly (that is a governed housekeeping/front-office decision); close preventive work without evidence where policy requires it |
| Surfaces | Maintenance board, mobile work order view, asset history |

### 2.8 Night Auditor

| Aspect | Detail |
|---|---|
| Goal | Close the operating day accurately, completely and promptly |
| Key activities | Pre-close validation, posting nightly charges and taxes, balance checks, exception resolution within authority, executing close, producing day reports, safe handover of unresolved exceptions |
| Decisions | Proceed/hold within policy; escalate defects rather than override them |
| Needs | A clear exception list with ownership, control totals that must balance, safeguard against partial close, recoverable close on failure |
| Must never be able to | Alter or delete postings, approve their own exceptions where maker–checker applies, close with unresolved failed controls, close the same business date twice |
| Surfaces | Night audit console, exception queue, control-total reconciliation, day reports |

### 2.9 Income Auditor

| Aspect | Detail |
|---|---|
| Goal | Independently verify that the day's revenue, taxes, settlements and adjustments are complete and correct |
| Key activities | Post-close verification, audit of adjustments/voids/comps, cash and settlement reconciliation review, exception follow-up, certification of the day, reporting to Finance |
| Decisions | Pass/hold a day's audit; raise findings; require remediation |
| Needs | Immutable source data, complete drill-down from summary to transaction, variance reporting, adjustment and void reporting by user, historical comparison |
| Must never be able to | Modify operational or financial source records; audit their own operational actions |
| Surfaces | Income audit workspace, audit reports, exception register |

### 2.10 Cashier

| Aspect | Detail |
|---|---|
| Goal | Receive and account for money exactly |
| Key activities | Open session with assigned float, receive payments, payouts within limits, drops, close session with counted cash, explain variances |
| Decisions | Accept/verify payment evidence; escalate discrepancies |
| Needs | Fast payment entry, clear outstanding-balance context, receipt evidence, session totals live, variance explanation workflow |
| Must never be able to | Close their own session without the required review where policy requires two-person control; delete transactions; alter posted payments |
| Surfaces | Cashier session, payment entry, close/reconciliation |

At the pilot, front-desk agents rotate through the cashier role; the control model must still prevent self-approval of a variance.

### 2.11 Finance Controller / Accountant

| Aspect | Detail |
|---|---|
| Goal | Own the accuracy of the books, receivables, taxes and banking relationships |
| Key activities | Review daily revenue and tax postings, AR and credit control, bank and acquirer reconciliation, deposit/refund/forfeiture governance, month close, statutory reporting |
| Decisions | Credit limits and holds, write-offs within policy, banking and settlement treatment, tax configuration changes (with tax adviser where applicable) |
| Needs | Reconciled ledgers with drill-down, AR ageing, exception queues with ownership, audit trails, reports that reconcile to operational totals |
| Must never be able to | Alter immutable posted records; approve their own reconciliations where SoD requires another party; change tax rules without effective-dated configuration and audit |
| Surfaces | Finance workspace, AR, reconciliation queues, financial reports |

**Blueprint-governance duties (charter §12):** approves the financial architecture and authority limits; sets the credit policy and suspension/reinstatement rules; certifies the close; owns reopen authority; signs tax configuration with the adviser; validates finance personas and separation of duties.

**Reference-pilot account:** provisioned as a role-based account with the `Financial Controller` role plus base desk access (convention and controls: §4.8). Replaced by a named-person account with MFA at the First-Property Deployment Gate.

### 2.12 General Manager / Property Manager

| Aspect | Detail |
|---|---|
| Goal | Run a profitable, compliant, well-rated property |
| Key activities | Review performance, approve exceptions within authority, manage staff and standards, handle escalations |
| Decisions | Approvals within delegated limits; staffing and policy decisions |
| Needs | Trustworthy daily numbers without preparation work, occupancy/rate/revenue trends, unresolved-exception visibility, staff activity transparency |
| Must never be able to | Silently change financial history; approve beyond configured limits; bypass audit |
| Surfaces | Management dashboard, approvals, reports |

### 2.13 Sales & Events Coordinator

| Aspect | Detail |
|---|---|
| Goal | Win and deliver group, corporate and event business accurately |
| Key activities | Manage leads and opportunities, prepare proposals, negotiate within limits, hold space and rooms, rooming lists, event billing instructions, coordinate with operations |
| Decisions | Proposal pricing within limits; block and space holds within policy |
| Needs | Availability for rooms and space, contract and rate history, account production, clear conversion path to an operational booking |
| Must never be able to | Convert a lead into a reservation or event without the operational validation path; hold inventory beyond policy limits |
| Surfaces | Sales pipeline, event calendar, group booking, function space |

### 2.14 F&B / Outlet Manager

| Aspect | Detail |
|---|---|
| Goal | Run profitable outlets with correct charging and stock control |
| Key activities | Menu and price control, outlet operations, room posting, discounts within limits, stock counts and consumption, outlet settlement |
| Decisions | Comp and discount within limits; stock variance escalation |
| Needs | Fast outlet operation, clear room-posting confirmation, stock and consumption visibility, settlement reconciliation |
| Must never be able to | Post to arbitrary rooms/folios without validation; alter posted outlet revenue; approve stock variances they caused |
| Surfaces | Outlet console, room posting, stock counts |

Existence and pilot inclusion adopted (OQ-014, OQ-015 closed).

### 2.15 System Administrator / IT

| Aspect | Detail |
|---|---|
| Goal | Keep the platform secure, current and available |
| Key activities | User provisioning, role assignment, configuration, integrations, monitoring, backup/restore, upgrade execution |
| Decisions | Technical operations within change policy |
| Needs | Safe configuration tooling, environment visibility, audit of administrative actions, recovery runbooks |
| Must never be able to | Grant themselves business authority (approvals, financial actions) via technical roles; read sensitive guest data without business need and authorization; change financial configuration without business approval |
| Surfaces | Administration, user management, integration monitoring, operations dashboards |

### 2.16 Group / Chain Executive (future)

| Aspect | Detail |
|---|---|
| Goal | Compare and improve property performance across a portfolio |
| Key activities | Portfolio KPIs, comparative analysis, standard configuration, capital and commercial decisions |
| Decisions | Group policy and standards (not operational transactions) |
| Needs | Consolidated reporting that reconciles to property books, drill-down with respect for property scope |
| Must never be able to | See property data outside their scope; alter property books |
| Surfaces | Group dashboards, comparison reports |

Tier: Target; deferred from pilot (BR-MVP-013).

### 2.17 External Auditor (statutory/independent)

| Aspect | Detail |
|---|---|
| Goal | Obtain evidence for an opinion |
| Key activities | Sample transactions, trace postings, verify controls, inspect audit trails |
| Needs | Read-only, time-boxed access to complete and immutable records; audit trail integrity; exportable evidence |
| Must never be able to | Modify anything; see data beyond the audit mandate |
| Surfaces | Reporting/read-only access under contract |

### 2.18 Technical Lead (platform governance)

| Aspect | Detail |
|---|---|
| Goal | Own the technical integrity of the platform and the technical approvals of the blueprint |
| Key activities | Technical design authority; architecture decisions and ADR maintenance; environment, release and migration gates; integration contracts; performance and reliability engineering; incident command for technical escalations |
| Decisions | Technical approach within the blueprint; technical release readiness; remediation and escalation plans |
| Needs | Architecture and ADR registers, release state, test and incident evidence, environment and recovery visibility |
| Must never be able to | Change approved business requirements, financial policy or prices; grant themselves business authority through technical roles (separation of duties); bypass change control |
| Surfaces | Architecture and ADR registers, release gates, monitoring, runbooks |
| Reference-pilot account | Role-based account with technical administration access; no business authority. Named person + MFA at the First-Property Deployment Gate (convention and controls: §4.8) |

### 2.19 Security / Privacy Adviser (governance)

| Aspect | Detail |
|---|---|
| Goal | Keep guest and financial data lawful, minimised and protected; provide security and privacy sign-off |
| Key activities | Security model and role-matrix review; privacy impact assessments and lawful-basis review; retention schedule ownership (with counsel); access reviews; incident and breach procedures; security test oversight |
| Decisions | Security/privacy approval of designs and releases; retention and handling exceptions; breach-response recommendations |
| Needs | Security model, data inventory, access and audit evidence, incident records, privacy assessments |
| Must never be able to | Operate the business or perform transactions; grant themselves access; waive an obligation without recorded authority |
| Surfaces | Security model, audit logs, read-only evidence views |
| Reference-pilot account | Role-based account with read-only audit access and logged reads; named person + MFA at the First-Property Deployment Gate (convention and controls: §4.8) |

## 3. System actors (non-human principals)

| Actor | Nature | Constraint |
|---|---|---|
| Integration client (bank, acquirer, channel, messaging) | Authenticated machine identity with scoped permissions | Rotatable credentials, allow-listed operations, idempotency, full audit, no interactive authority |
| Scheduled job / batch worker | Platform identity executing defined processes | Cannot invent authority; runs under a named service identity configured with explicit permissions |
| AI assistant | Identity scoped to specific read tools and proposal services | Cannot execute state changes directly; proposals pass through governed services; all tool use authorized and audited |
| Reporting/exports consumer | Read identity for defined report surfaces | Property/company scope enforced server-side; exports carry provenance and are logged |

## 4. Role-model design requirements (for WP 0.5)

1. **Least privilege by default.** A new user has no operational authority until assigned.
2. **Explicit authority objects.** Consequential actions (refund above X, discount above Y, comp room, credit override, reopen day, rate override) are separate permissions with optional numeric limits, not bundled in "can edit".
3. **Separation of duties where money or control integrity is at stake.** Minimum separations to design, at least:
   - cash receiver vs. approver of their own variance;
   - requisitioner vs. approver of refunds/forfeitures above a threshold;
   - reservation author vs. approver of large discounts/compensations;
   - operational actor vs. approver of their own overrides;
   - rate changer vs. approver where limits are exceeded;
   - technical administrator vs. business authority (never implied);
   - income auditor vs. the operations being audited (for their own actions).
   Where the property's staffing makes strict separation impractical, the model must require a *recorded, authorized exception path* (e.g. approval deferred to the Finance Controller with evidence), not silent self-approval.
4. **Scope enforcement.** Every permission is qualified by property scope (and company scope for financial visibility) and enforced server-side on every read and write, including reports, exports, APIs and AI tools.
5. **Authority limits are configuration.** Numeric limits, thresholds and approval chains are property-configurable with effective dates and audit, not hard-coded assumptions.
6. **Override discipline.** Overrides are first-class actions: reason required, authority checked, before/after recorded, reportable.
7. **Emergency access.** Any break-glass access is time-boxed, extremely visible, and reviewed afterwards; it cannot be a standing role.
8. **Role-based accounts (reference pilot only — SEC-05 hardened).** Role-played UAT uses one role-based functional account per role that needs a scripted login, named by role and carrying only that role's permissions. Rules:
   - **MFA** is required where the platform supports it; where it does not, a **recorded time-boxed exception** applies with compensating controls: network-restricted access (LAN/loopback only), session logging, and a named operator register.
   - **Rotation** at least every 30 days and before any demonstration outside the core team; passwords never committed to any repository.
   - **Hard expiry at the reference release** (not the First-Property Deployment Gate) and immediate disablement on expiry or operator request, whichever is first.
   - **No real guest, financial or personal data** in the reference environment.
   - Every session and action is audited to the account; role accounts are used one operator at a time and never shared for day-to-day work.
   Production access remains personal — one named account per person (SEC-MODEL §9) — with MFA for privileged roles and immediate leaver deactivation.

## 5. Validation and next steps

- Validate personas and separation-of-duties expectations with the acting operations authority (OQ-003 closed) before the reference release; real-hotel validation at the First-Property Deployment Gate.
- Appoint or confirm the Technical Lead and Security/Privacy Adviser (OQ-033); their personas gate blueprint acceptance (charter §12).
- Produce the full role × permission × limit matrix in WP 0.5 (security model), traced to capability IDs and test obligations.
- Confirm staffing reality (who can perform which duty at 03:00, Sundays, and public holidays) — staffing assumptions that fail in practice become control failures (RSK-OPS-001).

## 6. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial personas and role-model requirements issued with WP 0.1 pass 1 | PROPOSED |
| 0.2 | 2026-09-23 | Synthetic reference pilot: validation authority updated (acting operations authority; real-hotel validation at the First-Property Deployment Gate) | PROPOSED |
| 0.3 | 2026-09-23 | Governance personas added (2.18 Technical Lead, 2.19 Security/Privacy Adviser); Finance Controller governance duties and reference-pilot account noted; role-based account rules added (§4.8) | PROPOSED |
| 0.4 | 2026-09-23 | SEC-05 hardening: reference-pilot accounts MFA/exception, 30-day rotation, hard expiry at reference release, operator register, no real data | PROPOSED |
