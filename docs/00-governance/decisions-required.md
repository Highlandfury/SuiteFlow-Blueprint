---
doc-id: GOV-DECISIONS
title: Decisions Required from the Product Owner and Hotel
status: PROPOSED
version: 0.5
date: 2026-09-23
owner: Product Owner (accountable)
applies-to: plain-language digest of the Open Questions Register
depends-on: [GOV-OQ, GOV-ANSWERS]
---

# Decisions Required — Plain-Language Digest

## What this is

The blueprint has an **Open Questions Register** (`open-questions.md`) containing every question that only you, the hotel, or the finance team can answer. It is numbered `OQ-001`, `OQ-002`, … — "OQ" means **Open Question**. The register remains the authoritative record; if this digest and the register ever disagree, the register wins.

## Update — answers adopted, pilot redefined (23 Sep 2026)

You asked for the pending questions to be answered on an **industry-standard/best-practice basis**, adopted the answers, redefined the pilot as a **synthetic reference pilot** (no real property — see `inputs-register.md` §3.1), and took the role of acting operations authority. Current state:

| Outcome | Count | Meaning |
|---|---|---|
| **Closed** | **26** | 25 industry-standard answers adopted + OQ-003 (you are the operations authority and acceptance approver) |
| Need a fact | **8** | 2 now: Finance Controller (OQ-002), Technical Lead/Security adviser (OQ-033) — interim arrangements recorded, expiring at the P0 sitting; 6 deferred to the first property (OQ-001/004/006/007/025/038) |
| Need legal/tax advice | **3** | Scopes of work ready to forward (`adviser-briefs-2026-09-23.md`); a professional confirms before launch (OQ-024/028/029) |
| Cost approval | **decided in principle** | OQ-010 model approved under delegated direction; contract signature remains yours |

## What still needs you

### 1. Facts — now (programme-level)

| ID | What to provide | Needed by |
|---|---|---|
| OQ-002 | Name the Finance Controller (and a deputy) — or confirm you are acting | Week 1 |
| OQ-033 | Name the Technical Lead and the Security/Privacy adviser (and deputies) | Week 1–2 |

### 2. Facts — deferred to the first property

No action now; these form the **First-Property Deployment Gate** and must be closed before any real deployment:

| ID | What will be needed when a property is secured |
|---|---|
| OQ-001 | Entity registration and tax documents (CAC, TIN, VAT registration, registered address) |
| OQ-004 | Bank roles: primary collections/operating account, POS acquirer, API/virtual accounts |
| OQ-006 | POS acquirer, terminals, settlement frequency and sample settlement reports |
| OQ-007 | Bank statement formats and access for both banks |
| OQ-025 | Existing data inventory (if any) — volumes, quality and history depth |
| OQ-038 | Site survey: links and failover, power/generator, UPS coverage, equipment room |

### 3. Professional advice

| ID | Recommended position | Confirmation needed |
|---|---|---|
| OQ-024 | Retention schedule: ID images 90 days; profiles 6 years; financial records 7 years; AML 5 years; logs 12 months+; CCTV 30 days | Legal/counsel |
| OQ-028 | Open-source policy with SBOM/license scanning; copyleft review before any distribution | Legal counsel |
| OQ-029 | Fiscalisation as a configurable adapter deriving from our invoice; confirm current FIRS e-invoicing obligations | Tax adviser |

### 4. Cost approval

| ID | Model (approved in principle under delegated direction; contract signature is yours) |
|---|---|
| OQ-010 | 24×7 coverage via ≥4 engineers two-deep or a managed NOC partner; paging ≤5 min; warm recovery for 1 h RPO/RTO; on-call compensation; two costed options (rota-first vs partner-first) due Week 3, lower whole-life cost selected, ≤12-month terms |

## What is now decided (adopted 23 Sep 2026)

These no longer need you unless you want to change them through change control. The one-line answers:

| ID | Decided answer |
|---|---|
| OQ-003 | You act as operations authority and acceptance approver until a real property is secured |
| OQ-005 | Online payment links: yes at enterprise target; at pilot, at most one provider path if prepayment is needed |
| OQ-008 | Night close: 02:00 WAT cutoff, audit 02:00–03:30, certified by 04:00 |
| OQ-009 | Keep 99.5% availability for pilot; 99.9% enterprise target |
| OQ-011 | Hotel-generated SuiteFlow document is the customer invoice; statutory/fiscal document derives and links |
| OQ-012 | Free cancellation 48 h+; one-night penalty inside 48 h; no-show = first night + tax; deposits per policy; logged overrides |
| OQ-013 | Corporate credit: limit at onboarding, 30-day terms, suspension at 60 days overdue |
| OQ-014 | Restaurant + bar (room service if operated) run in SuiteFlow at pilot |
| OQ-015 | Light stock control at pilot; full recipe costing later |
| OQ-016 | Manual rate management at pilot; RMS later |
| OQ-017 | Groups pilot-critical: blocks, rooming lists, master folio, deposits, conference space booking |
| OQ-018 | Direct + corporate at pilot; manual OTA handling if unavoidable; channel manager later |
| OQ-019 | Capture ID type/number for adults; scan only if legally required; never NIN images |
| OQ-020 | Manual keys at pilot; lock integration researched later |
| OQ-021 | 10% F&B service charge held as staff-distribution liability, never revenue |
| OQ-022 | Work orders + light preventive maintenance at pilot |
| OQ-023 | HRMS/directory owns employee identity; SuiteFlow consumes identity only; no payroll |
| OQ-026 | Hosting in Nigeria (or NDPA-adequate region) with in-country backups |
| OQ-027 | Multi-property already designed; second property is configuration, not re-architecture |
| OQ-030 | Automate gateway webhooks; bank confirmation manual until evidence permits; reconciliation is the control |
| OQ-031 | No AI at pilot; candidates listed for later phases |
| OQ-032 | English-only + WCAG 2.2 AA at pilot |
| OQ-034 | Inspect VIP/arrival-critical + ≥20% spot checks; daily stayover service; minibar only if operated |
| OQ-035 | Children ≤5 free; 6–12 sharing at 50%; early/late check-in/out 50%–full night |
| OQ-036 | Comps approved/reason-coded, ≤2% target, counted in occupancy at value; house use excluded |
| OQ-037 | Cash float ₦100k; zero tolerance, investigate >0.5%; payouts ≤₦50k need FOM; daily banking; dual custody |

*(Full text with reasoning, evidence required and affected documents: `industry-standard-answers.md`.)*

## How to answer what is still open

Reply in chat in any format, for example:

```
OQ-002: Finance Controller is <name> (or confirm you are acting)
OQ-033: Technical Lead is <name>; Security adviser is <name>
OQ-010: approved — 4-person rota plus managed NOC partner
```

The two programme-level facts, three advice items and one approval listed above are what remains before the blueprint can be ACCEPTED; the six property-dependent facts close at the First-Property Deployment Gate. The synthetic-pilot amendment is recorded in the inputs register (BR-PILOT-001/005/008/009/010).

## What happens if the remaining items stay open

The adopted answers are in force and the reference pilot can be configured to them; the remaining facts and advice items do not block design work. But financial sign-off needs the Finance Controller (OQ-002); blueprint approval needs the Technical Lead and the Security/Privacy adviser (OQ-033); and the six property-dependent items will surface at the first real deployment — the First-Property Deployment Gate exists so they are not discovered there. Until a real hotel validates operations, expect gaps between reference-pilot results and reality (RSK-PROD-003): synthetic acceptance proves the logic, not the hotel.

## Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial digest issued after Product Owner question on OQ numbering; register authoritative | PROPOSED |
| 0.2 | 2026-09-23 | Rewritten against the industry-standard answer pack (GOV-ANSWERS): what is answered (25), what still needs facts (9), advice (3) and cost approval (1) | PROPOSED |
| 0.3 | 2026-09-23 | All 25 industry-standard answers adopted; remaining items restated as facts, advice and one approval | PROPOSED |
| 0.4 | 2026-09-23 | Synthetic reference pilot: OQ-003 closed (you are the operations authority); property-dependent facts deferred to the First-Property Deployment Gate | PROPOSED |
| 0.5 | 2026-09-23 | OQ-010 approved in principle; interim governance arrangements recorded (expire at the P0 sitting); adviser briefs and research register opened | PROPOSED |
