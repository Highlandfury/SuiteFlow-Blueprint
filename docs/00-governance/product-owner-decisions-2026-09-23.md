---
doc-id: GOV-PO-DEC
title: Product Owner Decisions — P1 Acceptance-Readiness Items (23 Sep 2026, delegated)
status: PROPOSED
version: 0.4
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

## Next-stage record (v0.2) — P2 dispositions, interim governance, briefs, acceptance, P1

### A. P2 dispositions (all 16 fixed; no deferrals)

| ID | Resolution | Evidence / condition |
|---|---|---|
| FIN-09 | Service Charge Payable control account + posting family 17 (distribution/remittance) + monthly reconciliation (check 14) + worked ordering example; §7 citation corrected | FIN-ARCH v0.4; monthly recon live from D6; FC sign-off at appointment; VAT-base question stays with OQ-029 |
| FIN-10 | "Expiry" disposition removed from BR-FOL-008; aged/unclaimed deposits reported monthly; write-back/escheatment only on counsel-confirmed effective-dated rule | business-rules v0.6; FIN-10 closes with OQ-024 advice |
| FIN-14 | Family 11 refunds credit the original instrument clearing; family 14 Dr short/Cr over stated; chargebacks added (family 18) and matched in check 3 | FIN-ARCH v0.4 |
| FIN-15 | Remaining citation corrections completed (hygiene) | commit history |
| TEC-06 | CAP-AVL-003 moved from D3 to D2 (rebuild/reconciliation sits with availability) | roadmap v1.3 |
| TEC-07 | D4 builds the minimal readiness state it consumes; dependency on D5 removed | roadmap v1.3 |
| TEC-09 | One reporting budget: NFR P-9 (<5 min) for large historical; "<60 s" withdrawn | reporting v0.3 |
| TEC-10 | QA §8 rewritten as role-played UAT at the reference release; real roles at the gate | qa v0.5 |
| TEC-11 | Property-scoped restore + mandatory restore order (quiesce → resolve intents → reconcile → resume) + TO-REL-003 | deployment v0.4 |
| TEC-12 | Series-semantics section (transactional allocation, single allocator, gap audit, reset/void rules) + TO-ACC-005; fiscal specifics UNVERIFIED pending OQ-029 | FIN-ARCH v0.4 |
| TEC-13 | BR-RTM-009 rate-basis fidelity + TO-RTM-003 | business-rules v0.6 |
| TEC-14 | Consistency pass complete; **zero count/link mismatches required at pack freeze** | gate condition |
| TEC-15 | adr/README gains standard front matter (ADR-INDEX, PROPOSED); no index exemption | adr/README |
| SEC-11 | Approval lines aligned to charter §12; Technical Lead and Security/Privacy Adviser added to the role catalogue with explicit bundles | SEC-ROLES v0.4; SEC-MODEL v0.4; ADR-009/010/011 |
| SEC-12 | Cumulative shift/day/month limits + split/velocity detection feeding report #11; TO-SEC-011 | SEC-ROLES v0.4 |
| SEC-16→15 | Security test registry completed: TO-SEC-008 (MFA/session), 009 (audit tamper/restore), 010 (webhook signature/provider intent) added to the D9 pack | qa v0.5 |

### B. Interim governance (delegated)

1. **OQ-010 — approved in principle.** Coverage (≥4 engineers two-deep or managed NOC), paging ≤5 min, warm recovery for 1 h RPO/RTO, on-call compensation, tooling/drill time. Two costed options (rota-first vs partner-first) prepared by Week 3; lower whole-life cost selected; ≤12-month terms, no lock-in. **No spend committed; contract signature is the real Product Owner's step.** Status: `APPROVED-IN-PRINCIPLE (delegated; signature pending)`.
2. **OQ-002 / OQ-033 — interim acting arrangements.** Financing: real Product Owner holds financial approval (defaults stay PROPOSED). Technical: maintaining architect gives interim design direction only. Security: advisory support only; **sign-off remains pending — the blueprint cannot reach ACCEPTED without it.** Break-glass/incident reviewer interim: Technical Lead/PO. **All three expire at the P0 sitting (scheduled ≤14 Oct 2026) or on appointment, whichever is earlier.**
3. **Human-only (never delegated):** appointments and deputies; FC/TL/Security correctness sign-offs; spend; adviser engagement letters and fees; any override of this record.
4. **Appointment-ready charters** (purpose, decision rights, competencies, selection criteria, acceptance evidence) for the three roles are recorded in the persona's working record and summarised here: FC — accountancy + hospitality finance + Nigerian VAT familiarity, independence from sales/procurement, signs the scoped list; TL — enterprise architecture + PMS/ERP depth + tenancy/authorization/reliability, independent of delivery vendors, signs ADRs/NFR/deployment; Security adviser — security architecture + NDPA/privacy + incident response, independent of platform administration, signs security model/ADR-009 and participates in the tabletop. Term to the First-Property Deployment Gate review.

### C. Adviser engagement briefs

Prepared as `adviser-briefs-2026-09-23.md` (GOV-BRIEFS): tax (OQ-029), legal/privacy (OQ-024 + OQ-019 legal), OSS licensing (OQ-028). Engagement execution, fees and NDAs remain the real Product Owner's step.

### D. Acceptance pack and sitting (≤14 Oct 2026)

- **Artifacts:** registers; GOV-REVIEW with P0/P1/P2 logs; charter; master index; vision/scope/capability map (frozen pilot set)/roadmap/personas; ADR-001…011 with approver matrix; architecture set; evidence pack (freeze list, TO registry incl. TO-SEC-004…011, golden-day scope, OQ-010 in-principle record, interim arrangements, role charters).
- **Per-approver checklist:** PO (vision/scope/priorities/freeze, this record); FC (financial architecture, ADR-005…008, limits and consolidated table, reconciliation, golden-day cases, tax items explicitly UNVERIFIED); TL (target/domain/data/state, ADR-001…004/010/011, NFR/DEP/QA, sequencing); Security adviser (SEC-MODEL/ROLES, ADR-009/010/011 boundaries, incident/privacy, AI gate, exports, UAT regime, TO-SEC pack).
- **Pre-acceptable under delegation:** product/scope/priority calls, capability classification and freeze, gates and sequencing, conventions, D1–D12 and A1–A16 dispositions, interim arrangements, OQ-010 in-principle.
- **Human-only:** FC/TL/Security sign-offs, appointments, spend, legal/tax conclusions.
- **Outcomes:** (1) all appointed → full sign-offs, blueprint ACCEPTED, P0 exits; (2) appointee absent → PO items complete and **scope freeze effective**, recorded as "P0 conditional completion"; committed delivery does not start until outstanding sign-offs are recorded. No fabricated acceptance.

### E. Programme P1 kickoff

Opened `research-register.md` (GOV-RESEARCH): R1 vendor capability verification, R2 OPERA public benchmark, R3 Nigerian tax/legal primary sources (source packs for the advisers), R4 provider evidence, R5 NFR benchmark sanity, R6 licence/SBOM inventory. Starts now without appointments (register + evidence collection + adviser source packs); adviser conclusions wait on engagement; property-dependent evidence waits for the First-Property Gate.

### F. Execution artifacts issued (23 Sep 2026)

The four next actions were executed to the programme-side maximum under delegation; every human-only step remains reserved:

| Action | Artifact | Human step remaining |
|---|---|---|
| Appoint acting role capacities | `interim-appointments-2026-09-23.md` (GOV-APPOINT) — three instruments **countersigned by recorded Product Owner direction, 23 Sep 2026** | Human appointments OQ-002/OQ-033 (A-02/A-03) |
| 24×7 model / contract | `operations/support-model-and-procurement.md` (OPS-SUPPORT) — requirements, two options, structural cost model, matrix, Week-3 timeline, approval block | RFQ issuance, quote scoring, **contract signature and spend** (A-04/A-05) |
| Adviser engagements | `adviser-briefs-2026-09-23.md` v0.2 — three ready-to-forward emails + checklist + cadence | **Send**, fees, engagement letters, NDAs (A-06) |
| P0 sitting | `p0-sitting-pack.md` (GOV-SITTING) — invitation, agenda, checklists, minute template, outcome paths | **Convene and chair**, human sign-offs (A-07) |

All actions are tracked in `action-register.md` (GOV-ACTIONS) with owner, due date, trigger and evidence.

## Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial delegated PO decision record for the P1 acceptance-readiness items (12 decisions + 3 recommendations) | PROPOSED |
| 0.2 | 2026-09-23 | Next-stage record: P2 dispositions (16 fixed); interim governance (OQ-010 in-principle, OQ-002/033 arrangements with expiry); adviser briefs; acceptance pack/sitting plan; P1 research register opened | PROPOSED |
| 0.3 | 2026-09-23 | Execution artifacts issued: interim appointments (GOV-APPOINT), support procurement pack (OPS-SUPPORT), engagement emails (GOV-BRIEFS v0.2), sitting pack (GOV-SITTING), action register (GOV-ACTIONS) | PROPOSED |
| 0.4 | 2026-09-23 | Interim appointment instruments countersigned by recorded Product Owner direction (A-01 complete) | PROPOSED |
