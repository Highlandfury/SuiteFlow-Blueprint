---
doc-id: GOV-P2PLAN
title: Programme P2 — Current-State Audit Plan
status: PROPOSED
version: 0.4
date: 2026-09-23
owner: Technical Lead (audit owner at appointment); interim: Product Owner; prepared under delegated PO direction
applies-to: current-state audit of the implementation and ecosystem (charter §14 P2)
depends-on: [GOV-CHARTER, GOV-REVIEW, GOV-RESEARCH, GOV-INPUTS]
---

# Programme P2 — Current-State Audit Plan

## 0. Execution status

**Entered 23 Sep 2026 under P0 conditional completion** (GOV-PO-DEC §G). Workspace: `/home/suiteflow/suiteflow-audit` (separate from both repositories, per charter). **All ten passes complete 24 Sep 2026.** Baseline pinned (`c8daa6440d7d`; image sha256:f1fb23…); inventory generated (71 DocTypes, 4 pages, 73 tests, 19 patches, 20 docs); **204/204 capabilities classified** (82 verified / 95 partial / 7 defective / 20 missing); app suite 399 tests (381 OK); **79 findings (2 critical, 34 high, 38 medium, 5 low)**; audit report issued (`report.md`) — **pending Technical Lead review at appointment**. Obligations OB-01…OB-14 tracked; P3 gap predicates listed in the report §6.

## 1. Purpose and gate

Programme P2 audits the implementation **as evidence about the present**; it is never a specification (charter §3). **Gate to exit (charter §14):** evidence-based capability classifications — every frozen pilot capability classified with cited evidence, the inputs-register IMPL-ADR/DP statuses re-verified with commit evidence, and the audit report reviewed by the Technical Lead at appointment. Output feeds the P3 gap matrix. **Findings only: no fixes during the audit** (fixes become P4/D-phase tickets).

## 2. Scope

**In scope:**
- Implementation repository `Highlandfury/SuiteFlow`, baseline-pinned at audit start: `hotel_integration` app (71 DocType JSONs, 272 Python modules), 73 test files, 19 Kamra patches, scripts, config, docker/compose, `Makefile`, 20 docs, `uat-baselines`.
- Installed stack (pinned): frappe 16.31.0 · erpnext 16.32.3 · kamra 2.5.0 · hrms 16.16.0 · crm 1.81.2 · payments 0.0.1 (plus `hotel_integration` 0.1.0.dev0).
- Running dev/UAT stacks — **read-only queries only**; existing evidence packs and proof runs (e.g. `suiteflow-proof/adr002`, `proofs`, browser-test evidence).
- Recorded DP/IMPL-ADR inputs and validation outcomes (inputs register §4).

**Out of scope:** any change/fix; production systems (none exist); vendor internals; proprietary material (charter §15); re-audit of the blueprint.

## 3. Evidence rules

- **Baseline pins:** commit SHA(s), image digests, site/DB identifiers, container versions — recorded at start and re-checked at report.
- Every finding cites evidence: `path:line`, commit SHA, test log, query output or screenshot, with timestamp.
- Confidence labels per charter §6 (`VERIFIED`/`INFERRED`/`ASSUMED`/`UNVERIFIED`/`CONFLICTING`); conflicting evidence is recorded, never averaged.
- **Read-only discipline:** no writes to the implementation repo during audit; site/DB access read-only; tests run only in scratch/UAT copies; backups taken before any mutating test run.
- Evidence register template: `CAP-ID | requirement ref | artifacts | evidence | classification | confidence | notes`; separate rows for IMPL-ADR re-verifications.

## 4. Classification rubric

| Classification | Evidence test |
|---|---|
| Implemented-and-verified | Behaviour matches the target requirement (levels 1–4) and is evidenced by passing tests plus manual/role-play evidence |
| Partial | Some behaviour exists; specific requirement gaps evidenced |
| Defective | Behaviour exists but violates an invariant/business rule; failing or absent control evidenced |
| Architecturally-wrong | Works locally but contradicts target ownership, tenancy, temporal or authorization decisions |
| Duplicated | Two surfaces or ownerships for one datum; overlapping implementations |
| Missing | No implementation evidence for a target capability |
| Not-in-scope | Enterprise/phase capability not expected yet (per the frozen pilot set) |

Also recorded separately: **unsanctioned behaviour** (not in the target), **test debt**, **config debt**.

## 5. Audit units and sampling

- **Priority 1:** the 204 frozen pilot candidates — all mapped and classified.
- **Priority 2:** the 76 enterprise-delivery capabilities — inventory-level only (existence/overlap).
- **Priority 3:** the 3 pending question capabilities — noted, not classified.
- Every finding ties to a `CAP-ID` or invariant ID; implementation without a target tie is recorded as *unsanctioned/legacy* for P3/P4.

## 6. Audit passes

| # | Pass | Core evidence |
|---|---|---|
| 1 | Inventory baseline | Commit/image pins, site list, compose/env config |
| 2 | Capability mapping | CAP-ID → DocTypes, pages, APIs, patches, tests |
| 3 | Functional evidence | Test runs + role-played walkthroughs on UAT (keyboard/scope checks) |
| 4 | Financial evidence | Posting paths, idempotency, reconciliation outputs, golden-day runs |
| 5 | Security/scope evidence | **Framework-generic surface enumeration (ADR-009 §11)**, permission layers, UAT account state (SEC-05), class-A handling |
| 6 | Concurrency/reliability | Race/retry evidence, queue semantics, restore drills; vulnerability baseline reassessment (SEC-14) |
| 7 | Deployment/upgrade evidence | Release/image promotion, forward-only migrations, config/secrets handling |
| 8 | Data/migration evidence | Scope columns, backfill scripts, single-property assumptions (RSK-MIG-002) |
| 9 | Test-coverage assessment | 73 test files vs the TO registry |
| 10 | Documentation evidence | 20 repo docs vs the documentation architecture |

Passes produce evidence-register rows and findings. Two adversarial (ASTRA-style) passes challenge the audit conclusions before sign-off.

## 7. Obligations carried into P2

- ADR-009 §11: enumerate and grade every framework-generic surface (REST, report builder, attachments, imports/exports, Desk search) — pass 5.
- Re-verify IMPL-ADR-001…004 observed statuses with commit evidence (inputs register §4.2).
- Test single-site assumptions across identity, configuration, numbering and accounting (RSK-MIG-002) — pass 8.
- Confirm live UAT role-account controls (SEC-05) — pass 5.
- Re-run the vulnerability baseline assessment (SEC-14) — pass 6.
- Confirm the golden-day fixture covers the adopted finance decisions (no-show with/without deposit, tax-inclusive extraction, reconciliation checks 12–14, service charge) — pass 4.

## 8. Roles and governance

- **Audit owner:** Technical Lead (at appointment); interim: Product Owner delegation.
- Passes executed by the role assistants (finance/technical/security personas) with human review; **no audit content is an approval**.
- **Independence:** the audit does not implement; fixes are separate tickets under change control.
- **Reporting:** current-state audit report recorded separately from the blueprint; classification table; evidence index; findings register; P3-ready gap predicates.

## 9. Timeline (indicative, inside the P1–P2 envelope)

| Stage | Content |
|---|---|
| Entry | P0 acceptance or conditional completion (scope freeze effective) |
| Week A | Baseline pin; inventory; mapping plan |
| Weeks B–C | Passes 3–8 evidence collection |
| Week D | Coverage/documentation passes; adversarial passes; audit report |

If appointments are delayed, interim role assistants execute the passes; **sign-off waits for appointed humans.**

## 10. Risks

| Risk | Control |
|---|---|
| Audit becomes a fix session | Findings-only rule; fixes ticketed separately |
| Evidence gaps (missing history/config) | Record as gaps with confidence labels; no inference |
| Live-site drift during audit | Baseline pins; read-only access; re-check pins at report |
| Bias toward current implementation | Requirement refs cited from the frozen blueprint; charter §3 hierarchy enforced |
| Time-box erosion | Priority-1 pilot set first; depth proportional to P3 needs |

## 11. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial P2 audit plan prepared under delegated PO direction (scope, evidence rules, rubric, passes, obligations, timeline) | PROPOSED |
| 0.2 | 2026-09-23 | Audit entered under conditional completion; workspace created; pass 1 (baseline + inventory) complete | PROPOSED |
| 0.3 | 2026-09-24 | Pass 2 complete: 204/204 capabilities mapped; 40 findings recorded | PROPOSED |
| 0.4 | 2026-09-24 | All passes 1–10 complete; audit report issued (79 findings); pending TL review | PROPOSED |
