---
doc-id: ADR-005
title: Revenue recognition, ledger ownership and the accounting boundary
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Financial Systems Architect (drafted); Finance Controller (approval; OQ-002 open)
applies-to: full enterprise target
depends-on: [ARCH-DOMAIN, ADR-001, ADR-003, ARCH-STATES, ARCH-RULES]
supersedes: rules on DP-CTX-003 and DP-ADR-001
---

# ADR-005: Revenue recognition, ledger ownership and the accounting boundary

## Context

The programme must decide where operational financial records end and accounting begins, when revenue is recognised, and how the two worlds exchange effects without duplication or gaps. The proposed input DP-CTX-003 stated: revenue recognised once during the approved business-day posting; settlements as balance-sheet movements; a guest-ledger control account connecting the folio subledger to the books. DP-ADR-001 proposed a daily guest-ledger control posting plus a zero-revenue direct-bill transfer. Both are proposed inputs requiring validation against the target architecture.

## Problem

Which system owns which financial record, at what cadence are accounting effects posted, and what guarantees prevent duplicate, missing or orphan financial effects?

## Target-state requirement

- Revenue is recognised once, on the correct business day, to the correct accounts and tax treatment (BR-ACC-001/006).
- Every balance reconciles: guest ledger, deposits, payments by method, taxes, AR (BR-ACC-004).
- Every posting is traceable from operational source to accounting document and back (CAP-RPT-012).
- Duplicate posting, missed posting and orphan transactions are P0 defects by definition (mandate §37).
- Postings are idempotent and recoverable across retries and restarts (INV-ACC-1/2).
- Financial history is immutable; corrections are additive (ADR-003, INV-ACC-4).

## Options

| Option | Summary | Assessment |
|---|---|---|
| A. Per-transaction real-time GL posting | Every folio item creates an accounting document immediately | Rejected as the default: multiples of document volume, intraday noise, more failure surface, and hotel finance operates daily; retained for cases that require an individual external document |
| B. Daily aggregated postings by family with full per-transaction links | One daily journal per property per business day, aggregated by posting family; SuiteFlow retains item-level evidence and drill-down | **Chosen**, with refinements |
| C. No control posting; reconcile subledger to GL periodically | Periodic reconciliation of unaudited aggregates | Rejected: fails reconciliation frequency and control-account discipline |

## Decision

1. **Ledger ownership.** SuiteFlow's Guest Financials context is the **subledger of record** for guest balances, folio items, payments, deposits and cashier sessions. The Accounting Authority is the **book of record** for general ledger, AR ledger, tax liabilities, statutory documents and period close. Neither duplicates the other's authority.
2. **Posting cadence.** A **daily posting run per property per business day** creates aggregated journals by posting family (see financial architecture §4), dated to that business day. Individual accounting documents are created only where an external party requires one (corporate/agent invoices, write-offs, statutory documents).
3. **Revenue recognition.** Revenue is recognised **once**, when the service is delivered, through the daily business-day posting: room revenue night by night; outlet revenue on service; other revenue on delivery. Settlement (cash, POS, transfer, cheque) and deposits are **balance-sheet movements**, never revenue.
4. **Guest-ledger control.** The books carry a Guest Ledger Control account; its balance must equal the sum of open folio balances at all times at day level (`BR-ACC-004`). SuiteFlow computes the expectation; the Accounting Authority holds the balance; reconciliation proves agreement.
5. **Direct bill.** A City Ledger Transfer moves a settled balance from Guest Ledger Control to AR Control with **zero revenue effect** — revenue was already recognised on its business dates. The transfer produces exactly one AR document (idempotent, INV-FOL-9).
6. **Allowances, comps and discounts.** Allowances and discounts post as contra-revenue against the revenue account they reduce; **comps post at value with an offsetting contra-revenue allowance in the same journal**, preserving statistical integrity (occupancy/ADR include comps) and making the cost visible rather than invisible (default per OQ-036 (closed); BR-FOL-005).
7. **Mapping completeness.** Every posting family maps to configured accounts, tax and cost-centre dimensions. An unmapped family **blocks the close** rather than posting to a suspense account.
8. **Idempotency and drill-down.** Each posting job carries a durable identity `(property, business_date, run_type)`; re-execution resolves to the original outcome. Every aggregated figure drills down to the contributing items; every item's evidence records its posting link once committed.
9. **Reversals.** Correcting a posted journal is done by a linked reversal/new posting on the appropriate business date; posted journals are never edited (BR-ACC-005).

## Reasoning

- Hotels are daily businesses: revenue is reported and reviewed by business day; operators and finance share that rhythm. Daily aggregated postings match the control reality (daily reconciliation) and keep document volume proportionate.
- Aggregation with item-level links delivers both: finance-grade control accounts and hotel-grade drill-down, without requiring real-time GL.
- Zero-revenue AR transfer preserves the accounting truth that direct billing is a credit decision, not a revenue event.
- Comp-at-value treatment prevents the classic distortion where comps silently vanish from ADR/occupancy and hide the cost of service recovery and VIP programmes.
- Mapping-completeness blocking prevents the "suspense account" failure that makes hotel P&Ls unexplainable.

## Trade-offs

- GL is not current intraday; the operational view (SuiteFlow) is. Accepted: the property manages intraday from operations, not from the ledger.
- The daily run is a concentration point; mitigated by idempotent, resumable close (INV-NAU-2) and alerting.
- Aggregation reduces per-transaction journal auditability in the GL; recovered through drill-down links and SuiteFlow evidence, which audit can follow.
- Contra-revenue comp treatment slightly increases journal complexity; accepted for statistical honesty.

## Risks

| Risk | Mitigation |
|---|---|
| Duplicate posting across retry/close-recovery | Durable posting identity + unique constraint + reconciliation (BR-ACC-002) |
| Missed posting (family silently absent) | Mapping completeness check blocks close; daily reconciliation reports by family |
| Wrong tax treatment | Effective-dated tax rules with per-item snapshots (INV-FOL-5); Finance review of tax mappings |
| Direct-bill transfer creating revenue | Zero-revenue transfer rule with tests asserting no revenue lines |
| Posting link drift (item claims a link it doesn't have) | Link written atomically with posting; reconciliation detects orphans both directions |

## Consequences

- Financial architecture document defines posting families, control accounts, reconciliation suite and failure model (WP 0.4).
- Accounting Interface entities (SUITE PostingRecord, AccountingDocumentLink, ReconciliationCase) become load-bearing; their integrity is P0.
- Finance owns the account-mapping configuration; SuiteFlow enforces it completely.
- Test obligations: revenue recognised exactly once; transfer zero-revenue; comp at value; mapping completeness blocks close; retry idempotency; drill-down reconciliation.

## Implementation impact

No immediate implementation change. The current implementation's accounting adapter and control-document patterns are evidence for Programme P2; Programme P4 decides transition per domain.

## Migration impact

- Opening balances must be migrated with reconciliation to zero difference across all control accounts before go-live.
- If current books carry historical mispostings, remediation is additive on an open accounting date (ADR-003) with documented evidence.

## Review trigger

- Finance-controller objection to daily cadence or comp-at-value treatment.
- Statutory requirements (OQ-011 closed/OQ-029) demanding per-transaction statutory documents.
- Evidence that posting-family aggregation cannot support required statutory reporting.

## Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial decision issued with WP 0.4 | PROPOSED |
