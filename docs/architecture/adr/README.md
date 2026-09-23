---
doc-id: ADR-INDEX
title: Blueprint Architecture Decision Records — Index
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Principal Architect (maintained); Technical Lead (approval; pending OQ-033)
applies-to: docs/architecture/adr/
---

# Blueprint Architecture Decision Records

This directory holds the SuiteFlow blueprint-level ADRs. They are numbered `ADR-001…` and are authoritative within this repository.

**Disambiguation rule.** Other decision records exist outside this repository and are always cited with an explicit prefix:

| Prefix | Source | Status meaning |
|---|---|---|
| `ADR-nnn` | This repository (blueprint) | Target architecture decisions |
| `IMPL-ADR-nnn` | Implementation repository `Highlandfury/SuiteFlow` (`docs/adr/`) | Current-implementation decisions (level 5 of the source-of-truth hierarchy) |
| `DP-ADR-nnn` | Architecture and Financial Control Decision Pack v0.1 | Proposed inputs to validate (see inputs register) |

A blueprint ADR is issued when a decision changes domain ownership, source of truth, financial authority, tenant isolation, authentication, authorization, core state machines, reservation architecture, folio architecture, accounting architecture or integration architecture (charter §8).

**Standard structure:** context, problem, target-state requirement, options, decision, reasoning, trade-offs, risks, consequences, implementation impact, migration impact, review trigger.

**Validation outcomes.** When a blueprint ADR rules on a proposed input (`DP-ADR-nnn`, `IMPL-ADR-nnn`), the outcome is recorded in the inputs register as one of CONFIRMED / MODIFIED / REPLACED / REJECTED, with a link to the ruling ADR.
