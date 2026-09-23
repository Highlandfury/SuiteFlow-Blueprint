---
doc-id: GOV-RESEARCH
title: Research Register — Programme P1 (External Research and Benchmark Validation)
status: PROPOSED
version: 0.2
date: 2026-09-23
owner: Technical Lead (review at appointment; interim: Product Owner); maintained by the programme
applies-to: Programme P1 evidence for blueprint claims
depends-on: [GOV-CHARTER, GOV-ANSWERS]
---

# Research Register — Programme P1

One row per claim or question requiring external evidence. **Statuses:** `OPEN` / `IN-PROGRESS` / `PARTIAL` (capability or source confirmed; full answer pending) / `EVIDENCED` / `BLOCKED` (access or input missing) / `CONFLICTING` / `CLOSED`. Evidence classes: primary (statute, official notice, vendor official docs, local verified artefacts) / secondary. No proprietary source code is consulted (charter §15). Tax/legal conclusions remain with the engaged advisers; this register compiles sources and evidence, not legal advice.

## Collection log — pass 1 (23 Sep 2026)

| Target | Result |
|---|---|
| docs.frappe.io (Framework intro, REST API, Users and Permissions) | ✅ retrieved (official docs, updated 2026) |
| docs.erpnext.com / docs.frappe.io/erpnext | ✅ retrieved (official docs) |
| ndpc.gov.ng | ✅ retrieved (official NDPC site) |
| docs.oracle.com/en/industries/hospitality/ | ✅ retrieved (public index; capability detail not yet reviewed) |
| firs.gov.ng (2 URLs) | ❌ timeouts from this environment — needs alternate access or adviser |
| Web search provider | ❌ unavailable — direct URL retrieval only |
| Local stack inspection (site `suiteflow-night-audit-tests.localhost`, container `suiteflow-night-audit-tests-20260918-backend-1`) | ✅ `bench version`, licence files, REST probes, DocType presence checks |

## R1 — Vendor capability verification (Frappe v16 / ERPNext / Kamra)

Pinned stack observed: **frappe 16.31.0 · erpnext 16.32.3 · kamra 2.5.0 · hrms 16.16.0 · crm 1.81.2 · payments 0.0.1 · hotel_integration 0.1.0.dev0** (`bench version`, 23 Sep 2026).

| ID | Claim / question | Evidence collected | Status | Affects |
|---|---|---|---|---|
| RS-101 | Framework-generic surfaces can be inventoried and closed or mediated per ADR-009 §11 | REST `/api/resource/*` and `/api/method/*` confirmed present (unauth probes → HTTP 403); DocTypes present on the site: `File`, `Data Import`, `Data Export`, `Server Script`, `DocPerm`, `Custom DocPerm`, `User Permission`, `Role Permission for Page and Report`, `API Request Log`; official docs: auto-generated REST for all DocTypes with token/password/OAuth auth (docs.frappe.io/framework/user/en/api/rest, updated 2026-05-10) | PARTIAL — capability confirmed; closure/mediation design and full surface enumeration remain ADR-009 §11 + Programme P2 | ADR-009, SEC-MODEL §2.3, TO-SEC-001 |
| RS-102 | Record-level property/company scoping is available as defence in depth | Official docs confirm **User Permissions** restrict documents by Link-field value per user (docs.frappe.io/framework/user/en/basics/users-and-permissions, updated 2026-02-17) | PARTIAL | ADR-009, tenancy model |
| RS-103 | Worker/queue semantics support outbox + dispatch separation and idempotent consumers | Not yet verified (local workers exist; semantics review pending) | OPEN | ADR-010, data-model §7 |
| RS-104 | Audit hooks and append-only options sufficient for the independent sink | `API Request Log` DocType present; append-only sink options not yet reviewed | PARTIAL | SEC-MODEL §10 |
| RS-105 | Upgrade/migration policy and versioning support the release model | Not yet verified | OPEN | DEP-ARCH, roadmap D1 |

## R2 — OPERA public-document benchmark

| ID | Claim / question | Evidence collected | Status | Affects |
|---|---|---|---|---|
| RS-201 | Capability comparison against the frozen pilot candidate set (public documentation only) | Oracle Hospitality public documentation index reachable (`https://docs.oracle.com/en/industries/hospitality/`; Hotels section linked); capability-by-capability review not started | IN-PROGRESS | Capability map, scope |
| RS-202 | Gaps/extras vs target workflows (recorded as findings; conflicts never averaged) | Not started | OPEN | Workflows, capability priorities |

## R3 — Nigerian tax and legal primary sources (for the advisers)

| ID | Claim / question | Evidence collected | Status | Affects |
|---|---|---|---|---|
| RS-301 | VAT rate/tax point and hotel treatment | FIRS unreachable from this environment (two URL attempts timed out). No primary source retrieved — tax position remains **UNVERIFIED** | BLOCKED (access) | FIN-02/12, OQ-029 |
| RS-302 | Lagos consumption/levy obligations for hotels/restaurants | Not attempted yet (needs Lagos State source or adviser) | BLOCKED (access) | FIN-ARCH §7 |
| RS-303 | FIRS e-invoicing/fiscalisation obligations and timelines | FIRS unreachable; no primary source retrieved | BLOCKED (access) | INT-014, ADR-008 |
| RS-304 | NDPA retention/breach/transfer positions | NDPC official site captured: commission established under **Nigeria Data Protection Act 2023**; **General Application and Implementation Directive (GAID) 2025** published (`ndpc.gov.ng/wp-content/uploads/2025/07/NDP-ACT-GAID-2025-MARCH-20TH.pdf`); official **privacy breach reporting portal** (`services.ndpc.gov.ng/breach/`); controller/processor registration and audit filing services; NDP Act guide 2025 published. Retention/breach timings not yet extracted from the primary texts | PARTIAL | SEC-MODEL §14/§15, OQ-024 |
| RS-305 | CBN payment rules (settlement, MSSC) | Not attempted yet (CBN or adviser) | BLOCKED (access) | FIN-ARCH §6, OQ-006 |

## R4 — Provider evidence (for Programme P3 / first-property gate)

| ID | Claim / question | Evidence needed | Status | Affects |
|---|---|---|---|---|
| RS-401 | Payment gateway webhooks: idempotency/referenceability | Provider docs/sandbox | OPEN | OQ-030, ADR-011 |
| RS-402 | Bank statement formats/access for UBA/Wema | Bank evidence (property stage) | OPEN (deferred) | OQ-007 |
| RS-403 | POS/acquirer settlement-report formats | Acquirer evidence (property stage) | OPEN (deferred) | OQ-006 |
| RS-404 | Door-lock vendor APIs | Vendor docs | OPEN (deferred) | OQ-020, INT-010 |

## R5 — Operational/NFR benchmark sanity

| ID | Claim / question | Evidence needed | Status | Affects |
|---|---|---|---|---|
| RS-501 | Performance budgets (P-1…P-9) plausible at 200-room scale | Public benchmarks + synthetic volume model | OPEN (public benchmark review pending) | NFR §2 |
| RS-502 | 99.5%/99.9% availability and 1 h RPO/RTO practice | Public references + pilot drill evidence | OPEN | NFR A-1…A-3 |

## R6 — Licence/SBOM inventory

**EVIDENCED (local artefact inspection, 23 Sep 2026).** Sources: `apps/*/license.txt`, `apps/*/LICENSE`, `package.json`, `README.md` in the pinned bench.

| Component | Version | Licence | Source artefact |
|---|---|---|---|
| frappe | 16.31.0 | **MIT** | `frappe/LICENSE`; `package.json: "license": "MIT"` |
| erpnext | 16.32.3 | **GPL-3.0** | `erpnext/license.txt`; `package.json: "license": "GPL-3.0"` |
| hrms | 16.16.0 | **GPL-3.0** | `hrms/license.txt`; `package.json` |
| crm | 1.81.2 | **GPL-3.0** | `crm/LICENSE`; `package.json` |
| kamra | 2.5.0 | **AGPL-3.0** | `kamra/license.txt` (GNU AFFERO GPL); README: "AGPL-licensed" |
| payments | 0.0.1 | **MIT** | `payments/license.txt` ("The MIT License") |
| hotel_integration | 0.1.0.dev0 | **undeclared** | no licence file or manifest declaration found |

**Findings.**

- **LIC-01 (High):** Kamra is **AGPL-3.0** — network copyleft. SaaS-hosted operation generally does not trigger distribution obligations, but **on-premise property deployment is distribution** and AGPL imposes source-offer obligations on the distributed work. Counsel must rule before any on-premise release (feeds C3/OQ-028, RSK-LIC-001).
- **LIC-02 (Medium):** `hotel_integration` declares no licence. A licence and third-party notice policy are required before distribution; SBOM/attribution generation must include it.
- Interpretation is deliberately not asserted here; licence *consequences* are counsel's (engagement brief §3). The inventory is input evidence.

## Programme P1 gate evidence

Register complete with statuses; every material blueprint claim carries a source or an explicit UNVERIFIED label; ASSUMED claims upgraded to VERIFIED or recorded CONFLICTING; owners per section; reviewed by the Technical Lead at appointment. The gate cannot close while R3 conclusions remain with the advisers (expected inside the P1–P2 envelope, by Week 8). **Pass 1 closed:** R1 partial, R2 started, R3 partial/blocked-access, R6 evidenced; R4/R5 and FIRS/CBN access remain.

## Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Research register opened: R1–R6 with initial questions and owners | PROPOSED |
| 0.2 | 2026-09-23 | Pass 1 evidence collected: vendor surface probes + official docs (R1), Oracle index (R2), NDPC sources (R3), full licence inventory with LIC-01/LIC-02 findings (R6); FIRS/CBN access blocked from this environment | PROPOSED |
