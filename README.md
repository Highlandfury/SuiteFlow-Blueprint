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

Phase 0, work package 0.1 (Product Foundation). See the charter's deliverable map for the full planned structure.

## Licensing

Repository content licence is pending, consistent with the SuiteFlow programme's open licensing position. No external distribution until counsel approves.
