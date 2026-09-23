---
doc-id: RPT-ARCH
title: Target Reporting Architecture
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Data Architect (drafted); Finance Controller (approval; OQ-002 open)
applies-to: full enterprise target
depends-on: [FIN-ARCH, ADR-003, ADR-005, SEC-MODEL, BR-RPT]
---

# Target Reporting Architecture

## 1. Purpose and principles

Reporting is how the hotel steers; it must reconcile to the same numbers finance sees, and every figure must be drillable to source. Principles:

1. **One number, one definition.** Statistical and financial definitions are versioned configuration (BR-RPT-008); no competing spreadsheets define reality.
2. **Reconcile or it is a defect.** Every report reconciles to source transactions and drills down (BR-RPT-001, CAP-RPT-012).
3. **As-of is explicit.** Every output states business date, generation time, scope and certification status.
4. **Scope-bound.** Reports, exports, schedules and AI reads use the principal's property/company scope, enforced server-side (SEC-MODEL §6).
5. **Closed data for finance.** Financial reports read frozen close totals; open-day figures are badged provisional.
6. **Derived means rebuildable.** Read models are projections with documented rebuild procedures (data architecture §8).
7. **No production queries.** Heavy reporting never runs against operational tables under load.

## 2. Data flow

```text
Operational records (folios, payments, stays, tasks)
        │  events / projections (idempotent)
        ▼
Read models:   operational boards (live)  ·  daily aggregates (business-date)  ·  control expectations  ·  analytical extracts (Ph15)
        ▼
Reports:  operational · revenue · financial · audit · housekeeping/maintenance · management · chain (Ph14)
        ▼
Delivery: screen · print · export (permissioned) · scheduled
```

Read model classes:

| Class | Content | Freshness | Source |
|---|---|---|---|
| Live boards | Room state, queues, tasks, cashier totals | Seconds | Direct events/projections |
| Daily aggregates | Revenue by family, taxes, payments by method, deposits movement, statistics | At close (frozen), updated during open day as provisional | Posting runs and folio items |
| Control expectations | Guest ledger, deposits, clearing, AR, tax expectations for reconciliation | Each posting run | Financial architecture §10 |
| Analytical extracts | Historical, cross-property, BI | On schedule (Ph15) | Closed aggregates only |

## 3. Report catalogue

| # | Report | Definition source | Frequency | Primary users |
|---|---|---|---|---|
| 1 | Arrivals / in-house / departures | Operational state | Live | Front office |
| 2 | No-shows, cancellations, amendments | State transitions | Daily | Front office, revenue |
| 3 | Manager's flash / daily revenue report | Frozen close totals | Daily | GM, finance |
| 4 | Revenue by family and cost centre | Posting families | Daily | Finance |
| 5 | Tax report (collected vs liability) | Tax lines vs GL | Daily | Finance |
| 6 | Payments by method and settlement state | SM-PAYMENT states | Daily | Finance, cashiering |
| 7 | Cash and banking report | Sessions, drops, deposits | Daily | Finance |
| 8 | Deposit liability and ageing | Deposit lifecycle | Daily | Finance |
| 9 | AR ageing and statements | AR documents | Daily / monthly | Finance, sales |
| 10 | Allowances, comps, voids by user | Adjustment items | Daily | Income audit, GM |
| 11 | Overrides and limits usage | Transition evidence | Daily | Income audit, security |
| 12 | Cashier variance report | Sessions | Daily | Finance |
| 13 | Reconciliation exceptions | FIN-ARCH §10 | Daily | Finance, income audit |
| 14 | Occupancy, ADR, RevPAR, segmentation | BR-RPT-002…007 definitions (versioned) | Daily / monthly | GM, revenue |
| 15 | Pace and pickup | Reservations vs prior periods | Weekly / monthly | Revenue, sales |
| 16 | Housekeeping productivity and discrepancies | Tasks, inspections | Daily | Housekeeping, GM |
| 17 | Maintenance SLA and PM compliance | Work orders, PM tasks | Weekly | Chief engineer, GM |
| 18 | Close report with certification status | Close run + audit certification | Daily | Finance, audit |
| 19 | Document series audit | Number series | Monthly | Finance |
| 20 | Group pickup and wash | Block vs pickup | Per group / weekly | Sales, revenue |
| 21 | Guest history and production | Guests, stays, folios | On demand | Front office, sales |
| 22 | Chain consolidation *(Ph14)* | Closed property results | Monthly | Group executives |

## 4. Definitions governance

- Statistical definitions (available/occupied rooms, house-use and comp treatment, ADR, RevPAR, TRevPAR) are versioned configuration per BR-RPT-008; a definition change versions subsequent reports and is visible in the report provenance.
- Financial report definitions derive from the posting families and chart mapping (FIN-ARCH §3); finance owns changes with effective dates.
- A report definition change never restates published history; restatements occur only through close/reopen governance (ADR-006) and are versioned (BR-NAU-006).

## 5. Drill-down and reconciliation

Every summary cell opens to contributing transactions (folio items, payments, work orders) with scope preserved and class-A masking applied. A report that cannot drill down is incomplete by definition. Daily reports reconcile to control accounts; discrepancies appear as exceptions, not as adjusted report figures.

## 6. Scheduling and delivery

- Schedules: definition, parameters, scope, recipients, time (business-date aware), format (screen/print/export), failure alerting.
- Recipients receive only what their scope permits; external recipients require explicit configuration and class-A prohibition (SEC-MODEL §10).
- Delivery failures surface on the schedule panel and to the owner; a failed delivery is never silent.
- Close-window reports (manager flash, day pack) are auto-generated at close and distributed per property configuration.

## 7. Exports and extracts

Exports are permission-scoped, logged, and carry provenance (as-of, certification, scope, definition version). Class-A exports require approval. Bulk extracts for BI derive from closed, reconciled data only.

## 8. Performance and retention

- Interactive report budget: daily/detail reports < 5 s; large historical reports < 60 s; nightly pack generation within the close window (NFR document).
- Read models can be rebuilt; report instances (generated outputs) are retained per financial retention policy for audit reproduction (ADR-003).
- Analytical extracts partition by business date and property; retention per data architecture §9.

## 9. Test obligations

| Obligation | Gate |
|---|---|
| Every report reconciles to source and drills down | Report acceptance |
| Scope tests: no cross-property/company figures, counts, or export leakage | Every release |
| As-of/certification labelling correct after close, reopen, restatement | Close/reopen tests |
| Scheduled delivery failure visibility | Notification tests |
| Definition versioning: changing a definition versions reports without restating history | Governance tests |
| Performance budgets under production-scale synthetic data | Performance phase |

## 10. Open items

| Item | Owner |
|---|---|
| OQ-011 (closed) invoice/statement formats | Finance |
| OQ-036 (closed) statistical defaults (comp/house) — BR-RPT-003 | Product Owner / Hotel Ops |
| OQ-017 (closed) group reporting depth for pilot | Hotel Ops |
| OQ-024 retention for report instances | Security / Legal |
| OQ-026 (closed) hosting region (data residency for extracts) | Product Owner |

## 11. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial reporting architecture issued with WP 0.7 | PROPOSED |