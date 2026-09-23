---
doc-id: GOV-SIGNOFF-FIN
title: Finance Sign-off Pack — Programme P0 Acceptance Sitting
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Interim Finance Controller capacity (role assistant, GOV-APPOINT); for the human appointee
applies-to: P0 acceptance sitting (≤14 Oct 2026)
depends-on: [GOV-APPOINT, GOV-SITTING, GOV-REVIEW]
---

# Finance Sign-off Pack — Programme P0 Acceptance Sitting

**Prepared by:** interim Finance Controller capacity (role assistant; GOV-APPOINT v0.2) · 23 Sep 2026 · **Review conclusion for the human appointee — not a sign-off.**

## 1. Documents reviewed

`finance/financial-architecture.md` v0.5; ADR-005/006/007/008; `architecture/business-rules.md` v0.6 (FOL/CSH/CRP/NAU/INA/ACC); `security/role-and-authority-matrix.md` v0.5 §3–§5; `qa/strategy.md` v0.6; `product/roadmap.md` v1.3 (First-Property Deployment Gate); `industry-standard-answers.md` v0.5; `open-questions.md` v0.7; `review-findings-2026-09-23.md` v0.4 (all 45 findings resolved); `interim-appointments-2026-09-23.md` v0.2; `p0-sitting-pack.md`.

## 2. Conclusions

The financial architecture is **coherent and close to signing-ready, but not unconditionally signable**. Core controls agree across documents: revenue recognised once per business day; settlements and deposits as balance-sheet movements; obligation-linked deposits with conservation; one-penalty-one-recognition with tax-inclusive forfeiture extraction (worked example balances: ₦43,000 → ₦40,000 + ₦3,000); one close-blocking table (ADR-006 §3); a 14-check reconciliation suite (13 daily + 1 monthly); SoD rules and PROPOSED authority defaults.

Freeze-clean items identified in this review **have been corrected** (answers-pack version/history; reconciliation cadence wording; forfeiture-deviation authority wording; register/QA ID misalignments).

Decisively outstanding: no appointed human Finance Controller (OQ-002); the interim capacity expires at the sitting; tax advice (OQ-029) is absent; charter §12 requires the Technical Lead on the financial architecture (OQ-033 pending).

## 3. Conditions outstanding

1. **OQ-002** — appoint Finance Controller and deputy (owner: Product Owner; due: at or before the sitting). Interim capacity expires there (≤14 Oct 2026).
2. **FC signature** on the consolidated refund/payout/waiver table and authority-limit defaults (owner: human FC; due: on appointment).
3. **OQ-029 tax advice** — VAT rate/tax points, forfeiture tax treatment, service-charge tax base, fiscalisation (owner: tax adviser; due: before invoicing flows, per ADR-008 §8).
4. **Pack-freeze zero-mismatch check** at the sitting (owner: document owners).
5. **Golden-day fixture financial cases** — no-show with/without deposit, forfeiture tax, bank/acquirer mismatch, session variance, partial deposit lifecycle; zero unexplained difference (owner: QA + Finance; due: D7 exit).
6. **Technical Lead sign-off** on the financial architecture (owner: TL appointee; due: on appointment).

## 4. Residual risks

- **RSK-FIN-004:** all Nigerian tax/legal positions remain UNVERIFIED until advice lands.
- **RSK-FIN-003:** bank/acquirer reconciliation is manual/simulated; zero-unexplained-difference untested against real statements.
- **RSK-FIN-001 / RSK-PROD-003:** synthetic acceptance proves logic, not hotel reality; coverage depends on the golden-day fixture.
- **Acting-capacity lapse:** without OQ-002, financial approvals fall back to the Product Owner — a governance gap, not professional finance oversight.

## 5. Proposed sign-off wording (for the human appointee to adopt or amend)

> I have reviewed the financial architecture, the accounting-boundary ADRs, the finance business rules, the role and authority matrix, the QA strategy and the roadmap's First-Property Deployment Gate. I am satisfied that the financial architecture is coherent and fit as the acceptance baseline, subject to the conditions above; all tax-related positions remain UNVERIFIED until tax advice (OQ-029) is received. Signed: __________ , Finance Controller, date __________.

## 6. Status note

This is an AI role conclusion prepared under interim appointment (GOV-APPOINT v0.2) — **not a human sign-off**; it carries no professional liability and does not constitute charter §12 approval.

## 7. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Finance sign-off pack prepared by the interim role capacity for the P0 sitting | PROPOSED |
