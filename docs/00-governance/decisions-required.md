---
doc-id: GOV-DECISIONS
title: Decisions Required from the Product Owner and Hotel
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Product Owner (accountable)
applies-to: plain-language digest of the Open Questions Register
depends-on: [GOV-OQ]
---

# Decisions Required — Plain-Language Digest

## What this is

The blueprint has an **Open Questions Register** (`open-questions.md`) containing every question that only you, the hotel, or the finance team can answer. It is numbered `OQ-001`, `OQ-002`, … — "OQ" means **Open Question**.

This document is a plain-language digest of that register, written for decision-making rather than architecture. **The register remains the authoritative record**; if the two ever disagree, the register wins.

## Why I keep asking these

When a rule depends on a decision that isn't made yet, I still have to design *something* so the work continues. So each question below carries a **working assumption** — clearly marked in the documents as `[OQ-nnn]` — a sensible hotel-industry default. Nothing built from an assumption is treated as final. When you answer, the assumption is replaced and the affected documents are updated through change control.

## How to answer

Reply in chat in any format, for example:

```
OQ-012: forfeit first night on no-show; full refund if cancelled 48h+ before arrival
OQ-014: yes, restaurant and bar run in SuiteFlow at pilot
OQ-008: close starts 02:00 WAT, must finish by 04:00
```

One line is enough. I record each answer in the register, update the affected rules, and tell you what changed.

## Group 1 — Answer now: these unblock the financial design (WP 0.4)

| ID | Question in plain words | Working assumption until you decide | Why it matters |
|---|---|---|---|
| OQ-001 | What is the hotel's registered company name and tax number (TIN/VAT)? | None — legal identity is a fact only you have | Invoicing, tax setup, statutory documents |
| OQ-002 | Who is the hotel's Finance Controller (the person who approves financial policy)? | Role exists but unfilled | Approvals, sign-offs on financial rules |
| OQ-003 | Who is the hotel-side operations representative (and who accepts the system at go-live)? | Role exists but unfilled | Validating workflows against real hotel practice |
| OQ-008 | What time does the hotel day close, and by what time must it finish? | 02:00 start, 04:00 finish (common practice) | Night audit design, staffing, alerts |
| OQ-011 | Which document is the official invoice: the hotel's own folio invoice, or the accounting system's tax invoice? | Hotel-generated invoice from SuiteFlow | Invoicing, tax, direct billing |
| OQ-012 | Cancellation, deposit, no-show and refund policy: refund windows, forfeit amounts, who may override | 24–48h cancellation window; first-night forfeit on no-show; deposits refundable per policy | Every front-desk money decision |
| OQ-013 | Corporate credit: default limits, who approves, when do we suspend a company that doesn't pay | Limit set per account at onboarding; suspension on overdue AR | Direct billing exposure |
| OQ-019 | Do we scan/record guest ID documents at check-in, and how long do we keep them? | Capture ID number/type at check-in; scan only if required by law | Registration, privacy, data retention |
| OQ-021 | Is a service charge added (e.g. 10%), and how is it distributed? | 10% service charge on F&B only, standard VAT treatment | Pricing, taxes, staff distribution |

## Group 2 — Answer when you can: these shape what the pilot must actually do

| ID | Question in plain words | Working assumption until you decide |
|---|---|---|
| OQ-014 | Does the pilot run the restaurant/bar/room service inside SuiteFlow, or on a separate POS? | Restaurant and bar run in SuiteFlow at pilot |
| OQ-015 | Do we manage kitchen/bar stock (inventory counts, recipes) at the pilot? | Light stock control at pilot |
| OQ-017 | How important are group/conference bookings at the pilot (the property is a conference centre)? | Groups are pilot-critical |
| OQ-018 | Do bookings come through Booking.com/Expedia or other channels, or only direct/corporate? | Direct and corporate only at pilot |
| OQ-020 | Do we integrate the door locks, or are keys handled manually? | Manual keys at pilot |
| OQ-022 | Does the engineering/maintenance module run at the pilot, or does maintenance stay informal? | Work orders run at pilot |
| OQ-025 | Is there existing data (an old system, spreadsheets) to bring across, and how much? | Migration of guest/reservation/opening balances assessed in Phase 2 |
| OQ-034 | Housekeeping standards: is supervisor inspection required before a room is sold? Stayover service frequency? Minibar? | Inspection only for VIP/rush rooms; minibar in room-service scope |
| OQ-035 | Free-stay child age, extra-person pricing, early check-in / late-checkout charges | Children ≤ 5 free; early/late charged per policy |
| OQ-036 | Comp rooms and staff house use: who approves, limits, and do they count in occupancy statistics? | Comps need manager approval and count in occupancy; house use excluded |
| OQ-037 | Cash policy: till float size, variance tolerance, payout limits | Zero tolerance with review above 0.5% of session volume |
| OQ-038 | What is the internet and power reality at the hotel (links, redundancy, UPS)? | One link with graceful degradation; offline mode designed in WP 0.7 |
| OQ-004 | Which bank/POS acquirer does what (UBA, Wema roles)? | Both banks used manually at pilot |
| OQ-006 | Which POS terminals/acquirer, and what does a settlement report look like? | Manual batch reconciliation from terminal printouts |
| OQ-007 | Can we get bank statements electronically, and in what format? | Manual statement review |

## Group 3 — Deferred: no action needed now

| ID | Question | When it matters |
|---|---|---|
| OQ-005, OQ-030 | Online payment links; whether bank/payment interfaces can automate reconciliation | With payment integration design (later) |
| OQ-009, OQ-010 | Confirm the 99.5% availability target; approve 24×7 support cost | Deployment design (WP 0.7) |
| OQ-016 | Revenue management: manual or automated rate recommendations | Rates phase (after pilot) |
| OQ-023 | Whether HR data beyond user identity is needed | HR boundary (enterprise phase) |
| OQ-024 | Guest data retention periods | Privacy model (WP 0.5) |
| OQ-026, OQ-028, OQ-029 | Cloud region, licensing, fiscalisation rules | Deployment/legal (later) |
| OQ-027 | When a second property is expected | Multi-property phase (after pilot) |
| OQ-031, OQ-032 | AI priorities; accessibility/language requirements | Later phases |
| OQ-033 | Named Technical Lead and Security adviser | Blueprint acceptance (can nominate later) |

## What happens if you never answer

The working assumptions above remain in force, clearly marked. The pilot can be configured to them. The risk is not failure — it is that the hotel receives a system built to *common practice* instead of *Golfview practice*, and changes then become configuration work or rework. Group 1 answers before WP 0.4 completes remove most of that risk.

## Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial digest issued after Product Owner question on OQ numbering; register authoritative | PROPOSED |
