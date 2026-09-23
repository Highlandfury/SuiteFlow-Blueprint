---
doc-id: ADR-007
title: Deposits, prepayments and forfeiture accounting
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Financial Systems Architect (drafted); Finance Controller (approval; OQ-002 open)
applies-to: full enterprise target
depends-on: [ARCH-DOMAIN, ADR-003, ADR-005, ARCH-RULES]
supersedes: rules on DP-ADR-005
---

# ADR-007: Deposits, prepayments and forfeiture accounting

## Context

Guest deposits are the source of several classic hotel accounting defects: deposits recognised as revenue on receipt, applied twice, refunded beyond what was received, forfeited without basis, or lost track of when a reservation never materialises. DP-ADR-005 proposed that deposits remain liabilities until application, refund or approved forfeiture, with the tax-point decision left open. That open point must be closed (or defaulted) for the target.

## Problem

How are deposits and prepayments recorded, applied, refunded, forfeited, taxed and reconciled so that the liability is always provable and revenue is never recognised early?

## Target-state requirement

- Deposit liability conservation at all times: received = applied + refunded + forfeited + remaining (INV-FOL-7).
- No revenue recognised on receipt; revenue on application follows the underlying service recognition (ADR-005); revenue on forfeiture is an explicit, authorised event.
- Refunds only from cleared funds, with method fidelity and authority (BR-FOL-012).
- Partial application, partial refund and partial forfeiture are normal and exactly tracked.
- The deposit liability control account reconciles daily (BR-ACC-004).
- Tax treatment is deterministic and configuration-driven, pending tax advice (OQ-021 closed, OQ-029).

## Options

| Option | Summary | Assessment |
|---|---|---|
| A. Deposit recognised as revenue on receipt | Simplest operational flow | Rejected: violates recognition principle; creates refund/chargeback exposure against recognised revenue; distorts period reporting |
| B. Deposit as unallocated customer credit | Balance sheet liability without obligation linkage | Rejected: reconciliation to reservations becomes impossible; application mistakes undetectable |
| C. Deposit as liability linked to a specific obligation, with lifecycle states | Received → held → applied/refunded/forfeited, per reservation or group, with conservation and control account | **Chosen** |

## Decision

1. **Deposit is a liability with an owning obligation.** Every deposit references the reservation or group agreement that entitled it. Unlinked receipts are exceptions requiring finance resolution, not silent credits.
2. **Application is settlement, not revenue.** Applying a deposit reduces the folio balance and transfers between liability and guest ledger control within the daily posting (ADR-005).
3. **Forfeiture is a recognition event** posted to a dedicated **cancellation/no-show revenue** account — never silently into room revenue — preserving room statistics. Forfeiture requires policy basis, authority and recorded guest communication (BR-FOL-009).
4. **Partial lifecycle operations** (partial application, partial refund, partial forfeiture) follow the policy calculation recorded at booking; operator judgement is not a calculation method (BR-FOL-011).
5. **Tax defaults (pending tax advice):**
   - VAT is **not** charged on deposit receipt (it is not consideration for a supply yet).
   - On application, tax follows the underlying supply's tax treatment for its business dates.
   - On forfeiture, the default assumption is that the amount **is taxable consideration** for the cancellation right, taxed at the applicable rate for the cancelled service; where tax advice concludes otherwise, the mapping changes by configuration with effect from the advice date. **STATUS: UNVERIFIED — tax adviser confirmation required (OQ-021 closed/OQ-029).**
6. **Refunds** are executed only from cleared funds, ideally by the original method; bank-detail changes require enhanced verification (BR-FOL-012).
7. **Unclaimed deposits** age under finance monitoring; treatment of genuinely unclaimed funds (escheatment, retention, or write-back) follows jurisdictional advice — **UNVERIFIED**, flagged for Phase 1 research.
8. **Deposit liability reporting** is a standard daily and month-end output: opening balance, receipts, applications, refunds, forfeitures, closing balance, reconciled to the control account.

## Reasoning

- The obligation-linked liability model is the only one that makes conservation provable and application mistakes detectable.
- Dedicated forfeiture revenue keeps room-statistics integrity (ADR/RevPAR unaffected by cancellations) and gives finance visibility into cancellation income.
- Defaulting the tax position explicitly — rather than leaving it silent — lets design continue while ensuring the tax adviser's ruling is a tracked deliverable, not an accident.
- Partial-operation exactness matches how hotels actually work during cancellation disputes.

## Trade-offs

- More state and evidence than a simple "customer credit": accepted; this is precisely where money is disputed.
- Dedicated forfeiture account adds mapping configuration; accepted for statistics quality.
- Tax default may need remapping if advice differs; mitigated by effective-dated mapping configuration (ADR-003).

## Risks

| Risk | Mitigation |
|---|---|
| Deposit applied to the wrong folio/obligation | Application targets the linked obligation by default; cross-obligation requires authority and evidence |
| Forfeiture without basis becoming routine | Authority limits, communication evidence, daily reporting (BR-INA-005) |
| Unapplied deposits ageing silently | Ageing report with thresholds; finance ownership |
| Tax treatment wrong on forfeiture/receipt | UNVERIFIED flags; tax adviser ruling is a launch gate for invoicing flows |

## Consequences

- Deposit entities, states and evidence stand as per ARCH-DOMAIN/SM-DEPOSIT/BR-FOL-008…012.
- Finance owns deposit ageing review; operations own application discipline at close.
- Test obligations: conservation identity under partial operations; no revenue on receipt; forfeiture authority; tax mapping snapshot; control-account reconciliation.

## Implementation impact

No immediate change. Phase 2 verifies current deposit behaviour; Phase 4 decides transition.

## Migration impact

Legacy deposit balances migrate as liabilities with obligation links where recoverable; unlinked legacy balances are a finance-owned reconciliation case, never silently written off.

## Review trigger

- Tax adviser's ruling on deposit/forfeiture tax points.
- Evidence of unclaimed/lapsed deposits requiring policy.
- Finance objection to dedicated forfeiture account mapping.

## Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial decision issued with WP 0.4 | PROPOSED |
