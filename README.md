# SuiteFlow Target-State Blueprint

**Status:** Active — Phase 0 (Target Product Definition)
**Date opened:** 23 September 2026
**Owner:** SuiteFlow programme (Product Owner accountable)

This repository is the canonical specification for SuiteFlow: the hotel platform we *should* build, designed from hospitality requirements, enterprise PMS capability benchmarks, accounting principles, security principles and real hotel operating workflows.

It is deliberately separate from the implementation repository [`Highlandfury/SuiteFlow`](https://github.com/Highlandfury/SuiteFlow). That repository contains the current implementation: the `hotel_integration` Frappe application, compatibility automation, patches and UAT evidence. The implementation is **evidence about the present**, not a specification for the future.

## Why this repository exists

Enterprise software fails when the implementation defines the product. SuiteFlow's mandate is the opposite order:

```text
TARGET STATE  (this repository)
      ↓
CURRENT STATE (implementation repository, audited later)
      ↓
GAP
      ↓
TRANSITION ARCHITECTURE
      ↓
IMPLEMENTATION ROADMAP
```

The target blueprint is authored before, and independently of, any inspection of current code, databases, DocTypes, screens or workflows. Current-state analyses, when they happen (Phase 2), are recorded separately and compared against this blueprint — never used to silently redefine it.

## What belongs here

- Product vision, scope, capability map, roadmap
- Target domain model, data model and state machines
- Target workflows for reservations, front desk, housekeeping, maintenance, finance and night audit
- Target financial, security, integration, reporting, UX, QA, deployment and documentation architectures
- Architecture Decision Records (ADRs) issued at blueprint level
- Governance: charter, inputs register, glossary, risk register, open questions

## What does not belong here

- Application code, DocType definitions, SQL, patches or infrastructure configuration
- Confidential or proprietary material from any commercial vendor
- Claims about the current SuiteFlow implementation (those belong to Phase 2 evidence records)

## Related documents outside this repository

| Document | Location | Role |
|---|---|---|
| Product Owner Kickoff Decision Log v0.2 | `/home/suiteflow/docs/` (programme machine) | Accepted business requirement input, recorded in the inputs register |
| Architecture and Financial Control Decision Pack v0.1 | `/home/suiteflow/docs/` | Proposed architecture input, subject to blueprint validation |
| Product Owner Baseline | `Highlandfury/SuiteFlow` → `docs/` | Accepted pilot baseline, recorded in the inputs register |
| Implementation repository | `Highlandfury/SuiteFlow` | Current-state implementation and evidence (Phase 2 audit target) |

## How to read this repository

Start with [`docs/00-governance/charter.md`](docs/00-governance/charter.md) for the status model, source-of-truth hierarchy and change control. Every document carries front matter declaring its status, version and confidence labels. Anything not marked `ACCEPTED` is not yet authority.

## Current phase

**Phase 0 complete — WP 0.8 Blueprint Consolidation delivered; awaiting Product Owner acceptance. Pilot redefined (23 Sep 2026) as a synthetic reference pilot — no real property; the Product Owner acts as operations authority and acceptance approver.**

- `docs/TARGET-STATE-BLUEPRINT.md` v1.2 (PROPOSED): consolidated master index with inventory, architecture summaries, acceptance model (reference pilot + First-Property Deployment Gate), the Phase 0 exit gate and next steps.
- `product/roadmap.md` v1.1: full roadmap with per-phase objectives, capabilities, inputs, dependencies, risks, tests and exit criteria for pilot phases 1–9 (synthetic migration, role-played UAT, reference release), the First-Property Deployment Gate and enterprise phases 10–20.
- Phase 0 exit gate: 7 approval groups remain (Product Owner incl. acting operations acceptance; Finance via OQ-002; Security adviser via OQ-033; Technical Lead; ADR confirmations; open questions). See the blueprint §12.
- Acceptance-readiness review recorded (`docs/00-governance/review-findings-2026-09-23.md`): 45 findings — 3 critical, 13 high — to resolve in the pre-acceptance fix pass.

### How we got here

| WP | Deliverable set | Status |
|---|---|---|
| 0.1 | Governance, vision, scope, personas, capability map (283), roadmap skeleton | Complete |
| 0.2 | Target-state, ownership, tenancy, domain model (80 invariants), data architecture, ADR-001…004 | Complete |
| 0.3 | 18 state machines, 22 workflows, 101 business rules | Complete |
| 0.4 | Financial architecture, ADR-005…008; financial inputs validated | Complete |
| 0.5 | Security model, role/authority matrix, ADR-009 | Complete |
| 0.6 | UX architecture (16 screens) | Complete |
| 0.7 | Integration (14 interfaces), reporting (22 reports), NFR, QA (29 test obligations), deployment, documentation, AI; ADR-010/011 | Complete |
| 0.8 | Blueprint v1.0 consolidation; full roadmap; acceptance model | Complete |

### Programme next steps

Phase 1 (external research and benchmark validation) → Phase 2 (current-state audit) → Phase 3 (gap matrix) → Phase 4 (transition architecture) → delivery phases 1–20. Governed proof spikes are permitted earlier with Product Owner approval.

**Open questions:** 26 of 38 closed (25 adopted industry-standard answers + OQ-003 operations authority, 23 Sep 2026); 12 remain — 8 facts (2 now: Finance Controller, Technical Lead/Security adviser; 6 deferred to the first property), 3 professional-advice items, 1 cost approval. See `docs/00-governance/industry-standard-answers.md` and the register; decision digest in `docs/00-governance/decisions-required.md`.

## Licensing

Repository content licence is pending, consistent with the SuiteFlow programme's open licensing position. No external distribution until counsel approves.
