---
doc-id: FIN-ARCH
title: Target Financial Architecture
status: PROPOSED
version: 0.4
date: 2026-09-23
owner: Financial Systems Architect (drafted); Finance Controller (approval; OQ-002 open)
applies-to: full enterprise target
depends-on: [ADR-005, ADR-006, ADR-007, ADR-008, ARCH-DOMAIN, ARCH-RULES]
---

# Target Financial Architecture

## 1. Purpose

This document defines the target financial model end to end: who owns which record, how money and revenue move through the system, which accounts control what, how each payment method settles, how tax is treated, how everything reconciles daily, and how failures are contained. It is the specification that accounting-boundary ADRs (ADR-005…008) rule on and that implementation tickets and test obligations will trace to.

The chain, restated precisely:

```text
Operational event (services, charges, payments, deposits)
    ↓  (subledger of record: SuiteFlow Guest Financials)
Folio items with responsibility, tax and applied-basis evidence
    ↓  (daily business-day posting: ADR-005)
Control accounts and books (book of record: Accounting Authority)
    ↓  (daily reconciliation suite: §10)
Proven agreement, or an owned exception
```

## 2. Books of record and ownership

| Record | Subledger owner (SuiteFlow) | Book of record |
|---|---|---|
| Folio, folio items, responsibility decisions | ✔ | — |
| Payments and their settlement states | ✔ | — |
| Deposits and lifecycle | ✔ | — |
| Cashier sessions, counts, variances | ✔ | — |
| AR origination (transfer events) | ✔ (event) | AR documents and balances |
| Control accounts (guest ledger, deposits, clearing, AR, tax, service charge payable) | expectation computed daily | balances held |
| Revenue accounts and recognition journals | posting intent and evidence | journals |
| Tax liabilities and statutory documents | tax computation evidence | liabilities and documents |
| Period close and statutory statements | — | ✔ |

Neither side duplicates the other's authority (ADR-005 §1). SuiteFlow never writes journals directly; the Accounting Authority never alters operational records.

## 3. Posting families and account mapping

Every posting run aggregates by **family**; every family maps to configured accounts, tax treatment and dimensions. An unmapped family **blocks the close** (ADR-005 §7).

| # | Posting family | Typical debit | Typical credit |
|---|---|---|---|
| 1 | Room revenue | Guest Ledger Control | Room Revenue (+ Tax Payable, + Service Charge Payable where applicable) |
| 2 | F&B and other outlet revenue | Guest Ledger Control / Outlet settlement paths | F&B Revenue / Other Revenue (+ tax, service charge) |
| 3 | Other operating revenue (laundry, transport, misc) | Guest Ledger Control | Other Revenue (+ tax) |
| 4 | Allowances, discounts, comps | Contra-revenue accounts | Guest Ledger Control |
| 5 | Payments by method (cash, POS, transfer, cheque) | Method clearing accounts | Guest Ledger Control |
| 6 | Payment clearing transfers | Bank / Cash in transit | Method clearing accounts |
| 7 | Deposit receipts | Bank / Cash | Deposit Liability |
| 8 | Deposit applications | Deposit Liability | Guest Ledger Control |
| 9 | Deposit refunds | Deposit Liability | Bank / Cash |
| 10 | Cancellation/no-show charges and deposit forfeitures | Guest Ledger Control (folio-path charge) / Deposit Liability (direct forfeiture) | Cancellation/No-show Revenue (+ tax per ADR-007 §5) |
| 11 | Refunds (overpayment/service failure) | Guest Ledger Control | Original instrument clearing account (Bank only for direct refunds) |
| 12 | Direct bill transfers (zero revenue) | AR Control | Guest Ledger Control |
| 13 | AR settlements | Bank | AR Control |
| 14 | Cash over/short | Cash Over/Short (Dr short / Cr over) | Cashier session clearing |
| 15 | Bank fees, acquirer fees | Fee expense | Bank / Card Clearing |
| 16 | Commissions (agent) | Commission expense / payable | Commission Payable / AR |
| 17 | Service-charge distribution / remittance | Service Charge Payable | Bank (on remittance evidence; distribution computation outside SuiteFlow per OQ-023) |
| 18 | Chargebacks | Reclaimed funds (original method clearing) | AR Control / Guest Ledger Control as applicable (fees per family 15) |

Account names are illustrative; the chart of accounts and transaction-code mapping are Finance-owned configuration (CAP-ACC-001), effective-dated under ADR-003.

## 4. Posting model

| Aspect | Rule |
|---|---|
| Cadence | One posting run per property per business day (ADR-005 §2); individual documents only where an external party requires one |
| Identity | `(property, business_date, run_type)` durable identity; retries resolve to the original outcome (BR-ACC-002) |
| Contents | All families with activity that day; completeness check by family, not by presence |
| Evidence | Every aggregated line drills down to contributing items; every item records its posting link once committed |
| Idempotency | Re-execution produces no duplicates; duplicate detection is part of the run, not left to inspection |
| Reversal | Corrections by linked reversal/new posting on the appropriate date (BR-ACC-005) |
| Failure | Run failure leaves the day `CLOSING`, resumable from checkpoint; no partial publishing (ADR-006 §6) |
| Cross-day items | Items dated to other open days post in their own day's run; nothing is silently re-dated |

## 5. Money flows (worked)

### 5.1 Room revenue at night close

Guest in-house, rate ₦100,000/night with 7.5% VAT applied by configuration:

```text
Dr Guest Ledger Control       107,500
    Cr Room Revenue                          100,000
    Cr Tax Payable                             7,500
```

Revenue recognised once for the night (ADR-005 §3); statistics unaffected by settlement method.

### 5.2 Payment by method

| Method | On receipt | On settlement |
|---|---|---|
| Cash | `Dr Cash on Hand / Cr Guest Ledger Control` | At session close/drop: `Dr Cash in Transit / Cr Cash on Hand`; at bank: `Dr Bank / Cr Cash in Transit` |
| POS card | `Dr Card Clearing / Cr Guest Ledger Control` | On acquirer batch: `Dr Bank / Dr Card Fees / Cr Card Clearing` |
| Bank transfer | `Dr Transfer Clearing / Cr Guest Ledger Control` | On bank confirmation: `Dr Bank / Cr Transfer Clearing` |
| Cheque | `Dr Cheque Clearing / Cr Guest Ledger Control` | On bank value: `Dr Bank / Cr Cheque Clearing`; dishonour reverses into guest/AR exposure |

Settlement state changes only on evidence (BR-CSH-004); "authorization" is never settlement.

### 5.3 Deposit lifecycle

```text
Receipt:    Dr Bank / Cr Deposit Liability
Apply:      Dr Deposit Liability / Cr Guest Ledger Control
Refund:     Dr Deposit Liability / Cr Bank
Forfeit:    Dr Deposit Liability / Cr Cancellation/No-show Revenue (+ tax per ADR-007)
```

Conservation proven continuously (INV-FOL-7); partial operations tracked exactly.

### 5.4 Direct bill (zero-revenue transfer)

```text
Transfer:   Dr AR Control / Cr Guest Ledger Control
Settlement: Dr Bank / Cr AR Control
```

Revenue was recognised on its business dates; the transfer is purely a credit movement (ADR-005 §5). Credit eligibility checked before transfer (BR-CRP-001); one AR document per transfer.

### 5.5 Comp and allowance

Comp at value with offsetting allowance in the same journal (§3 families 1 and 4):

```text
Revenue at value:   Dr Guest Ledger Control / Cr Room Revenue
Allowance:          Dr Comp/Allowance (contra) / Cr Guest Ledger Control
Net folio balance:  zero; revenue and cost both visible
```

### 5.6 No-show and cancellation (one penalty, one recognition)

**Rule.** For any one night, at most one revenue recognition occurs. A no-show determination completes before the nightly posting run and **suppresses the room charge** for that reservation's night. The penalty posts once — either charged to the folio (family 10, debit Guest Ledger Control) or satisfied from the deposit — never both. Where a deposit exists, it is **applied** to the penalty first (settlement, family 8); only a policy-computed excess is forfeited, and only if the policy says it is non-refundable. Application and forfeiture are mutually exclusive for the same amount, and a folio charge and a forfeiture are mutually exclusive for the same amount. Cancellation penalties follow the same rule. Room-revenue statistics stay clean.

**Worked example — guaranteed no-show, first night ₦40,000 + 7.5% VAT, deposit ₦43,000 (illustrative; tax position UNVERIFIED pending OQ-029):**

```text
1. No-show determined before the room-charge run → the night's room charge is suppressed.
2. Penalty charged (family 10):  Dr Guest Ledger Control         43,000
                                Cr Cancellation/No-show Revenue  40,000
                                Cr Tax Payable                    3,000
3. Deposit applied (family 8):  Dr Deposit Liability            43,000
                                Cr Guest Ledger Control           43,000
Total revenue recognised: ₦40,000 once. No room revenue for the night. Deposit liability zero.
```

Partial deposit: the application reduces the folio balance; the remaining balance is settled or transferred per policy. Deposit exceeding a refundable penalty: refund per policy, otherwise the non-refundable remainder is forfeited — never also charged.

### 5.7 Refund of guest overpayment

`Dr Guest Ledger Control / Cr Bank` with refund authority (BR-FOL-012); refunds never exceed cleared funds.

### 5.8 Cash variance

```text
Short: Dr Cash Over/Short / Cr Cash on Hand
Over:  Dr Cash on Hand / Cr Cash Over/Short
```

Variance approval and accounting treatment recorded (BR-CSH-003).

**Service-charge ordering (illustrative; tax treatment UNVERIFIED pending OQ-029).** Room charge ₦100,000; service charge 10% = ₦10,000 collected as Service Charge Payable (liability). Default tax assumption: the room charge is the taxable base and the service charge is excluded — to be confirmed by the adviser; the mapping is effective-dated. Journals: Dr Guest Ledger Control 110,000 (+ tax) / Cr Room Revenue 100,000, Cr Service Charge Payable 10,000, Cr Tax Payable per rate.

## 6. Payment methods and clearing

| Method | Clearing account | Settlement evidence | Failure handling |
|---|---|---|---|
| Cash | Cash on Hand → Cash in Transit | Session count, drop, bank deposit | Variance workflow (BR-CSH-003); cash-in-transit ageing |
| POS card | Card Clearing (per acquirer) | Acquirer batch/settlement report matching | Unmatched batch case; duplicate capture detection by reference |
| Bank transfer | Transfer Clearing (per bank) | Bank statement/confirmation | Unverified transfers remain pending; re-verification workflow |
| Cheque | Cheque Clearing | Bank value confirmation | Dishonour reverses and restores exposure; credit review triggered (BR-CRP-002) |

Each clearing account ages visibly; unmatched items beyond policy windows are finance-owned exceptions (BR-INT-003).

## 7. Tax architecture

| Element | Treatment | Status |
|---|---|---|
| Tax engine | Effective-dated rules per tax type: rate, basis, applicability (service categories), exemptions, rounding, computation order | REQUIRED |
| VAT | Applied per configured rate on applicable services; tax payable liability; **tax point is a per-tax-type configuration attribute (delivery / invoice / payment / receipt), default service delivery/business date** | Rate and treatment **UNVERIFIED** for the pilot jurisdiction pending Programme P1 primary-source research and tax advice (service-charge answer OQ-021 closed; tax advice OQ-029 open) |
| Consumption/levy taxes | Modelled as additional configurable tax types where applicable | **UNVERIFIED** applicability to this property |
| Service charge | Default: collected as a liability for distribution (not revenue), distinct from tax; distribution paid only on remittance evidence and reconciled monthly | OQ-021 (closed; GOV-ANSWERS §4.2); distribution evidence and monthly reconciliation per §10 |
| Withholding tax | Out of scope for guest operations at pilot; relevant to commissions/payables where applicable | **UNVERIFIED** |
| Fiscalisation | If required, an accredited provider becomes an adapter (INT-014); documents derive from SuiteFlow's operative document (ADR-008) | **UNVERIFIED** (OQ-029) |
| Tax evidence | Every taxed item stores the rule version used (INV-FOL-5); tax reports reconcile collected vs posted liability daily | REQUIRED |

No tax rule enters implementation without a cited statutory source and Finance/tax-adviser sign-off (RSK-FIN-004).

**Penalty and forfeiture computation (FIN-02 resolution).** Guest-facing penalties and forfeitures are quoted **tax-inclusive** by default: tax is extracted as `amount × rate ÷ (1 + rate)` (for example, at 7.5%: ₦43,000 → ₦40,000 revenue + ₦3,000 tax), never added on top of the received amount. The extraction rule and mapping are effective-dated configuration, **UNVERIFIED** until the tax adviser rules on OQ-029; a ruling that changes the basis is applied by configuration with effect from its date.

## 8. Deposits

As per ADR-007: obligation-linked liability, conservation invariant, dedicated forfeiture revenue, partial operations exact, tax defaults flagged. **Aged/unclaimed deposits** are reported monthly for finance review; no write-back, expiry or escheatment runs without a counsel-confirmed, effective-dated rule (FIN-10) — until then they remain liabilities.

## 9. Direct bill and receivables

- Origination: transfer of a settled folio balance to an eligible account; blocks on credit breach without exception (BR-CRP-001/002).
- AR documents: one per transfer, idempotent; ageing buckets per agreed terms; statements from AR.
- Collections: overdue thresholds trigger holds and review (BR-CRP-005); suspension affects future transfers only.
- Write-offs: authority per limits, reported, historical receivables retained (BR-CRP-006).
- Agent commissions: accrue per agreement; payable on cleared settlement; reversal follows payment state (BR-CRP-004).

## 10. Reconciliation suite (daily)

Every item below runs daily; an **unexplained difference blocks the stage it protects (ADR-006 §3)** — day certification/advance for control-total checks, period close for any open case. A known, policy-defined difference produces a ReconciliationCase with owner, due date and escalation. The close gate consumes this suite (BR-NAU-001).

| # | Reconciliation | Left side | Right side | Tolerance |
|---|---|---|---|---|
| 1 | Guest ledger | Σ open folio balances (SuiteFlow) | Guest Ledger Control (books) | zero |
| 2 | Deposits | Σ held deposits by state | Deposit Liability (books) | zero |
| 3 | Payments by method | Σ payments by method/settlement state | Clearing accounts (books) | zero |
| 4 | Cash | Sessions + drops + counts | Cash on Hand / Cash in Transit | zero |
| 5 | AR | Σ open AR documents | AR Control (books) | zero |
| 6 | Tax | Σ tax lines by type/business date | Tax Payable movement | zero |
| 7 | Revenue | Operational revenue reports by family/day | Posted revenue by family/date | zero |
| 8 | Posting completeness | All families present with mapped accounts | Run logs | no missing/unmapped |
| 9 | Link integrity | Every item has ≤1 posting link; every posting has source items | Posting records | no orphans either direction |
| 10 | Cashier sessions | All sessions closed or explicitly excepted | Session records | as per policy |
| 11 | Document series | Issued numbers gapless where required; no reuse | Series configuration | as per series policy |
| 12 | Acquirer settlement | Terminal batch totals by acquirer/terminal | Acquirer settlement report (batch, fees, net credit) | zero after fees; unmatched batches aged |
| 13 | Bank statement | Bank statement entries (credits/debits) | Cash deposits + POS settlement credits + transfer/cheque clearing expected | zero unexplained |
| 14 | Service-charge payable *(monthly)* | Σ collected-not-distributed by period | Service Charge Payable control | zero |

Chargebacks are matched against the original method clearing within check 3; disputed amounts follow the failure model (§12).

**Number-series semantics (TEC-12 resolution).** Where law or policy requires gapless numbering: allocation is transactional with a single allocator per series (unique constraint), gaps are detected by check #11 and audited, reset policy is configured (never mid-period unless statutory), and void/reissue preserves the original number with a linked void record. Fiscal-series particulars and gapless requirements are **UNVERIFIED pending OQ-029**; semantics are effective-dated configuration, tested by TO-ACC-005.

## 11. Close and period close

- Business-day close per ADR-006; control totals frozen at close, restatements versioned.
- Period (month) close: after daily closes, finance reviews the suite above, resolves or owns exceptions, freezes the period; reopen follows day-reopen governance with stronger authority (BR-ACC-007).
- Statutory reporting runs from the frozen period.

## 12. Failure model (mandate §37)

| Failure | Control that prevents it | Detection | Recovery |
|---|---|---|---|
| Double posting | Durable posting identity; unique constraints; duplicate detection in run | §10.9 link integrity; duplicate query | Reverse the duplicate link/post with evidence; root-cause fix |
| Missing posting | Family completeness check; mapping completeness blocks close | §10.7/10.8 | Run resumes; missing family posts in its business day |
| Orphan transaction | Links written atomically; FK integrity | §10.9 both directions | Resolve link or reverse the orphan with evidence; never delete |
| Wrong tax | Effective-dated rules; per-item snapshots; Finance-owned mappings | §10.6; tax report review | Corrective posting with authority; tax adviser informed |
| Wrong balance | Folio invariant at every write; continuous checks | §10.1; balance tests | Correct by additive item with authority; investigate cause class |
| Incorrect refund | Cleared-funds limit; authority bands; method fidelity | §10.3; refund report | Recovery process if funds overpaid; fraud review if indicated |
| Incorrect allocation | Payment/allocation tracking; deposit conservation | §10.2/10.3 | Re-allocation with approval; evidence retained |
| Duplicate payment (external) | Idempotent ingestion by namespaced reference | §10.3; webhook dedupe tests | Return/reverse with acquirer/bank; exception documented |
| Double-recognised no-show/cancellation penalty | No-show suppression before the room-charge run; one-penalty-one-recognition rule; application/forfeiture exclusivity | §10.7 family check; TO-FOL-006 | Reverse the erroneous posting with authority; restate statistics if published |
| Failed reconciliation | Daily run with owners and ageing; unexplained differences block per ADR-006 §3 | §10 dashboard | Case resolution before day certification and before period close |

## 13. Financial reporting outputs

| Output | Source | Frequency |
|---|---|---|
| Daily revenue and manager's report | Frozen close totals | Daily |
| Trial balance extract / GL summary | Accounting Authority | Daily (on demand) |
| Tax report (collected vs liability) | §10.6 | Daily |
| Cash and banking report | §10.4 | Daily |
| Deposit liability and ageing | §10.2 | Daily |
| AR ageing and statements | AR | Daily / monthly |
| Allowances, comps, voids by user | Income audit | Daily |
| Reconciliation dashboard | §10 | Daily |
| Close report with certification status | ADR-006 | Daily |
| Document series audit | §10.11 | Monthly |

## 14. Multi-property and multi-entity

- Books are kept per property (per entity as configured); no inter-property postings at pilot.
- Group consolidation reads closed property results; it never re-owns them (ADR-002 §5).
- Shared tenant-level configuration (tax rules, account mapping templates) can be standardised with per-property overrides, effective-dated.

## 15. Open decisions and evidence requirements

| Item | Blocks | Owner |
|---|---|---|
| OQ-011 closed — invoice authority settled (operative document per ADR-008) | Invoicing implementation per ADR-008; Finance Controller confirmation at acceptance | Finance Controller |
| OQ-012 (closed) deposit/cancellation/no-show policy | Rule ACCEPTED status (defaults in force) | Product Owner / Finance |
| OQ-021 (closed) service charge and tax treatment | Tax configuration finalisation | Finance / tax adviser |
| OQ-029 fiscalisation requirements | Invoicing/integration scope | Legal / Finance |
| OQ-001 legal entity and tax identifiers | Document templates, statutory reporting | Product Owner |
| Nigeria tax primary-source research | Tax rule implementation (RSK-FIN-004) | Finance / Programme P1 research |
| Opening-balance migration evidence | Go-live (D9) | Finance / migration |
| Golden-day financial acceptance fixture | D7 exit gate | QA / Finance |

## 16. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial financial architecture issued with WP 0.4; validates DP-CTX-003, DP-ADR-001/002/005 via ADR-005…008 | PROPOSED |
| 0.2 | 2026-09-23 | P0 resolutions: no-show single-recognition rule + worked example (§5.6; FIN-01); tax-inclusive penalty/forfeiture computation (§7; FIN-02); closing/blocking semantics aligned to ADR-006 §3 (§10/§12; FIN-04) | PROPOSED |
| 0.3 | 2026-09-23 | P1 resolutions: bank/acquirer reconciliation checks 12–13 (§10; FIN-03); configurable tax point (§7; FIN-12) | PROPOSED |
| 0.4 | 2026-09-23 | P2 resolutions: service-charge lifecycle and charging example (FIN-09), refund instrument fidelity and chargebacks (FIN-14), series semantics (TEC-12) | PROPOSED |
