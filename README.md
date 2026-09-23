# SuiteFlow Target-State Blueprint

**Status:** Active — Phase 0 (Target Product Definition)
**Date opened:** 23 September 2026
**Owner:** SuiteFlow programme (Product Owner accountable)

This repository is the canonical specification for SuiteFlow: the hotel platform we *should* build, designed from hospitality requirements, enterprise PMS capability benchmarks, accounting principles, security principles and real hotel operating workflows.

It is deliberately separate from the implementation repository [`Highlandfury/SuiteFlow`](https://github.com/Highlandfury/SuiteFlow). That repository contains the current implementation: the `hotel_integration` Frappe application, compatibility automation, patches and UAT evidence. The implementation is **evidence about the present**, not a specification for the future.

## Why this repository exists

Enterprise software fails when the implementation defines the product. SuiteFlow's mandate is the opposite order:

```text
TARGET STATE  (this repository)
      ↓
CURRENT STATE (implementation repository, audited later)
      ↓
GAP
      ↓
TRANSITION ARCHITECTURE
      ↓
IMPLEMENTATION ROADMAP
```

The target blueprint is authored before, and independently of, any inspection of current code, databases, DocTypes, screens or workflows. Current-state analyses, when they happen (Phase 2), are recorded separately and compared against this blueprint — never used to silently redefine it.

## What belongs here

- Product vision, scope, capability map, roadmap
- Target domain model, data model and state machines
- Target workflows for reservations, front desk, housekeeping, maintenance, finance and night audit
- Target financial, security, integration, reporting, UX, QA, deployment and documentation architectures
- Architecture Decision Records (ADRs) issued at blueprint level
- Governance: charter, inputs register, glossary, risk register, open questions

## What does not belong here

- Application code, DocType definitions, SQL, patches or infrastructure configuration
- Confidential or proprietary material from any commercial vendor
- Claims about the current SuiteFlow implementation (those belong to Phase 2 evidence records)

## Related documents outside this repository

| Document | Location | Role |
|---|---|---|
| Product Owner Kickoff Decision Log v0.2 | `/home/suiteflow/docs/` (programme machine) | Accepted business requirement input, recorded in the inputs register |
| Architecture and Financial Control Decision Pack v0.1 | `/home/suiteflow/docs/` | Proposed architecture input, subject to blueprint validation |
| Product Owner Baseline | `Highlandfury/SuiteFlow` → `docs/` | Accepted pilot baseline, recorded in the inputs register |
| Implementation repository | `Highlandfury/SuiteFlow` | Current-state implementation and evidence (Phase 2 audit target) |

## How to read this repository

Start with [`docs/00-governance/charter.md`](docs/00-governance/charter.md) for the status model, source-of-truth hierarchy and change control. Every document carries front matter declaring its status, version and confidence labels. Anything not marked `ACCEPTED` is not yet authority.

## Current phase

**WP 0.6 — UX architecture** — complete, pending Product Owner review:

- `ux/architecture.md`: UX principles, design-system requirements, navigation, live-data/conflict handling, notification classes, permission-driven UI, error/empty-state standards, keyboard standards, mobile/tablet strategy, print/export, graceful degradation.
- **16-screen catalogue** (Front Desk, Reservation Console, Room Rack, Tape Chart, Guest 360, Reservation 360, Housekeeping Board, Maintenance Board, Cashier, Night Audit, Manager Dashboard, Group Management, Corporate Management, Rate Management, Reporting, Administration), each with purpose, users, information hierarchy, actions, filters, keyboard, validation/errors, permissions, empty state, mobile behaviour and traceability to workflows/capabilities.
- UI acceptance and test obligations (§30), including keyboard-only flows, permission-leak tests, performance budgets and degradation drills.

### Completed work packages

| WP | Deliverable set | Status |
|---|---|---|
| 0.1 | Governance, vision, scope, personas, capability map (283), roadmap skeleton | Complete |
| 0.2 | Target-state, ownership, tenancy, domain model (80 invariants), data architecture, ADR-001…004 | Complete |
| 0.3 | 18 state machines, 22 workflows, 101 business rules | Complete |
| 0.4 | Financial architecture, ADR-005…008; inputs validated | Complete |
| 0.5 | Security model, role/authority matrix, ADR-009 | Complete |
| 0.6 | UX architecture (16 screens), test obligations | Complete |

### Outstanding Product Owner dependencies

OQ-011 (invoice authority) · OQ-012 (deposit/cancellation defaults) · OQ-021/OQ-029 (tax/fiscalisation — UNVERIFIED) · OQ-001/OQ-002/OQ-003 (entity and named representatives) · OQ-014/017/022 (outlets, groups, maintenance pilot depth) · OQ-034 (housekeeping standards). All authority limits are proposed defaults. See `docs/00-governance/decisions-required.md`.

Remaining before first committed implementation: WP 0.7 (platform architecture), WP 0.8 (blueprint consolidation + full roadmap), then Phase 1 research, Phase 2 audit, Phase 3 gap, Phase 4 transition.

Next: WP 0.7 — integration, reporting, non-functional, QA, deployment, documentation and AI architecture.

## Licensing

Repository content licence is pending, consistent with the SuiteFlow programme's open licensing position. No external distribution until counsel approves.
