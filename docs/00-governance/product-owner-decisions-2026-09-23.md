---
doc-id: GOV-PO-DEC
title: Product Owner Decisions — P1 Acceptance-Readiness Items (23 Sep 2026, delegated)
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Product Owner (accountable); decisions taken by the Product Owner persona under explicit delegation of 23 Sep 2026
applies-to: P1 findings in GOV-REVIEW and the affected blueprint documents
depends-on: [GOV-REVIEW, GOV-CHARTER, PROD-ROADMAP, PROD-CAPMAP]
---

# Product Owner Decisions — P1 Acceptance-Readiness Items

**Delegation.** The decisions below were taken by the Product Owner persona under the real Product Owner's explicit delegation (23 Sep 2026) to decide the pending P1 items. They are recorded as **Product Owner direction (delegated)**. Financial, technical and security *correctness* remains with the Finance Controller, Technical Lead and Security/Privacy Adviser at appointment; money commitments and named-person appointments stay with the real Product Owner. Nothing here fabricates a human signature.

## D1 — TEC-04: 24-week clock start and freeze

**Call.** The 24-week horizon **starts 23 Sep 2026** and includes Programme P0 acceptance (by Week 3), Programme P1–P4 and Delivery D1–D9. **Target reference release: 10 Mar 2027.** Indicative envelope: P1–P2 Weeks 1–8; P3–P4 to Week 12 (platform-domain P4 rulings early, ~Week 6, to unblock D1); D9 exit by Week 24. The **pilot capability set freezes at P0 acceptance**; additions need a PO-approved trade-out; date changes are a PO decision against BR-PILOT-005. Staffing assumption: six-person core team, 24×7 model per OQ-010, FC/Security part-time governance, role-played hotel operations.
**Condition.** The OQ-010 budget itself remains with the real Product Owner (recommendation only).

## D2 — TEC-05: phase nomenclature

**Call.** Two named series, never bare numbers: **Programme phases P0–P4** (definition, research, audit, gap, transition) and **Delivery phases D1–D20** (D1–D9 pilot; D10–D20 enterprise). Numbers are preserved; the prefix is added. References read "Programme P2" / "Delivery D7". The former "Phase 5+ implementation" row is retired — implementation is the delivery series.

## D3 — TEC-08: capability classification after OQ closures

**Call.** Single-valued classification: **`Yes`** (pilot scope, Delivery D1–D9) / **`D10`–`D20`** (enterprise delivery) / **`Pend OQ-nnn`** (only OQ-007, OQ-024, OQ-029). Closed answers in pilot scope become `Yes`; closed answers that defer are placed in their enterprise phase (payment gateway CAP-INT-004, locks CAP-FO-011/CAP-INT-010, scanning hardware CAP-INT-011 → D16; recipe deduction CAP-INV-006 → D11). `Ph9` (CAP-PLT-016) counts in pilot → `Yes`. **Resulting provisional counts: 204 pilot candidates / 76 enterprise / 3 pending.** Counts are provisional until the P0 scope freeze, where the pilot set is fixed and overruns resolved by explicit de-scope.

## D4 — FIN-06: cashier variance semantics

**Call.** **Acceptance tolerance = zero** (every variance recorded, reason-coded, posted over/short). Independent investigation/review applies above **max(0.5% of session cash volume, ₦2,000)** — and in all cases above **₦20,000**, when unexplained/unrecorded, or on a repeat pattern (3+ variances by one cashier in 30 days). An explained variance closes with its record; an unexplained/unrecorded variance blocks the day advance. Defaults configurable; values PROPOSED until FC sign-off; negative cases join the golden-day fixture.

## D5 — FIN-08: Front Office Manager and consolidated authority

**Call.** **Add a Front Office Manager role** (department head) to the catalogue, distinct from the shift supervisor. **One consolidated refund/payout/waiver authority table** is the single source, by instrument: ≤₦20,000 Supervisor/DM; >₦20,000–≤₦50,000 FOM; >₦50,000–≤₦100,000 GM; >₦100,000–≤₦500,000 FC; >₦500,000 FC+GM. OQ-037 cash thresholds are an input to the cash column, not an additional layer. Conditions: original-instrument refunds, cleared funds for card/transfer, reason codes on waivers, penalty waivers per OQ-012, forfeiture deviations FC only, comps per OQ-036. Table PROPOSED until FC signs.

## D6 — FIN-11: financial configuration approval

**Call.** Financial configuration (tax rules, mappings, authority limits, number series) is approved by the **Finance Controller only**; **tax rules additionally require tax-adviser sign-off**; maker–checker applies to limits; the **GM is read-only** (second factor optional). Sys Admin executes but never approves. Until OQ-002 closes, interim financial approval stays with the real Product Owner; no GM approval route.

## D7 — FIN-13: First-Property Deployment Gate additions

**Call.** Add two blocking gate rows: (1) **tax-adviser sign-off** (VAT rate/tax points, forfeiture, service-charge tax base, fiscalisation OQ-029, statutory particulars); (2) **authority-limit defaults and the consolidated table signed by the Finance Controller**. Cite both in D7 exit criteria and the D9 go/no-go. Gate cannot close while OQ-002/OQ-029 are open.

## D8 — SEC-14: vulnerability remediation gate

**Call.** Per release: SBOM + dependency/image scans in the release pack. **Zero unfixed exploitable criticals** (a non-exploitable-in-configuration critical may be deferred ≤30 days with compensating controls and recorded acceptance; interim recorder Technical Lead/PO). **Highs: 30-day fix target; recorded acceptance ≤90 days** with owner, controls and expiry; no exception carries past the First-Property Deployment Gate without fresh adviser-level acceptance. The SECURITY_BASELINE debt is reassessed before the D9 release and before first-property deployment.

## D9 — SEC-03: incident and breach response

**Call.** Approve the incident/breach procedure: S0–S3 severity with scope-leak/class-A exposure always S0; declaration by Security/Privacy Adviser + Technical Lead (interim PO + TL); containment within 1 hour of confirmation for scope leaks/credential compromise/class-A exposure; forensic evidence preservation and append-only sink; **counsel decision tree for notifications** (timelines per counsel; NDPA duties UNVERIFIED); PIR ≤10 business days; breach register; **tabletop at the reference release** and before first-property deployment.

## D10 — SEC-06: export execution vs approval

**Call.** Split `export.execute` from `export.approve`: class-B exports run within role scope with approval recorded; **class-A exports require an approver independent of the executor**; exports watermarked, logged, time-boxed, verified recipients only. External-auditor path: time-boxed read-only, watermarked, fully logged, hard expiry, no writes.

## D11 — SEC-08: privacy operations

**Call.** Approve scope: DSAR workflow (interim 30-day design clock, counsel-confirmed; fulfilment evidence); lawful-basis register per processing activity; **processor/transfer register as an interface-enablement gate**; retention instantiated per category from the OQ-024 recommended schedule (UNVERIFIED defaults). Legal specifics remain counsel-confirmed.

## D12 — SEC-09: AI enablement gate

**Call.** No AI capability touching class A/B until (a) Security/Privacy Adviser sign-off, (b) DPIA, (c) injection-resistance and tool-authorization tests pass, (d) lawful basis for prompt/log handling established. The AI architecture's lawful-basis claim carries an explicit **UNVERIFIED** label pending counsel. Consistent with OQ-031 (no AI at pilot).

## Out of delegation — recommendations only

- **OQ-010 (24×7 budget):** recommend the real Product Owner approve the GOV-ANSWERS model by Week 3; interim: proceed on the planning assumption and evaluate a managed partner in parallel.
- **OQ-002 (Finance Controller):** recommend appointment by Week 1; interim financial approval with the real Product Owner; financial defaults stay PROPOSED.
- **OQ-033 (Technical Lead / Security Adviser):** recommend appointment with deputies by Week 1–2; interim technical approvals with the maintaining architect; **security sign-off remains pending — blueprint cannot reach ACCEPTED without it**; interim break-glass/incident reviewer: Technical Lead/PO as recorded.

## Residual uncertainties

Tax, retention and legal positions (OQ-024/028/029) remain UNVERIFIED pending counsel; OQ-002/OQ-033 are unappointed so no finance or security sign-off is claimed; OQ-010 cost approval and all real-property facts remain open; capability counts, phase durations and the pilot freeze are provisional until applied and confirmed at the P0 scope-freeze gate.

## Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial delegated PO decision record for the P1 acceptance-readiness items (12 decisions + 3 recommendations) | PROPOSED |
