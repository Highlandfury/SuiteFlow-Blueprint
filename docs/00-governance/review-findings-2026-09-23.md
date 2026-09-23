---
doc-id: GOV-REVIEW
title: Acceptance-Readiness Review — Finance, Technical and Security Passes (23 Sep 2026)
status: PROPOSED
version: 0.2
date: 2026-09-23
owner: Product Owner (accountable); findings produced by the Finance Controller, Technical Lead and Security/Privacy Adviser role-assistant personas
applies-to: blueprint v1.2 (PROPOSED) and the acceptance gate
depends-on: [GOV-CHARTER, GOV-OQ, GOV-ANSWERS]
---

# Acceptance-Readiness Review — Finance, Technical and Security Passes

## 1. Purpose and method

At Product Owner direction, the three governance role-assistant personas executed the charter §9 review passes over the PROPOSED blueprint:

| Pass | Charter §9 | Reviewer persona | Scope |
|---|---|---|---|
| Finance | Pass 4 (finance) | `finance-controller` | Financial architecture, ADR-005…008, finance business rules, role/authority limits, NFR financial integrity, roadmap finance phases, answers pack |
| Technical | Pass 3 (architecture) | `technical-lead` | Target state, domain/data model, ADR-001…004/010/011, NFR, deployment, QA, integrations, reporting, roadmap, capability map, master index |
| Security | Pass 5 (security) | `security-adviser` | Security model, role/authority matrix, ADR-009…011 boundaries, AI architecture, data retention, personas §4.8, UAT accounts, risk register |

**Findings only — no approvals.** These are AI-assisted review inputs for the human role-holders; nothing here is a sign-off. Findings are ordered by severity within each pass.

## 2. Summary

| Pass | Critical | High | Medium | Low | Total |
|---|---:|---:|---:|---:|---:|
| Finance | 1 | 5 | 8 | 1 | 15 |
| Technical | 1 | 4 | 9 | 1 | 15 |
| Security | 1 | 4 | 9 | 1 | 15 |
| **Total** | **3** | **13** | **26** | **3** | **45** |

**The three criticals:**

1. **FIN-01** — No-show/cancellation penalty can be recognised twice (room revenue + penalty revenue, or penalty + deposit forfeiture) with no posting family and no mutual-exclusion rule.
2. **TEC-01** — Availability/sellable-capacity definitions contradict each other (OOS treatment differs between BR-AVL-001, INV-PM-4, BR-AVL-003/BR-RPT-002 and the glossary): OOS rooms are sellable under one reading.
3. **SEC-01** — The single authorization enforcement point does not close framework-generic data surfaces (REST `/api/resource`, report builder, attachments, Desk search, bulk export), so scope leakage can bypass ADR-009 entirely.

**Cross-cutting themes:** stale counts/versions/statuses from rapid edits (now partly corrected, see §3); conflicting authority assignments across documents; definitional conflicts (capacity, variance tolerance, close exceptions, performance budgets); control gaps (bank/acquirer reconciliation, incident response, privileged-access lifecycle, audit tamper evidence); and synthetic-pilot drift in roadmap/capability/QA wording.

## 3. Disposition

**Hygiene corrections applied with this review (commit records):** master-index counts and version reference; inputs-register statuses (BR-PAY-009, BR-REL-004), front matter and malformed table row; charter deliverable-map versions; business-rules preamble/scope wording, BR-FOL-015 typo, BR-ACC-001 citation; reporting `depends-on`; integrations BR-RSV/BR-POS-2 citations; QA TO-ACC-003 citation; capability-map template marker; financial-architecture OQ-011 row.

**Recommended resolution order (needs a Product Owner decision to proceed):**

| Priority | Findings | Nature |
|---|---|---|
| P0 | TEC-01; FIN-01, FIN-02, FIN-04; SEC-01, SEC-02, SEC-04, SEC-05 | Definitional/control defects that must be fixed before the acceptance gate |
| P1 | FIN-03, FIN-05…08, FIN-11…13; TEC-02…05, TEC-08; SEC-03, SEC-06…10, SEC-13, SEC-14 | Controls and consistency requiring design edits or adviser input |
| P2 | Remaining medium/low findings | Mechanical or documentation fixes |

Findings that depend on inputs not yet available: Nigerian tax positions (FIN-02, FIN-12, FIN-13; OQ-029), retention/legal (SEC-07, SEC-08; OQ-024), named appointments (OQ-002/OQ-033), and Phase 1 evidence.

## 4. Finance pass findings

**FIN-01 — Critical — No-show/cancellation penalties: double-recognition paths and no posting family.**
Refs: `docs/finance/financial-architecture.md` §3 (families 1–16), §5.6; `ADR-007` decisions 2–3; `docs/architecture/business-rules.md` BR-FOL-009/010; `docs/architecture/state-machines.md` SM-RESERVATION #6; `docs/workflows/catalogue.md` WF-RSV-003.
Wrong: the no-show penalty must post as cancellation/no-show revenue "not room revenue", but no posting family covers a folio-path penalty (family 10 covers only deposit forfeiture credits); no rule excludes the no-show reservation from the nightly room-charge run (SM-INVENTORY #2 shows "stay active or no-show charged" as alternatives); and the deposit interplay is unspecified — a penalty charged to the folio *and* a deposit forfeited both credit cancellation/no-show revenue, with no mutual-exclusion rule.
Impact: first night recognised twice (room revenue + penalty revenue) and/or penalty + forfeiture double-counted; statistics and VAT misstated; no worked example and no test obligation (fires RSK-FIN-001's trigger verbatim).
Fix: one rule — "one penalty, one recognition": no-show determination completes before/within the posting run and suppresses the room charge; folio penalty maps to the dedicated Cancellation/No-show Revenue account via an explicit family/mapping; deposit either settles the penalty by application or is forfeited, never both (forfeit only the formula remainder after application); add a worked example + test obligation (golden-day negative cases with and without deposit).

**FIN-02 — High — Forfeiture VAT computation is unspecified and can be implemented unbalanced.**
Refs: `docs/finance/financial-architecture.md` §3 #10, §5.3, §7; `ADR-007` §5.
Wrong: "Dr Deposit Liability / Cr Cancellation/No-show Revenue (+ tax per ADR-007 §5)" does not say whether VAT is extracted from the forfeited amount (tax-inclusive) or added on top. Adding on top is impossible — only the received amount exists to debit — so the journal would not balance or would overstate the liability reduction.
Impact: unbalanced journals, overstated revenue and understated/overstated VAT liability; statutory exposure; no worked example or test obligation.
Fix: state the computation method explicitly (e.g., tax-inclusive extraction: ₦100 forfeited at 7.5% → revenue ₦93.02 + tax ₦6.98, illustrative), flag it UNVERIFIED pending OQ-029, add a worked example and test obligation, and make the mapping effective-dated per the adviser's ruling.

**FIN-03 — High — Bank and acquirer reconciliation missing from the "11 daily checks".**
Refs: `docs/finance/financial-architecture.md` §10 checks 1–11, §6, §13; `docs/integrations/architecture.md` §4.4 (INT-005); `docs/workflows/catalogue.md` WF-FIN-002; answers pack OQ-006/OQ-007; RSK-FIN-003.
Wrong: §10 reconciles the subledger to clearing accounts and Cash on Hand/Transit, but nothing reconciles clearing accounts and cash-in-transit to *bank statement lines/credits* or performs the OQ-006 three-way acquirer reconciliation (terminal batch → acquirer settlement → bank credit, including fees). INT-005 claims "Daily: … vs bank lines; cash deposits vs bank credits (FIN-ARCH §10)" — that check does not exist in §10.
Impact: cash/card balances can drift; refunds and "cleared funds" decisions rest on unreconciled settlement; the OQ-007 "zero unexplained difference" control is not actually designed.
Fix: add the bank/acquirer check to the suite (or explicitly map it into checks #3/#4 with batch-level acquirer matching and fee reconciliation), correct INT-005's cross-reference, and include an exception-aging rule.

**FIN-04 — High — Close gate: reconciliation-exception semantics conflict across five documents.**
Refs: `docs/finance/financial-architecture.md` §10 intro and §12 ("case resolution before close of the affected period"); `ADR-006` §3 ("Blocking conditions are exactly those in BR-NAU-001"); BR-NAU-001/BR-ACC-004; SM-NIGHT-AUDIT #3 ("control totals reconcile **or exceptions recorded**"); WF-NA-001.
Wrong: §10 says the close gate consumes the suite; BR-NAU-001's blocking list does not name failed reconciliation checks; §12 tolerates open cases until period close; SM-NIGHT-AUDIT allows the day to close with "exceptions recorded"; BR-ACC-004 says unreconciled differences block "financial close reporting, not operational operation".
Impact: a day can advance and be reported with unbalanced control accounts under at least two readings — the exact failure class ADR-006 option A was rejected for.
Fix: one blocking-conditions table (day advance vs day reporting vs period close) enumerating each check, its tolerance and exception path; align ADR-006 §3, BR-NAU-001, BR-ACC-004, FIN-ARCH §10/§12, SM-NIGHT-AUDIT #3 and WF-NA-001 to it.

**FIN-05 — High — Invoice/document authority contradiction inside ADR-008 and against the answers pack.**
Refs: `ADR-008` decisions 1, 2, 3, 6; answers pack OQ-011; `docs/finance/financial-architecture.md` §2, §9; `docs/product/capability-map.md` CAP-FOL-011/CAP-CRP-010.
Wrong: decision 1 makes SuiteFlow the issuing authority for "guest-facing **and corporate** financial documents"; decision 6 says corporate statements are produced from AR by the Accounting Authority; decision 2 assigns "AR invoices/credit notes" to the Accounting Authority; OQ-011 lists corporate statements under the SuiteFlow document. Decision 3/OQ-011 also say the derived statutory document carries "the same series reference" — ambiguous between one document and two series regimes.
Impact: dual-document risk for one supply, series/numbering ambiguity with statutory exposure, AR-to-folio disputes.
Fix: one authority sentence — SuiteFlow issues all customer-facing documents, including corporate statements rendered from AR data; Accounting Authority AR documents are ledger records, not customer invoices; a legally required fiscal document is a derived, one-to-one linked document in its own legally required series (delete or define "same series reference").

**FIN-06 — High — Cashier variance tolerance is self-contradictory and close-blocking.**
Refs: `docs/architecture/business-rules.md` BR-CSH-002 ("zero tolerance with review above 0.5% of session volume") and BR-CSH-003; SM-CASHIER-SESSION #4/#5; `docs/security/role-and-authority-matrix.md` §4 cashier-variance bands; OQ-037; BR-NAU-001.
Wrong: if tolerance is zero, every variance is "beyond tolerance", so every session goes PENDING_REVIEW and blocks the night close (BR-NAU-001), making the 0.5% threshold meaningless; if 0.5% is the real review threshold, "zero tolerance" is misleading and sub-0.5% variances close with no defined review.
Impact: either nightly close blockage at trivial variances, or unreviewed variances clearing; inconsistent approval expectations against the matrix bands.
Fix: define two explicit values — acceptance tolerance (zero, all variances recorded and explained) and investigation/review threshold (e.g., >0.5% of session volume or a low NGN floor, configurable) — and align BR-CSH-002/003, SM-CASHIER-SESSION, the matrix and OQ-037 wording.

**FIN-07 — Medium — Credit exposure formula is garbled and suspension/override rules contradict.**
Refs: BR-CRP-001, BR-CRP-002, BR-FOL-013, `docs/security/role-and-authority-matrix.md` §4 credit row, OQ-013.
Wrong: BR-CRP-001 contains two conflicting formulas joined by an ellipsis; BR-CRP-002 blocks transfers "automatically", BR-FOL-013 permits "a recorded authorised exception", the matrix gives GM override ≤₦100k exposure, and OQ-013 says suspension at 60 days/limit breach means **no** new direct-bill bookings.
Impact: wrong eligibility decisions — unsecured exposure or wrongly blocked sales; audit cannot test a rule with two formulas.
Fix: replace BR-CRP-001 with one formula and worked example; state that suspension for 60-day overdue is lifted only by FC reinstatement (OQ-013), while a first-time limit breach may take the recorded exception path; define suspension scope (new bookings vs transfers).

**FIN-08 — Medium — Cash-payout/refund bands conflict; FOM and penalty-waiver authorities are absent from the matrix.**
Refs: answers pack OQ-037 vs `docs/security/role-and-authority-matrix.md` §2/§4; OQ-012 waivers; SM-DEPOSIT #4.
Wrong: the two limit tables disagree on the same transaction (e.g., ₦80k cash refund); FOM does not exist as a catalogue role; cancellation-penalty waiver/forfeiture-deviation authority is described in OQ-012/OQ-037 but not fully in the matrix.
Impact: approval failures at the desk or approvals outside authority; audit exceptions on refunds/waivers.
Fix: consolidate refunds, payouts and waivers into one authority table keyed to catalogue roles (add FOM or map it to DM/Supervisor), and state whether OQ-037 cash thresholds apply additionally based on payment method.

**FIN-09 — Medium — Service-charge liability lifecycle is incomplete despite OQ-021 being closed.**
Refs: `docs/finance/financial-architecture.md` §2, §3 (#1/#2), §7, §10, §13; OQ-021; CAP-ACC-007.
Wrong: no control account for Service Charge Payable in §2, no posting family for distribution/remittance, no reconciliation (OQ-021 says "reconciled monthly" — §10 is silent), no worked example of tax ordering (does the 10% service charge enter the VAT base?), and §7 cites "default per BR-FOL-010 context" — BR-FOL-010 is the no-show rule, not service charge. The capability-map pilot flag for CAP-ACC-007 still reads "OQ-021 (closed)" rather than `Yes`.
Impact: collected-vs-distributed service-charge liability can drift untracked; staff-distribution disputes; incorrect VAT base if ordering is assumed rather than configured.
Fix: add Service Charge Payable to control accounts, a monthly reconciliation with owner, a named distribution-origination authority, a worked example (charge + 10% SC + VAT ordering), correct the citation, and normalise the pilot flag.

**FIN-10 — Medium — Deposit "expiry" contradicts the conservation invariant and state machine.**
Refs: BR-FOL-008; INV-FOL-7; SM-DEPOSIT; `ADR-007` §7.
Wrong: BR-FOL-008 permits an "expiry" disposition, but INV-FOL-7's identity (received = applied + refunded + forfeited + remaining) has no expiry term, SM-DEPOSIT has no EXPIRED state, and ADR-007 §7 leaves unclaimed-deposit treatment UNVERIFIED.
Impact: an expiry/write-back executed outside the invariant breaks the daily deposit reconciliation and the conservation proof.
Fix: either remove "expiry" from BR-FOL-008 or add the disposition explicitly — state, identity term, authority, and its own effective-dated mapping (escheatment/retention) pending the jurisdictional advice.

**FIN-11 — Medium — GM approval rights over financial configuration conflict with FC/adviser ownership.**
Refs: `docs/security/role-and-authority-matrix.md` §3 "Configuration (financial): GM R/A*"; charter §12; ADR-005; FIN-ARCH §7; BR-PLT-001.
Wrong: the matrix lets the GM approve financial configuration (tax rules, account mappings) with 2FA, without FC or tax-adviser involvement.
Impact: unmapped/wrong tax configuration can be activated by non-finance authority — exactly the RSK-FIN-004 trigger.
Fix: restrict financial-configuration approval to FC (tax rules additionally adviser-signed); GM may be read-only or a second factor on FC-initiated changes.

**FIN-12 — Medium — Tax point is hard-coded to service delivery while the whole tax position is UNVERIFIED.**
Refs: `docs/finance/financial-architecture.md` §7; ADR-007 §5; OQ-021/OQ-029.
Wrong: one tax-point basis is baked into the design; Nigerian VAT tax points are unresolved, and deposits/cancellations are precisely where the basis differs.
Impact: if advice concludes a different tax point, this becomes rework or misfiled VAT periods; no worked examples for room night, F&B with service charge, or forfeiture.
Fix: make tax point a per-tax-type configuration attribute (delivery / invoice / payment / receipt) with the current assumption as the UNVERIFIED default; add the three worked examples.

**FIN-13 — Medium — First-Property Deployment Gate does not require tax-adviser sign-off.**
Refs: `docs/product/roadmap.md` gate table; ADR-007 §5; ADR-008 §8; QA §8.
Wrong: OQ-029 fiscalisation and the VAT/tax-point/forfeiture/service-charge positions are launch gates in ADR-007/008, but the gate table names no tax-adviser item; FC signature of "taxes" is not adviser sign-off.
Impact: a real property could go live with unverified statutory tax configuration — the programme's own highest-rated finance risk.
Fix: add gate rows — "Tax adviser sign-off: VAT rate/tax points, forfeiture, service-charge tax base, fiscalisation (OQ-029)" and "Authority-limit defaults signed by FC" — and cite them in Phase 7's exit criteria and Phase 9 go/no-go.

**FIN-14 — Low — Reconciliation-suite detail defects.**
Refs: `docs/finance/financial-architecture.md` §10 vs §13; §3 family 11; family 14; SM-PAYMENT #7.
Wrong: minor internal inconsistencies in the suite and family map (check cadence, refund instrument posting, cash-over/short account, chargeback mapping).
Fix: align wording; add chargeback mapping; make family 11 credit the original clearing account.

**FIN-15 — Low — Citation and traceability defects.**
Refs: `docs/qa/strategy.md` TO-ACC-003 (corrected with this review); `docs/TARGET-STATE-BLUEPRINT.md` §2 vs §11 (corrected); `docs/finance/financial-architecture.md` §15 OQ-011 row (corrected); BR-FOL-015 typo (corrected); BR-ACC-001 citation (corrected).
Fix: correction edits under change control (no meaning change). Remaining items verified in this review.

### Finance pass — conditions before sign-off (proposal for the human to adopt or amend)

1. OQ-002 resolved: named FC (and deputy) or a recorded interim arrangement; matrix limits re-scoped and signed as FC-approved defaults.
2. FIN-01/FIN-02 dispositions recorded: no-show/cancellation single-recognition rule, folio-path penalty mapping, deposit-vs-forfeit exclusivity, and forfeiture tax computation each with worked examples and test obligations in the golden-day fixture.
3. FIN-03/FIN-04: bank/acquirer checks added to the daily suite and one day-close/period-close blocking-conditions table aligned across ADR-006, BR-NAU-001, BR-ACC-004, FIN-ARCH §10/§12, SM-NIGHT-AUDIT and WF-NA-001.
4. FIN-05: invoice-issuing authority and series wording corrected; corporate statements assigned to exactly one producer.
5. FIN-06/FIN-08: variance threshold semantics and one consolidated refund/payout/waiver authority table signed.
6. FIN-07: credit exposure formula replaced and suspension/override boundaries stated; FIN-09/FIN-10: service-charge control and deposit-expiry positions recorded.
7. FIN-11/FIN-12: financial-configuration approval restricted to FC (+ tax adviser for tax rules); tax-point configuration option added.
8. FIN-13: First-Property Deployment Gate amended to require tax-adviser sign-off and FC-signed limits.
9. Golden-day fixture scope explicitly includes: no-show with/without deposit, cancellation forfeiture tax, bank/acquirer mismatch, session variance, deposit partial lifecycle — with zero unexplained difference.

### Finance pass — not verifiable from the documents
Nigerian tax/legal positions (all UNVERIFIED; OQ-029); current implementation behaviour (Phase 2 not performed); bank/acquirer facts (deferred); role limits (PROPOSED defaults); test-obligation completeness (29 representative only); detailed finance workflow steps (WP 0.6 detail not present).

### Finance pass — strengths worth preserving
Subledger/books-of-record split with one writer per datum; daily aggregated postings with durable identity and unmapped-family close block; zero-revenue direct-bill transfer and comp-at-value with contra-revenue; obligation-linked deposit liability with conservation invariant; honest UNVERIFIED labelling; reconciliation suite with owners/ageing; certification-before-advance; versioned reopen; WAT/business-date discipline; eight SoD rules; golden-day acceptance anchor.

## 5. Technical pass findings

**TEC-01 — Critical — Availability/sellable-capacity definitions contradict each other (OOS treatment).**
Refs: `docs/architecture/business-rules.md` BR-AVL-001/003, BR-RPT-002; `docs/architecture/domain-model.md` INV-PM-4/INV-PM-7; `docs/architecture/state-machines.md` SM-ROOM #6; `docs/00-governance/glossary.md` §1.
Wrong: BR-AVL-001 computes availability as physical − OOO only (OOS not deducted), while INV-PM-4/CapacityCalendar subtract OOO *and* OOS; BR-AVL-003/BR-RPT-002 keep OOS in "capacity/available-room statistics" while SM-ROOM #6 says OOS "inventory reduced"; the glossary uses "sellable inventory" for another quantity. One term, three definitions.
Impact: implementing BR-AVL-001 literally makes OOS rooms sellable → oversell, relocation/walk cost; implementing the model makes BR-RPT-002 statistics wrong.
Fix: define once — physical rooms; sellable capacity = physical − OOO − OOS; available-room statistics = physical − OOO — correct BR-AVL-001/BR-RPT-002/glossary, add a divergence test obligation.

**TEC-02 — High — Ownership matrix uses slash-owners and hedged ownership, breaching "exactly one owner per datum".**
Refs: `docs/architecture/target-state.md` §4; CAP-PM-009/010; ADR-001 §2.
Impact: multiple write/release paths for availability-driving state and reconciliation records — RSK-ARCH-001; guest-visible.
Fix: split each row to one owner; make Control the approval/evidence owner only; align capability owner codes; enforce with an architecture test.

**TEC-03 — High — Outbox immutability vs mutable dispatch state.**
Refs: ADR-003 class-1 table; `docs/architecture/data-model.md` §4/§7; ADR-010 §1.
Impact: either immutability is violated or claim/attempt/replay evidence is non-durable; TO-INT-001 and TO-REL-002 lack a defined state model.
Fix: keep the event row immutable; add a separate delivery-attempt/dispatch table; index dispatch there; restate ADR-010 §1 and data-model §7.

**TEC-04 — High — 24-week horizon is not assessable; clock start undefined.**
Refs: `docs/product/scope.md` §4; `docs/product/roadmap.md` §1/§3; capability-map §3.4.
Impact: whether the 24 weeks includes blueprint acceptance, research, audit, gap and transition is unstated; RSK-ARCH-003 cannot be managed.
Fix: state the clock start; add phase durations/effort and a staffing assumption (OQ-010); tie the horizon to an explicit scope-freeze gate.

**TEC-05 — High — "Phase" numbering collision between governance, programme and delivery plans.**
Refs: charter §14; `docs/product/roadmap.md` §1/§3; `TARGET-STATE-BLUEPRINT.md` §13.
Impact: tickets, gate references and sign-offs cite ambiguous phases; traceability degradation (RSK-GOV-001).
Fix: rename one series (e.g. programme P0–P4; delivery D1–D9/D10–D20) and update cross-references.

**TEC-06 — Medium — Roadmap Phase 2 exit criterion depends on a Phase 3 capability.**
Refs: roadmap Phase 2/Phase 3 (CAP-AVL-003).
Fix: move CAP-AVL-003 to Phase 2 or restate the exit criterion and tests.

**TEC-07 — Medium — Phase 4 ↔ Phase 5 circular dependency.**
Refs: roadmap Phase 4 Dependencies vs Phase 5 Dependencies.
Fix: place the minimal readiness state model in Phase 4 (Phase 5 completes policy depth), or split 4a/4b.

**TEC-08 — Medium — Capability-map pilot status is stale after OQ adoption; phase buckets inconsistent.**
Refs: `docs/product/capability-map.md` §2.3/§3.3/§3.4; roadmap Phases 3/5/6; CAP-AVL-007, CAP-MNT-*, CAP-GST-009, CAP-INT-005, CAP-PLT-016.
Impact: the 160 count is not the set the roadmap will build; RSK-PROD-001 threshold and the 24-week assessment are miscalibrated.
Fix: define post-closure pilot status, reclassify against roadmap phases, move `Ph9` into the pilot bucket, restate §3.3/§3.4.

**TEC-09 — Medium — Reporting performance budget conflicts with the NFR.**
Refs: `docs/reporting/architecture.md` §8 ("< 60 s") vs NFR §2 P-9 ("< 5 min").
Fix: adopt one budget and update the other.

**TEC-10 — Medium — QA UAT wording is stale after the synthetic rescope.**
Refs: `docs/qa/strategy.md` §8 vs §2/§5; roadmap Phase 9.
Fix: rewrite §8 as role-played execution with the Product Owner (acting operations authority) plus finance review; real-role execution at the first property.

**TEC-11 — Medium — Recovery design misses two promised elements.**
Refs: ADR-002 Trade-offs vs `docs/deployment/architecture.md` §5.
Impact: per-property recovery and post-PITR duplicate-effect protection are asserted, not designed or drillable.
Fix: add a §5 subsection plus runbook: property-scoped export/restore, and the restore order (quiesce workers → resolve uncertain intents → reconcile → resume).

**TEC-12 — Medium — Statutory number-series semantics never finalised where ADR-004 promised (WP 0.4).**
Refs: ADR-004 §2/Consequences; FIN-ARCH §10 #11; ADR-008.
Impact: gapless/transactional allocation, reset policy, gap audit and concurrent allocation for invoice/fiscal series are unspecified (OQ-029 open).
Fix: add a series-semantics section to FIN-ARCH/ADR-008 with test obligations.

**TEC-13 — Medium — No booked-rate fidelity rule between booking and posting.**
Refs: BR-RTM-006/007; ADR-003 Context; domain-model §3.3.
Impact: whether future nights are held at the booked rate or re-resolved when the calendar changes is undefined → repricing disputes.
Fix: add a BR-RTM rule: rate-basis snapshot per room-night at booking; re-resolution only on amendment/policy; posting divergence flagged; add a test obligation.

**TEC-14 — Medium — Stale versions, statuses and counts in governance records.**
Refs: `TARGET-STATE-BLUEPRINT.md` §2/§12; `charter.md` §13; `inputs-register.md` front matter/BR-REL-004/BR-PAY-009/§5; `business-rules.md` preamble.
Fix: consistency pass (link + count + status check). **Partially applied with this review; residual items in the P2 fix list.**

**TEC-15 — Low — Dangling references and template remnants.**
Refs: reporting front matter (fixed); integrations BR-RSV/BR-POS-2 (fixed); capability-map `<!-- COUNTS -->` (fixed); ADR README front matter (charter §5 exemption decision).
Fix: repoint/add missing rules; remove markers; decide index-file exemption.

### Technical pass — conditions for sign-off

1. TEC-01 resolved (must-fix before acceptance).
2. TEC-02 and TEC-03 corrected (one owner per datum; outbox event vs dispatch split).
3. TEC-04–TEC-08 closed out: clock start and staffing stated, phase naming disambiguated, Phase 2/3 and Phase 4/5 sequencing fixed, capability-map pilot semantics and counts restated; scope-freeze decision recorded.
4. TEC-09–TEC-13 fixed or explicitly accepted with owner and evidence; TEC-14/15 consistency pass done.
5. OQ-002 and OQ-033 named per charter §12; all 11 ADRs remain PROPOSED until their approvers sign.

### Technical pass — not verifiable
Implementation behaviour and vendor claims; Nigerian tax/legal; provider settlement formats; source hashes/IMPL-ADR statuses (Phase 2 re-verification); line-level consistency across the full BR/INV/WF corpus (sampled); counting rules behind "42 documents"/"33 accepted requirements".

### Technical pass — strengths
One-owner source-of-truth matrix with build-failing architecture tests; four-class temporal strategy; opaque identity/numbering; outbox + idempotency + write-ahead provider intents; availability explicitly derived; invariants tied to test obligations; runbook catalogue and restore drills; First-Property Deployment Gate; honest scope caution.

## 6. Security pass findings

**SEC-01 — Critical — The single enforcement point does not close platform-generic data surfaces.**
Refs: ADR-009 §Decision 2/5/10 and §Risks; `docs/security/security-model.md` §6; inputs register §3.5 (Frappe v16/Kamra/ERPNext); `ROLE_BASED_UAT_ACCOUNTS.md`.
Failure path: framework-generated surfaces (REST `/api/resource/*`, report builder, file/attachment URLs, bulk import/export, Desk list search, admin UI) are neither enumerated nor closable by the authorization service; a broad generic read grant reads another property/company's data without the enforcement point seeing it; TO-SEC-001 passes because it tests the new service only.
Fix: amend ADR-009 with an invariant and Phase 2 audit obligation to enumerate and disable/mediate every framework-generic surface; enforce property scope at the record-permission layer as defence in depth; add a CI route/permission inventory test; extend TO-SEC-001 to per-surface negatives.

**SEC-02 — High — Break-glass can be self-granted; alert/review recipient undefined while OQ-033 is open.**
Refs: role-matrix §4; security-model §9; SoD #8; personas §2.15; UX admin console claim.
Fix: explicit no-self-grant rule; two-person grant; reviewer independent of both; name the alert/review recipient now; add a break-glass test obligation.

**SEC-03 — High — No incident/breach response procedure exists.**
Refs: personas §2.19; scope data-protection obligations; deployment §10; QA §6.
Fix: add incident/breach section (severity alignment, declaration authority, containment, evidence handling, counsel decision tree, PIR, breach register) and a reference-release tabletop; named owner upon OQ-033.

**SEC-04 — High — Privileged role grants lack dual control/independent review.**
Refs: role-matrix §3/§5/§6; security-model §7; UAT accounts provisioning.
Fix: maker–checker for provisioning and role grants (grantor ≠ approver ≠ beneficiary); finance/security grants need FC/adviser approval; reviews independent of the admin; self-grant/self-approval denial tests.

**SEC-05 — High — Role-based UAT accounts are not adequately controlled for privileged roles.**
Refs: `ROLE_BASED_UAT_ACCOUNTS.md`; security-model §3/§9; personas §4.8.
Gaps: no MFA on the privileged role accounts; single custody of credentials; rotation only before external demos; disablement deferred to the First-Property Deployment Gate (unbounded lifetime); no session logging requirement; `technical.lead` holds System Manager (effectively unrestricted) with "no business authority" as convention.
Fix: MFA where supported else a recorded time-boxed exception with network restriction evidence; per-session rotation; named operator register; hard expiry; no-real-data rule; include in access reviews; retire at the reference release.

**SEC-06 — Medium — Export permissions: execute vs approve undefined; external-auditor contradiction.**
Refs: role-matrix §3; personas §2.17; reporting §7; CAP-RPT-011.
Fix: explicit export-execute permission separate from approval; class-A approval independent of executor; watermarked, logged, time-boxed auditor export path; tests.

**SEC-07 — Medium — Data-at-rest lifecycle stops at the live database (backups, device caches, paper; class B encryption).**
Refs: security-model §5; deployment §5/D-4/D-5; data-model §9.
Fix: backup/PITR handling rules (sealed, access-restricted, defined expiry, legal-hold exception, crypto-shred where supported); device-cache encryption/purge; paper fallback secure destruction; encryption at rest for A and B with key management.

**SEC-08 — Medium — NDPA operational gaps: DSARs, lawful-basis register, processor/transfer gate, retention instantiation.**
Refs: data-model §9; CAP-GST-009; answers pack OQ-024/OQ-026; personas §2.19.
Fix: privacy-operations section (DSAR workflow with clocks and evidence; lawful basis + retention mapping per category subject to counsel; processor/transfer register as an interface-enablement gate; erasure tests incl. derived copies and legal hold).

**SEC-09 — Medium — AI enablement lacks a security/privacy gate; lawful-basis claim unlabelled.**
Refs: `docs/ai/architecture.md` §3/§6; charter §6; OQ-033.
Fix: require adviser sign-off + DPIA and injection tests for any capability touching class A/B before enablement; label the consent/lawful-basis statement UNVERIFIED pending counsel.

**SEC-10 — Medium — AI "invoker authority ceiling" contradicted by service-identity execution wording.**
Refs: security-model §8 vs AI architecture §2/§1.2.
Fix: authority at execution = intersection(invoker, service identity, policy); above-invoker execution requires a named human approver; test obligation.

**SEC-11 — Medium — Approval lines contradict charter §12; Security/Privacy Adviser role absent from the role catalogue.**
Refs: security model / ADR-009 / role-matrix front matter vs charter §12; role matrix §2 vs personas §2.19.
Fix: align approval lines to charter §12; add governance roles to the catalogue with explicit permission bundles; record that security sign-off remains pending OQ-033.

**SEC-12 — Medium — Authority limits are per-transaction; no aggregation or split-detection.**
Refs: role-matrix §4; security-model §10/§12.
Fix: add period-based cumulative limits (shift/day/month) and split/velocity detection as a named control feeding report #11, with a test obligation.

**SEC-13 — Medium — Append-only audit depends on the platform the administrator controls.**
Refs: security-model §10; data-model §9/§11; ADR-009 §9.
Fix: ship audit/security events to an append-only sink outside admin control (WORM/object-lock, separate credentials); hash-chain evidence; audit continuity check in restore drills.

**SEC-14 — Medium — No vulnerability-remediation gate despite known image debt.**
Refs: security-model §13; deployment §7; evidence `/home/suiteflow/suiteflow/docs/SECURITY_BASELINE.md` (39 critical / 515 high; "must be reassessed before production release approval").
Fix: release gate — SBOM + scan per release, no unfixed exploitable criticals, highs time-bound with adviser-recorded acceptance, patch cadence evidence in the release pack.

**SEC-15 — Low — Test-obligation registry omits key security obligations.**
Refs: QA §4 (TO-SEC-001…003 only) vs security-model §13.
Fix: register TO-SEC-004…008 (break-glass lifecycle/self-grant denial, MFA/session enforcement, audit tamper/append-only, webhook signature verification) and add to reference-release and penetration-test scope.

### Security pass — conditions for sign-off

1. Appointments: Security/Privacy Adviser + deputy and Technical Lead named (OQ-033); FC named (OQ-002); break-glass/incident alert recipients defined.
2. ADR-009 amendment (SEC-01): framework-generic surface closure invariant, route inventory test, Phase 2 audit obligation.
3. Privileged access (SEC-02/04/05): no-self-grant break-glass with two-person grant; role grants on maker–checker with independent review; UAT accounts under a recorded time-boxed exception (MFA or compensating controls, rotation, expiry, session logging, no real data).
4. Incident/breach (SEC-03): procedure with evidence preservation, counsel notification decision tree, PIR, tabletop at the reference release.
5. Privacy (SEC-07/08): counsel confirmation of OQ-024 retention and breach duties; DSAR/lawful-basis/processor-transfer design; A/B at-rest encryption and backup/cache/paper handling.
6. AI (SEC-09/10): security/privacy sign-off + DPIA per capability touching A/B; authority-intersection rule; injection tests; UNVERIFIED label on the lawful-basis claim.
7. Reporting/audit (SEC-06/13): export execute/approve split; independent audit sink/tamper evidence.
8. Release security (SEC-14/15): vulnerability thresholds with time-bound acceptance; TO-SEC additions; pen-test scope covering generic surfaces.
9. Document hygiene (SEC-11): approval lines aligned to charter §12; sign-offs recorded per charter §12.

### Security pass — not verifiable
Implementation reality (enforcement point existence, generic route exposure, UAT account MFA/network reachability, default accounts, audit tamper controls, export behaviour); security test-pack execution; vulnerability disposition beyond the baseline document; legal positions (all UNVERIFIED pending counsel); no committed credentials found in the files searched.

### Security pass — strengths
Deny-by-default with one enforcement point and effective-dated limits; payload-bound maker–checker with execution-time re-validation; scope leakage treated as the top defect class with per-surface negative tests; Class-A masking/read-logging/export prohibition; break-glass design intent (time-box, alerts, review); outbox/idempotency/reconciliation discipline with restored-worker protection; QA stop-the-line for security; honest confidence labelling and documented UAT-account rules.

## 7. Resolution log — P0 fixes applied (23 Sep 2026)

The P0 findings from §2 are resolved in the documents as follows; each resolution was applied through change control with a version increment.

| Finding | Resolution | Evidence |
|---|---|---|
| TEC-01 | Capacity definitions made single-valued: sellable capacity = physical − OOO − OOS; statistical capacity = physical − OOO | glossary v0.2; business-rules v0.4 (BR-AVL-001/003, BR-RPT-002); state-machines v0.2 (SM-ROOM); TO-AVL-001 |
| FIN-01 | One-penalty-one-recognition rule; no-show night suppressed from room charges; application/forfeiture exclusivity; posting family 10 extended; worked example | financial-architecture v0.2 §3/§5.6; business-rules v0.4 (BR-FOL-009/010); ADR-007 v0.2; TO-FOL-006 |
| FIN-02 | Penalty/forfeiture tax extracted tax-inclusive with worked example; effective-dated; UNVERIFIED pending OQ-029 | financial-architecture v0.2 §7; ADR-007 v0.2 §5; TO-FOL-007 |
| FIN-04 | Canonical stage-by-stage blocking table (day advance / certification / period close); unexplained differences never advance | ADR-006 v0.2 §3; business-rules v0.4 (BR-NAU-001, BR-ACC-004); financial-architecture v0.2 §10/§12; state-machines v0.2 (SM-NIGHT-AUDIT); workflows v0.2 (WF-NA-001) |
| SEC-01 | Framework-generic surface closure invariant; surface inventory; CI route/permission test; Phase 2 enumeration obligation | ADR-009 v0.2 §11 + risks; security-model v0.2 §2.3; TO-SEC-001 extended |
| SEC-02 | No-self-grant; two-person break-glass grant; named independent reviewer (Technical Lead; interim Product Owner); alerts at grant/expiry | security-model v0.2 §9; role-matrix v0.2 §4; TO-SEC-004 |
| SEC-04 | Privileged grants on maker–checker (requester ≠ approver ≠ beneficiary); independent quarterly review; finance/security approvals defined | role-matrix v0.2 §3/§5; TO-SEC-005 |
| SEC-05 | Reference-pilot account hardening: MFA or recorded time-boxed exception with network restriction and session logging; 30-day rotation; hard expiry at the reference release; named operator register; no real data | personas v0.4 §4.8; security-model v0.2 §3; implementation repo `docs/ROLE_BASED_UAT_ACCOUNTS.md` |

**Still open:** the P1 and P2 findings (33 items) and the input-dependent items (tax advice OQ-029; retention OQ-024; appointments OQ-002/OQ-033). Next resolution pass: P1.

## 8. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial review record: finance, technical and security passes executed by role-assistant personas over blueprint v1.2; 45 findings (3 critical, 13 high, 26 medium, 3 low); hygiene corrections applied | PROPOSED |
| 0.2 | 2026-09-23 | P0 resolution log added: TEC-01, FIN-01/02/04, SEC-01/02/04/05 resolved via versioned document changes and new test obligations | PROPOSED |
