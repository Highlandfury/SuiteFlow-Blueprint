---
doc-id: ADR-002
title: Tenancy, property scope and isolation
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Principal Architect (drafted); Security Engineer (review); Product Owner (approval)
applies-to: full enterprise target; pilot is the degenerate one-property case
depends-on: [ADR-001, PROD-CAPMAP, GOV-INPUTS]
supersedes: rules on DP-CTX-001
---

# ADR-002: Tenancy, property scope and isolation

## Context

The accepted pilot baseline is one property, one company, one site (BR-PILOT-002). The Product Owner has directed an enterprise target that supports multi-property operation, property groups and chain reporting (23 Sep 2026), with pilot-first delivery. The proposed input DP-CTX-001 assumed one isolated Frappe site and database per hotel, with a signed application image promoted without rebuild. That proposal made sense for the pilot; it is questionable as an enterprise target.

## Problem

What is the tenant boundary, and how is isolation between customers, properties and legal entities achieved — logically and, where required, physically — without making chain capabilities an integration problem?

## Target-state requirement

- Chain capabilities must be target-native: shared guest profiles, central reservations, negotiated-rate and corporate structures, consolidated reporting, standardised configuration (mandate §57; CAP-PM-003, CAP-BI-002/003, CAP-CRP-008).
- Per-property operations and accounting must remain independent: each property closes its own business day and keeps its own books (CAP-NAU-*, CAP-ACC-*, BR-MVP-002).
- Property-level permissions must be enforceable for every read and write, including reports, exports, APIs and AI tools (CAP-PLT-003).
- Single-property customers must not be burdened with chain complexity.
- Data-residency and contractual isolation requirements may arise per customer.

## Options

| Option | Summary | Assessment |
|---|---|---|
| A. One deployment per property (federation of sites) | Each hotel is an isolated application + database; group capability is built by integrating sites | Rejected as the default: chain capabilities become cross-database integration (shared profiles, central reservations, consolidation), and every chain feature pays the federation tax |
| B. One deployment per tenant with property partitions | A tenant (operator/owner organisation) has one application + database; properties are first-class partitions; scope enforced platform-wide | **Chosen** |
| C. Single global multi-customer deployment with tenant column | All customers share one deployment; isolation is a column filter | Rejected: unacceptable blast radius and recovery coupling; a customer's incident or restore becomes everyone's |
| D. Hybrid: B default, A available per customer | Tenant deployment by default; hard per-property isolation offered where regulation or contract demands it | **Chosen as the option set**: D is B plus an explicitly supported variant, not a second architecture |

## Decision

1. **The tenant is the deployment unit.** A tenant is one customer organisation — an operator, owner or management company — served by one application instance and one database.
2. **Properties are first-class partitions inside the tenant.** Every operational and financial record carries an explicit property reference. Records that are genuinely tenant-wide (for example shared guest profiles governed by consent and scope rules) are modelled explicitly as tenant-scope, never as an accident of a missing property field.
3. **Legal entities (companies) are a second partition dimension** for financial visibility and reporting, mapped to properties as configured (one entity may span properties; one property may involve several entities).
4. **Property and company scope are enforced at the platform layer** on every access path — command, query, report, export, API, webhook consumer and AI tool. Scope is not a UI convention; it is an invariant with automated negative tests.
5. **Accounting closes remain per property** (and per legal entity as configured). Tenant-level consolidation reads closed property results; it never re-owns them.
6. **One-property tenants are the degenerate case**: identical code paths, no special cases. Configuration and UI may hide inapplicable chain surfaces but must not create a separate mode.
7. **Hard physical isolation is an available variant, not the default.** A customer with statutory, residency or contractual requirements may be served by a dedicated property-level deployment. Such deployments remain compatible with the tenant model through the same contracts (import/export and consolidation interfaces), but they are a commercial/regulatory exception — correctness must never depend on physical separation.
8. **A tenant's deployment is promoted as a versioned artefact** (image + migrations), never rebuilt per customer (consistent with DP-CTX-001's promotion intent).

## Reasoning

- Chain capabilities required by the mandate are, at their core, shared data with scoped visibility. One database with enforced scope serves them natively; federation makes every one of them an integration project and an eventual consistency problem.
- Isolation is achieved logically by scope enforcement — which is mandatory anyway (CAP-PLT-003) and would still be required in a federated model for legal entities within one hotel company.
- A hotel group's most valuable data asset is guest and corporate relationship history; fragmenting it per property undermines the enterprise value proposition.
- Per-property recovery is a solvable design problem (property-scoped export/restore tooling, CAP-PLT-016); cross-database re-unification is not.

## Trade-offs

- Blast radius: a tenant-wide incident affects all of that customer's properties. Mitigated by property-scoped recovery tooling, staged releases and per-tenant isolation from other customers.
- Scope enforcement becomes safety-critical: a missing filter leaks data across properties. Mitigated by platform-level enforcement, negative tests per surface and audit of new queries (RSK-SEC-001).
- Partial restore is harder than whole-tenant restore. Accepted and designed explicitly (WP 0.7 recovery architecture).
- The Control Plane and reporting must consistently handle property partitioning; complexity must not leak into user-facing behaviour.

## Risks

| Risk | Mitigation |
|---|---|
| Cross-property data exposure through a missing scope filter | Platform invariant + per-surface negative tests; no raw query surfaces without scope injection |
| Enterprise complexity degrades single-property usability | Degenerate-case rule (decision 6); configuration hides inapplicable surfaces |
| Property-scoped restore inconsistency (one property restored to a different point than its siblings) | Recovery design must define and enforce cross-property consistency for tenant-wide entities (profiles, corporate accounts) |
| Regulatory isolation demand arrives late | Physical isolation variant (decision 7) keeps the option open |

## Consequences

- Data model must carry property (and where relevant company) references as first-class dimensions (`data-model.md`, WP 0.2 pass 2).
- Every API, report and AI tool specification must state its scope semantics.
- Tenant onboarding/offboarding becomes a first-class platform capability.
- DP-CTX-001 is MODIFIED: isolated site per hotel becomes tenant-per-deployment with property partitions, plus an optional dedicated-deployment variant. Recorded in the inputs register.

## Implementation impact

No immediate change. The pilot is a one-property tenant: the same model applies degenerately. Programme P4 transition architecture will decide how the current single-property assumptions migrate to property partitioning.

## Migration impact

- Property references must be back-filled for all records on any future multi-property adoption; migrations must be restartable and validated (CAP-PLT-013/016).
- Tenants already deployed per property (if any) would be consolidated through controlled migration, not by ad-hoc data copying.

## Review trigger

- A customer contract requiring physical isolation as the only acceptable model.
- Evidence that property-scope enforcement cannot be proven robustly across all surfaces.
- Measured tenant-level scale or availability limits.

## Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial decision issued with WP 0.2 pass 1 | PROPOSED |
