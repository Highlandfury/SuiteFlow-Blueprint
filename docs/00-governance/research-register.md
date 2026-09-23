---
doc-id: GOV-RESEARCH
title: Research Register — Programme P1 (External Research and Benchmark Validation)
status: PROPOSED
version: 0.3
date: 2026-09-23
owner: Technical Lead (review at appointment; interim: Product Owner); maintained by the programme
applies-to: Programme P1 evidence for blueprint claims
depends-on: [GOV-CHARTER, GOV-ANSWERS]
---

# Research Register — Programme P1

One row per claim or question requiring external evidence. **Statuses:** `OPEN` / `IN-PROGRESS` / `PARTIAL` (capability or source confirmed; full answer pending) / `EVIDENCED` / `BLOCKED` (access or input missing) / `CONFLICTING` / `CLOSED`. Evidence classes: primary (statute, official notice, vendor official docs, local verified artefacts) / secondary. No proprietary source code is consulted (charter §15); benchmark docs are used to derive requirements, never implementations. Tax/legal conclusions remain with the engaged advisers.

## Collection log

### Pass 1 (23 Sep 2026)

| Target | Result |
|---|---|
| docs.frappe.io (Framework intro, REST API, Users and Permissions) | ✅ retrieved (official docs, updated 2026) |
| docs.erpnext.com / docs.frappe.io/erpnext | ✅ retrieved |
| ndpc.gov.ng | ✅ retrieved |
| docs.oracle.com/en/industries/hospitality/ | ✅ retrieved |
| firs.gov.ng (2 URLs) | ❌ timeouts — alternate access or adviser required |
| Web search provider | ❌ unavailable — direct URL retrieval only |
| Local stack inspection (site `suiteflow-night-audit-tests.localhost`) | ✅ versions, licences, REST probes, DocType checks |

### Pass 2 (23 Sep 2026)

| Target | Result |
|---|---|
| OPERA Cloud Services 26.3 — Release Readiness Guide (`oprnc/c_feature_summary.htm`) | ✅ retrieved (feature summary + enablement detail) |
| OPERA Cloud Services 26.3 — User Guide TOC (`ocsuh/ch_getting_started.htm`) | ✅ retrieved (guide structure; Integration Platform developer portal referenced) |
| docs.frappe.io/framework/user/en/database-migrations | ✅ retrieved (migration/patch system, updated 2026-02-17) |
| Two guessed Frappe doc URLs (`background-jobs`, `bench/reference/commands`) | ❌ 404 — direct-URL discovery is limited without search |

### Pass 3 (23 Sep 2026)

| Target | Result |
|---|---|
| OPERA Cloud 26.3 "All Books" index (`books.html`) | ⚠️ page returned without a book list (dynamically rendered) — limitation recorded |
| Frozen pilot capability set extracted locally (204 candidates across 22 domains) | ✅ domain walk completed against the public OPERA areas; five candidate findings (R2-F1…F5) recorded |
| Fixed/recurring-charge coverage check in the blueprint | ✅ BR-NAU-003 (recurring charges idempotent) and CAP-RSV-016 (packages/fixed charges) confirmed present |

## R1 — Vendor capability verification (Frappe v16 / ERPNext / Kamra)

Pinned stack observed: **frappe 16.31.0 · erpnext 16.32.3 · kamra 2.5.0 · hrms 16.16.0 · crm 1.81.2 · payments 0.0.1 · hotel_integration 0.1.0.dev0** (`bench version`).

| ID | Claim / question | Evidence collected | Status | Affects |
|---|---|---|---|---|
| RS-101 | Framework-generic surfaces can be inventoried and closed or mediated per ADR-009 §11 | REST `/api/resource/*`, `/api/method/*` present (unauth probes → HTTP 403); DocTypes present: `File`, `Data Import`, `Data Export`, `Server Script`, `DocPerm`, `Custom DocPerm`, `User Permission`, `Role Permission for Page and Report`, `API Request Log`; official docs confirm auto-generated REST for all DocTypes with token/password/OAuth auth | PARTIAL — capability confirmed; closure/mediation design remains ADR-009 §11 + Programme P2 | ADR-009, SEC-MODEL §2.3, TO-SEC-001 |
| RS-102 | Record-level property/company scoping available as defence in depth | Official docs confirm **User Permissions** restrict documents by Link-field value per user | PARTIAL | ADR-009, tenancy model |
| RS-103 | Worker/queue semantics support outbox + dispatch separation and idempotent consumers | **EVIDENCED (capability):** Redis-backed queue configured (`redis_queue` set); `RQ Job` DocType present with `status`/`job_id`; `enqueue`/`enqueue_doc`/`get_queues` APIs present; dedicated `queue-short`/`queue-long` worker services in the deployment topology | EVIDENCED (transport) — event immutability + separate dispatch/attempt state remain application design per ADR-010 | ADR-010, data-model §7 |
| RS-104 | Audit hooks and append-only options sufficient for the independent sink | `Version`, `Activity Log`, `Access Log`, `Webhook Request Log`, `Error Log`, `API Request Log` all present; append-only/WORM sink options not yet reviewed | PARTIAL | SEC-MODEL §10 |
| RS-105 | Upgrade/migration policy supports the release model | Migration system documented: `bench migrate`, pre/post model-sync patches, hash-based schema sync, **no reverse schema migrations** (docs.frappe.io/framework/user/en/database-migrations) — informs the DEP-ARCH forward-only migration policy; upstream release/support-window policy still to confirm | PARTIAL | DEP-ARCH, roadmap D1 |

## R2 — OPERA Cloud public benchmark

**Public sources (OPERA Cloud Services 26.3):** Release Readiness Guide feature summary; User Guide; Security Guide; Compatibility Matrix; Network Guidelines; Licensing Information User Manual; Reporting & Analytics; Distribution; Fiscal Regulatory Compliance; ID Document Scanning Interface; Validated Interfaces list. Integration surfaces documented via the Oracle Hospitality Integration Platform (developer portal). *Limitation recorded:* the "All Books" index page is dynamically rendered and did not return a book list; per-capability depth rests on the User Guide/feature summary and needs the Programme P2/P3 pass with the Technical Lead.

**Domain walk of the frozen pilot set (204 candidates) against public OPERA areas — requirements-level only:**

| SuiteFlow domain (pilot caps) | OPERA public evidence area | Observation |
|---|---|---|
| RSV Reservations (19) | Reservations / Reservation Sales Screen (age thresholds, copy, sharing, attachments, rooming lists) | Consistent; OPERA's age-threshold control maps to the OQ-035 child policy (already adopted). |
| AVL Availability (7) | Blocks/inventory (borrow availability, blocks with room inventory) | Consistent; SuiteFlow block holds/cutoffs cover the same concept. |
| RTM Rates (11) | Rate management (mass actions, pricing schedules, package copy, total-package tax) | Consistent; OPERA's total-package-rate tax calculation adds a worked-example candidate for the tax engine (package base net/gross). |
| PM Property (11) | Rooms/types, OOO/OOS, key encoders | Consistent; lock integration deferred per OQ-020. |
| FO Front office (18) | Front Desk (keys, eSign registration card, room moves, queues) | Consistent; eSign registration is a later UX enhancement candidate (SuiteFlow registration evidence exists in paper/scan form). |
| HSK Housekeeping (9) | Housekeeping task sheets, mobile | Consistent. |
| MNT Maintenance (5) | *(Not in this public set — OPERA maintenance sits with a separate product)* | SuiteFlow's light PM pilot scope is an extra relative to this doc set; consistent with the adopted answer (OQ-022). |
| FOL Folio (13) | Cashiering & Financials, folio history, routing, comp folios (multiple authorizers with limits) | Consistent; multi-authorizer comp routing with combined limits is a depth candidate for routing instructions. |
| CSH Cashiering (10) | Cashiering (additional charges, simplified settlement, payment instructions) | Consistent; OPERA's conditional "additional charges" at End of Day expand the recurring-charge depth (see R2-F3). |
| INA Income audit (7) | Toolbox imbalance analysis | Consistent (SuiteFlow income-audit controls already richer). |
| NAU Night audit (10) | End of Day (room & tax + additional charges) | Consistent; BR-NAU-003 covers recurring-charge idempotency. |
| ACC Accounting (12) | Accounts Receivable (transfer invoices), holding ledger | Mostly consistent; **holding ledger for revenue recognition is a report-catalogue candidate (R2-F1)**. |
| POS Outlets (10) | Restaurants (separate docs); POS interfaces | Consistent; external POS integration deferred per MVP. |
| GRP Groups (9) | Block Management (pre-filled rooming lists XLS/CSV, statistics, mass borrow) | Consistent; **rooming-list import is a candidate depth item (R2-F2)**. |
| CRP Corporate (6) | Accounts Receivable / Client Relations | Consistent. |
| GST Guests (10) | Client Relations (address validation, property-specific PII storage/visibility, retention) | Consistent; SuiteFlow has stricter Class-A retention design; address auto-validation is a later-provider candidate. |
| CRM (3) | Membership/Loyalty; mobile guest experience | Consistent (minimal consent projection at pilot; loyalty at D12). |
| RPT Reporting (8) | Reports (28-day/yearly forecasts, manager report) + R+A product | Consistent; forecasting/BI deferred to D15. |
| INV Inventory (6) | Materials Control (separate product) | SuiteFlow light stock control is deliberately lighter at pilot; consistent. |
| INT Integrations (3 pilot) | Integration Platform (OHIP portal, async queue UI, validated interfaces) | Consistent; SuiteFlow's provider-neutral adapter with capability flags matches the OHIP-style separation. |
| HRM HR boundary (2) | Role Manager task permissions | Consistent (identity boundary only at pilot). |
| PLT Platform (15) | OPERA Controls, Role Manager, Business Events, Page Composer | Consistent; OPERA's off-by-default controls with enable steps validate SuiteFlow's effective-dated, audited configuration governance. |

**Benchmark findings (candidates for assessment, not commitments):**

- **R2-F1** — *Holding-ledger style revenue-recognition reporting*: assess against the 22-report catalogue (finance + reporting pass).
- **R2-F2** — *Rooming-list import (XLS/CSV)*: assess as a group-workflow depth item in GRP-003.
- **R2-F3** — *Conditional recurring/additional charges* (conditions, amount bases, transaction code selection): assess against BR-NAU-003/CAP-RSV-016 depth.
- **R2-F4** — *Comp folio multi-authorizer routing with combined limits*: assess in folio routing depth.
- **R2-F5** — *Capability enablement documentation*: OPERA documents "steps to enable" per feature; SuiteFlow's documentation architecture should carry an equivalent per-configurable-capability enablement note (docs pass).

| ID | Claim / question | Status | Next |
|---|---|---|---|
| RS-201 | Capability comparison against the frozen pilot candidate set | **PARTIAL — domain walk complete** for all 22 pilot domains; five candidate findings recorded; per-capability depth pending | Per-capability depth with the Technical Lead at appointment |
| RS-202 | Gaps/extras vs target workflows (conflicts recorded, never averaged) | PARTIAL — domain-level gaps/extras captured; workflow-level comparison pending | Programme P2/P3 |

## R3 — Nigerian tax and legal primary sources (for the advisers)

| ID | Claim / question | Evidence collected | Status | Affects |
|---|---|---|---|---|
| RS-301 | VAT rate/tax point and hotel treatment | FIRS unreachable from this environment (2 attempts). No primary source — **UNVERIFIED** | BLOCKED (access) | FIN-02/12, OQ-029 |
| RS-302 | Lagos consumption/levy obligations | Not attempted (needs Lagos State source or adviser) | BLOCKED (access) | FIN-ARCH §7 |
| RS-303 | FIRS e-invoicing/fiscalisation obligations | FIRS unreachable | BLOCKED (access) | INT-014, ADR-008 |
| RS-304 | NDPA retention/breach/transfer positions | NDPC official site: NDPA 2023; **GAID 2025** published; official **breach-reporting portal**; controller/processor registration + audit filing; NDP Act guide. Retention/breach timings not yet extracted | PARTIAL | SEC-MODEL §14/§15, OQ-024 |
| RS-305 | CBN payment rules | Not attempted (CBN or adviser) | BLOCKED (access) | FIN-ARCH §6, OQ-006 |

## R4 — Provider evidence (Programme P3 / first-property gate)

| ID | Claim / question | Status | Affects |
|---|---|---|---|
| RS-401 | Payment gateway webhooks: idempotency/referenceability | OPEN | OQ-030, ADR-011 |
| RS-402 | Bank statement formats/access for UBA/Wema | OPEN (deferred) | OQ-007 |
| RS-403 | POS/acquirer settlement-report formats | OPEN (deferred) | OQ-006 |
| RS-404 | Door-lock vendor APIs | OPEN (deferred) | OQ-020, INT-010 |

## R5 — Operational/NFR benchmark sanity

| ID | Claim / question | Status | Next |
|---|---|---|---|
| RS-501 | Performance budgets (P-1…P-9) plausible at 200-room scale | OPEN — public benchmark sources not yet located (OPERA docs are functional, not performance) | Synthetic volume model + public hosting benchmarks |
| RS-502 | 99.5%/99.9% availability and 1 h RPO/RTO practice | OPEN | Public references + D9 drill evidence |

## R6 — Licence/SBOM inventory

**EVIDENCED (local artefact inspection, 23 Sep 2026)** — sources: `apps/*/license.txt`, `apps/*/LICENSE`, `package.json`, `README.md`.

| Component | Version | Licence | Source artefact |
|---|---|---|---|
| frappe | 16.31.0 | **MIT** | `frappe/LICENSE`; `package.json` |
| erpnext | 16.32.3 | **GPL-3.0** | `erpnext/license.txt`; `package.json` |
| hrms | 16.16.0 | **GPL-3.0** | `hrms/license.txt`; `package.json` |
| crm | 1.81.2 | **GPL-3.0** | `crm/LICENSE`; `package.json` |
| kamra | 2.5.0 | **AGPL-3.0** | `kamra/license.txt` (GNU AFFERO GPL); README |
| payments | 0.0.1 | **MIT** | `payments/license.txt` |
| hotel_integration | 0.1.0.dev0 | **undeclared** | no licence file or manifest declaration |

**Findings.** **LIC-01 (High):** Kamra is AGPL-3.0 — network copyleft; SaaS operation generally fine, **on-premise deployment is distribution** with source-offer obligations; counsel ruling required (C3, RSK-LIC-001). **LIC-02 (Medium):** `hotel_integration` declares no licence — fix before distribution; include in SBOM/attribution.

## Programme P1 gate evidence

Register complete with statuses; every material blueprint claim carries a source or an explicit UNVERIFIED label; ASSUMED claims upgraded to VERIFIED or recorded CONFLICTING; owners per section; reviewed by the Technical Lead at appointment. The gate cannot close while R3 conclusions remain with the advisers (expected inside the P1–P2 envelope, by Week 8). **Progress:** R1 largely evidenced (RS-103 evidenced; RS-101/102/104/105 partial), R2 in progress with public sources and pattern table, R3 partial/access-blocked, R4 deferred, R5 open, R6 evidenced.

## Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Research register opened: R1–R6 with initial questions and owners | PROPOSED |
| 0.2 | 2026-09-23 | Pass 1: vendor surface probes + official docs, Oracle index, NDPC sources, licence inventory (LIC-01/LIC-02); FIRS/CBN access blocked | PROPOSED |
| 0.3 | 2026-09-23 | Pass 2: queue/migration audit evidence (RS-103 evidenced, RS-105 partial); OPERA Cloud 26.3 public benchmark captured with pattern comparison table (RS-201 partial) | PROPOSED |
