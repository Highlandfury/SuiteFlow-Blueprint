---
doc-id: OPS-SUPPORT
title: 24×7 Support Model — Procurement Pack (OQ-010)
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Product Owner (contract signature and spend); prepared under delegated PO direction
applies-to: BR-REL-001/002/003/005; OQ-010 in-principle approval
depends-on: [GOV-PO-DEC, DEP-ARCH, GOV-RISK]
---

# 24×7 Support Model — Procurement Pack

Prepared under **Product Owner direction (delegated), 23 Sep 2026**. Status: `APPROVED-IN-PRINCIPLE (delegated; real-PO signature pending)`. **No spend is committed; contract signature is the real Product Owner's human step.**

## 1. Requirements recap

Genuine 24×7 for a six-person core team: coverage by **≥4 on-call engineers two-deep or a managed NOC partner**; close-failure paging **≤5 minutes**, 24×7; **warm recovery for the 1-hour RPO/RTO** (continuous replication, failover capability); on-call compensation; monitoring/paging tooling; runbooks, escalation matrix and drill time; terms **≤12 months, no lock-in**; select the **lower whole-life cost meeting the NFRs**.

## 2. Options

- **A — Rota-first:** core-team on-call rota (4 engineers, two-deep); tooling, warm recovery and drills run in-house; partner only for overflow.
- **B — Partner-first:** managed NOC partner for 24×7 monitoring/L1 escalation and warm-recovery operations; core team retains L3/business-hours; hotel-side L1 contacts role-played at the synthetic pilot.

## 3. Structural cost model (12-month horizon)

Line items only; **no quotes invented** — every rate/quantity is a placeholder marked `ASSUMED`, pending RFQ responses.

| Line item | A — Rota-first | B — Partner-first |
|---|---|---|
| On-call compensation (nights/weekends/holidays) | 4 engineers × ₦[ASSUMED]/month | Backup-only rota ₦[ASSUMED]/month |
| Partner retainer (monitoring/NOC/L1) | Overflow ₦[ASSUMED]/month | ₦[ASSUMED]/month |
| Warm recovery (replication, standby, failover ops) | ₦[ASSUMED]/month | ₦[ASSUMED]/month |
| Tooling/telemetry | ₦[ASSUMED]/month | ₦[ASSUMED]/month |
| Drill/readiness time | [ASSUMED] engineer-days/month | [ASSUMED] engineer-days/month |
| Onboarding/transition (one-off) | ₦[ASSUMED] | ₦[ASSUMED] |
| Exit/transition exposure | Low | [ASSUMED] |

Whole-life cost = 12 monthly items + one-offs, computed only from returned quotes.

## 4. Evaluation matrix

Weights PROPOSED: cost — 12-month whole-life (30%); coverage safety — two-deep/NOC, holidays, close window (30%); reliability — ≤5 min paging and 1 h RPO/RTO warm recovery, evidenced by drill records (20%); lock-in — terms/exit (10%); onboarding time — to 24×7 live (10%).

## 5. Recommendation logic

After quotes: select the **lower 12-month whole-life cost that demonstrably meets every NFR above**; if neither option does, re-scope and re-issue — never select on price alone. The recommendation goes to the real Product Owner; **contract signature and spend remain human-only**.

## 6. Timeline (Week 3 envelope)

- By **30 Sep**: requirement pack/RFQ issued; shortlist and evidence requests.
- By **7 Oct**: quotes in (≥2 per line); references checked.
- By **14 Oct**: matrix scored; recommendation tabled at the P0 sitting.
- After signature: engagement/contract drafting begins.

## 7. Approval block

Approved (real Product Owner — human step): Name __________ Signature __________ Date __________

Contract engagement remains with the real Product Owner.

## 8. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial procurement pack prepared under delegated PO direction (structural cost model; no quotes; human signature pending) | PROPOSED |
