---
doc-id: ARCH-DATA
title: Target Data Architecture
status: PROPOSED
version: 0.2
date: 2026-09-23
owner: Data Architect (drafted); Product Owner (approval)
applies-to: full enterprise target
depends-on: [ARCH-DOMAIN, ADR-002, ADR-003, ADR-004]
---

# Target Data Architecture

## 1. Purpose

This document turns the domain model into data-architecture rules: what is stored, how it is identified, how time and history are represented, how integrity and concurrency are protected, how performance is achieved, and how data is retained, archived, migrated and (where required) erased. Physical schema design happens during implementation; these rules are the acceptance constraints it must satisfy.

## 2. Principles

1. **Single authoritative owner per datum** (target-state §4). Copies are derived, rebuildable and reconciled.
2. **Opaque identity, separate human numbers, namespaced external references** (ADR-004).
3. **Four temporal classes, exactly one per entity** (ADR-003): immutable transaction, effective-dated configuration, mutable-with-history, derived/read model.
4. **Business date is a first-class column** on every financial and operational event; no reliance on wall-clock timestamps for financial placement.
5. **Property and company scope are first-class dimensions** on every operational and financial record except genuinely tenant-global data (ADR-002), which is explicitly modelled.
6. **Integrity is enforced by the database where it is cheap and by governed services where semantics require it.** Unique constraints defend invariants; services enforce policy.
7. **No hard deletes** of financial, audit or evidence records. Operational cancellation is a state. Erasure exists only as governed privacy action under retention law (CAP-GST-009).
8. **Optimistic concurrency with explicit version stamps** on mutable aggregates; conflicting writers receive a deterministic, retry-safe conflict.
9. **Read models are separate and rebuildable**, with as-of semantics and reconciliation to source.
10. **Volume assumptions are stated and tested**, not silently assumed.
11. **Migration is restartable, checkpointed, dry-run-able and reconciled.**
12. **Every entity is classified** at design time into a temporal class, an owner, and a scope; unclassified entities fail architecture review.

## 3. Scope and dimensions

| Dimension | Applies to | Notes |
|---|---|---|
| Tenant | Everything (implicit: one database per tenant, ADR-002) | Not a column to filter within a deployment; isolation is physical at deployment level |
| Property | All operational and financial records | Explicit column with indexed access paths; genuinely tenant-global records (e.g. shared profiles) carry scope semantics instead |
| Company / legal entity | Financial records and configuration | Enables per-entity books and reporting within one property or across properties |
| Business date | All financial events, operational events, reports | Asserted against the BusinessDay aggregate; used for partitioning and reporting |
| Currency | Folios, payments, deposits, accounting documents | One currency per folio; multi-currency is deferred (BR-MVP-012) |

## 4. Temporal classes in storage

| Class | Storage rules | Index implications |
|---|---|---|
| Immutable transaction | Append-only tables; no UPDATE/DELETE grants in application paths; correction links | Partition candidates by business date / property; indexes on source reference and business date |
| Effective-dated configuration | Version rows with `effective_from`, `effective_to`, `recorded_at`, actor and approval; resolution indexes `(scope, entity, effective_from)`; overlap/gap validation | Point-in-time resolution must be an indexed lookup, not a scan |
| Mutable with history | Current-state row with version stamp + immutable transition log (actor, reason, before/after) | Current-state indexes for operations; transition log indexed by entity and occurred-at |
| Derived / read model | Rebuildable projections with as-of marker and source reconciliation | Optimised for read patterns; never a write target of operational code |

## 5. Identity and reference rules (condensed from ADR-004)

- Internal identity: opaque, immutable, tenant-unique, never reused; all cross-context references use it.
- Human numbers: property-scoped configurable series; unique per series; never reassigned; display/search only.
- External references: namespaced records with uniqueness on (provider namespace, provider identifier) within tenant.
- Party merges: alias resolution, never destructive; all historical references remain resolvable.

## 6. Integrity constraints and concurrency

| Constraint class | Examples | Enforcement |
|---|---|---|
| Uniqueness | One active stay per room; no duplicate allocation for the same room-night/source; (provider, reference) unique; series numbers unique; idempotency key unique per command scope | Database unique constraints (partial where "active" semantics apply) plus service checks |
| Referential integrity | All cross-context references resolve; no orphans on status transitions | Foreign keys to internal identities; deletion prohibited (soft state) |
| Balance identities | Folio balance identity (INV-FOL-1); deposit conservation (INV-FOL-7); control totals frozen at close (INV-NAU-5) | Service-enforced with continuous reconciliation checks and daily verification |
| Temporal validity | Config resolution always yields exactly one version; no overlapping OOO windows; no items outside open business dates | Validation at write + scheduled integrity audits |
| Concurrency | Version stamp on mutable aggregates; deterministic conflict error; retry-safe semantics | Optimistic concurrency; service retry guidance |
| Idempotency | Externally-triggered and financially-effective commands carry a durable idempotency identity (ADR-003, CAP-PLT-007) | Unique constraint on identity; outcome replay returns the original result |

## 7. Performance and indexing patterns

Volume assumptions (ASSUMED, to be validated with hotel data in Programme P2/D7): one 200-room property produces roughly 70–80k room-nights, 1–3 million folio items, and comparable audit/outbox volumes per year. Chain deployments multiply by property count. Per-property deployments below one million items per year are operationally small; the design must nevertheless survive chain scale without redesign.

| Access pattern | Required shape |
|---|---|
| Arrivals / departures / in-house by property and business date | `(property, business_date, state)` with covering indexes |
| Availability and allocation queries by date range and room type | `(property, room_type, date)` on allocations; range-friendly ordering |
| Rate resolution by plan, room type and date | Effective-dated resolution index; snapshot lookup for posted items |
| Open folio lookup by room, guest, reservation, company | Multiple indexed references, scoped by property |
| Folio statement and drill-down | `(folio_id, business_date, sequence)` |
| AR ageing and exposure | `(company, status, due_date)` |
| Close and reconciliation runs | `(property, business_date, control)` |
| Audit and evidence drill-down | `(correlation_id)`, `(entity_type, entity_id, occurred_at)` |
| Outbox dispatch and retry | Separate dispatch/attempt rows (claim, visibility deadline, outcome, next_attempt_at); the event row itself stays immutable |
| Search (name, phone, email, document number) | Normalized, indexed search surfaces, scope-aware |

Partitioning/archival candidates: folio items, audit events, outbox/inbox records, stock movements, rate amounts — partitioned by property and business date where volume justifies.

## 8. Read models, reporting and analytics

- Reporting reads from projections, never from operational tables under load; every projection states its as-of point (ADR-003 class 4).
- Every report reconciles to source and supports drill-down to the authoritative transaction (CAP-RPT-012).
- Analytical extracts for BI (D15) are derived from closed, reconciled data; analytical figures never redefine operational truth.
- Rebuild procedure: a documented, tested routine that reconstructs each projection from source classes 1–3; its success is an acceptance obligation.

## 9. Retention, archival and privacy

Retention periods are **open** (OQ-024) and must be set with counsel before production; the architecture supports per-category configuration from day one:

| Category | Direction (to confirm) | Mechanism |
|---|---|---|
| Guest identity documents | Short operational retention with lawful basis | Restricted access, encryption, scheduled review/erasure with legal-hold check |
| Financial records (folios, payments, tax documents) | Long statutory retention (jurisdiction-dependent) | Immutable storage; archival by business date; never deleted before retention expiry |
| Audit and evidence | Long retention consistent with audit needs | Append-only store, partitioned, archival to cold storage |
| Operational non-financial records | Moderate retention | Configurable ageing and summarisation |
| Consent and communication | Purpose-bound retention, withdrawal-aware | Consent versioning, suppression kept even after other data is minimised |
| Derived/read models | Disposable | Rebuildable; no retention obligation |

Archival preserves scope dimensions, identity and reconstructability (an archived record must still be resolvable to its human number and evidence).

**Retention defaults pending counsel** are the OQ-024 recommended schedule (ID images 90 days; profiles 6 years; financial records 7 years; AML 5 years; audit/security logs 12 months+; CCTV 30 days), per-category configurable. **Lifecycle breadth (SEC-07/SEC-08 resolutions):** backup/PITR generations inherit retention and access rules; deletion is evidenced and covers derived copies; legal holds suspend deletion; erasure tests cover caches and projections.

## 10. Migration principles

1. Restartable and checkpointed; partial progress is a normal state, never a corrupt one.
2. Dry-run against production-like volumes with reconciliation reports before execution.
3. Classify every migrated dataset into its temporal class; never "tidy" history.
4. Map legacy identifiers to internal identities and register human numbers into series without reuse.
5. Reconcile counts, balances, deposit conservation and control totals post-import; discrepancies become reconciliation cases, not silent corrections.
6. No destructive step without backup, restore point and verified rollback (charter §15; RSK-MIG-001).

## 11. Prohibited anti-patterns

- Direct SQL writes to financial documents or ledger records from application or reporting code.
- Editing or deleting immutable records; deleting audit or outbox rows.
- Using human numbers or natural attributes as cross-context references.
- Caching a balance as an independent source of truth (balances are derived from items and reconciled).
- Unclassified entities, unowned data, or a second copy of a datum without a rebuild-and-reconcile rule.
- Reporting queries that bypass scope enforcement (ADR-002).
- Schema changes outside the versioned migration framework.

## 12. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial data architecture issued with WP 0.2 pass 2 | PROPOSED |
| 0.2 | 2026-09-23 | P1 resolutions: outbox dispatch state (TEC-03); retention defaults and lifecycle breadth incl. backups/caches/erasure (SEC-07/08) | PROPOSED |
