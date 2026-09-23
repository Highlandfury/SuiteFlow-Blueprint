---
doc-id: GOV-CHARTER
title: SuiteFlow Blueprint Charter
status: PROPOSED
version: 0.4
date: 2026-09-23
owner: Product Owner (accountable)
applies-to: this repository
---

# SuiteFlow Blueprint Charter

This charter defines how the target-state blueprint is written, reviewed, approved, versioned and used. It is the authority for every other document in this repository. Where another document conflicts with this charter on matters of process, this charter wins.

## 1. Purpose

The blueprint specifies the SuiteFlow hotel platform as it should exist if designed correctly from first principles for enterprise hospitality operations. It exists so that the system is built toward a defined target rather than evolved from accidental implementation decisions.

The blueprint:

- is designed from hospitality requirements, enterprise PMS capability benchmarks, accounting principles, security principles and real operating workflows;
- is authored before the current implementation is audited;
- treats the current implementation as evidence about the present, never as the specification;
- is versioned, reviewed and explicitly approved before it becomes authority.

## 2. Scope of this repository

In scope: product definition, capability map, target domain/data/workflow/financial/security/UX/integration/reporting/QA/deployment/documentation architectures, blueprint-level ADRs, governance records.

Out of scope: application code, database schemas, DocType definitions, infrastructure configuration, vendor-proprietary material, claims about current SuiteFlow behaviour.

## 3. Source-of-truth hierarchy

When two credible statements conflict, the higher level wins. A lower level may never silently override a higher level; conflicts are raised and resolved through change control (section 7).

| Level | Source | Examples |
|---|---|---|
| 1 | Approved business requirement | Product Owner decisions, contracts, regulatory obligations |
| 2 | Approved Target-State Blueprint (this repository, status ACCEPTED) | Vision, scope, capability map, domain model, state machines |
| 3 | Approved blueprint-level ADR | Recorded architecture decisions |
| 4 | Approved domain and business rules | Rate/tax/financial rule catalogues |
| 5 | Approved current-implementation specification | Implementation-level ADRs and specs in `Highlandfury/SuiteFlow` |
| 6 | Current repository code and configuration | `hotel_integration`, patches |
| 7 | Current database state | Live/UAT data |
| 8 | Automated tests | Existing test suites |
| 9 | External official documentation | Vendor product documentation |
| 10 | External research and secondary sources | Analyst material, community posts |
| 11 | Agent assumptions | Must always be labelled as such |

Two consequences follow, and they are non-negotiable:

1. A capability currently implemented in `hotel_integration`, Kamra or ERPNext has **no architectural entitlement** at level 6 or below. It must justify itself against levels 1–4.
2. An implementation-level ADR that has been "accepted for implementation" is level 5. Blueprint validation may confirm, modify or supersede it. This applies to all current ADR-001…004 records.

## 4. Document status model

| Status | Meaning |
|---|---|
| DRAFT | Being written; not circulated for decision |
| PROPOSED | Complete for review; may be used for planning, not for implementation commitments |
| ACCEPTED | Named approvers have accepted; changes now require change control |
| SUPERSEDED | Replaced by a later document version; retained for history |
| REJECTED | Considered and declined; the reason is recorded |
| WITHDRAWN | Removed before decision; the reason is recorded |

Only ACCEPTED content may be cited as a requirement in implementation tickets. PROPOSED content may drive prototyping, proof spikes and research, but not committed delivery scope.

## 5. Front-matter standard

Every document in this repository begins with YAML front matter:

```yaml
---
doc-id: DOMAIN-PREFIX-NNN        # stable identifier, never reused
title: Human-readable title
status: DRAFT | PROPOSED | ACCEPTED | SUPERSEDED | REJECTED | WITHDRAWN
version: 0.1
date: YYYY-MM-DD
owner: Role accountable for the content
applies-to: scope of applicability
supersedes: doc-id or version, when applicable
depends-on: [doc-id, …], when applicable
---
```

Documents must not silently change meaning under the same version. Editorial corrections that do not change meaning are recorded in the commit history; meaning changes require a version increment.

## 6. Confidence and uncertainty vocabulary

Every material statement is classifiable. Documents must use these labels wherever a reader could otherwise mistake an assumption for a fact:

| Label | Meaning |
|---|---|
| VERIFIED | Confirmed by a primary source or reproducible evidence; the evidence is cited |
| INFERRED | Logically derived from verified statements; the derivation is stated |
| ASSUMED | Taken as true for planning without current evidence; must be confirmed |
| UNVERIFIED | A claim exists but has not been checked |
| CONFLICTING | Credible sources disagree; the conflict is recorded, not averaged away |

Programme-level statements in this repository are PROPOSED and therefore carry implicit ASSUMED status unless individually labelled otherwise. No document may present a vendor capability, a legal requirement or a system behaviour as VERIFIED without a cited primary source.

## 7. Change control

1. Any change to ACCEPTED content requires an impact analysis covering: affected requirements, architecture, domain model, workflows, financial model, security model, APIs, tests, migration and documentation.
2. If the change alters an architectural decision, an ADR is created or updated before the change is considered complete.
3. The change is recorded in the document's version history with the reason, the requester and the approver.
4. Lower-level artifacts (implementation tickets, current-state documents) may never force a silent blueprint change. If implementation convenience conflicts with the blueprint, the conflict is raised as a decision.

## 8. Architecture Decision Record policy

An ADR is required when a decision changes: domain ownership, source of truth, financial authority, tenant isolation, authentication, authorization, core state machines, reservation architecture, folio architecture, accounting architecture, or integration architecture.

Blueprint ADRs live in `docs/architecture/adr/` and use the standard structure: context, problem, target-state requirement, options, decision, reasoning, trade-offs, risks, consequences, implementation impact, migration impact, review trigger.

The existing implementation-level ADR-001…004 and the proposed ADR-001…005 in the decision pack are inputs to be validated during Phase 0/1. Validation outcomes will be recorded as one of: CONFIRMED (and promoted), MODIFIED (superseding ADR issued here), REPLACED, or REJECTED — each with reasoning.

## 9. Review protocol

Substantial content passes through explicit, separate review perspectives before it is proposed for acceptance. These are review passes by the same programme team, not simulated independent people, and findings are reported as findings — never as invented quotations or fictional reviewers.

Standard passes for each work package:

1. **Product pass** — does this solve the right problem for the right user?
2. **Hospitality domain pass** — is this how a hotel actually operates? (Section 15 tests apply.)
3. **Architecture pass** — boundaries, ownership, coupling, upgradeability.
4. **Finance pass** — revenue recognition, folio, settlement, posting, reconciliation, tax.
5. **Security pass** — authorization, exposure, tenancy, secrets, audit.
6. **Concurrency and reliability pass** — simultaneous users, retries, crash recovery, partial failure.
7. **QA pass** — testability, negative cases, permissions, financial assertions, regression.
8. **ASTRA pass 1 (adversarial)** — assume a subtle defect exists; search for what is missing.
9. **ASTRA pass 2 (adversarial)** — assume pass 1 missed something; ask what could still fail in production, after restart, after retry, mid-transaction, after upgrade, during night audit, financially.
10. **Documentation pass** — traceability and consistency with the rest of the blueprint.

Work packages close with a Team Status report (product, domain, architecture, engineering, QA, security, ASTRA, documentation, release views, then the final decision).

## 10. Operational realism test

Every major workflow in this blueprint must answer, explicitly:

- Who performs this, when, and what do they see?
- What information do they need at that moment?
- What can go wrong, and what happens if the operator makes a mistake?
- Who can override it, and what is recorded?
- What is audited?
- What happens financially?
- What happens operationally afterwards?

A workflow that only makes sense in a data model is rejected.

## 11. Identifier conventions

| Artifact | Pattern | Example |
|---|---|---|
| Capability | `CAP-<DOMAIN>-NNN` | `CAP-RSV-014` |
| Domain | two-to-three-letter code (table below) | RSV |
| Business rule | `BR-<DOMAIN>-NNN` | `BR-FOL-007` |
| State machine | `SM-<ENTITY>` | `SM-RESERVATION` |
| Workflow | `WF-<DOMAIN>-NNN` | `WF-FO-002` |
| Requirement / user story | `REQ-<DOMAIN>-NNN` | `REQ-RSV-021` |
| Open question | `OQ-NNN` | `OQ-014` |
| Risk | `RSK-<CATEGORY>-NNN` | `RSK-FIN-003` |
| ADR | `ADR-NNN` | `ADR-006` |
| Test obligation | `TO-<DOMAIN>-NNN` | `TO-FIN-004` |

**Domain codes** (25 domains; codes are stable and never reassigned):

| Code | Domain | Code | Domain | Code | Domain |
|---|---|---|---|---|---|
| PM | Property & inventory | CSH | Cashiering | BI | Analytics & BI |
| RSV | Reservations | NAU | Night audit & business date | CRM | CRM & communication |
| AVL | Availability & inventory control | INA | Income audit | HRM | Workforce & HR boundary |
| RTM | Rates & revenue management | ACC | Accounting & finance integration | INT | Integrations |
| FO | Front office | POS | Outlets & F&B | PLT | Platform & administration |
| GST | Guest profiles & privacy | INV | Inventory & purchasing | AI | AI assistance |
| GRP | Groups & events | SAL | Sales & catering | | |
| CRP | Corporate & travel trade | RPT | Reporting | | |
| HSK | Housekeeping | MNT | Maintenance & engineering | | |
| FOL | Folio & billing | | | | |

Identifiers are never reused. Retired identifiers remain reserved.

## 12. Approval authorities (blueprint)

| Content area | Approvers |
|---|---|
| Product vision, scope, capability priorities | Product Owner (acting operations acceptance authority until a hotel is secured; OQ-003 closed) |
| Domain model, state machines, workflows | Product Owner; Hotel Operations representative; Technical Lead |
| Financial architecture | Finance Controller; external tax adviser where applicable; Technical Lead |
| Security and privacy | Security/Privacy adviser; Technical Lead |
| Integration architecture | Technical Lead; Finance for financial integrations |
| Blueprint release (v1.x) | Product Owner with the above sign-offs recorded |

Named individuals for the pilot roles are currently open items (see inputs register).

## 13. Deliverable map

Planned structure; documents appear as their work packages execute. Only files that exist are listed as present.

```text
docs/
├── TARGET-STATE-BLUEPRINT.md          [present — master index v1.2]
├── 00-governance/
│   ├── charter.md                     [present]
│   ├── inputs-register.md             [present]
│   ├── glossary.md                    [present]
│   ├── open-questions.md              [present]
│   ├── decisions-required.md          [present — plain-language digest]
│   └── risk-register.md               [present]
├── product/
│   ├── vision.md                      [present — WP 0.1]
│   ├── scope.md                       [present — WP 0.1]
│   ├── personas-and-roles.md          [present — WP 0.1]
│   ├── capability-map.md              [present — WP 0.1]
│   └── roadmap.md                     [present — v1.1 with per-phase exit criteria]
├── architecture/                      [WP 0.2 complete — pending review]
│   ├── target-state.md                [present — WP 0.2]
│   ├── domain-model.md                [present — WP 0.2]
│   ├── data-model.md                  [present — WP 0.2]
│   ├── state-machines.md              [present — WP 0.3]
│   ├── business-rules.md              [present — WP 0.3]
│   ├── non-functional-requirements.md [present — WP 0.7]
│   └── adr/                           [present — ADR-001…ADR-011; see adr/README.md]
├── security/                          [WP 0.5 complete]
│   ├── security-model.md              [present]
│   └── role-and-authority-matrix.md   [present]
├── workflows/                         [WP 0.3 catalogue present; per-domain detail with WP 0.6]
│   ├── catalogue.md                   [present — 22 workflows]
│   ├── reservations/  front-desk/  housekeeping/  maintenance/  finance/  night-audit/   [WP 0.6 detail]
├── finance/                           [WP 0.4 complete]
│   └── financial-architecture.md      [present]
├── ux/                                [WP 0.6 complete]
│   └── architecture.md                [present — 16 screens]
├── integrations/                      [WP 0.7 complete]
│   └── architecture.md                [present — 14 interfaces]
├── reporting/                         [WP 0.7 complete]
│   └── architecture.md                [present — 22 reports]
├── qa/                                [WP 0.7 complete]
│   └── strategy.md                    [present]
├── deployment/                        [WP 0.7 complete]
│   └── architecture.md                [present]
├── manuals/                           [WP 0.7 complete]
│   └── documentation-architecture.md  [present — 21-document set]
├── ai/                                [WP 0.7 complete]
│   └── architecture.md                [present — governance-first]
├── migration/  operations/  release/   [WP 0.8 or implementation phase as applicable]
```

## 14. Phase model

| Phase | Content | Gate to exit |
|---|---|---|
| 0 | Target product definition (this repository, WP 0.1–0.8) | Blueprint ACCEPTED (current version) |
| 1 | External research and benchmark validation | Research register complete; blueprint claims labelled with sources |
| 2 | Current-state audit of implementation and ecosystem | Evidence-based capability classifications |
| 3 | Target vs current gap matrix | Every capability mapped with an action and priority |
| 4 | Transition architecture | Per-domain keep/extend/refactor/wrap/replace decisions |
| 5+ | Implementation through small, testable tickets | Ticket-level Definition of Ready/Done |

## 15. Standing constraints

- No proprietary source code, implementation detail, confidential information or protected assets from commercial PMS systems may be copied or reproduced. Public capability documentation is used only to derive requirements, never implementations.
- No fabricated evidence. System behaviour not directly observed is labelled UNVERIFIED.
- No destructive production operations without impact analysis, backup, rollback plan, verification and explicit authorization.
- AI-generated content follows the same evidence and review rules as any other content. In the product itself, AI may explain, summarize, search, analyze, classify, recommend, draft and propose governed actions — it may never independently determine prices, availability, tax, balances, payments, inventory, room assignment or permissions.

## 16. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial charter issued with WP 0.1 pass 1 | PROPOSED |
| 0.2 | 2026-09-23 | Version references updated for blueprint v1.1 after adoption of the industry-standard answers | PROPOSED |
| 0.3 | 2026-09-23 | Operational-acceptance authority updated for the synthetic reference pilot (Product Owner acting; OQ-003 closed) | PROPOSED |
| 0.4 | 2026-09-23 | Review-pass corrections: deliverable-map version references aligned (master index v1.2, roadmap v1.1) | PROPOSED |
