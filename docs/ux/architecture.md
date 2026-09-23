---
doc-id: UX-ARCH
title: Target UX Architecture
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Frontend Engineering + Hospitality Domain Architect (drafted); operational validation by the acting operations authority (OQ-003 closed)
applies-to: full enterprise target; pilot screens marked
depends-on: [ARCH-STATES, ARCH-RULES, WF-CATALOGUE, SEC-MODEL, SEC-ROLES]
---

# Target UX Architecture

## 1. Purpose

This document specifies the target operating interfaces: cross-cutting UX rules, then a screen catalogue in which every screen states purpose, users, information hierarchy, actions, filters, keyboard workflow, validation and error behaviour, permissions, empty states and mobile behaviour (mandate §18). Screens are the *presentation* of workflows; workflows (`WF-CATALOGUE`) and state machines (`ARCH-STATES`) are the source of truth for behaviour. Where a screen and a workflow disagree, the workflow wins and the screen is corrected.

Traceability: every screen lists the workflows (`WF-*`) and capabilities (`CAP-*`) it serves. A screen that serves no workflow does not exist.

## 2. UX principles

1. **The counter sets the pace.** Front-desk flows are optimised for the check-in queue: minimum keystrokes, no page reloads, no dead ends, resumable if interrupted.
2. **Keyboard-first, pointer-friendly.** Every high-frequency action has a shortcut; every shortcut has a visible affordance. Power users work without touching the mouse; new users are never lost.
3. **Money actions are deliberate, not dangerous.** Payments, refunds, adjustments and close steps require explicit confirmation with the amount and consequence visible; irreversible actions are labelled as such.
4. **Errors state the fix.** A blocked check-in says *which* gate failed, *why*, and *what to do* — including who can override. "Invalid operation" is a defect.
5. **Hotel language, not database language.** "Due out", "stayover", "walk", "comp", "room move" — the glossary is normative for UI wording.
6. **Never colour alone.** Room status, priority and alerts use icon/shape/text in addition to colour (accessibility, printouts, colour-blind staff).
7. **Permissions shape the screen, not just the buttons.** Users see the surfaces they can act on; forbidden actions are explained, not silently missing where their absence would confuse.
8. **Live where it matters.** Room board, arrival queues and cashier totals are live; reports state their as-of point. Stale information is labelled, never presented as current.
9. **One screen per job.** A housekeeping attendant gets a task list, not a dashboard. A night auditor gets a checklist, not a rate calendar.
10. **Graceful degradation.** When connectivity or a service fails, the screen says so plainly, protects what is already captured, and limits actions to those that cannot corrupt financial state.
11. **Accessible to be usable.** WCAG 2.2 AA target: full keyboard reachability, visible focus, screen-reader labels on core flows, contrast, scalable text (OQ-032 closed for language/localisation scope).
12. **Operational density with calm spacing.** Information-dense lists for scanning; generous hit targets on touch screens; whitespace where a decision is required.

## 3. Design system requirements

| Area | Requirement |
|---|---|
| Density | Lists show the maximum useful rows without scrolling on a 1366×768 storefront device at default zoom |
| Typography | Tabular numerals for money; consistent alignment; amounts always right-aligned with currency |
| Colour semantics | Distinct, accessible palette for occupancy states / readiness / priority / alerts, each with an icon or text equivalent; dark-mode-safe where relevant |
| Components | Room cell, person chip, money field, method selector, checklist item, timeline, approval card, confirmation dialog with typed acknowledgement for high-risk actions |
| Money display | Currency from folio context; no mixed-currency arithmetic in UI; rounding shown as configured |
| Date/time | Business date always visible in the header; local time for timestamps; "today" means the property's business date |
| Notifications | Severity classes (info / attention / blocking) with acknowledgement where blocking; never auto-dismiss for money or close events |
| Print | Folios, registration cards, receipts, day packs and reports render from the same data with print stylesheets; paper output never contains class-A data unless the workflow requires it |

## 4. Navigation architecture

- **Role home.** Each principal lands on the screen matching their job (agent → Front Desk; attendant → My Tasks; auditor → Night Audit/Income Audit; manager → Dashboard).
- **Global search (Ctrl/⌘+K).** One search across reservations, guests, rooms, folios, work orders — scope-enforced, class-A results masked.
- **Workspace tabs.** Cross-screen navigation keeps context (guest, reservation, room, folio) in a persistent context bar; opening a related screen never loses the current task.
- **Command palette.** Keyboard-invokable actions limited to the principal's permissions.
- **Breadcrumbs and back.** Every drill-down is reversible without losing filters.
- **Role switching** (users with multiple roles): explicit, visible, audited; never ambient.

## 5. Live data, conflict and freshness

| Behaviour | Rule |
|---|---|
| Live surfaces | Room board, arrival/departure queues, cashier totals, task boards: subscribe to updates; visual change cue without stealing focus |
| Conflict handling | When a stale action fails (room taken, folio changed), the screen states what changed and offers the corrected choice; no silent overwrite |
| Freshness labels | Reports, dashboards and exports show "as of" business date and generation time; live surfaces show a connection indicator |
| Reconnect | After a drop, the screen reconciles to server state before re-enabling financial actions; queued non-financial edits (task notes, requests) sync with conflict flags |
| Clock | Server business date governs; UI never computes financial dates locally |

## 6. Notification and alert UX

- **Classes:** info (toast, non-blocking), attention (banner, dismissible with acknowledgement), blocking (modal with required acknowledgement and next action).
- **Close window:** financial alerts (failed run, pending variance, unreconciled difference) escalate visually to blocking for the night auditor and page per BR-REL-005.
- **Approvals:** maker–checker requests and decisions appear in a dedicated queue with payload diff visible; approvals never granted through a toast.
- **Escalation visibility:** unresolved exceptions reappear in the owning role's home until resolved (never "lost after dismiss").

## 7. Permission-driven UI

1. Navigation items and actions a principal cannot use are hidden only when hiding cannot mislead; where absence would confuse (for example a supervisor approval the agent expected), the action is shown disabled with the reason and the path to request it.
2. Class-A fields render masked with an "unmask" affordance that is itself permissioned and logged.
3. Override affordances show the required authority and, when unavailable, the escalation route (who to call), never a dead end.
4. Every screen declares the permissions it renders; UI permission tests verify no hidden data leaks through counts, search, autocomplete or error text.

## 8. Error and empty state standards

| Situation | Pattern |
|---|---|
| Blocked transition | Checklist of gates with pass/fail, failing gate expanded with reason and resolution path (override authority or fix step) |
| Validation error | Field-level message stating the fix; preserves entered data |
| Concurrency conflict | Refresh-and-retry with a visible diff when the conflict changed money; never a bare "try again" |
| Service degraded | Banner naming the affected capability, what still works, what is paused, and support contact; financial actions disabled with explanation |
| No results | What was searched, likely causes (scope, spelling, status), and the primary create action when permitted |
| Empty queue | Affirmative message ("No arrivals remaining for 23 Sep") with next scheduled activity, never a blank panel |
| First use | Minimal onboarding hints for high-frequency flows; disabled with training mode per property |

## 9. Keyboard standards

- Global: `Ctrl/⌘+K` command palette, `/` search, `Esc` close layer, `?` shortcut help, `F2` context bar focus.
- Lists/queues: arrow navigation, `Enter` open, `Space` preview, letters for actions (`C` check-in, `P` payment, `M` move, `E` extend, `O` checkout where context makes them unambiguous).
- Money entry: amount field accepts `1,250.00` and `1250`; `+`/`-` for payout/receipt context; confirmation requires explicit `Enter`/click.
- Grid (room rack/tape chart): arrow keys move cell focus, `Shift+arrows` extend selection, `Enter` opens the room context, typed letters jump by room number.
- No action required for operation is keyboard-unreachable; no keyboard shortcut is destructive without confirmation.

## 10. Mobile and tablet strategy

| Context | Device | UX priority |
|---|---|---|
| Front desk | Desktop/tablet with keyboard | Full flows; tablet for queue views and walk-throughs |
| Housekeeping attendant | Phone/tablet | My tasks, one-thumb actions, photo/notes evidence, offline-tolerant sync; no dashboards |
| Housekeeping supervisor | Tablet | Board, assignment, inspection forms with large targets |
| Maintenance technician | Phone | Work orders, parts notes, photo evidence, completion |
| Night auditor / cashier | Desktop | Full flow; no mobile close |
| Manager | Phone (read), desktop (act) | Dashboard summary and approvals; drill-down on desktop |
| Guest-adjacent operators | Tablet | Future guest-facing and kiosk flows are out of pilot scope (OQ-031 closed) |

Responsive rule: a screen states its breakpoint behaviour; tables collapse to cards; primary action stays visible without hover.

## 11. Print and export UX

- Folio invoice/pro forma, receipts, registration card, group statements and day packs print from the screen context with correct series numbers and statutory particulars (ADR-008; formats OQ-011 (closed)).
- Exports are permission-scoped, logged, and class-A exports require approval and carry provenance (SEC-MODEL §10).
- Report exports state "as of" and scope; scheduled deliveries use the same definitions (WP 0.7 reporting architecture).

## 12. Graceful degradation

| Failure | Screen behaviour |
|---|---|
| Connectivity loss | Banner; read from last-known state labelled; non-financial captures (tasks, requests, notes) queue locally with sync and conflict flags; financial actions blocked with explanation |
| Authorization service unavailable | Money actions fail closed with clear message and escalation; operational read and task flows follow the documented availability policy (WP 0.7) |
| Integration down (channel, bank feed) | Status banner on affected screens; manual reference capture remains available where policy permits (for example recording a bank transfer reference pending confirmation) |
| Print device failure | Reprint with audit trail; no duplicate series numbers |
| Report service down | Last generated instance available with as-of timestamp (never silently empty) |

## 13. Screen catalogue

Screens are listed in §14–§29. Each states: purpose; users; information hierarchy; primary actions; filters/search; keyboard; validation and errors; permissions; empty state; mobile behaviour; traceability.

## 14. Front Desk — role home for front office **[P]**

| Aspect | Specification |
|---|---|
| Purpose | Run the shift: move arriving, in-house and departing guests through correct, fast operations |
| Users | Front desk agents; supervisors with override affordances |
| Information hierarchy | 1) Header: business date, occupancy, arrivals/departures counts, connection status, alert bell. 2) Arrivals queue (due-in, VIP, readiness, credit/payment flags, assignment). 3) Departures due-out (balance status, minibar/late flags). 4) In-house quick list (search-driven). 5) Traces/requests due now. 6) Quick actions bar (walk-in, payment, folio lookup, room move). |
| Primary actions | Check-in wizard; assign/move room; take payment; open folio; extend stay; checkout; capture request; register walk-in; print registration |
| Filters/search | Queue filters (all / VIP / unassigned / blocked / early); global search `/`; room/floor filters |
| Keyboard | `/` search, arrows navigate queue, `C` check-in, `P` payment, `M` move, `E` extend, `O` checkout; `Ctrl+Enter` confirm money dialogs |
| Validation & errors | Check-in shows gate checklist with pass/fail and reasons (BR-FO-001); override affordance names required authority and escalates; failures never discard captured data; stale-room conflict offers corrected choices |
| Permissions | Actions render per SEC-ROLES; overrides visible only with authority; approval requests route to the maker–checker queue |
| Empty state | "No arrivals remaining for <business date>" with next-day preview; empty in-house list explains search-first behaviour |
| Mobile | Tablet: queue views, guest lookup, request capture. Full check-in remains desktop-primary; tablet check-in permitted with all gates intact |
| Traceability | WF-FO-001/003/004/005/006; CAP-FO-001…010/012…016 |

## 15. Reservation Console **[P]**

| Aspect | Specification |
|---|---|
| Purpose | Create and amend the commercial contract correctly and quickly |
| Users | Reservations agents; front desk (off-hours); supervisors for overrides |
| Information hierarchy | 1) Search (guest/date/party). 2) Availability strip for the requested range by room type. 3) Rate options with inclusions/restrictions explained. 4) Guarantee/deposit panel. 5) Guest panel (duplicate warnings, preferences, alerts). 6) Summary with repricing preview and confirm. |
| Primary actions | New reservation; search/modify; cancel; reinstate; take deposit; assignment; pre-registration; send confirmation |
| Filters/search | Flexible search; availability filters (party size, room type, rate eligibility); "show only eligible rates" default |
| Keyboard | `N` new, `/` search, arrows across availability dates, `Enter` select rate/room, `Ctrl+Enter` confirm; repricing preview on `R` |
| Validation & errors | No availability shows the nearest alternatives and waitlist offer; restriction violations name the rule (BR-AVL-004); repricing changes highlight what moved and why; duplicate guest warning requires acknowledgement |
| Permissions | Discount/override affordances per authority bands (SEC-ROLES §4); cancellation waivers escalate |
| Empty state | Search finds nothing → create action with prefilled dates; no availability in range → alternative date grid |
| Mobile | Tablet read/create for simple bookings; complex multi-room and group intake desktop-first |
| Traceability | WF-RSV-001/002/003; CAP-RSV-001…022 |

## 16. Room Rack **[P]**

| Aspect | Specification |
|---|---|
| Purpose | The operational picture of every room: occupancy, readiness and saleability at a glance |
| Users | Front desk, supervisors, housekeeping supervisors (read), duty managers |
| Information hierarchy | Grid by floor/zone with room cells; cell shows room number, guest initials/VIP, departure date, dual state (occupancy × readiness) with icon+text, alerts (DND, credit, discrepancy); legend always visible |
| Primary actions | Assign, move, unassign; open room context (guest, folio, tasks); mark OOO/OOS request; block/unblock; highlight for queue |
| Filters/search | Floor/zone/type/status filters; "only sellable"; "only dirty due-in"; search by guest or room |
| Keyboard | Arrow navigation, `Enter` open context, `M` move (with validation), `B` block, `Esc` clear selection; type-to-jump by room number |
| Validation & errors | Moves validate type/readiness/do-not-move and explain refusals; conflicts show who changed what; no colour-only signalling |
| Permissions | Read for all operational roles; move/block/override per SEC-ROLES; override reasons required |
| Empty state | Not applicable (physical inventory always renders); unassigned queue panel shows "all arrivals assigned" affirmatively |
| Mobile | Supervisor tablet view with larger cells; tap-through actions; attendant sees only task-relevant rooms elsewhere |
| Traceability | WF-FO-003; WF-HK-001; CAP-PM-006/008/009/012; CAP-FO-006; SM-ROOM |

## 17. Tape Chart **[P]**

| Aspect | Specification |
|---|---|
| Purpose | Sell and control inventory over time: availability, bookings, blocks and restrictions across dates |
| Users | Reservations, revenue, sales, supervisors |
| Information hierarchy | Date columns × room-type (or room) rows; booking bars with status, rate plan code, party; block bars; restriction overlays (stop sell/CTA/CTD); availability totals row; occupancy % per date |
| Primary actions | Create/modify booking from the chart; drag to move dates; extend; place/steal block inventory (authority); apply restriction to range; open day detail |
| Filters/search | Room type/floor; source/channel; include/exclude blocks; zoom (7/14/30/60/90 days); occupancy threshold highlight |
| Keyboard | Arrow pan/select, `Enter` open, `Shift+arrows` range select for restrictions, `N` new booking at cursor, `Z` zoom |
| Validation & errors | Moves/drags validate availability and restrictions before commit with preview; refused moves explain the blocking rule and nearest legal option; concurrent changes prompt reconcile |
| Permissions | Restriction/rate edits per revenue authority; block over-allocation per BR-GRP-001; read for front office |
| Empty state | Dates beyond inventory horizon show "rate not loaded for <dates>" with the action for revenue |
| Mobile | Read-only overview on tablet; edits desktop (density and precision) |
| Traceability | WF-RSV-001/002/004; CAP-AVL-001…010; CAP-RTM-002/009; SM-INVENTORY |

## 18. Guest 360 **[P]**

| Aspect | Specification |
|---|---|
| Purpose | One trustworthy view of the person: who they are, how they like to be served, what must not be missed — lawfully |
| Users | Front desk, reservations, sales (scoped), managers; class-A readers per permission |
| Information hierarchy | 1) Identity header (name, status, VIP, alerts). 2) Contact and documents (masked class-A). 3) Preferences and accessibility. 4) Relationships and accounts. 5) Stay history with financial summary (scoped). 6) Consent and communication preferences. 7) Duplicate/merge indicators and audit trail. |
| Primary actions | Edit profile; record consent/withdrawal; add preference; merge (governed); open reservation/folio; start communication; request ID unmask (logged) |
| Filters/search | Find within profile (history, notes, documents); timeline filters |
| Keyboard | `/` find, tab sections, `E` edit, `M` merge (governed), `Ctrl+Enter` save; audit trail on `H` |
| Validation & errors | Merge is non-destructive and reversible in evidence (INV-GST-1/2); duplicate warnings during edit; consent withdrawal applies prospectively with clear confirmation; class-A access failures explain the permission path |
| Permissions | Field-level per SEC-MODEL §5; merge requires supervisor; exports restricted |
| Empty state | New profile shows onboarding form and "search first" prompt to avoid duplicates |
| Mobile | Read + preference/consent capture on tablet; merge desktop-only |
| Traceability | CAP-GST-001…012; WF-RSV-001; SM-* guest-related |

## 19. Reservation 360 **[P]**

| Aspect | Specification |
|---|---|
| Purpose | Everything about one booking: terms, rooms, guests, money, obligations and history |
| Users | Reservations, front desk, supervisors, finance (routing/credit context) |
| Information hierarchy | 1) Status header (state, guarantee, deposit, alerts). 2) Rooms and guests (per-room rate, plan, assignment, traces). 3) Financial panel (folios/windows, routing, deposits, balance expectations). 4) Amendment timeline (who/what/why, applied basis). 5) Communications and confirmations. 6) Actions panel (state-dependent). |
| Primary actions | Modify; cancel/reinstate; assign/move; pre-register; take/apply deposit; edit routing; send confirmation; convert to stay context at arrival |
| Filters/search | Timeline filters; per-room drill; balance detail |
| Keyboard | `M` modify, `C` cancel (confirms consequences), `R` routing, `D` deposit, `A` assign, `H` timeline |
| Validation & errors | Consequential actions show before/after with pricing and policy basis (BR-RTM-007, BR-FOL-011); blocked actions explain the legal transition and the alternative |
| Permissions | Waivers/overrides per authority; finance actions per role |
| Empty state | Not applicable; "no amendments recorded" shown as positive confirmation |
| Mobile | Read and light actions (assignment, traces) on tablet |
| Traceability | WF-RSV-002/003/004; CAP-RSV-003…022; SM-RESERVATION |

## 20. Housekeeping Board **[P]**

| Aspect | Specification |
|---|---|
| Purpose | Get the right rooms ready in the right order with verified quality |
| Users | Housekeeping supervisors (act), front desk (read), managers (read) |
| Information hierarchy | 1) Shift header (business date, arrivals due, departures, rush/VIP count, staffing). 2) Room columns by readiness state (dirty / in progress / clean / inspected / blocked) with priority flags. 3) Attendant assignments and workload credits. 4) Inspection queue. 5) Discrepancy and issue alerts. 6) Productivity strip. |
| Primary actions | Assign/rebalance (drag or bulk); mark priority; inspect (pass/fail with findings); resolve discrepancy; report maintenance issue; unblock DND |
| Filters/search | Zone/floor; attendant; priority; state; "arrival-critical only" |
| Keyboard | Arrow navigation, `A` assign, `I` inspect, `R` rebalance, `P` priority, `D` discrepancy |
| Validation & errors | Inspection pass requires findings-free checklist where configured; failed inspection reopens the task with reasons; readiness release respects policy (BR-HSK-002); no colour-only states |
| Permissions | Attendants see own tasks; supervisors assign/inspect; self-inspection only under recorded policy exception (BR-HSK-003) |
| Empty state | "All rooms ready for current arrivals" affirmative banner; zero-task shift explains next board cycle |
| Mobile | Supervisor tablet-first; attendant phone-native task list with offline-tolerant completion queue |
| Traceability | WF-HK-001/002; CAP-HSK-001…012; SM-HOUSEKEEPING-TASK; SM-ROOM |

## 21. Maintenance Board **[P — pilot scope; OQ-022 closed]**

| Aspect | Specification |
|---|---|
| Purpose | Triage, assign and close maintenance work with evidence, and keep OOO impact visible |
| Users | Chief engineer, technicians, supervisors, front desk (visibility of OOO) |
| Information hierarchy | 1) Header: open work orders by SLA class, OOO rooms, overdue tasks. 2) Queue by priority/SLA with source (guest, inspection, PM, staff). 3) Assignments by trade. 4) OOO/OOS panel linked to inventory impact. 5) Asset history and PM schedule. 6) Parts holds and cost capture. |
| Primary actions | Triage/set priority; assign; accept; add notes/parts/photos; place hold; complete; verify; request OOO; return to service |
| Filters/search | Priority, SLA breach, trade, room/asset, source, OOO status |
| Keyboard | Arrow navigation, `T` triage, `A` assign, `H` hold, `C` complete, `V` verify |
| Validation & errors | Verification requires evidence per policy; return-to-service requires verified work order (BR-MNT-001); SLA clock visible and never silently paused; rejected verification reopens with findings |
| Permissions | Technicians execute; engineers verify; OOO creation respects authority; front desk sees impact, cannot clear |
| Empty state | "No open work orders" affirmative; PM schedule shows next due tasks |
| Mobile | Technician phone-first: accept, notes, photo, complete; supervisor tablet for triage/verify |
| Traceability | WF-MT-001; CAP-MNT-001…008; SM-WORK-ORDER; SM-ROOM saleability |

## 22. Cashier **[P]**

| Aspect | Specification |
|---|---|
| Purpose | Receive and account for money exactly, in a session that closes cleanly |
| Users | Cashiers (front desk operators in the cashier role), supervisors/finance for review |
| Information hierarchy | 1) Session header: cashier, till, open time, live totals by method. 2) Payment entry (method-specific fields: cash, POS terminal/reference, transfer reference, cheque details). 3) Payout and drop panel. 4) Open balance lookup (folio/guest) with context. 5) Close panel: expected vs counted by method, variance, review routing. |
| Primary actions | Open session with float; take payment; payout within limits; record drop; verify transfer; close session; explain variance; reprint receipt |
| Filters/search | Session lines by method/time; folio lookup; reprint history |
| Keyboard | Numeric-first entry; `F2` amount, `F3` method cycle, `Ctrl+Enter` commit payment, `+`/`-` payout/receipt toggle, `C` close panel |
| Validation & errors | Payment requires open session; method fields validate references (Luhn-free: no card data stored — reference only); payouts above limits require authority; close blocks on missing counts; variance beyond tolerance routes to review with reason (BR-CSH-002/003); POS entry states "pending settlement — not yet cleared" so cashiers do not treat slips as money |
| Permissions | Own session; supervisor review; finance unlimited with reason; no self-approval of variance |
| Empty state | No open session → prominent "Open session" with float confirmation; closed session → receipt reprint and history |
| Mobile | Phone/till tablet payment capture with numeric pad; close and variance review desktop-first |
| Traceability | WF-FIN-001; CAP-CSH-001…010; SM-CASHIER-SESSION; SM-PAYMENT |

## 23. Night Audit **[P]**

| Aspect | Specification |
|---|---|
| Purpose | Close the operating day once, provably, recoverably — with no surprises in the morning |
| Users | Night auditors; supervisors/FC for exception approvals; on-call engineers (alerted, not in-screen) |
| Information hierarchy | 1) Header: business date, close status, elapsed time, certification state. 2) Blocking checklist (unposted charges, open sessions, pending departures, discrepancies, pending clearance, mapping gaps) each with count, owner and drill-through. 3) Posting run panel (status, families, amounts, retry). 4) Control totals: expected vs actual by family with drill-down. 5) Exception queue with resolution actions. 6) Day pack generation and print/share. |
| Primary actions | Resolve/route exceptions; run close; resume failed run; review control differences; generate day pack; escalate (wake-up path) |
| Filters/search | Checklist by severity; exception owner; posting family |
| Keyboard | Checklist-driven: arrows + `Enter` to drill, `R` resolve, `X` run close (typed confirmation), `Esc` back — deliberate friction on the close action only |
| Validation & errors | Close blocks on any unresolved blocking item, naming it and the path to resolve (BR-NAU-001); failed runs show checkpoint and resume state, never partial advancement (ADR-006); certification status always visible; no override of the control itself |
| Permissions | Night auditor executes within limits; approvals route to supervisor/FC; reopen requires FC with impact assessment (ADR-006 §5) |
| Empty state | Fully clean checklist shows "All checks passed — ready to close" with run-times estimate |
| Mobile | Monitor-only on tablet (status/alerts); close execution desktop |
| Traceability | WF-NA-001/002/003/004; CAP-NAU-001…010; CAP-INA-001…008; SM-NIGHT-AUDIT |

## 24. Manager Dashboard **[P]**

| Aspect | Specification |
|---|---|
| Purpose | Run the property from trustworthy numbers and visible exceptions |
| Users | GM, duty managers, finance (finance variant), group executives (group variant, future) |
| Information hierarchy | 1) KPI strip: occupancy, ADR, RevPAR, room+total revenue, comps/allowances, AR overdue (as-of business date). 2) Today: arrivals/departures/VIP, readiness risk, no-shows. 3) Exceptions: unreconciled differences, variances, credit holds, open maintenance, unresolved audit findings. 4) Trends: 7/30-day occupancy and rate; pace for next 30. 5) Approvals queue (maker–checker) requiring the manager. |
| Primary actions | Drill into any figure (to source transactions); review/approve requests; assign exceptions; open reports; acknowledgements |
| Filters/search | Date range, business date vs calendar; segment/outlet; property (future group view) |
| Keyboard | `D` drill, arrows across KPIs, `A` approvals, `E` exceptions, `R` report catalogue |
| Validation & errors | Every figure states its as-of point and reconciles; if the day is uncertified, the dashboard labels it; stale figures are badged, never silently shown as current |
| Permissions | Property scope; financial drill-down per role; group view read-only (future) |
| Empty state | Pre-open property (or first pilot days) shows expected-data explanation rather than zeros |
| Mobile | Summary cards + approvals; full drill on desktop |
| Traceability | CAP-RPT-007; CAP-RPT-002/003; CAP-BI-* (future variants) |

## 25. Group Management **[P — OQ-017 (closed)]**

| Aspect | Specification |
|---|---|
| Purpose | Convert committed group business into delivered, billable reality without disputes |
| Users | Sales, events coordinator, reservations, finance (deposits/credit), front desk (rooming list context) |
| Information hierarchy | 1) Block header (dates, rate agreement, cutoff, deposit schedule, attrition terms, owner). 2) Pickup panel: block vs pickup by room type/date with wash projection (BR-GRP-001/004). 3) Rooming list manager (named guests → rooms → reservations, gaps highlighted). 4) Master account and routing panel. 5) Function space and events linked to the block. 6) Activity timeline and documents. |
| Primary actions | Create/amend block; issue rooming list; convert list entries to reservations; extend cutoff (authority); apply deposit; adjust master routing; calculate attrition preview; complete/cancel group |
| Filters/search | Block status/date/owner; pickup gaps; unassigned rooming entries |
| Keyboard | `L` rooming list, `P` pickup report, `D` deposit, `R` routing, `C` convert selected |
| Validation & errors | Pickup beyond block requires authority and is flagged; cutoff extension logged and reported (BR-AVL-005); attrition preview shows the formula and evidence before any charge (BR-GRP-003); master account must exist before group routing (BR-GRP-005) |
| Permissions | Sales amend within limits; revenue for inventory/rate deviations; finance for deposits/credit/attrition |
| Empty state | New block shows onboarding checklist (terms, deposit, cutoff, rooming contact) |
| Mobile | Read/approve on tablet; rooming list editing desktop/tablet |
| Traceability | WF-RSV-004; CAP-GRP-001…012; SM-GROUP-BLOCK |

## 26. Corporate Management **[P]**

| Aspect | Specification |
|---|---|
| Purpose | Manage commercial relationships and credit exposure without unsecured risk |
| Users | Sales, finance controller, GM (approvals) |
| Information hierarchy | 1) Account header (type, status, credit profile, terms). 2) Rate agreements with validity and eligibility. 3) Billing instructions and default routing. 4) Exposure panel: unbilled direct-bill exposure, AR balance, ageing buckets, limit remaining (BR-CRP-001). 5) Production: recent stays, room-nights, revenue, commission exposure. 6) Activity and documents. |
| Primary actions | Create/edit account; negotiate rate agreement (activate/expire); set billing instructions; adjust credit limit (authority); suspend/reinstate; open AR statement; log activity |
| Filters/search | Account status/type/owner; overdue accounts; expiring agreements; exposure threshold |
| Keyboard | `R` rate agreement, `C` credit panel, `B` billing instructions, `S` statement, `A` activity |
| Validation & errors | Limit changes require finance authority and are audited; suspension explains the consequence (future transfers blocked; existing stays unaffected — BR-CRP-002/003); rate agreement overlaps/eligibility conflicts are explained before activation |
| Permissions | Sales propose; finance approves credit and limits; GM above top bands |
| Empty state | New account shows credit assessment checklist before direct-bill enablement |
| Mobile | Read and approval on tablet; negotiation and statements desktop |
| Traceability | WF-FIN-004; CAP-CRP-001…010; SM-CORPORATE-CONTRACT |

## 27. Rate Management **[P]**

| Aspect | Specification |
|---|---|
| Purpose | Make correct pricing easy and wrong pricing hard, with full history |
| Users | Revenue manager, GM (approvals), finance (tax/service-charge context read) |
| Information hierarchy | 1) Rate calendar: dates × room types × plans with amounts, occupancy basis and restriction badges. 2) Plan detail: inclusions, cancellation policy, LOS rules, tax applicability. 3) Derived rules (base, adjustment, floor/ceiling). 4) Restriction editor over ranges. 5) Change preview: affected dates, occupancy, revenue impact estimate. 6) History/version panel with effective dates and approvals. |
| Primary actions | Edit amounts (single/range/bulk); create derived rules; set restrictions; activate/suspend/retire plans; approve above-band changes; compare versions |
| Filters/search | Plan, room type, date range, occupancy basis; "only changed since"; "only restricted" |
| Keyboard | Grid navigation; `E` edit cell, `Shift+arrows` range, `V` preview, `Ctrl+Enter` commit, `H` history |
| Validation & errors | Effective-date overlap and gap validation (ADR-003); past-date edits are impossible — the screen explains why and offers the forward version; bulk changes require preview confirmation naming impacted dates (BR-RTM-003); derived-rate circularity is blocked with the dependency chain shown |
| Permissions | Edit within revenue bands; above-band changes route to GM approval (maker–checker) |
| Empty state | No rates loaded for a horizon shows the generation action and last-loaded date |
| Mobile | Read on tablet; editing is desktop (density, precision, money) |
| Traceability | CAP-RTM-001…013; SM-RATE; BR-RTM-001…008 |

## 28. Reporting **[P]**

| Aspect | Specification |
|---|---|
| Purpose | Answer operational, financial and audit questions with figures that reconcile and drill down |
| Users | All roles per scope; finance and income audit as primary daily users |
| Information hierarchy | 1) Catalogue by domain (operational / revenue / financial / audit / housekeeping / maintenance). 2) Parameter panel (business date range, property scope shown as fixed chip, filters). 3) Result area with summary → drill-down to transactions. 4) Provenance strip: as-of business date, generated at, certification status, scope. 5) Schedules and delivery history. |
| Primary actions | Run, drill, export (permissioned/approval for class A), schedule, share, reprint historical instance, save view |
| Filters/search | Catalogue search; saved views; "my reports"; scheduled-only |
| Keyboard | `/` find report, `Enter` run, `D` drill, `E` export (with confirmation), `S` schedule |
| Validation & errors | Every report reconciles to source and states its as-of point (BR-RPT-001); uncertified-day figures are badged; export of class-A requires approval and logs provenance; failed scheduled deliveries surface in the schedule panel, never silently |
| Permissions | Report definitions scope-aware; drill-down cannot escape scope; export restrictions per data class |
| Empty state | No data for range explains why (pre-opening, unclosed day, no transactions) rather than rendering zeros |
| Mobile | KPI reports readable; complex tables indicate desktop recommendation |
| Traceability | CAP-RPT-001…012; BR-RPT-001…008; SEC-MODEL §6 |

## 29. Administration **[P]**

| Aspect | Specification |
|---|---|
| Purpose | Configure and operate the platform safely: people, policies, integrations, evidence |
| Users | System administrators (technical), finance controller (financial configuration approvals), security (break-glass review), managers (property policy) |
| Information hierarchy | 1) People: users, roles, MFA state, provisioning requests, quarterly review queue. 2) Policy: authority limits (with maker–checker on change), timings, routing defaults, statistics definitions (versioned). 3) Financial configuration: tax rules, account mappings, number series — finance-approved, effective-dated (read-only to admins). 4) Property setup: rooms/types/features, OOO patterns, numbering. 5) Integrations: health, credentials rotation status, queue/lag, reconciliation exceptions. 6) Evidence: audit search (scoped), break-glass log, export log, configuration change history. 7) System: version, release notes, backup/recovery status. |
| Primary actions | Provision/deprovision; assign roles (with SoD warnings); request policy changes; rotate credentials; retry integration queues; run access review; review break-glass; open audit search |
| Filters/search | Global evidence search by correlation/entity/actor; integration by status/lag; users by role/state |
| Keyboard | Navigation + standard form shortcuts; no bulk destructive shortcuts |
| Validation & errors | SoD conflicts are warnings requiring explicit, recorded override; admins cannot approve financial configuration (approval routes to finance); limit changes cannot exceed the approver's own authority (BR-PLT-001); integration retries are idempotent, showing attempt history |
| Permissions | Admin for technical actions; finance for financial config; security for break-glass review; no self-escalation possible |
| Empty state | Fresh tenant shows a provisioning checklist (property, rooms, rates, taxes, users, integration) |
| Mobile | Alerts and integration health only; configuration desktop |
| Traceability | CAP-PLT-001…016; SEC-MODEL §9/§10; SEC-ROLES §6 |

## 30. UI acceptance and test obligations

| Obligation | Applies to |
|---|---|
| Scripted end-to-end flows pass with keyboard only: check-in, payment, move, checkout, close | All screens |
| Screen-reader labels and focus order verified on core flows | All screens |
| Negative and empty states render per §8 for every queue and form | All screens |
| Permission UI tests prove hidden data cannot leak via counts, search, autocomplete or error text | All screens |
| Responsive tests at defined breakpoints per screen (desktop/tablet/phone) | All screens |
| Concurrency tests: stale room, stale folio, stale rate, duplicate payment attempt show corrected-choice UX | Front Desk, Room Rack, Cashier, Rate Management |
| Performance budgets: room rack 200 rooms < 1 s interactive; tape chart 90 days × 10 types < 2 s; queue navigation instant; report run within NFR (WP 0.7) | Room Rack, Tape Chart, Reporting |
| Degradation drills: connectivity loss, authorization outage, integration down per §12 | Front Desk, Cashier, Night Audit, Reporting |
| Print fidelity: folio/receipt/registration/series numbers | Front Desk, Cashier, Reporting |
| No colour-only signalling audit | All screens |
| Operational walkthrough with hotel staff per workflow | Pilot screens |

## 31. Open items and dependencies

| Item | Owner | Effect |
|---|---|---|
| OQ-003 closed — acting operations authority (Product Owner) | Product Owner | Workflow and screen validation; real-hotel validation at the first property |
| OQ-011 (closed) invoice formats | Finance | Document layout and particulars |
| OQ-014 (closed)/017/022 outlets, groups, maintenance pilot depth | Product Owner / Hotel Ops | Which screens are pilot-live vs configured off |
| OQ-032 (closed) accessibility/language scope | Product Owner | Localisation, assistive requirements beyond WCAG AA |
| OQ-034 (closed) housekeeping standards | Hotel Ops | Board defaults, inspection affordances |
| Device strategy and network reality | Acting operations authority (OQ-003 closed) / Platform | Responsive priorities, offline tolerance design; site details deferred to the first property |
| UX copy deck and hotel terminology review | Domain + Hotel Ops | Wording in all screens (glossary normative) |

## 32. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial UX architecture (16 screens) issued with WP 0.6 | PROPOSED |

