---
doc-id: DOC-ARCH
title: Target Documentation Architecture
status: PROPOSED
version: 0.2
date: 2026-09-23
owner: Technical Documentation (drafted); Product Owner (approval)
applies-to: full enterprise target and programme
depends-on: [GOV-CHARTER, QA-STRATEGY, DEP-ARCH]
---

# Target Documentation Architecture

## 1. Purpose and principles

Documentation is part of the product, not an artefact produced after it. Principles:

1. **No undocumented behaviour.** A feature is not done while its documentation is missing (definition of done).
2. **Docs-as-code.** Documentation lives in version control, changes by pull request, and is versioned with the release it describes.
3. **One source per topic.** Blueprint (this repository) defines *what should be*; implementation repositories document *what is*; manuals describe *how to operate*. Where they disagree, the discrepancy is a defect to resolve, not a nuance to explain.
4. **Generated where possible.** API reference is generated from contracts; report catalogues, permission matrices and configuration references are generated from their authoritative definitions.
5. **Written for the reader.** Front desk staff read task-oriented steps, not architecture; finance reads controls and reconciliation; engineers read contracts.
6. **Currency gates releases.** Release notes, migration notes and affected manuals are release evidence (QA gate §5).

## 2. Documentation set

| # | Document | Audience | Owner | Maintenance trigger |
|---|---|---|---|---|
| 1 | Administrator Manual (property configuration, users, policy, integrations health) | System admin, GM | Platform | Configuration feature change |
| 2 | Front Desk Manual (arrivals, stays, departures, payments, overrides) | Front office | Operations | Workflow/UI change |
| 3 | Reservations Manual (booking, amendments, deposits, groups intake) | Reservations | Operations | Workflow/UI change |
| 4 | Housekeeping Manual (boards, tasks, inspections, discrepancies) | Housekeeping | Operations | Workflow/UI change |
| 5 | Maintenance Manual (work orders, PM, assets) | Engineering | Operations | Workflow/UI change |
| 6 | Finance Manual (close, reconciliation, AR, deposits, refunds, tax) | Finance, income audit | Finance | Financial rule change |
| 7 | Cashiering Guide (sessions, methods, variance) | Cashiers | Finance | Rule/UI change |
| 8 | Night Audit Playbook (checklist, failure recovery, escalation) | Night audit | Finance + ops | Close process change |
| 9 | Management Guide (dashboards, reports, approvals) | GM, managers | Product | Reporting change |
| 10 | Sales & Groups Guide | Sales, events | Product | Commercial workflow change |
| 11 | POS/Outlets Guide *(scope per OQ-014 (closed))* | F&B | Operations | Outlet feature change |
| 12 | Reports Catalogue (definitions, as-of semantics, drill-down) | All roles | Finance + data | Report definition change |
| 13 | Developer Manual (architecture, module map, contracts, testing) | Engineers | Engineering | Architecture/API change |
| 14 | API Manual (endpoints, auth, idempotency, errors, versioning) | Integrators | Engineering (generated) | Contract change |
| 15 | Security & Privacy Guide (roles, limits, break-glass, data handling) | Managers, security | Security | Security model change |
| 16 | Deployment & Operations Manual (environments, recovery, runbooks) | SRE, support | SRE | Deployment change |
| 17 | Migration Manual (data mapping, reconciliation, cutover, rollback) | Programme + finance | Data + finance | Migration change |
| 18 | Troubleshooting Guide (symptom → cause → action; close/integration/degraded modes) | Service desk, all | SRE | Incident learnings |
| 19 | Training Materials (role curricula, exercises, certification checklist) | Hotel staff | Operations + product | Workflow/UI change |
| 20 | Release Notes (versioned changes, migrations, known issues, rollback) | All | Release manager | Every release |
| 21 | AI Manual *(D19)* (assistant capabilities, limits, governance) | All staff | Product + security | AI capability change |

## 3. Repository mapping

| Content | Repository | Enforced |
|---|---|---|
| Target architecture, rules, ADRs, this catalogue | `SuiteFlow-Blueprint` | Change control (charter §7) |
| Implementation docs, developer/API manuals, runbooks, release notes | Implementation repository | As-code review |
| Operations manuals (1–12, 16–19) | Operations documentation location (decision in WP 0.8: same repo or dedicated docs site) | Release gate |
| Generated references (API, permissions, reports, configuration) | Generated into the docs build | CI |

The blueprint's canonical artefacts (mandate §62) map onto this set; the implementation repository's `docs/` remains the home for implementation-level specifications (IMPL-ADR prefix rules per ADR README).

## 4. Release notes and change communication

Every release note states: version, date, affected capabilities (`CAP-*`), migrations and data effects, configuration changes required, operational changes with step pointers, known issues, rollback summary, and documentation updated. Finance-affecting changes additionally state reconciliation impact and are acknowledged by the Finance Controller role.

## 5. Training materials

Training is role-based (per personas and roles): job-shadow scripts, scenario exercises using synthetic UAT data, competency checklist per role, and refresher triggers when a workflow materially changes. Certification of staff competency is part of the First-Property Deployment Gate (the reference pilot uses role-played scenarios).

## 6. Localisation

English at pilot (OQ-032 closed scope); documentation architecture supports translation by separating content from presentation and maintaining terminology per the glossary.

## 7. Governance and review

- Every material feature change triggers documentation impact assessment in its ticket (Definition of Done includes documentation).
- Manuals are reviewed with the role that uses them before the reference release; hotel-staff review and acceptance of documentation is a First-Property Deployment Gate item.
- Quarterly documentation health review: freshness, gaps found during incidents, user feedback.
- Incidents must yield documentation improvements (troubleshooting entries, runbook updates) as a post-incident action.

## 8. Open items

| Item | Owner |
|---|---|
| OQ-003 closed — acting operations authority (manual validation) | Product Owner |
| OQ-011 (closed) document formats for finance/guest documents | Finance |
| Operations documentation hosting decision | WP 0.8 |
| OQ-024 retention (documentation of data handling) | Security/Legal |

## 9. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial documentation architecture issued with WP 0.7 | PROPOSED |
| 0.2 | 2026-09-23 | Synthetic reference pilot: training certification and manual acceptance moved to the First-Property Deployment Gate | PROPOSED |