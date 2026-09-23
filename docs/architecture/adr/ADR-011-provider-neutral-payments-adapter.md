---
doc-id: ADR-011
title: Provider-neutral payments adapter
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Integration Architect (drafted); Finance Controller (review; OQ-002 open); Product Owner (approval)
applies-to: full enterprise target; provider selection remains open
depends-on: [ADR-010, ADR-005, ADR-007, FIN-ARCH, SEC-MODEL]
supersedes: rules on DP-ADR-004
---

# ADR-011: Provider-neutral payments adapter

## Context

DP-ADR-004 proposed a provider-neutral Payments adapter with the pilot provider selected by an evidence matrix; it remained open. The accepted pilot payment scope is cash, POS terminal, bank transfer and cheque, with **no card data stored** (BR-PAY-002/007) and one proven provider path only if required (BR-MVP-005). The financial architecture already defines a card clearing account and settlement-state discipline (SM-PAYMENT, BR-CSH-004).

## Problem

What contract must any payment provider satisfy, so the pilot can run with manual references today and add an acquirer or gateway later without redesigning payments?

## Target-state requirement

- Card data (PAN/CVV) is never captured, stored or logged by SuiteFlow (BR-PAY-007).
- Payment settlement state reflects reality; no provider assertion is treated as settlement without acquirer/bank evidence (BR-CSH-004).
- Provider-specific logic is isolated; multiple providers can coexist per property and method.
- Uncertain outcomes are resolvable; duplicate captures are detectable.
- Webhooks and status queries are authenticated, deduplicated and reconciled.
- The design works with **no provider integration at all** (manual reference capture) as the pilot baseline.

## Options

| Option | Summary | Assessment |
|---|---|---|
| A. Direct provider integration in payment flows | Call the provider inline from payment code | Rejected: provider specifics leak into domain; one provider becomes architectural |
| B. Manual references only, no adapter contract | Record terminal slips and match manually | Necessary fallback, insufficient as the only design: blocks future automation and standardisation |
| C. Provider-neutral contract with adapters; manual path as the degenerate provider | Define capture/refund/status/settlement/webhook contract; adapters implement it; manual entry implements the same contract | **Chosen** |

## Decision

1. **Domain contract.** The payments domain defines a provider-neutral contract: payment capture (by reference or token), refund, status query, settlement/batch ingestion, webhook consumption, and capability declaration (which methods/features a provider supports). Provider differences are capability flags, not branching domain code.
2. **No card data.** SuiteFlow stores provider references, terminal IDs, last-4 where lawfully permitted for guest service, and settlement metadata — never PAN, CVV, or track data. Tokenisation, where used, belongs to the provider.
3. **Manual provider is first-class.** The pilot's manual reference path (recording POS terminal slips, transfers and cheques with references and evidence) implements the same contract, so switching to an automated provider changes the adapter, not the workflow.
4. **State fidelity.** `PENDING_CLEARANCE` remains until acquirer/bank evidence matches (BR-CSH-004); the adapter provides the evidence or the exception path.
5. **Webhooks and polling.** Inbound webhooks are verified (signature/credential), deduplicated by namespaced reference (ADR-004), retried with backoff, and reconciled; uncertain states are resolved by status query or by reconciliation case (ADR-010 §4/5). Polling is a supported alternative where a provider lacks webhooks.
6. **Provider selection remains the intended open decision.** The pilot evidence matrix (Programme P1/P3) evaluates UBA/Wema roles (OQ-004), terminal/acquirer settlement reports (OQ-006), statement access (OQ-007) and any online payment-link need (OQ-005 closed). Until selection completes, manual references plus bank reconciliation are the accepted scope (BR-MVP-005).
7. **Multiple providers.** Configuration binds provider × property × method; adapters are versioned, feature-flagged and withdrawable without touching payment records.
8. **Disputes.** Chargebacks and disputes flow through DisputeCase regardless of provider, with evidence held in SuiteFlow and outcome postings additive (FIN-ARCH §5.6, SM-PAYMENT #7).

## Reasoning

- Making the manual path implement the same contract prevents the classic "we'll integrate later" migration trap: the domain never learns the provider's shape.
- Capability flags keep partial providers (for example settlement-file-only) honest instead of forcing lowest-common-denominator workflows.
- The contract is testable without any provider: manual provider + simulated adapter satisfy the same conformance suite.

## Trade-offs

- An abstraction layer costs some provider-specific convenience; accepted to preserve replaceability.
- Settlement ingestion quality varies by provider; reconciliation workload is real and staffed (OQ-002).
- Manual baseline means more human reconciliation at pilot; mitigated by matching tooling (WF-FIN-002).

## Risks

| Risk | Mitigation |
|---|---|
| Provider lacks status query or idempotency | Interface terms require it, or reconciliation mitigations become contractual (ADR-010 review trigger) |
| Card data creeping into logs or notes | Field-level prohibitions, redaction tests, code review rules |
| Settlement file formats undocumented | Evidence matrix gates provider commitment (OQ-006) |
| Manual reconciliation errors | Two-person review on unmatched items; ageing report; income-audit controls (BR-INA-004/005) |

## Consequences

- Integration architecture defines the adapters catalogue entry with the conformance suite.
- Payment UI states per method (BR-CSH-004) are provider-independent.
- Test obligations: conformance suite against the manual provider and at least one simulated adapter; webhook verification/dedupe/replay; settlement matching; refunds and chargebacks.

## Implementation impact

No immediate change. The existing implementation's payments path is evidence for Programme P2.

## Migration impact

Any historical payment records migrate with references preserved; settlement states recompute only from evidence, never by assumption.

## Review trigger

- Provider selection completed (OQ-004/005/006) — bind the adapter and re-validate.
- Regulatory change to card handling or payment-service rules.
- A provider requiring capture of card data directly in SuiteFlow (rejected by this ADR; escalate).

## Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial decision issued with WP 0.7; rules on DP-ADR-004 | PROPOSED |
