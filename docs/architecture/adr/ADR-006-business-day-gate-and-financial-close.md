---
doc-id: ADR-006
title: Business-day gate and financial close
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Financial Systems Architect (drafted); Finance Controller and Hotel Operations (approval; roles open)
applies-to: full enterprise target
depends-on: [ARCH-STATES, ARCH-RULES, ADR-005]
supersedes: rules on DP-ADR-002
---

# ADR-006: Business-day gate and financial close

## Context

DP-ADR-002 proposed a Hotel Business Day approval gate enforced around the night audit. The implementation-package contract later evolved toward stricter sequencing: income audit as an authoritative pre-advance gate, and a durable end-of-day record recoverable at intermediate states. Both are inputs to validate; the target must state its own contract.

## Problem

What gates must pass before a hotel's operating day advances, in what order, and how are failures and corrections governed?

## Target-state requirement

- A business day is the unit of financial placement and reporting (CAP-NAU-001).
- The day advances only when required controls pass or blocking exceptions are resolved with evidence (BR-NAU-001).
- The close is idempotent, resumable and failure-tolerant (INV-NAU-2).
- Corrections after close are additive, authorised and versioned (INV-NAU-4/5; ADR-003).
- The gate must reflect real hotel operations at 02:00–04:00 (OQ-008 closed: adopted default) with a real staffing model (RSK-OPS-002).

## Options

| Option | Summary | Assessment |
|---|---|---|
| A. Post-close audit only | Advance the day; audit findings handled next morning | Rejected as default: a false day can be advanced and used for decisions before verification; corrections then multiply |
| B. Separate close and certification controls with configurable ordering | Close advances on control-total reconciliation; income audit certifies within the operating cycle | Viable, and appropriate for properties with morning audit staffing |
| C. Certification before advance | Income audit certifies before the day can advance | **Chosen as pilot default**: strongest control; requires income-audit staffing in the small hours or an agreed morning close window |
| D. Manual committee approval | Human approval without system control totals | Rejected: theatre, not control |

## Decision

1. **Business-date discipline.** Every financial item carries the open business date; posting to a closed date is rejected; the day is the atomic unit of advancement.
2. **Close sequence (target):** pre-close validation → nightly postings → control-total reconciliation → income-audit certification → day advance → reports. Each step is idempotent and checkpointed; failures leave the day `CLOSING` and resumable.
3. **Blocking conditions** are exactly those in BR-NAU-001; every blocked condition is resolvable through a defined, authorised path (for example: pending departure, variance review, mapping gap, unresolved discrepancy) rather than by override of the control itself.
4. **Certification timing is property-configurable** with the same control result: the pilot default is certification before advance (option C), matching a control-first posture; a property with legitimate morning-only audit staffing may configure option B, in which case reporting for that day is explicitly marked "pending certification" until certified.
5. **Reopen and remediation.** A closed day reopens only under finance-controller authority with reason and impact assessment; corrections are posted on the remediation basis defined in BR-NAU-005; reclosed control totals are versioned, originals preserved; affected reports are reissued as new versions (BR-NAU-006).
6. **No partial advancement.** Partial close (some postings committed, day not advanced) is a recoverable state, never a publishable one: reports produced mid-close are marked provisional and unreleasable.
7. **Operational continuity.** A failed close never blocks guest operations: check-in, service and payments continue; only the day's financial advancement is held. Guest-facing and revenue systems must never freeze because an accounting step is degraded.

## Reasoning

- The control-first default prevents the most damaging failure class: an advanced, reported day that later proves wrong, with corrections spread across subsequent days.
- Configurability acknowledges staffing reality without weakening the invariant: the control (certification) always happens; only its position relative to advancement varies, and reports carry the status.
- Recoverability and no-partial-advancement make the gate trustworthy rather than brittle.
- Operational continuity protects the hotel from its own controls — the front desk does not stop because finance is degraded.

## Trade-offs

- Pre-advance certification can delay the close beyond the cutoff where income-audit staffing is thin; the mitigation is staffing/process design, not weaker control (RSK-OPS-002, OQ-008 (closed)).
- Configurability introduces two close profiles; mitigated by identical control requirements and report status labelling.
- Reopen governance adds workflow; accepted as the price of immutable history.

## Risks

| Risk | Mitigation |
|---|---|
| Close staffing cannot support pre-advance certification | Configurable timing (decision 4); OQ-008 (closed) resolution; training and rota design |
| Close run partially commits then fails repeatedly | Checkpointed idempotent runs; engineering escalation path (WF-NA-002) |
| Reopen used to avoid fixing root causes | Reopen metrics reported monthly to finance and audit |
| Business-date abuse (posting today into yesterday) | Posting window enforcement plus audit reporting of backdated volumes |

## Consequences

- Night-audit state machine and BR-NAU-001…007 stand as the contract; this ADR confirms their financial framing.
- Reports carry certification status; finance sign-off depends on it.
- Income-audit staffing is a launch gate (with finance/operations sign-off), not an implementation detail.

## Implementation impact

No immediate change. The implementation's stricter pre-advance posture aligns with the pilot default; Phase 2 verifies actual behaviour under load, and Phase 4 decides transitions.

## Migration impact

Historical day states and reopen events migrate with their evidence; no reconstruction of past approvals beyond what evidence exists.

## Review trigger

- Hotel cannot staff certification pre-advance and exercises option B.
- Regulatory requirements imposing specific close or certification timing.
- Repeated reopen events indicating a systemic control design problem.

## Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial decision issued with WP 0.4 | PROPOSED |
