---
doc-id: ADR-001
title: Application topology and domain ownership
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Principal Architect (drafted); Product Owner (approval)
applies-to: full enterprise target
depends-on: [GOV-CHARTER, GOV-INPUTS, PROD-CAPMAP]
supersedes: none (rules on DP-CTX-001, DP-CTX-002)
---

# ADR-001: Application topology and domain ownership

## Context

The enterprise target (capability map, 283 capabilities) concentrates a hotel's commercial life in a small number of domains with hard transactional requirements: inventory and availability (a room must not be sold twice), folios and payments (a balance must never be wrong), night audit and close (a day must post once and recoverably), and cash (counts must reconcile). The programme operates with a small team, 24×7 production obligations and a 1-hour RPO/RTO. The existing programme proposed a modular monolith on Frappe with Kamra as operations source, ERPNext as accounting and `hotel_integration` as control plane (DP-CTX-001, DP-CTX-002). Those are proposed inputs, not approved architecture.

## Problem

Which application topology and which ownership model give the target its required integrity, operability and upgradeability without introducing distributed-systems failure modes the team cannot support?

## Target-state requirement

- Exactly one authoritative owner for every datum and every decision.
- Financial effects are transactional, idempotent, reconcilable and auditable end-to-end.
- Operational workflows (assignment, check-in, posting, close) work under concurrency without lost updates or duplicates.
- The system remains operable and upgradeable by a small team at 24×7.
- Enterprise capabilities (chain reporting, central profiles, multi-property) can be added without re-architecture.
- No proprietary implementation is copied from commercial systems; the topology must be justified on its own merits.

## Options

| Option | Summary | Assessment |
|---|---|---|
| A. Modular monolith with layered ownership | One deployable application + database per tenant; strict internal module boundaries; clearly separated ownership layers | **Chosen** |
| B. Domain microservices | Separate services per domain with own datastores and sagas for consistency | Rejected: distributed transactions replace local ones, availability calculations and folio integrity become saga problems, operability cost unaffordable for the team size and 24×7 target |
| C. Product federation without a unified ownership model | Keep independent products and integrate opportunistically | Rejected: the current programme already demonstrates the failure mode (duplicated and ambiguous truths); federation without ownership rules is what the target must replace |

## Decision

1. **SuiteFlow is a modular monolith.** One deployable application and one database per tenant (see ADR-002 for tenancy). Nothing in this ADR forbids future extraction of a specific component when measured evidence justifies it; extraction is not permitted as a design fashion.
2. **Four logically distinct layers exist inside the monolith**, with strict ownership roles:

| Layer | Owns | Does not own |
|---|---|---|
| **Platform Services** | Identity, authorization, property/company scope, effective-dated configuration, audit, event outbox, notifications, numbering, observability, recovery tooling | Business decisions or business records |
| **Hotel Operations Core** | Operational source records: property/inventory, rates, reservations, stays, room states, guests, housekeeping/maintenance tasks, folios, charges, payments, deposits, cashier sessions, outlets | Financial books, cross-domain policy decisions, reconciliation |
| **Control Plane** | Governed state transitions and their evidence, policy resolution, business date, idempotent posting orchestration, reconciliation state, night audit/income audit execution, integration adapters, reporting read models | Duplicating operational source records; holding financial balances of record |
| **Accounting Authority** | General ledger, receivables, tax documents, statutory books, period close (bounded interface; financial architecture finalised in WP 0.4) | Operational workflows and hotel-facing documents where those are the agreed source |

3. **One owner per datum/decision**, expressed in the source-of-truth matrix (`../target-state.md` §4). Derived data is always rebuildable and reconciled; it is never authoritative.
4. **External systems are reached only through adapters.** Adapters are infrastructure: they translate protocols and provider quirks; they hold no domain rules and no authoritative data. Provider-neutral interfaces are defined by the domain.
5. **Financial effects have a single gateway.** No module writes accounting documents directly; all financial effects pass through the posting service with durable identity, idempotency and reconciliation links (detail in WP 0.4).
6. **Module boundaries are enforced, not requested.** Internal dependency rules are verified by automated architectural tests; a violation fails the build.
7. **Product binding is deferred and constrained.** This ADR defines roles and their contracts. Binding a concrete product to a role (operations core, accounting authority, control plane) is a Phase 3/4 decision that requires role-contract evidence. No existing product inherits architectural authority from its presence in the current implementation.

## Reasoning

- Hotels are an ACID business. Availability, folio balances and close integrity are exactly the problems distributed sagas make harder and less auditable. Option A keeps these invariants local and provable.
- The ownership crises the current programme has experienced (duplicated accounting effects, ambiguous business-date authority, bypassable controls) are ownership failures, not deployment-topology failures. Making ownership explicit and testable addresses the root cause directly.
- A modular monolith with enforced boundaries preserves the option to extract components later, whereas microservices foreclose the simple option now.
- Adapter discipline and a single financial gateway deliver most of the isolation benefit attributed to service separation, without distributed transactions.

## Trade-offs

- Single-deployment blast radius; one bad release can take the property down. Mitigated by staged promotion, rehearsed rollback and recovery design (WP 0.7).
- Module discipline depends on enforcement; without architectural tests, erosion is inevitable. The ADR therefore mandates the tests as part of the decision itself.
- Scaling is achieved by vertical growth and read replicas until measured evidence forces otherwise; this is accepted deliberately.
- The Control Plane is a large responsibility (89 CTRL capabilities). Its scope must be justified capability-by-capability in WP 0.4/0.7 — a bloated control plane would recreate the ambiguity this ADR forbids.

## Risks

| Risk | Mitigation |
|---|---|
| Module erosion over time | Dependency rules + architectural tests in CI; ownership map reviewed in governance cadence |
| Monolith performance at chain scale | Read models for reporting; measured optimisation; extraction allowed with evidence |
| Control-plane overreach | Every CTRL capability must state what it owns and what it must not duplicate; reviewed at WP 0.4 |
| Product binding decided by convenience | Phase 3/4 evidence rule (decision 7) |

## Consequences

- The blueprint requires an explicit module map with dependency rules (`../target-state.md` §3).
- Posting-service design becomes a first-class deliverable (WP 0.4).
- Architecture tests (no cross-module writes, no direct financial writes, adapter isolation) become acceptance obligations.
- The target does not assume the current three-product reality; it defines roles that may be satisfied by one, several or different products over time.

## Implementation impact

No immediate implementation change. The current implementation remains evidence for Phase 2. Transition architecture (Phase 4) will decide per domain how existing modules map onto these layers.

## Migration impact

Any future transition must preserve: transactional integrity of availability/folio/close, property scope, audit completeness and reconciliation links. Migration is executed per domain, never as a big-bang re-platforming.

## Review trigger

- Evidence that a component must scale or fail independently beyond the monolith's measured capability.
- A product-binding decision that cannot satisfy the role contract of the layer it fills.
- Regulatory or contractual isolation requirements that invalidate ADR-002's tenancy model.

## Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial decision issued with WP 0.2 pass 1 | PROPOSED |
