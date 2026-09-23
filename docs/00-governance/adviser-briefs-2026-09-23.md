---
doc-id: GOV-BRIEFS
title: Adviser Engagement Briefs — Tax, Legal/Privacy, OSS Licensing (23 Sep 2026)
status: PROPOSED
version: 0.2
date: 2026-09-23
owner: Product Owner (engagement execution); prepared under delegated PO direction
applies-to: OQ-024, OQ-028, OQ-029 and dependent blueprint content
depends-on: [GOV-OQ, GOV-ANSWERS]
---

# Adviser Engagement Briefs

Ready-to-forward scopes of work prepared under delegated Product Owner direction (23 Sep 2026). **Engagement letters, fees and NDAs are the real Product Owner's execution step; no spend is committed here.**

## 1. Nigerian tax adviser — OQ-029 (with FIN-02/12/13, OQ-021 tax element, TEC-12)

**Scope.** Nigerian tax treatment of hotel operations as implemented in a PMS: VAT/consumption taxes, tax points, deposits/cancellations/no-shows, service charge, fiscalisation/e-invoicing, statutory invoice particulars and number-series requirements.

**Questions.**
1. VAT tax point per revenue type (delivery vs invoice vs payment vs receipt).
2. Confirm the tax-inclusive extraction method for penalties/forfeitures (FIN-02 worked example; 7.5% illustration).
3. Does the 10% service charge enter the VAT base; is its distribution subject to withholding or other taxes?
4. Are deposits/cancellations/no-show fees taxable on receipt, application or forfeiture?
5. Current FIRS e-invoicing/fiscalisation obligations and timeline; applicability to hotels; integration approach (adapter INT-014).
6. Required invoice/series particulars and any gapless-numbering rule.
7. Rounding and computation order.
8. Effective dates and imminent changes.

**Deliverables.** Written opinion; per-tax-type tax-point values; effective-dated mapping table; worked examples matching the golden-day fixture (room night, F&B + service charge, forfeiture, no-show with/without deposit); gate-ready sign-off statement; open-items memo.

**Blueprint impact.** Closes OQ-029 and the OQ-021 tax dependency; confirms/amends FIN-02; sets FIN-12 configuration; completes TEC-12 series semantics; evidence for FIN-13 gate rows; updates RSK-FIN-004.

## 2. Legal counsel — OQ-024 (with OQ-019 legal confirmation, SEC-03/07/08, FIN-10)

**Scope.** NDPA/privacy compliance for guest and employee data in a hotel PMS, plus the specific legal dependencies flagged by the review.

**Questions.**
1. Confirm/amend the retention schedule (ID images 90 d; profiles 6 y; financial 7 y; AML 5 y; logs 12 m+; CCTV 30 d).
2. DSAR workflow, statutory timelines and evidence.
3. Breach notification duties and clocks (NDPC reporting, data-subject notification) — confirm/refine the incident decision tree.
4. Lawful basis per processing activity.
5. Cross-border transfer conditions for hosting/providers (processor register requirements).
6. Is ID scanning legally required at Nigerian hotel registration (OQ-019 legal dependency)?
7. Treatment of unclaimed deposits — escheatment vs continued liability (closes FIN-10).
8. Employee-data and consent requirements.

**Deliverables.** Legal memo; confirmed retention schedule; notification matrix; processor/transfer register requirements; DSAR design confirmation; statements citable in the blueprint.

**Blueprint impact.** Closes OQ-024 and the OQ-019 legal dependency; updates SEC-MODEL §5/§14/§15, data-model §9, FIN-10 write-back rule; feeds D11 privacy operations and the incident procedure.

## 3. OSS licensing counsel — OQ-028 (with RSK-LIC-001)

**Scope.** Licence position for the platform stack (Frappe/ERPNext/Kamra and dependencies) under the intended operating and distribution models (SaaS-hosted; on-premise property deployment).

**Attached input.** `research-register.md` R6 licence inventory (23 Sep 2026): frappe 16.31.0 **MIT**; erpnext 16.32.3 / hrms 16.16.0 / crm 1.81.2 **GPL-3.0**; kamra 2.5.0 **AGPL-3.0**; payments 0.0.1 **MIT**; hotel_integration **undeclared**. Findings LIC-01 (AGPL on-premise distribution) and LIC-02 (undeclared licence).

**Questions.**
1. Licence classification of each component and dependency.
2. Network-copyleft/AGPL implications for SaaS operation, if present.
3. On-premise distribution and source-offer/attribution obligations.
4. Bundling/compatibility of planned components.
5. Required notices and attribution.
6. Trademark constraints.
7. OSS policy and release-gate controls (SBOM/licence scanning in CI; pre-distribution review).

**Deliverables.** Licence inventory + risk opinion; OSS policy; pre-distribution gate checklist; citations for the release pack.

**Blueprint impact.** Closes OQ-028; settles release/distribution architecture constraints; RSK-LIC-001 treatment; D1 topology input; release-pack gate item.

## 4. Engagement emails (ready to forward)

Drafts prepared under **Product Owner direction (delegated), 23 Sep 2026**. Sending, engagement letters, fees and NDAs are the real Product Owner's human step. No firm names or addresses are invented; complete bracketed fields before sending.

### 4.1 Nigerian tax adviser (OQ-029)

**Subject:** Engagement — Nigerian tax treatment of hotel operations in a PMS (OQ-029)

Dear [Adviser name],

[Programme] is preparing a hotel management platform for a Nigerian pilot and needs a Nigerian tax adviser to confirm the tax treatment of hotel operations as implemented in the system: VAT tax points by revenue type; penalties/forfeitures; the 10% service charge; deposits/cancellations/no-shows; FIRS e-invoicing/fiscalisation; invoice particulars and number-series rules.

Attachment: `adviser-briefs-2026-09-23.md` §1 (Tax brief, GOV-BRIEFS).

Requested: written opinion; effective-dated tax-point mapping; worked examples matching the golden-day fixture; gate-ready sign-off statement; open-items memo. Timing: acknowledge within 3 business days; draft within 3 weeks; final within 6 weeks of engagement (timings ASSUMED, fixed in the engagement letter).

Fees, engagement letter and NDA are handled by the Product Owner's office. Please direct scheduling to [contact].

Kind regards,
[Sender name], [role]

### 4.2 Legal counsel (OQ-024, with OQ-019 legal, SEC-03/07/08, FIN-10)

**Subject:** Engagement — privacy/legal review for a hotel PMS in Nigeria (OQ-024)

Dear [Adviser name],

[Programme] is preparing a hotel management platform for a Nigerian pilot and needs legal counsel for NDPA/privacy compliance across guest and employee data, plus flagged dependencies: the retention schedule (ID images, profiles, financial, AML, logs, CCTV); DSAR workflow and timelines; breach notification duties and clocks; lawful basis per processing activity; cross-border transfer conditions; whether ID scanning is legally required at hotel registration; treatment of unclaimed deposits.

Attachment: `adviser-briefs-2026-09-23.md` §2 (Legal/Privacy brief, GOV-BRIEFS).

Requested: legal memo; confirmed retention schedule; notification matrix; processor/transfer register requirements; DSAR design confirmation; statements citable in the blueprint. Timing: as §4.1.

Fees, engagement letter and NDA are handled by the Product Owner's office. Please direct scheduling to [contact].

Kind regards,
[Sender name], [role]

### 4.3 OSS licensing counsel (OQ-028, with RSK-LIC-001)

**Subject:** Engagement — OSS licensing review (Frappe/ERPNext/Kamra stack) (OQ-028)

Dear [Adviser name],

[Programme] is preparing a hotel management platform built on Frappe/ERPNext/Kamra for a Nigerian pilot, operated SaaS-hosted and, later, on-premise at properties. We need an OSS licensing opinion on the stack and its dependencies: licence classification; network-copyleft/AGPL implications for SaaS operation; source-offer and attribution duties for on-premise distribution; bundling/compatibility; required notices; trademark constraints; release-gate controls (SBOM/licence scanning in CI; pre-distribution review).

Attachment: `adviser-briefs-2026-09-23.md` §3 (OSS licensing brief, GOV-BRIEFS).

Requested: licence inventory and risk opinion; OSS policy; pre-distribution gate checklist; citations for the release pack. Timing: as §4.1.

Fees, engagement letter and NDA are handled by the Product Owner's office. Please direct scheduling to [contact].

Kind regards,
[Sender name], [role]

**Engagement checklist.** Attach the matching brief; confirm scope/milestones, conflict check, fee quote, engagement letter and NDA; name a single contact. Execution and signature are the real Product Owner's human step; no programme commitment exists before countersignature.

**Attachment-ready standalone briefs** (send one per adviser): `adviser-briefs/brief-tax.md`, `adviser-briefs/brief-legal-privacy.md`, `adviser-briefs/brief-oss-licensing.md`. An RFQ pack for the 24×7 service is at `operations/rfq-24x7-support.md`.

**Follow-up cadence.** Day 0 send → Day 3 reminder if unacknowledged → Day 7 confirm kick-off and engagement-letter status → weekly written status until deliverables → escalate to the Product Owner if any milestone slips by more than one week (threshold ASSUMED).

## 5. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial adviser briefs prepared under delegated PO direction (C1–C3) | PROPOSED |
| 0.2 | 2026-09-23 | Engagement emails, checklist and follow-up cadence added (human send/signature steps retained) | PROPOSED |
| 0.3 | 2026-09-23 | OSS brief attaches the R6 licence inventory (Kamra AGPL-3.0; hotel_integration undeclared) | PROPOSED |
| 0.4 | 2026-09-23 | Attachment-ready standalone briefs and the 24x7 RFQ pack prepared | PROPOSED |
