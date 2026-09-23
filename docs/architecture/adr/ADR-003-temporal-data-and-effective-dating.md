---
doc-id: ADR-003
title: Temporal data and effective-dating strategy
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Data Architect (drafted); Technical Lead (review); Product Owner (approval)
applies-to: full enterprise target
depends-on: [ARCH-DOMAIN, ADR-001, ADR-002]
---

# ADR-003: Temporal data and effective-dating strategy

## Context

Hotels face questions that only temporal discipline can answer: What rate applied to a stay booked in March and modified in May? Which tax rule governed a night in a month when the rule changed mid-month? What did this room's status look like when the discrepancy was reported? What did the folio look like before the disputed post-stay correction? The current implementation experiences the failure mode this ADR prevents: ambiguous authority over "the truth at a point in time".

The domain model (WP 0.2 pass 2) distinguishes four data classes: immutable transaction records, effective-dated configuration, mutable operational state with history, and derived read models. This ADR fixes how time is represented for each.

## Problem

Which temporal strategy applies to which class of data, so that historical reconstruction is always possible, corrections never falsify history, and performance remains acceptable at 200-room and chain scale?

## Target-state requirement

- Any historical document (folio statement, night audit report, rate sheet, tax computation) must be reproducible exactly as issued.
- Configuration changes take effect by date, never by rewriting the past.
- Financial corrections are additive and linked; posted history is immutable (INV-FOL-2, INV-ACC-4).
- The business date, not the wall clock, governs financial placement (CAP-NAU-001).
- Derived data is rebuildable to any as-of point required for audit (CAP-RPT-012).

## Options

| Option | Summary | Assessment |
|---|---|---|
| A. In-place mutation with audit log | Current-value columns edited directly; history inferred from audit entries | Rejected: reconstruction depends on audit completeness and cannot support exact statement reproduction |
| B. Full bitemporal modelling everywhere | Valid time + transaction time on all tables | Rejected: heavy complexity and cost for data that never needs retroactive validity analysis; over-engineering for the class of data that is simply immutable |
| C. Classified temporal strategies | Distinct, explicit strategy per data class, enforced by convention tests | **Chosen** |
| D. Event sourcing for all state | All state derived from an event log | Rejected as universal strategy: high operational cost; retained selectively via the outbox for integration, not as the primary read model |

## Decision

Four temporal strategies, applied by data class:

| Class | Strategy | Examples |
|---|---|---|
| **1. Immutable transaction records** | Append-only. Written once with business date and actor; corrections create new linked records. No updates, no deletes. | FolioItem, Payment, Refund, StockMovement, AuditEvent, OutboxEvent, transition evidence, RegistrationAcceptance, ConsentRecord, AccountingDocumentLink |
| **2. Effective-dated configuration** | Versioned with `effective_from` / `effective_to`, `recorded_at`, actor and approval. Changes supersede; they never edit a past version. Resolution is deterministic for any point in time. | RatePlan terms, RateAmount, RateRestriction, TaxRule, ServiceChargeRule, AuthorityLimit, BillingInstruction, ConfigurationItem, RoomType assignment |
| **3. Mutable operational state with history** | Current state is mutable (subject to legality), but every transition is recorded immutably with actor, reason and timestamp. Historical questions are answered from the transition records (state machines, WP 0.3). | Reservation, Stay, Room operational state, HousekeepingTask, WorkOrder, Account, BusinessDay, GroupBlock, Payment settlement status * |
| **4. Derived and read models** | Rebuildable from classes 1–3. Carry an as-of marker and a reconciliation obligation to source. Never authoritative. | Availability projection, room boards, reporting models, dashboards, analytical extracts |

\* Payment is class 1 for its financial lines and class 3 for its settlement *status*; the status history is recorded as a transition log over the immutable lines.

Additional rules:

1. **Business-date stamping.** Every financial and operational event carries the property business date under which it occurred. Business dates are asserted against the BusinessDay aggregate (rejected if not open, except governed remediation).
2. **Applied-basis snapshots.** Transactions that depend on configuration (rate, tax, routing, policy) capture the resolved version identifiers in their evidence, so a historical transaction is explainable without reconstructing config history.
3. **Corrections are additive.** Void, allowance, reversal and remediation records link to their originals. The original is never changed.
4. **No retroactive configuration edits.** A correction to a past effective period creates a new version with a recorded decision; the original version remains for history.
5. **As-of reconstruction test.** Any historical document must be reproducible by the rebuild procedure as an acceptance obligation (test obligations in WP 0.7/0.8).
6. **Bitemporal exception.** Where a jurisdiction or contract requires transaction-time history on configuration (for example, proving exactly which tax version was visible to an operator at a moment), `recorded_at` plus approval evidence satisfies the need; full bitemporal tables are not adopted unless a specific obligation demands them.

## Reasoning

- The classification matches how hotels actually dispute history: "what did we charge and why" (class 1 + snapshots), "what were the rules then" (class 2), "what state was it in" (class 3), "what does the report show" (class 4).
- Selective rigor avoids the operational cost of universal bitemporality while exceeding the audit expectations of hotel finance.
- Snapshotting resolved bases at transaction time delivers explainability even if configuration history were lost; combined with class 2, it gives two independent reconstruction paths.

## Trade-offs

- Storage growth from immutable history; mitigated by partitioning by business date/property and archival policy (data-model.md).
- Discipline required: developers must not "fix" history in place. Enforced by convention tests, immutability validation and audit review.
- As-of queries against class 2 require careful indexing (`entity, effective_from, effective_to`) to avoid slow historical scans.
- Snapshot storage duplicates some configuration data; accepted for explainability.

## Risks

| Risk | Mitigation |
|---|---|
| In-place edits creeping in through bulk operations | Immutability validation rules; reconciliation detects unexplained changes |
| Effective-date overlaps or gaps | Deterministic resolution rules + validation at creation; resolution failures are surfaced, never guessed |
| Performance of historical reconstruction | Read models + indexed access patterns; documented rebuild procedure tested in UAT |
| Snapshot drift (evidence claims a version that never existed) | Snapshot references verified at write time against the effective configuration |

## Consequences

- The data model document adopts these four classes explicitly per entity (WP 0.2 pass 2).
- State machines (WP 0.3) define which transitions are lawful for class 3 entities and what evidence each records.
- Reporting (WP 0.7) must express as-of semantics and reconcile to source.
- Any new entity added to the model must be classified into exactly one temporal class at design time; an unclassified entity fails architecture review.

## Implementation impact

No immediate implementation change. The current implementation already exhibits partial versions of these patterns; Phase 2 will classify what exists, and Phase 4 decides per domain.

## Migration impact

- Historical migrations must preserve business dates, applied bases and evidence; migration scripts classify data into the four classes and validate counts, balances and reconstruction samples.
- Migration must not "tidy" historical values; discrepancies are recorded as reconciliation cases rather than silently corrected.

## Review trigger

- A statutory or audit obligation requiring full bitemporal records.
- Evidence that as-of reconstruction cannot meet audit needs with the chosen strategy.
- Performance evidence that the classified strategy is insufficient at chain scale.

## Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial decision issued with WP 0.2 pass 2 | PROPOSED |
