---
doc-id: ADR-004
title: Identity, numbering and reference strategy
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Data Architect (drafted); Technical Lead (review); Product Owner (approval)
applies-to: full enterprise target
depends-on: [ARCH-DOMAIN, ADR-002, ADR-003]
---

# ADR-004: Identity, numbering and reference strategy

## Context

Hotel systems fail in specific, predictable ways when identity is modelled casually: room numbers reused and demerged from history; reservation "numbers" recycled; external references (channel, bank, acquirer) stored in free-text fields; guest records merged destructively; searches relying on mutable attributes. The domain model references entities across contexts, which requires identity that never changes meaning. Human-facing numbers must still exist because hotels operate on them (arrival lists, folio numbers, invoice series).

## Problem

How are entities identified internally, how are human-facing numbers allocated, and how are external references attached, so that identity is stable, search is effective, and statutory or contractual numbering obligations can be met?

## Target-state requirement

- References between contexts and systems are stable for the life of the record, and never reused or recycled.
- Human-facing numbers are unique, searchable, controllable per property and never reassigned (CAP-PLT-005).
- External system references are traceable and deduplicated per provider (CAP-INT-001, CAP-PLT-010).
- Merges (guests, accounts) preserve resolution from every historical reference (INV-GST-1).
- Legal numbering obligations (invoices, vouchers, fiscal series) can be met without redesign (OQ-011 closed/OQ-029 open).
- Room numbers, names, emails and phone numbers may change freely without breaking anything.

## Options

| Option | Summary | Assessment |
|---|---|---|
| A. Natural keys | Primary identity derived from business attributes (room number, reservation code, email) | Rejected: mutable attributes cannot be identity; renumbering and re-use break history |
| B. Database sequential integers only | Auto-increment identities, no separate human numbering | Rejected: leaks volume information, cannot express per-property series, merging/collapsing and restore complicate stability |
| C. Opaque internal identity + separate human numbers + namespaced external references | Two-layer identity model | **Chosen** |

## Decision

### 1. Internal identity

- Every entity has an opaque, immutable internal identifier, unique within the tenant, generated at creation, never derived from business data, never reused.
- Identifiers are globally unique in practice (UUID-style) to allow safe data movement, imports and future consolidation without collisions.
- Cross-context references use only internal identity, never human numbers or natural attributes.
- Identity is never encoded with meaning (no "property prefix + type + sequence" as primary key); where display convenience suggests prefixes, they are attributes of the human number, not the identity.

### 2. Human-facing numbers

- Human numbers (reservation, folio, cashier session, work order, invoice, voucher, event) are attributes with the same stability rules, allocated from **configured, property-scoped number series**.
- Series behaviour is configurable: prefix/suffix, width, reset rules (annual, financial period, never), and gap policy.
- Where law requires gapless or sequential series (candidate: invoices, fiscal documents — OQ-011 (closed)/OQ-029), the series supports gapless allocation with transactional reservation and audit of any gap. Series deployment is finalised in WP 0.4.
- Numbers are unique per series and never reassigned, even if a record is voided or cancelled.
- Numbers are displayed, printed, searchable and quotable — never used as foreign keys.

### 3. External references

- References to external systems (channel confirmation, bank reference, acquirer batch, provider message, lock system, fiscal ID) are modelled as **namespaced external reference records**: provider namespace + provider-assigned identifier + entity reference + validity/metadata.
- Uniqueness: `(provider, provider_identifier)` is unique within the tenant for idempotent ingestion (webhook deduplication, CAP-PLT-010).
- References are never free-text fields on domain entities; a domain entity may carry a convenience attribute only where the domain genuinely owns the value (e.g. folio's own number).
- Provider namespaces are registered configuration, not ad-hoc strings.

### 4. Party identity and merges

- Guests and commercial accounts follow the same internal-identity rules.
- A merge establishes an explicit survivor, retains the folded identities as aliases, and provides deterministic alias resolution for every historical reference (INV-GST-1/2). Destructive merges are prohibited.
- Watchlist, credit and consent data follow the survivor according to explicit merge rules reviewed with Finance/Security (WP 0.5).

### 5. Identity of physical assets

- Rooms, tables and equipment are entities; their human labels (room number, table code) are mutable attributes with history. Renumbering is an event, not a new identity.
- A room reclassified or renumbered keeps its identity and therefore its complete maintenance and occupancy history.

### 6. Search

- Business-key search (name, phone, email, document number, room number, arrival date) is served by indexed, normalized search surfaces over the identity-preserving attributes, not by making those attributes identity.
- Search must respect scope (ADR-002) and privacy rules (CAP-GST-003/012).

## Reasoning

- Two-layer identity is the only model that survives renumbering, merges, imports, restores and multi-property adoption without semantic breakage.
- Series as configuration satisfies both simple operational numbering and strict statutory regimes without schema change.
- Namespaced external references make idempotent integration and reconciliation tractable, especially across acquirers and channels.
- The merged-alias pattern is what makes non-destructive guest merges real rather than aspirational.

## Trade-offs

- Slightly more complex lookups (identity vs number) and occasional confusion in support conversations; mitigated by consistent display and search.
- Opaque identifiers are less human-debuggable than sequences; mitigated by correlation metadata and rich audit.
- Gapless series under failure require transactional care and can be slower; only applied where law requires.

## Risks

| Risk | Mitigation |
|---|---|
| Teams treating human numbers as keys anyway | Architectural tests and code review rules; cross-context references validated by schema |
| Series resets or gaps violating statute | Series configuration reviewed with Finance; gap audit; release gate for statutory series (WP 0.4) |
| External reference collisions across providers | Namespace + uniqueness constraints; ingestion dedupe tests |
| Merge rules losing financial or watchlist history | Explicit merge policy reviewed by Finance/Security; alias resolution tested with historical transactions |

## Consequences

- Data model (WP 0.2 pass 2) defines identity, number and external-reference columns per entity class.
- Integration architecture (WP 0.7) standardises external reference handling.
- Financial architecture (WP 0.4) finalises statutory series requirements for invoices/vouchers.
- Test obligations: uniqueness, reuse prohibition, rename history, merge alias resolution, webhook deduplication.

## Implementation impact

No immediate change. Phase 2 classifies current identity practices; Phase 4 decides migration per domain.

## Migration impact

- Migration must generate internal identities for legacy records and map legacy numbers into series without reuse.
- Legacy free-text external references are parsed into namespaced reference records where recoverable; unrecoverable cases are recorded as data-quality exceptions, not invented.

## Review trigger

- Statutory numbering requirements (OQ-011 closed, OQ-029) demanding series semantics beyond this decision.
- Multi-property consolidation requirements that expose identity collisions.
- Evidence of identity instability under restore or consolidation.

## Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial decision issued with WP 0.2 pass 2 | PROPOSED |
