---
doc-id: OPS-RFQ
title: Request for Quotation — 24×7 Support and Recovery Service
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Product Owner (issue and contract signature); prepared under delegated PO direction
applies-to: OQ-010 procurement (support model approved in principle)
depends-on: [OPS-SUPPORT]
---

# Request for Quotation — 24×7 Support and Recovery Service

*Send this pack to 2–3 candidate providers. Responses due **7 October 2026**. Selection recommendation tabled at the programme's acceptance sitting (≤14 October 2026).*

## 1. About the engagement

A hotel management platform programme (Nigerian pilot property profile: 200 rooms, operations around the clock) requires **24×7 first-response support and recovery capability** for its hosted production environment, beginning with the reference release (target March 2027; trial/onboarding from October 2026).

## 2. Requirements

1. **Coverage:** genuine 24×7 first response by either (a) a rota of ≥4 qualified engineers structured two-deep, or (b) a managed NOC partner performing monitoring/L1, with programme-side escalation.
2. **Response:** alert paging to an on-call owner **within 5 minutes**, 24×7, including the nightly financial close window (02:00–04:00 WAT).
3. **Recovery:** warm recovery capacity meeting **1-hour RPO and 1-hour RTO**, rehearsed with timed restore drills (quarterly and pre-release).
4. **Operations:** monitoring/telemetry, incident tooling, runbooks and escalation matrix, drill time, and post-incident review support.
5. **Commercial:** 12-month initial term, clear exit/transition terms, no lock-in beyond 12 months.

## 3. Two options to quote

- **Option A — Rota-first:** provider supplies overflow/backup rota and tooling; core team runs the primary on-call rota (two-deep).
- **Option B — Partner-first:** provider runs 24×7 monitoring/L1 and warm-recovery operations; programme retains L3/business-hours.

## 4. Response template (complete and return)

| Item | Unit | Option A (₦/month) | Option B (₦/month) | Notes |
|---|---|---|---|---|
| On-call / NOC coverage | month | | | State staffing model and response SLA |
| Warm recovery (replication, standby, failover ops) | month | | | Describe architecture and 1h RPO/RTO evidence |
| Monitoring/telemetry tooling | month | | | Include licences and data-residency notes |
| Drill and readiness time | month | | | Timed-restore drill commitment |
| Onboarding/transition (one-off) | once | | | |
| Exit/transition support | once | | | |
| **12-month total** | | | | |

Also provide: (1) two references from comparable engagements; (2) evidence of ≤5-minute paging and recovery drills; (3) assumptions, exclusions and contract-term summary; (4) data-protection/processing terms (Nigeria Data Protection Act 2023 considerations).

## 5. Evaluation

| Criterion | Weight |
|---|---|
| 12-month whole-life cost (from quotes only) | 30% |
| Coverage safety (two-deep/NOC, holidays, close window) | 30% |
| Reliability evidence (≤5-min paging; 1h RPO/RTO drills) | 20% |
| Lock-in / exit terms | 10% |
| Onboarding time to 24×7 live | 10% |

Selection: lowest whole-life cost that demonstrably meets every requirement; if neither option does, the programme re-scopes and re-issues. No contract is awarded on price alone.

## 6. Timeline

| Milestone | Date |
|---|---|
| Questions/clarifications to the programme | by 26 Sep 2026 |
| RFQ issued | by 30 Sep 2026 |
| Responses due | **7 Oct 2026** |
| Evaluation and recommendation | by 14 Oct 2026 |
| Contracting (with the Product Owner) | after selection |

## 7. Vendor email (draft)

> **Subject:** RFQ — 24×7 support and recovery service (hotel platform pilot)
>
> Dear [Provider],
>
> Please find attached our Request for Quotation for 24×7 support and recovery services for a hotel management platform pilot in Nigeria. The service covers 24×7 first response (≤5-minute paging), warm recovery to a 1-hour RPO/RTO, monitoring, drills and incident support, in either a rota-first or partner-first model — please quote both options.
>
> Responses are due **7 October 2026**; use the response template in the pack. Questions by 26 September to [contact].
>
> Kind regards,
> [Sender name], [role] — SuiteFlow Programme

## 8. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial RFQ pack prepared under delegated PO direction (requirements, options, template, evaluation, timeline, email draft) | PROPOSED |
