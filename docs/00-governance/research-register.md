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

**Public sources (OPERA Cloud Services 26.3):** Release Readiness Guide feature summary; User Guide; Security Guide; Compatibility Matrix; Network Guidelines; Licensing Information User Manual; Reporting & Analytics; Distribution; Fiscal Regulatory Compliance; ID Document Scanning Interface; Validated Interfaces list. Integration surfaces documented via the Oracle Hospitality Integration Platform (developer portal).

**Capability areas observed in the public feature set (release 26.3):** Accounts Receivable, Block Management, Cashiering & Financials, Client Relations (profiles), Country-Specific compliance, Distribution, Events (space), Exports, Front Desk (keys, eSign registration), Integrations (async API queue UI), Membership/Loyalty, Mobile, Property APIs, Reports (forecast, holding ledger for revenue recognition, manager report), Reservations (sales screen, sharing, age thresholds), Role Manager (task-level permissions), Toolbox (imbalance analysis, AR transfer invoices utility).

**Benchmark observations vs the SuiteFlow target (requirements-level only; no implementation copying):**

| OPERA public pattern observed | SuiteFlow counterpart |
|---|---|
| Per-property **feature controls** delivered disabled with documented "steps to enable" | Property-configurable capabilities with effective dates (BR-PLT-001) — consistent; SuiteFlow config governance is stricter (change control + audit) |
| **Role Manager task-level permissions** for screens/utilities | Role catalogue + authority matrix + explicit permission areas — consistent direction |
| **Business events** capturing user name on posting create/update/delete for auditability | Append-only audit + posting-link evidence — consistent; SuiteFlow adds hash-chained sink (SEC-13) |
| **PAN restrictions** in free-text/check-number/profile fields with removal logging | SuiteFlow stores no PAN at all (BR-PAY-007) — stronger baseline |
| **Property-specific PII storage/visibility controls** and profile identification retention | Class-A handling + retention schedule (OQ-024) — consistent; SuiteFlow adds per-category retention config |
| **Pay by Link** for batch deposits + payment-status check | OQ-005/ADR-011: enterprise-target capability; pilot at most one provider path — consistent phasing |
| **Holding ledger for revenue recognition** report | SuiteFlow revenue-recognition postings + reconciliation suite — reporting equivalent to assess in the report catalogue (potential RPT addition) |
| **AR transfer invoices utility** for internal settlement | SuiteFlow zero-revenue direct-bill transfer (ADR-005) — same concept, already designed |
| **Async queue UI** for monitoring API requests | Queue/observability surfaces in DEP-ARCH + NFR observability — consistent |
| **Package/total-rate tax calculation** options (net/gross bases, per-night) | Tax engine with per-tax-type computation order and tax points (FIN-ARCH §7) — consistent |

| ID | Claim / question | Status | Next |
|---|---|---|---|
| RS-201 | Capability comparison against the frozen pilot candidate set | PARTIAL — public doc structure and notable patterns captured; systematic per-capability pass pending | Walk the frozen pilot candidate list against the public guide areas; record gaps/extras as findings |
| RS-202 | Gaps/extras vs target workflows (conflicts recorded, never averaged) | OPEN | Workflow-level comparison in Programme P2/P3 with the Technical Lead |

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
