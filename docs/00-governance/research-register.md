---
doc-id: GOV-RESEARCH
title: Research Register — Programme P1 (External Research and Benchmark Validation)
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Technical Lead (review at appointment; interim: Product Owner); maintained by the programme
applies-to: Programme P1 evidence for blueprint claims
depends-on: [GOV-CHARTER, GOV-ANSWERS]
---

# Research Register — Programme P1

One row per claim or question requiring external evidence. **Statuses:** `OPEN` / `IN-PROGRESS` / `EVIDENCED` / `CONFLICTING` / `CLOSED`. Evidence classes: primary (statute, official notice, vendor official docs) / secondary (analyst, press). No proprietary source code is consulted (charter §15). Tax/legal conclusions remain with the engaged advisers (see `adviser-briefs-2026-09-23.md`); this register compiles sources and evidence, not legal advice.

## R1 — Vendor capability verification (Frappe v16 / ERPNext / Kamra)

| ID | Claim / question | Evidence needed | Owner | Status | Affects |
|---|---|---|---|---|---|
| RS-101 | Framework-generic surfaces (REST `/api/resource`, report builder, attachments, imports/exports, Desk search) can be closed or mediated per ADR-009 §11 | Official docs + platform inspection evidence | Technical Lead (interim PO) | IN-PROGRESS | ADR-009, SEC-MODEL §2.3, TO-SEC-001 |
| RS-102 | Property/company scope enforcement mechanisms available at record level (defence in depth) | Official docs | Technical Lead | OPEN | ADR-009, tenancy model |
| RS-103 | Worker/queue semantics support outbox + dispatch separation and idempotent consumers | Official docs | Technical Lead | OPEN | ADR-010, data-model §7 |
| RS-104 | Audit hooks and append-only options sufficient for the independent sink requirement | Official docs | Security (interim PO) | OPEN | SEC-MODEL §10 |
| RS-105 | Upgrade/migration policy and versioning support the release model | Official docs | Technical Lead | OPEN | DEP-ARCH, roadmap D1 |

## R2 — OPERA public-document benchmark

| ID | Claim / question | Evidence needed | Owner | Status | Affects |
|---|---|---|---|---|---|
| RS-201 | Capability comparison against the frozen pilot candidate set (public documentation only) | Published capability docs | Product Owner (delegated) | OPEN | Capability map, scope |
| RS-202 | Gaps/extras vs target workflows (recorded as findings; conflicts never averaged) | Public docs | Product Owner | OPEN | Workflows, capability priorities |

## R3 — Nigerian tax and legal primary sources (for the advisers)

| ID | Claim / question | Evidence needed | Owner | Status | Affects |
|---|---|---|---|---|---|
| RS-301 | VAT rate/tax point and hotel treatment | FIRS/statutory text | Tax adviser (sources compiled by programme) | OPEN | FIN-02/12, OQ-029 |
| RS-302 | Lagos consumption/levy obligations for hotels/restaurants | State law/guidance | Tax adviser | OPEN | FIN-ARCH §7 |
| RS-303 | FIRS e-invoicing/fiscalisation obligations and timelines | Official notices | Tax adviser | OPEN | INT-014, ADR-008 |
| RS-304 | NDPA retention/breach/transfer positions | NDPA + NDPC guidance | Counsel | OPEN | SEC-MODEL §14/§15, OQ-024 |
| RS-305 | CBN payment rules (settlement, MSSC) | CBN guidance | Finance (interim PO) | OPEN | FIN-ARCH §6, OQ-006 |

## R4 — Provider evidence (for Phase 3 / first-property gate)

| ID | Claim / question | Evidence needed | Owner | Status | Affects |
|---|---|---|---|---|---|
| RS-401 | Payment gateway webhooks: idempotency/referenceability | Provider docs/sandbox | Integration (interim) | OPEN | OQ-030, ADR-011 |
| RS-402 | Bank statement formats/access for UBA/Wema | Bank evidence (property stage) | Finance | OPEN (deferred) | OQ-007 |
| RS-403 | POS/acquirer settlement-report formats | Acquirer evidence (property stage) | Finance | OPEN (deferred) | OQ-006 |
| RS-404 | Door-lock vendor APIs | Vendor docs | Operations | OPEN (deferred) | OQ-020, INT-010 |

## R5 — Operational/NFR benchmark sanity

| ID | Claim / question | Evidence needed | Owner | Status | Affects |
|---|---|---|---|---|---|
| RS-501 | Performance budgets (P-1…P-9) plausible at 200-room scale | Public benchmarks + synthetic volume model | Technical Lead | OPEN | NFR §2 |
| RS-502 | 99.5%/99.9% availability and 1 h RPO/RTO practice | Public references + pilot drill evidence | SRE | OPEN | NFR A-1…A-3 |

## R6 — Licence/SBOM inventory

| ID | Claim / question | Evidence needed | Owner | Status | Affects |
|---|---|---|---|---|---|
| RS-601 | Component licence classification and copyleft position | SBOM + licence texts | Counsel (inventory by programme) | OPEN | OQ-028, RSK-LIC-001 |

## Gate evidence (Programme P1 exit)

Register complete with statuses; every material blueprint claim carries a source or an explicit UNVERIFIED label; ASSUMED claims upgraded to VERIFIED or recorded CONFLICTING; owners per section; reviewed by the Technical Lead at appointment. The gate cannot close while R3 conclusions remain with the advisers (expected inside the P1–P2 envelope, by Week 8).

## Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Research register opened: R1–R6 with initial questions and owners | PROPOSED |
