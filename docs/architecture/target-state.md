---
doc-id: ARCH-TARGET
title: Target-State Architecture Overview
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Principal Architect (drafted); Product Owner (approval)
applies-to: full enterprise target
depends-on: [ADR-001, ADR-002, PROD-CAPMAP]
---

# Target-State Architecture Overview

## 1. Purpose

This document is the top-level view of the SuiteFlow target architecture: what the system is composed of, who owns what, and where the hard boundaries are. Details live in the linked documents; where this overview and a specialised document disagree, the specialised document wins for its subject and this overview is corrected.

Related documents: `adr/ADR-001` (topology and ownership), `adr/ADR-002` (tenancy and scope), `domain-model.md` and `data-model.md` (WP 0.2 pass 2), `state-machines.md` (WP 0.3), financial architecture (WP 0.4), security model (WP 0.5), UX architecture (WP 0.6), integration/reporting/NFR (WP 0.7).

## 2. System context

### 2.1 Actors

| Actor | Interacts via | Notes |
|---|---|---|
| Hotel staff (front desk, housekeeping, maintenance, finance, management, sales) | Operational consoles and mobile views | Scoped by property, role and authority limits |
| Property/group management | Dashboards, reports, governance surfaces | Tenant-scope visibility where authorized |
| External auditor | Read-only audit surfaces | Time-boxed, evidenced |
| Integrations (bank, acquirer, channel, messaging, locks, fiscal) | Adapters and webhooks | Machine identities, no interactive authority |
| Guests | Indirect at pilot (through staff); direct self-service in later phases | Guest-facing surfaces are a later-phase capability |
| AI assistants | Scoped read tools and proposal services | Never authoritative; all actions governed |

### 2.2 External systems (roles, not products)

| External role | Direction | Boundary |
|---|---|---|
| Accounting authority / financial books | Outbound postings, inbound balances and period state | Posting service; reconciliation links (WP 0.4) |
| Banks and acquirers | Outbound payment references; inbound settlement and statement data | Adapters + statement ingestion (INT-004/005) |
| Channels, OTAs, booking engines | Bidirectional availability/rates/reservations | Adapter with ARI contract (INT-002/003, AVL-008) |
| Outlet POS (external) | Bidirectional orders/tickets and settlement | Adapter (INT-006, POS-008) |
| Messaging providers (email, SMS, WhatsApp) | Outbound messages, inbound status | Adapter (INT-009) |
| Door locks, ID devices, kiosks, RMS, fiscal systems | Provider-specific | Adapters (INT-010…014) |

Products are bound to these roles only through Phase 3/4 evidence (ADR-001 decision 7). Nothing in this document presumes a specific product.

## 3. Layers and module map

### 3.1 Layers

```text
+--------------------------------------------------------------+
| Experience: consoles, boards, mobile views, back-office      |  no business rules
+--------------------------------------------------------------+
| Application services: commands / queries / proposals         |  validation, authorization, orchestration
+--------------------------------------------------------------+
| Operations Core            | Control Plane       | Accounting |
| operational source records | governance & derived| books      |
+--------------------------------------------------------------+
| Platform Services: identity, scope, config, audit, events,   |
| numbering, notifications, observability, recovery            |
+--------------------------------------------------------------+
| Infrastructure: database, queues, adapters, external systems |
+--------------------------------------------------------------+
```

### 3.2 Module map

| Module | Capability domains | Layer | Owns |
|---|---|---|---|
| Platform | PLT | Platform | Identity, scope, configuration, audit, events, numbering, notifications, observability |
| Property & Inventory | PM, AVL | Core | Properties, rooms, room types, features, zones, OOO/OOS, inventory allocations, availability projection |
| Rates | RTM | Core | Rate plans, rate calendar, restrictions, negotiated and derived rates |
| Reservations | RSV | Core | Reservation lifecycle, blocks linkage, guarantees, routing instructions, traces |
| Front Office | FO | Core | Arrivals, check-in, stays, moves, extensions, checkout, incidents, service recovery |
| Guests & Privacy | GST | Core | Guest identity, preferences, relationships, consent, retention |
| Groups, Corporate & Sales | GRP, CRP, SAL | Core | Group accounts and blocks, corporate accounts and credit, sales pipeline, events |
| Service Operations | HSK, MNT | Core | Housekeeping tasks and inspections, work orders, asset history |
| Guest Financials | FOL, CSH | Core | Folios, charges, payments, deposits, refunds, cashier sessions, payment settlement state |
| Outlets & Inventory | POS, INV | Core | Outlet sales, room posting, stock, recipes, counts |
| Night Audit | NAU | Control | Business date, pre-close validation, close execution, recovery, reopen control |
| Income Audit | INA | Control | Day verification, exception register, certification |
| Accounting Interface | ACC | Control + Accounting | Financial mapping, posting orchestration, reconciliation, accounting document links |
| Reporting & Analytics | RPT, BI | Control | Read models, report definitions, drill-down, analytical extracts |
| Integrations | INT | Control (adapters) | Provider contracts, retries, deduplication, reconciliation queues |
| AI | AI | Control | Assistants, proposals, tool authorization, evaluations |

### 3.3 Dependency rules (enforced by architectural tests)

1. Experience calls application services only. No direct database access; no business logic in views.
2. Application services coordinate modules; modules never call each other's internal storage.
3. The Operations Core never writes accounting documents; all financial effects pass through the posting service (Accounting Interface).
4. The Control Plane never duplicates operational source records; it references them by stable identity and stores evidence, decisions and derived state.
5. The Accounting Authority never writes operational records; corrections flow back as governed remediation references, not silent mutations.
6. Adapters are called only through provider-neutral domain interfaces defined by the consuming module.
7. Reporting and AI read through query/read-model interfaces; they never mutate.
8. No module bypasses Platform scope, authorization or audit.

## 4. Source-of-truth and ownership matrix

Rule: exactly one authoritative owner per datum; every other occurrence is a derived copy that must be rebuildable and reconciled. "Owner" here means the module/layer accountable for correctness.

| Datum / decision | Authoritative owner | Authoritative form | Derived copies |
|---|---|---|---|
| Property, building, room type, room, features | Property & Inventory | Operational records, effective-dated where applicable | — |
| OOO/OOS windows and maintenance holds | Property & Inventory (governed by Control for release) | Hold records | Availability projection |
| Inventory allocations (reservations, blocks, holds) | Property & Inventory | Allocation records | Availability projection |
| Availability ("what can I sell") | — (derived) | Never authoritative | Projection rebuilt from allocations and holds; must reconcile to source |
| Rate plan definitions, rate amounts, restrictions | Rates | Effective-dated records with full history | Published channel rates (adapter) |
| Applied rate and tax on a folio item | Guest Financials | Immutable item snapshot at posting | Reporting |
| Reservation and its lifecycle | Reservations | Reservation aggregate + transition evidence | Arrival lists |
| Stay, room occupancy, room readiness | Front Office / Service Operations | Stay and room-state records | Room boards, availability inputs |
| Guest profile, preferences, consent | Guests & Privacy | Profile records (tenant-scope); consent versions | CRM projection (consented subset only) |
| Folio, charges, payments, deposits, refunds | Guest Financials | Immutable transaction items; balance derived from items | Reporting, reconciliation |
| Cashier session, counted cash, variance | Guest Financials | Session records + count evidence | Accounting postings |
| Business date (open/closed state) | Night Audit (Control) | Business-day record per property | All daily reports |
| Policy versions (cancellation, credit, routing defaults, approval limits) | Platform configuration (governed by Control) | Effective-dated configuration records | Resolved policy per transaction, captured in evidence |
| Transition evidence for governed actions | Control Plane | Immutable evidence records | Audit views |
| Posting link and idempotency identity | Accounting Interface | Posting records | Reconciliation reports |
| Reconciliation state and exceptions | Accounting Interface / Income Audit | Reconciliation records | Management and audit views |
| GL entries, AR documents, tax documents, statutory books, period close | Accounting Authority | Accounting documents | Balances read back for reconciliation |
| Statutory/customer-facing invoice | **Open — OQ-011; to be ruled in WP 0.4** | — | — |
| Audit trail | Platform | Append-only audit store | Audit reporting |
| Outbox events | Platform | Transactional outbox rows | Consumer procesing state |
| Configuration (policies, timings, limits, numbering series) | Platform | Effective-dated, property/tenant-scoped | Resolved configuration snapshots |
| Read models / reports | Reporting (derived) | Rebuildable projections | Exports |
| AI proposals | AI (Control) | Non-authoritative proposal records | — (proposals expire; only governed actions persist) |

## 5. Boundary rules

### 5.1 Command and query separation

State changes happen exclusively through application-service commands that validate, authorize, execute and emit evidence. Reads happen through query surfaces that enforce scope. No "smart" client-side logic may substitute for server decisions (rates, availability, balances, taxes, permissions).

### 5.2 Event boundary

Events are facts about completed transactions, written to the transactional outbox in the same transaction as the state change. They carry stable identity, version, correlation and property scope. Consumers are idempotent. Events never replace the need for direct reconciliation of money.

### 5.3 Financial boundary

`Operational event → posting record (idempotency + links) → accounting document → reconciliation`. Revenue is recognised once, on the approved business date (subject to WP 0.4 confirmation). Reversals are new, linked documents; posted history is never edited.

### 5.4 Security boundary

Authorization, property/company scope and authority limits are enforced server-side at the application-service layer using platform services. Sensitive fields (identity documents, financial details) have explicit access rules. No report, export or AI tool bypasses scope.

### 5.5 Integration boundary

All external interaction passes through adapters that implement provider-neutral interfaces, with: authentication, timeouts, retries with backoff, idempotency keys, deduplication, dead-letter state, reconciliation and observability. Provider-specific data is confined to adapter-owned storage; the domain sees canonical models only.

### 5.6 AI boundary

AI reads through scoped query tools and produces proposals through proposal services. Execution of any state change requires the same authorization and validation as a human action. AI identities are service identities with explicit tool allow-lists and full audit.

## 6. Concurrency and transaction principles

| Principle | Applies to |
|---|---|
| One writer per aggregate at a time; conflicting writers fail deterministically with a retry-safe error | Folios, reservations, stays, blocks, cashier sessions |
| Inventory consumption is transactional and uniqueness-constrained; double-selling fails rather than double-books | Reservations, blocks, holds |
| Every externally-triggered or financially effective command requires an idempotency identity | Payments, postings, integrations, close |
| Business date is asserted at transaction time; posting to a closed date is rejected | All financial effects |
| Long operations (close, migration, bulk posting) are resumable, checkpointed and idempotent | Night audit, imports, channel sync |
| Read models may lag; they must display their as-of point and reconcile to source | All reporting |

Detailed strategy per aggregate is defined with the state machines (WP 0.3); financial concurrency is finalised in WP 0.4.

## 7. Deployment topology principles

Detail belongs to WP 0.7; the principles established here:

1. One deployment per tenant (ADR-002), promoted as a versioned artefact; never rebuilt per customer.
2. Environments: development, test/UAT, production — with production-like data volume for performance and recovery evidence.
3. Recovery: continuous database recovery and file/config replication consistent with 1-hour RPO/RTO; restored workers must not duplicate external or accounting effects (BR-REL-007).
4. Observability: every governed transition, integration attempt and financial posting is traceable end-to-end by correlation identity.
5. No direct production modifications; all change flows through versioned migration and release process.

## 8. Validation outcomes recorded by this work package

| Proposed input | Outcome | Ruling |
|---|---|---|
| DP-CTX-001 (one isolated site per hotel) | **MODIFIED** | Tenant is the deployment unit; properties are first-class partitions with enforced scope; optional dedicated-property deployment remains available (ADR-002) |
| DP-CTX-002 (Kamra operations / ERPNext accounting / control plane) | **ROLES CONFIRMED, PRODUCT BINDING DEFERRED** | The three-layer ownership model is adopted (ADR-001); which products fill the roles is a Phase 3/4 evidence decision |
| DP-CTX-003 (revenue recognised once; settlements as balance-sheet movements; guest-ledger control account) | **DEFERRED to WP 0.4** | Financial architecture validation is the subject of WP 0.4 |

## 9. Open items carried forward

- WP 0.2 pass 2: domain model, data model, temporal and identity ADRs.
- WP 0.4: financial ownership, invoice/document authority (OQ-011), deposit and tax treatment (OQ-012, OQ-021).
- WP 0.5: security model including scope enforcement design and role matrix.
- WP 0.7: deployment/recovery design, integration contracts, NFRs.

## 10. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial target-state overview issued with WP 0.2 pass 1 | PROPOSED |
