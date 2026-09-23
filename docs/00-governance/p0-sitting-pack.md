---
doc-id: GOV-SITTING
title: P0 Acceptance Sitting Pack (target ≤14 Oct 2026)
status: PROPOSED
version: 0.2
date: 2026-09-23
owner: Product Owner (chair; convening and sign-offs are human steps); pack prepared under delegated PO direction
applies-to: Programme P0 acceptance gate (charter §12/§14)
depends-on: [GOV-PO-DEC, GOV-REVIEW, GOV-APPOINT]
---

# P0 Acceptance Sitting Pack

Prepared under **Product Owner direction (delegated), 23 Sep 2026**. Human-only items — appointments, sign-offs, spend, legal/tax conclusions — remain human-only; **no acceptance is pre-claimed.**

## 1. Invitation (draft to send)

> You are invited to the **SuiteFlow Programme P0 acceptance sitting**.
> **Date:** [on or before 14 Oct 2026] · **Time:** [time] WAT · **Location:** [venue/video link] · **Chair:** real Product Owner.
> **Purpose:** record acceptance of the blueprint pack, resolve the human appointment slots (OQ-002, OQ-033) where possible, and record the outcome.
> Please confirm attendance by [RSVP date]. Papers: pre-read list §7; no decision is taken outside the pack.

## 2. Attendees / approvers

| Seat | Capacity at the sitting | Human sign-off |
|---|---|---|
| Real Product Owner | Chair; PO approvals and countersignatures | Human |
| Finance Controller | Acting capacity (role-assistant persona); appointment pending OQ-002 | **Not claimed** — human appointment required |
| Technical Lead | Acting capacity; maintaining-architect fallback recorded; appointment pending OQ-033 | **Not claimed** — human appointment required |
| Security/Privacy Adviser | Acting advisory capacity; appointment pending OQ-033 | **Not claimed** — human appointment required |
| Programme team / document owners | Supporting; no approval authority | — |

Human-only: appointments/deputies; FC/TL/Security correctness sign-offs; spend; legal/tax conclusions.

## 3. Agenda (130 minutes)

| # | Item | Min |
|---|---|---|
| 0 | Chair opens; quorum; acting-vs-human declarations | 5 |
| 1 | Pack walk-through: index, versions, freeze counts | 10 |
| 2 | PO items: vision/scope/priorities; classification and freeze; D1–D12; A1–A16; conventions | 20 |
| 3 | Finance items: FC checklist | 20 |
| 4 | Technical items: TL checklist | 20 |
| 5 | Security/privacy items | 20 |
| 6 | Human appointments; interim expiry | 10 |
| 7 | Outcome path and record | 20 |
| 8 | Close: actions and minutes | 5 |

## 4. Artifact index

Registers (GOV-OQ, risk, inputs); GOV-REVIEW with P0/P1/P2 logs; charter; master index; vision/scope/capability map (frozen pilot set)/roadmap/personas; ADR-001…011 with approver matrix; architecture set; evidence pack (freeze list; TO registry incl. TO-SEC-004…011; golden-day scope; OQ-010 in-principle record; interim appointments — GOV-APPOINT; role charters).

## 5. Per-approver checklists (compact)

**Role sign-off packs (prepared by the interim capacities):** `signoff-packs/finance.md`, `signoff-packs/technical.md`, `signoff-packs/security-privacy.md` — each contains reviewed documents, conclusions, outstanding conditions and proposed sign-off wording for the human appointee.


- **Product Owner:** vision/scope/priorities; classification (204/76/3 provisional) and freeze; D1–D12, A1–A16; OQ-010 in-principle; interim arrangements.
- **Finance Controller:** financial architecture; ADR-005…008; authority limits and consolidated refund/payout/waiver table; reconciliation (checks 1–14); golden-day cases; tax items explicitly **UNVERIFIED**.
- **Technical Lead:** target/domain/data/state; ADR-001…004/010/011; NFR/DEP/QA; sequencing (TEC-06/07).
- **Security/Privacy Adviser:** SEC-MODEL/ROLES; ADR-009/010/011 boundaries; incident/privacy operations; AI gate; exports; UAT account regime; TO-SEC pack.

## 6. Minute template (headings)

Date/time/location · chair · attendance (present/absent; acting vs appointed) · quorum · declarations · pack version · per-approver decisions (accepted / accepted-with-conditions / not accepted) · conditions table (item, owner, due) · scope-freeze statement · interim-arrangement expiry · outstanding human sign-offs · actions · next sitting · **chair attestation (human signature)**.

## 7. Outcome paths

1. **Full sign-off:** all three appointments seated; sign-offs recorded; blueprint ACCEPTED; Programme P0 exits.
2. **Conditional completion:** any appointee absent → PO items complete; **scope freeze effective**, recorded as "P0 conditional completion"; committed delivery does not start until outstanding sign-offs are recorded; re-convene within 5 business days of each appointment. **No fabricated acceptance.**

## 8. Pre-read list

GOV-PO-DEC v0.2 · GOV-BRIEFS · GOV-OQ v0.5 · GOV-REVIEW v0.4 · GOV-CHARTER · master index · vision/scope/capability map/roadmap/personas · ADR set + approver matrix · architecture set · evidence pack · GOV-APPOINT · this pack.

## 9. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial sitting pack prepared under delegated PO direction (invitation, agenda, checklists, minute template, outcome paths) | PROPOSED |
| 0.2 | 2026-09-23 | Role sign-off packs linked | PROPOSED |
