---
doc-id: GOV-DECISIONS
title: Decisions Required from the Product Owner and Hotel
status: PROPOSED
version: 0.2
date: 2026-09-23
owner: Product Owner (accountable)
applies-to: plain-language digest of the Open Questions Register
depends-on: [GOV-OQ, GOV-ANSWERS]
---

# Decisions Required — Plain-Language Digest

## What this is

The blueprint has an **Open Questions Register** (`open-questions.md`) containing every question that only you, the hotel, or the finance team can answer. It is numbered `OQ-001`, `OQ-002`, … — "OQ" means **Open Question**. The register remains the authoritative record; if this digest and the register ever disagree, the register wins.

## Update — industry-standard answers prepared (23 Sep 2026)

At your direction, every pending question has now been answered on an **industry-standard/best-practice basis** in `industry-standard-answers.md`. The outcome:

| Outcome | Count | Meaning |
|---|---|---|
| Answered by industry standard | **25** | Concrete answer recorded; adopt them (all or with exceptions) and the design proceeds on them |
| Need a fact from you or the hotel | **9** | No standard can invent a company name, a person, a bank's API, or a site survey — see below |
| Need legal/tax advice | **3** | Recommended position recorded; a professional confirms before launch |
| Need your cost approval | **1** | The 24×7 staffing/recovery model |

## What still needs you

### 1. Facts only you or the hotel can provide

| ID | What to provide | Needed by |
|---|---|---|
| OQ-001 | CAC certificate, TIN, VAT registration, registered address of the legal entity | Week 1 |
| OQ-002 | Name the Finance Controller (and a deputy) | Week 1 |
| OQ-003 | Name the Hotel Operations representative / acceptance approver (and a deputy) | Week 1 |
| OQ-004 | Which bank is the primary collections/operating account, which is the POS acquirer, and does either offer API/virtual accounts? | Week 1 |
| OQ-006 | POS acquirer, merchant/terminal IDs, settlement frequency and a sample settlement report | Week 1 |
| OQ-007 | Bank statement samples and access method for both banks | Week 1–2 |
| OQ-025 | Is there existing data to migrate (old system, spreadsheets, paper)? Volume and quality? | Week 2 |
| OQ-033 | Name the Technical Lead and the Security/Privacy adviser (and deputies) | Week 1–2 |
| OQ-038 | Connectivity/power reality at the property — schedule the site survey (links, failover, generator, UPS) | Week 1 |

### 2. Professional advice

| ID | Recommended position | Confirmation needed |
|---|---|---|
| OQ-024 | Retention schedule: ID images 90 days; profiles 6 years; financial records 7 years; AML 5 years; logs 12 months+; CCTV 30 days | Legal/counsel |
| OQ-028 | Open-source policy with SBOM/license scanning; copyleft review before any distribution | Legal counsel |
| OQ-029 | Fiscalisation as a configurable adapter deriving from our invoice; confirm current FIRS e-invoicing obligations | Tax adviser |

### 3. Cost approval

| ID | Recommended model | Decision needed |
|---|---|---|
| OQ-010 | 24×7 needs a rota of at least four on-call engineers or a managed NOC partner, plus warm recovery capacity for the 1-hour RPO/RTO | Approve the staffing/cost model by Week 3 |

## What is now answered by industry standard (adoptable)

These 25 no longer need you unless you want to differ. The one-line answers:

| ID | Answer | Adopt? |
|---|---|---|
| OQ-005 | Online payment links: yes at enterprise target; at pilot, at most one provider path if prepayment is needed | |
| OQ-008 | Night close: 02:00 WAT cutoff, audit 02:00–03:30, certified by 04:00 | |
| OQ-009 | Keep 99.5% availability for pilot; 99.9% enterprise target | |
| OQ-011 | Hotel-generated SuiteFlow document is the customer invoice; statutory/fiscal document derives and links | |
| OQ-012 | Free cancellation 48 h+; one-night penalty inside 48 h; no-show = first night + tax; deposits per policy; logged overrides | |
| OQ-013 | Corporate credit: limit at onboarding, 30-day terms, suspension at 60 days overdue | |
| OQ-014 | Restaurant + bar (room service if operated) run in SuiteFlow at pilot | |
| OQ-015 | Light stock control at pilot; full recipe costing later | |
| OQ-016 | Manual rate management at pilot; RMS later | |
| OQ-017 | Groups pilot-critical: blocks, rooming lists, master folio, deposits, conference space booking | |
| OQ-018 | Direct + corporate at pilot; manual OTA handling if unavoidable; channel manager later | |
| OQ-019 | Capture ID type/number for adults; scan only if legally required; never NIN images | |
| OQ-020 | Manual keys at pilot; lock integration researched later | |
| OQ-021 | 10% F&B service charge held as staff-distribution liability, never revenue | |
| OQ-022 | Work orders + light preventive maintenance at pilot | |
| OQ-023 | HRMS/directory owns employee identity; SuiteFlow consumes identity only; no payroll | |
| OQ-026 | Hosting in Nigeria (or NDPA-adequate region) with in-country backups | |
| OQ-027 | Multi-property already designed; second property is configuration, not re-architecture | |
| OQ-030 | Automate gateway webhooks; bank confirmation manual until evidence permits; reconciliation is the control | |
| OQ-031 | No AI at pilot; candidates listed for later phases | |
| OQ-032 | English-only + WCAG 2.2 AA at pilot | |
| OQ-034 | Inspect VIP/arrival-critical + ≥20% spot checks; daily stayover service; minibar only if operated | |
| OQ-035 | Children ≤5 free; 6–12 sharing at 50%; early/late check-in/out 50%–full night | |
| OQ-036 | Comps approved/reason-coded, ≤2% target, counted in occupancy at value; house use excluded | |
| OQ-037 | Cash float ₦100k; zero tolerance, investigate >0.5%; payouts ≤₦50k need FOM; daily banking; dual custody | |

*(Full text with reasoning, evidence required and affected documents: `industry-standard-answers.md`.)*

## How to answer

Reply in chat in any format, for example:

```
adopt all industry-standard answers
adopt all except OQ-012 (cancel window is 24h, not 48h)
OQ-002: Finance Controller is <name>
OQ-038: site has fibre + 4G failover, generator, UPS on front desk
```

Adopting the defaults closes the 25 answered questions as the design baseline; the three fact groups above are then what remains before the blueprint can be ACCEPTED. Nothing adopted here overrides an accepted business requirement.

## What happens if you never answer

The industry-standard answers in `industry-standard-answers.md` remain the working defaults, clearly marked. The pilot can be configured to them. The risk is not failure — it is that the hotel receives a system built to *common practice* instead of *Golfview practice*, and changes then become configuration work or rework. The Group-1 facts (entity, Finance Controller, operations representative) and the site survey remove most of that risk.

## Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial digest issued after Product Owner question on OQ numbering; register authoritative | PROPOSED |
| 0.2 | 2026-09-23 | Rewritten against the industry-standard answer pack (GOV-ANSWERS): what is answered (25), what still needs facts (9), advice (3) and cost approval (1) | PROPOSED |
