---
doc-id: SEC-MODEL
title: Target Security Model
status: PROPOSED
version: 0.4
date: 2026-09-23
owner: Security Engineer (drafted); Security/Privacy Adviser + Technical Lead (approval; pending appointment, OQ-033); Product Owner (interim)
applies-to: full enterprise target
depends-on: [ADR-002, ADR-009, ARCH-DOMAIN]
---

# Target Security Model

## 1. Purpose and scope

This document specifies how the target architecture authenticates principals, authorizes actions, isolates tenant/property/company data, protects sensitive information, governs privileged paths, and proves all of it. It covers staff users, machine principals, integrations and AI. Physical security, network perimeter design and hosting controls are addressed in the deployment architecture (WP 0.7); they are assumed present, not substituted for application controls.

## 2. Principles

1. **Deny by default.** Nothing is permitted unless explicitly granted to the principal attempting it.
2. **Server-side control.** The UI may hide what a user cannot do; only the server decides. Client-supplied scope, role or amount fields are never trusted.
3. **Single enforcement point.** Authorization is evaluated in one place for every command, query, report, export, API call, webhook consumption and AI tool invocation (ADR-009). Framework-generated surfaces (REST routes, report builder, attachments, imports/exports, Desk search) are inventoried and closed or mediated — see ADR-009 §11.
4. **Least privilege with explicit authority.** Money-moving actions carry numeric limits; above them, maker–checker. No one widens their own authority.
5. **One incident class is never tolerated: scope leakage.** Cross-property/cross-company exposure is treated as a critical defect regardless of who noticed.
6. **Sensitive by default, masked by default.** Identity documents, watchlist data and bank details are restricted, masked, read-logged and export-controlled.
7. **Evidence, not memory.** Authentication events, denials, approvals, limit usage, sensitive reads, configuration changes and break-glass are audited append-only.
8. **Fail closed for money, degrade explicitly for service.** When authorization cannot be evaluated, financial actions stop; read-only operational degradation follows a documented, security-reviewed policy only.
9. **No shared accounts.** Interactive access is personal; integrations use named service identities.
10. **Security testing is acceptance.** The controls here are unproven until negative tests demonstrate them.

## 3. Principals and authentication

| Principal | Authentication | Notes |
|---|---|---|
| Staff user | Unique account, password policy, session management; second factor required for privileged roles (finance controller, system administrator, manager) and for break-glass. Reference-pilot role accounts follow personas §4.8 (time-boxed, rotated, logged) | Sessions expire; concurrent session policy per configuration |
| Service identity (batch, workers) | Managed credential bound to a named service role | Least-privilege scopes; no interactive authority; rotation policy |
| Integration client (bank, acquirer, channel, messaging) | Per-adapter credentials (key/token/allow-listed network), rotatable | Allow-listed operations only; rate limited; full audit |
| AI assistant | Service identity with explicit tool allow-list and read scopes | No state-changing authority except via proposal flows executed under a human or governed service identity |
| External auditor | Time-boxed read-only account with contract scope | All reads logged; export-controlled |

Authentication requirements:

- Password/credential policy: length over complexity theatre, breach-list check where available, lockout with backoff, no security questions.
- Second factor: required for the privileged roles above; strongly recommended for all managers.
- Session: idle and absolute timeouts; logout invalidation; no credentials in URLs or logs.
- API/integration: token or signature verification per interface; secrets never in query strings; replay protection via nonce/timestamp where providers support it; idempotency per BR-INT-001.
- Failed authentication and lockout events are logged and alertable.

## 4. Authorization model

Per ADR-009: principal × action × resource × context → allow / deny / require-approval, evaluated at the single enforcement point, with property/company scope and effective-dated numeric limits.

The role catalogue, permission summaries, authority-limit defaults and separation-of-duties rules are in `role-and-authority-matrix.md`.

## 5. Data classification and handling

| Class | Examples | Read | Write | Export | Notes |
|---|---|---|---|---|---|
| **A — Restricted** | Guest identity documents and numbers, watchlist entries, bank account details, refund bank details, security incident details | Explicit permission; masked by default; every read logged | Explicit permission; maker–checker where configured | Prohibited except approved, logged, watermarked exports | Retention per OQ-024; encryption at rest and in transit |
| **B — Confidential** | Guest PII, folios, payments, deposits, AR, rate agreements, staff records boundary | Role-scoped by property/company | Role-scoped with limits | Scoped, logged | Standard confidentiality handling |
| **C — Internal** | Operational data: reservations without commercial terms, room states, tasks, occupancy | Role-scoped | Role-scoped | Scoped | — |
| **D — Configuration/Public-internal** | Property profile, room types, published rates (channel-facing) | Broad staff read; external per contract | Configuration authority only | — | Rate publication to channels is a derived projection |

Field-level rules: masking for class A in lists and search results; full view only within the permitted workflow (for example, check-in document verification); no class-A data in logs, error messages, analytics or AI prompts unless the AI use case is approved for that class and scoped accordingly.

**Data-at-rest lifecycle (SEC-07 resolution).** Classes A and B are encrypted at rest with managed keys. Backup/PITR generations inherit the same retention and access rules (sealed, access-restricted, defined expiry, legal-hold exception; crypto-shred for class A where the platform supports it). Degraded-mode/device caches are encrypted and purged at session end. Paper fallback captures (identity, payments) follow a secure collection-and-destruction procedure. Deleting a record also covers derived copies covered by the retention policy.

## 6. Tenancy and scope enforcement

Scope (tenant implicit at deployment level; property and company explicit) is enforced for **every surface class**:

| Surface class | Enforcement obligation |
|---|---|
| Commands | Scope from principal context; cross-scope targets rejected |
| Queries and lists | Scope injected server-side; no unfiltered raw query surfaces |
| Reports and dashboards | Scope-aware definitions; drill-down cannot escape scope |
| Exports and scheduled deliveries | Scope applied; class-A export approval; delivery recipients verified |
| Public APIs | Scope bound to the client principal; no scope parameters trusted from callers |
| Webhooks and callbacks | Signature/credential verification; payload scope validated against the owning integration |
| AI tools | Scope bound to the invoking principal; tool allow-list; no cross-scope aggregation |

Negative test obligation: for each surface class, tests must prove that a principal from property A (or company A) cannot read, write, aggregate, export or infer property B data — including via search, counts, error messages and timing-oracle-free response shapes.

## 7. Maker–checker catalogue

The following are dual-control when configured (defaults in the role matrix; always dual-control above limits):

| Action | Default posture |
|---|---|
| Refunds above band; refund bank-detail changes | Require approval + enhanced verification |
| Adjustments/allowances/comps above band | Require approval |
| Cashier variance approval | Require approval (never self-approval) |
| Deposit forfeiture | Require approval |
| Direct-bill transfers beyond credit eligibility | Require approval |
| Day reopen / period reopen | Require finance-controller authority with impact assessment |
| Credit limit overrides; account suspension/reinstatement | Require finance authority |
| Rate changes above revenue-defined bands; restriction overrides | Require revenue authority |
| Configuration changes to tax rules, mappings, authority limits, number series | **Finance Controller approval; tax rules additionally require tax-adviser sign-off**; maker–checker on limits; GM is read-only (FIN-11) |
| Role assignment changes; break-glass grants | Require security/admin authority; logged prominently |

Mechanism requirements per ADR-009 §4: approval bound to the exact intended payload; expiry; both identities recorded; approved execution re-validates preconditions at execution time.

## 8. Non-human principals

- **Integration clients**: one named principal per provider interface; least-privilege operation allow-list; secrets rotatable without downtime; all calls logged with correlation; failures alertable; replay/idempotency per WP 0.7 contracts.
- **Service identities**: no interactive login; scopes defined per job; job schedules and overrides audited.
- **AI assistants**: read tools scoped to the invoker; state changes only through governed services. **Execution authority = intersection(invoker authority, service identity permissions, policy)**; execution above the invoker's own authority requires a named human approver (SEC-10 resolution). Prompt and tool-call audit; evaluation gate before enabling each AI capability (WP 0.7).
- **Restore/DR procedures**: restored workers must run with duplicate-effect protection (BR-REL-007) — a security and financial control, not only an engineering detail.

## 9. Break-glass access

- Time-boxed elevation with a stated reason, granting the minimum additional permission.
- **No self-grant (SEC-02 resolution).** A principal can never request and grant its own break-glass or any role: request, grant and beneficiary are distinct identities; elevation is granted by two-person rule.
- Alerts security and finance at grant and expiry. The alert and 24-hour review recipient is a named role independent of the grantor — the Technical Lead (interim: Product Owner while OQ-033 is open); until a recipient is named, break-glass is disabled by policy.
- Every action under break-glass is flagged in audit; review within 24 hours by a second person independent of the grant and use; repeat use triggers investigation.
- Break-glass never grants financial approval authority; it may unblock access to perform an action that still requires normal authority for its approval steps.

## 10. Audit and monitoring

| Event class | Logged content | Retention direction |
|---|---|---|| Authentication (success/failure, lockout, MFA events) | Principal, method, source, outcome | Security retention policy |
| Authorization denials and limit breaches | Principal, action, resource, reason | As above |
| Sensitive reads (class A) | Principal, record reference, purpose context | As above |
| Maker–checker requests, approvals, rejections, expiries | Requester, approver, payload hash, outcome | Financial retention |
| Configuration changes (limits, tax, mappings, series) | Before/after, actor, effective date | Financial retention |
| Exports and scheduled deliveries | Principal, definition, scope, recipients | As above |
| Break-glass | Grant, expiry, actions, review outcome | Security retention |
| Integration failures and reconciliation exceptions | Interface, volume/value, owner | Operational retention |

Audit records are append-only (INV-PLT-4); access to audit data is itself scoped and logged; monitoring surfaces deny-bursts, lockout spikes, class-A read anomalies, break-glass use and reconciliation failures.

**Independence (SEC-13 resolution).** Audit and security events ship to an **append-only sink outside administrator control** (WORM/object-lock or a separate credential domain), hash-chained for tamper evidence; audit continuity is verified in restore drills.

## 11. Secrets and key management

- Centralised secret management; no secrets in repositories, tickets, logs or configuration files with production values.
- Rotation policy for integration credentials, service identities and encryption keys; rotation without service interruption.
- Recovery procedures include secrets restoration and post-restore validation (BR-REL-003; worker duplicate-effect protection).
- Environment separation: production secrets never present in test/development; production-like test data is masked or synthetic.

## 12. Threat model (top threats and controls)

| Threat | Control |
|---|---|
| Credential compromise (staff or integration) | MFA for privileged roles, lockout, least privilege, anomaly alerts, fast rotation |
| Insider fraud (refunds, voids, comps, cash) | Limits, maker–checker, full-population income-audit controls, pattern reporting (BR-INA-002/005) |
| Scope leakage via report/export/API/AI | Single enforcement point, surface registration, negative tests, export approval (RSK-SEC-001) |
| Guest identity document exfiltration | Class-A handling, masking, read logging, export prohibition (RSK-SEC-002) |
| Webhook spoofing / replay | Signature verification, namespaced reference dedupe, replay protection (BR-INT-001) |
| AI prompt injection via content (guest notes, documents) | Treat all content as untrusted; tool allow-list; no state change without governed authority; evaluation gate |
| Privilege escalation via technical role | Technical administration carries no business authority (personas §2.15; ADR-009) |
| Backup/restore abuse or duplicate external effects | Encrypted backups, access control, worker duplication protection (BR-REL-007) |
| Unauthorised data export to personal channels | Export approval, class-A prohibition, delivery audit |
| Session theft | Short sessions, secure cookies/tokens, no credentials in logs, device/session review for privileged accounts |

## 13. Security testing obligations

| Obligation | Gate |
|---|---|
| Cross-scope denial tests per surface class (positive and negative) | Foundation phase, every release |
| Self-approval denial and limit boundary tests | Foundation phase, every release |
| Payload-swap rejection for maker–checker | Maker–checker implementation |
| Sensitive-field masking and read-logging tests | Class-A implementation |
| Webhook signature/replay/dedupe tests | Integration implementation |
| AI tool authorization and injection-resistance tests | Each AI capability enablement |
| Secrets scanning in CI; dependency and image scanning | Continuous |
| Restore drill including duplicate-effect protection | Pre-pilot (BR-REL-002/003) |
| Independent penetration test focused on scope and money paths | Pre-pilot gate |
| Vulnerability remediation gate: no unfixed exploitable criticals at any release; highs fixed ≤30 days or time-boxed recorded acceptance ≤90 days; SBOM + dependency/image scans in the release pack | Every release; reassessed at D9 and before the first property (SEC-14) |

## 14. Incident and breach response

Security incidents (scope leak, credential compromise, unauthorized access, data loss) and privacy breaches. Owner: Security/Privacy Adviser with the Technical Lead (interim: Product Owner with the Technical Lead until OQ-033).

| Element | Requirement |
|---|---|
| Severity | Aligned to the programme defect scale S0–S3; scope leakage and class-A exposure are always S0 |
| Declaration | Security/Privacy Adviser + Technical Lead (interim: PO + TL); on-call may declare and contain immediately |
| Containment clocks | Scope leak, credential compromise or class-A exposure isolated/revoked within 1 hour of confirmation |
| Evidence | Forensic copy and chain of custody; events exported to the append-only sink (§10); log rotation never destroys evidence |
| Notification | Counsel decision tree: regulator, data subjects, insurers — timelines configured on counsel advice (NDPA duties UNVERIFIED); no legal position is asserted here |
| Post-incident review | PIR within 10 business days, tracked actions, breach register maintained |
| Rehearsal | Tabletop at the reference release (D9 evidence); repeated before first-property deployment |

## 15. Privacy operations

Legal specifics remain counsel-confirmed; the operating mechanics are design commitments.

1. **DSAR workflow** — intake, identity verification, tracked clock (interim design default 30 days from verified request; counsel confirms the statutory clock), fulfilment evidence, refusal reasons.
2. **Lawful-basis register** — per processing activity: purpose, lawful basis, retention category, recipients, storage location and cross-border status.
3. **Processor and transfer register as an enablement gate** — no new processor or cross-border transfer touching personal data goes live without a recorded agreement, transfer mechanism and register entry (ties to the integration interfaces and OQ-026).
4. **Retention instantiation** — per-category rules from the OQ-024 recommended schedule (UNVERIFIED pending counsel), evidenced deletion, legal-hold exception, and erasure tests covering derived copies and caches (§5).
5. **Owner** — Security/Privacy Adviser (interim: Product Owner until OQ-033); evidence surfaced at the acceptance gate and reference release.

## 16. Open items and dependencies

| Item | Owner | Note |
|---|---|---|
| OQ-024 guest data retention periods | Security / Legal | Drives class-A lifecycle and erasure design |
| OQ-019 (closed) ID capture requirements | Hotel Operations / Security | Drives class-A scope and workflow |
| OQ-026 (closed) hosting/region and OQ-028 licensing | Product Owner / Legal | Deployment security and data residency |
| OQ-002/OQ-033 named Finance/Security approvers | Product Owner | Approval gates for this model |
| Authenticator choice (MFA method) | Platform | Deployment-phase decision (WP 0.7) |

## 17. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial security model issued with WP 0.5 | PROPOSED |
| 0.2 | 2026-09-23 | P0 resolutions: framework-generic surfaces (SEC-01, §2.3); no-self-grant break-glass with named reviewer (SEC-02, §9); reference-pilot account rules (SEC-05, §3) | PROPOSED |
| 0.3 | 2026-09-23 | P1 resolutions: data-at-rest lifecycle (SEC-07, §5); AI authority intersection (SEC-10, §8); audit sink (SEC-13, §10); vulnerability gate (SEC-14, §13); incident/breach response (§14) and privacy operations (§15) added; financial-config approval restriction (FIN-11, §7) | PROPOSED |
| 0.4 | 2026-09-23 | P2: approval lines aligned to charter §12 (SEC-11) | PROPOSED |
