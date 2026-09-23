---
doc-id: ADR-009
title: Authorization and enforcement model
status: PROPOSED
version: 0.2
date: 2026-09-23
owner: Security Engineer (drafted); Principal Architect (review); Product Owner (approval)
applies-to: full enterprise target
depends-on: [ADR-001, ADR-002, ARCH-DOMAIN, ARCH-STATES]
---

# ADR-009: Authorization and enforcement model

## Context

The target architecture requires server-side enforcement of permissions, property/company scope, authority limits and maker–checker across every access path — commands, queries, reports, exports, APIs, webhooks and AI tools (ADR-002; BR-PLT-002). The current implementation's failure classes include bypassable controls and coarse role checks; the target must make enforcement structural rather than procedural. Every state machine says "requires authority"; that phrase only becomes real if authority is data-driven and centrally enforced.

## Problem

What authorization model gives the target least-privilege access, deterministic authority limits and provable scope isolation without becoming an opaque policy engine that nobody can audit?

## Target-state requirement

- Deny by default; no authority is implied by seniority, technical role or UI state.
- Every decision is enforced in one place, server-side, on every path.
- Authority limits are effective-dated configuration, property-scoped.
- Maker–checker is a first-class mechanism, not a convention.
- Scope violations are the highest-severity defect class (RSK-SEC-001).
- Authorization decisions and sensitive reads are auditable.
- The model must be explainable to an auditor in plain language.

## Options

| Option | Summary | Assessment |
|---|---|---|
| A. Coarse role checks per feature | Roles with feature flags | Rejected: the current failure mode; cannot express limits, scope or SoD |
| B. RBAC + scopes + effective-dated limits with a single enforcement point | Roles bundle permissions; permissions act on resources with scope and optional numeric limits; one authorization service evaluates every request | **Chosen** |
| C. Full policy engine (ABAC/policy language) | Arbitrary policy expressions over attributes | Rejected: opacity, testing burden, operational risk; the enterprise requirements are satisfiable with B |
| D. Database row-level security only | Enforce at storage layer | Rejected as sole mechanism: cannot express approval flows, limits or maker–checker; retained as defence in depth where available |

## Decision

1. **Model.** Authorization evaluates: principal (role set, property/company grants) × action (command/query) × resource (type, property, company, sensitivity class) × context (state, amount, business date) → allow / deny / require-approval. Anything not explicitly allowed is denied.
2. **Single enforcement point.** All application services call the authorization service; there is no direct path around it. UI checks are advisory copies for usability, never the control.
3. **Authority limits are data.** Numeric bands (adjustment, refund, discount, comp, write-off, reopen) are effective-dated, property-scoped configuration (BR-PLT-001). Changes require the authority they govern.
4. **Maker–checker is a mechanism.** Actions above limits or designated as dual-control create an approval request bound to the exact intended effect (payload hash); approval, rejection and expiry are evidenced; the approved effect executes under the approver's authority plus the requester's identity — both recorded.
5. **Scope injection.** Queries and commands receive property/company scope from the principal context, never from user-supplied parameters alone. Exports, reports and AI tools use the same scope source. Cross-scope references are rejected, not filtered silently.
6. **Sensitive data classes.** Data is classified (security model §5); class A (identity documents, watchlist, bank details) requires explicit permission, masks by default, logs every read, and is excluded from bulk exports without approval.
7. **Non-human principals.** Service identities, integration clients and AI assistants are principals with explicit permissions and scopes; they carry no interactive authority and cannot escalate. AI tool allow-lists are part of the principal definition.
8. **Break-glass.** A time-boxed, alerted, post-reviewed elevation path (CAP-PLT-015); never a standing role; grants are logged with reason and reviewed within 24 hours.
9. **Evidence.** Every authorization denial is logged; every approval, limit use and sensitive read is logged; audit records are append-only (CAP-PLT-006).
10. **Testing is part of the decision.** The model is not implemented until negative tests exist per surface class (command, query, report, export, API, webhook, AI tool) proving cross-scope denial and self-approval denial.
11. **Framework-generic surface closure (SEC-01 resolution).** The bound platform exposes framework-generated surfaces (REST `/api/resource/*`, report builder, file/attachment URLs, bulk import/export, Desk list/search, admin UI). These are enumerated in a maintained **surface inventory** and each is either disabled, mediated by the authorization service, or restricted by record-level permissions as defence in depth. A framework-generic surface that can read or write scoped data without scope enforcement is a critical defect; Phase 2 enumerates the surfaces and CI fails when routes or permissions change without inventory review.

## Reasoning

- RBAC + scopes + limits maps directly onto how hotels actually delegate authority ("supervisor may comp up to ₦X"); it is explainable to auditors and testable.
- A single enforcement point converts a diffuse class of bugs (missed checks) into one reviewable component with its own tests.
- Payload-bound maker–checker prevents the classic fraud pattern where an approved action is swapped for a different one between approval and execution.
- Rejecting a policy engine is deliberate: expressiveness is not the constraint; auditability and testability are.

## Trade-offs

- A central authorization component is a critical path; availability and performance must be designed (cached decisions with invalidation on configuration change; no cache where money is at stake beyond a bounded TTL).
- Fine-grained permission catalogues grow; mitigated by role bundles and review cadence.
- Some domain semantics (state-dependent permissions) still live in state machines; the authorization service evaluates permission + context, but transition legality remains the state machine's responsibility. The boundary: authorization answers *may this principal attempt this*, the state machine answers *is this transition valid now*.
- Payload hashing for approvals adds friction; accepted for the actions that require it.

## Risks

| Risk | Mitigation |
|---|---|
| Authorization component outage blocks operations | Availability design; bounded fail-closed behaviour for financial actions, explicit degraded-mode policy for read paths reviewed with security |
| Permission catalogue drift | Versioned permission catalogue; architecture tests assert every command/query declares a permission |
| Scope bypass through a new surface (report, export, tool) | Surface registration requirement: an unregistered surface fails tests and cannot ship. Includes framework-generic surfaces (REST routes, report builder, attachments, imports/exports, Desk search) — inventory, close or mediate each, and add a route/permission inventory test (§11) |
| Cached-decision staleness after limit changes | Invalidation on configuration change; short TTLs for financial permissions |
| Maker–checker honour-system | Payload binding + execution-time verification that the payload matches the approval |

## Consequences

- Security model document (WP 0.5) defines the permission catalogue outline, role bundles, limits defaults and SoD rules.
- Every application service must declare permissions and scope; architecture tests enforce declarations.
- API and integration architecture (WP 0.7) must specify principal types and scopes per interface.
- Test obligations: cross-scope denial per surface; self-approval denial; limit boundary tests; payload-swap rejection; cache-invalidation tests.

## Implementation impact

No immediate change. Phase 2 audits current authorization practices; Phase 4 plans transition. Foundation-phase implementation (when authorized) starts with this model rather than retrofitting it.

## Migration impact

Roles, role assignments and any existing limits are migrated with mapping evidence; inferred or undocumented authority has no migration path — it must be explicitly granted or users lose it (intentionally).

## Review trigger

- Performance or availability evidence requiring a different enforcement topology.
- Requirements for policy expressiveness beyond limits/scopes (e.g., complex delegation chains) that RBAC-lite cannot express safely.
- Audit finding that the model is not explainable in practice.

## Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial decision issued with WP 0.5 | PROPOSED |
| 0.2 | 2026-09-23 | SEC-01 resolution: framework-generic surface closure (decision 11) and CI route/permission inventory test in risks | PROPOSED |
