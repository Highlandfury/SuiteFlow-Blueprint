---
doc-id: GOV-RISK
title: Risk Register
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Product Owner (accountable); technical risks delegated to Technical Lead
applies-to: all blueprint and programme work
---

# Risk Register

Categories follow the programme mandate: architecture, product, hospitality domain, financial, security, data, integration, performance, scalability, migration, deployment, operational, licensing, AI, and programme governance.

Impact and likelihood ratings are **planning judgments (ASSUMED)**, to be re-rated as Phase 1 research and Phase 2 audit produce evidence. Severity = combination, recorded as H/M/L. Every risk has an owner who is accountable for acting on the trigger.

## 1. Register

| ID | Category | Risk | Impact | Likelihood | Sev | Owner | Mitigation / control | Trigger to act |
|---|---|---|---|---|---|---|---|---|
| RSK-GOV-001 | Programme | Blueprint drift: implementation proceeds in parallel and silently redefines the target; the gap matrix later rationalises whatever was built | H | H | **H** | Product Owner | Separate blueprint repository; change control (charter §7); every implementation item traced to a capability ID; gap matrix reviews current state against the frozen blueprint | An implementation decision that cannot cite an ACCEPTED blueprint requirement |
| RSK-GOV-002 | Programme | Evidence debt: documents claim behaviour that was never verified; "documented" is treated as "done" | H | M | **H** | Technical Lead | Confidence labels required; UNVERIFIED never presented as fact; phase gates require cited evidence; ASTRA passes | Any blueprint or status claim without a source or test reference |
| RSK-ARCH-001 | Architecture | Domain ownership ambiguity between hotel operations, control plane and accounting: two systems both believe they own a number (availability, balance, business date) | H | M | **H** | Principal Architect | Target ownership matrix (WP 0.2); one source of truth per datum; all derived copies rebuildable and reconciled | Two sources disagree on a balance, availability count or business date |
| RSK-ARCH-002 | Architecture | Multi-property/chain target is designed as an afterthought; single-property shortcuts in tenancy, numbering and configuration block the second property | H | M | **H** | Principal Architect | Property/company scoping designed from the start (WP 0.2, 0.5); single property must be a trivial case, not a special case | Any schema or permission decision that cannot express a second property |
| RSK-ARCH-003 | Architecture | Blueprint becomes unbuildable in the pilot horizon: over-engineered target displaces the 24-week pilot | H | M | **H** | Product Owner | Pilot-first roadmap; capability priorities P0–P4; explicit pilot release scope in scope.md; phased delivery | Pilot-critical capability slips while P3/P4 design work expands |
| RSK-PROD-001 | Product | Scope breadth (24 capability domains) consumed without prioritisation; everything is "must have" | H | H | **H** | Product Owner | Capability map with P0–P4 and pilot flags (WP 0.1 pass 2); decision log discipline | More than ~20% of capabilities marked pilot-critical |
| RSK-PROD-002 | Product | Accepted MVP boundaries conflict with hotel reality at kickoff (e.g. F&B/group business assumed out but present at the property) | H | M | **H** | Product Owner / Hotel Ops | OQ-014, OQ-017 resolved before capability prioritisation closes | Hotel walkthrough or UAT reveals an operating workflow with no capability coverage |
| RSK-DOM-001 | Hospitality domain | Blueprint workflows are not validated with the actual hotel; they reflect generic PMS theory rather than Golfview's practice | H | M | **H** | Hotel Operations rep (OQ-003 open) | Operational-realism test (charter §10); workflow review with named hotel staff; walkthrough against a real operating day | No named hotel reviewer at a workflow approval gate |
| RSK-DOM-002 | Hospitality domain | Terminology drift between blueprint English and hotel usage; staff misread screens and control states | M | M | M | Hotel Operations | Glossary is normative; UI wording reviewed with staff (WP 0.6); training materials reuse hotel vocabulary | Confusion reported in UAT or training |
| RSK-FIN-001 | Financial | Wrong revenue recognition or tax point: deposits, forfeitures, direct bills or nightly postings recognised at the wrong time or twice | H | M | **H** | Finance Controller (OQ-002 open) | Financial architecture (WP 0.4) with worked examples; golden-day acceptance fixture; independent income-audit controls | Any financial scenario without a worked end-to-end example and test obligation |
| RSK-FIN-002 | Financial | Duplicate accounting under retries/partial failure (at-least-once delivery) | H | M | **H** | Integration Architect | Durable identity, idempotency keys, uniqueness constraints, reconciliation checks (WP 0.7; validates DP-ADR-003) | Any posting path without idempotency and duplicate-detection design |
| RSK-FIN-003 | Financial | POS/acquirer settlement and bank reconciliation remain manual and unreconciled; cash and card balances drift | H | M | **H** | Finance Controller | Explicit clearing lifecycle for each payment method (BR-PAY-002/006); settlement matching and exception queue; recurring variance reporting | Unmatched POS batches older than the agreed clearance window |
| RSK-FIN-004 | Financial | Nigerian tax/regulatory position is wrong or incomplete (VAT, consumption tax, service charge, withholding, fiscalisation) | H | M | **H** | Finance Controller | Phase 1 primary-source research (OQ-029); tax adviser sign-off; configurable tax engine with effective dates | Tax rule implemented without a cited statutory source |
| RSK-SEC-001 | Security | Cross-property or cross-company data exposure through a missing scope filter in a query, report, API or AI tool | H | M | **H** | Security Engineer | Server-side scope enforcement as a platform invariant (WP 0.5); permission test obligations; negative tests per report/API | Any data-access path not covered by a scope test |
| RSK-SEC-002 | Security | Guest identity documents and personal data mishandled: over-retention, unencrypted storage, unauthorised access | H | M | **H** | Security Engineer | Purpose-specific access, encryption, retention rules (OQ-024), audit trail on sensitive reads; privacy impact assessment | ID images accessible outside the registration workflow or retained indefinitely |
| RSK-SEC-003 | Security | Secrets and credentials sprawl across environments; provider keys exposed or unrotated | M | M | M | Platform Engineer | Central secret management, rotation policy, no secrets in repos, recovery procedures include secrets restoration (BR-REL-003) | Any credential found in repository or ticket history |
| RSK-DATA-001 | Data | Migration from incumbent records produces duplicates, orphaned balances or broken guest history; opening balances do not reconcile | H | M | M | Data Architect | Migration architecture with dry runs and reconciliation (WP 0.7); opening-balance proof; duplicate strategy; rollback plan | OQ-025 answer reveals data of unverified quality |
| RSK-INT-001 | Integration | Bank/acquirer interfaces unavailable, immature or undocumented; payment automation cannot be proven in time | H | M | M | Integration Architect | Provider-neutral adapter design; manual/reference reconciliation fallback accepted in MVP (BR-MVP-005) | OQ-004/OQ-006 answers arrive late or with no interface documentation |
| RSK-INT-002 | Integration | No channel/OTA distribution at pilot limits commercial value or forces parallel manual channel management | M | M | M | Product Owner | OQ-018 resolution; manual channel handling documented as a pilot operating procedure if deferred | Hotel cannot operate its sales channels with the delivered scope |
| RSK-PERF-001 | Performance | Night audit and month-end reporting exceed their operating window at 200 rooms with realistic data volumes | H | M | M | Performance/DB Engineer | Volume assumptions stated; performance tests with production-scale synthetic data; close-window budget as NFR | Close runtime approaches the hotel's cutoff window in test |
| RSK-SCALE-001 | Scalability | Chain-level reporting and shared-profile requirements cannot be met by the pilot tenancy model without redesign | M | M | M | Principal Architect | Tenancy and reporting design reviewed against the multi-property target in WP 0.2/0.7 | First serious chain requirement appears after pilot go-live |
| RSK-MIG-001 | Migration | Cutover of a live hotel operation causes data loss or extended downtime; rollback fails | H | M | M | Release Manager | Rehearsed cutover, parallel-run plan, verified backups and restore, one-hour RTO drill (BR-REL-003), controlled go/no-go | First cutover rehearsal reveals an unproven rollback step |
| RSK-DEP-001 | Deployment | 24×7 support depends on too few people; escalation fails at 03:00 during close | H | M | M | SRE Lead | Named rota, severity model, runbooks, managed provider if needed (BR-REL-001); cost approval REL-002 | Rota cannot be filled without a single person on all critical paths |
| RSK-DEP-002 | Deployment | RPO/RTO targets unproven; restore drill reveals longer recovery or duplicate external effects | H | M | M | SRE Lead | Timed isolated restore with reconciliation; worker-safety control (BR-REL-007); drills before launch | Drill misses 1-hour objective or produces duplicate payments/documents |
| RSK-OPS-001 | Operational | Hotel staff cannot or will not operate the new controls (dual control, cashier close, ID capture) under peak load | H | M | M | Hotel Operations rep | Workflow realism (charter §10); training materials; role-based UX; pilot acceptance with hotel | Peak-period observation or UAT shows control steps being bypassed |
| RSK-OPS-002 | Operational | Business-date discipline breaks: postings land on the wrong operating day; close is repeatedly late or skipped | H | M | M | Finance Controller | Business-date lock and close-window alerts (BR-REL-005); exception reporting; income-audit gate | Any posting to a closed date or repeated late close |
| RSK-LIC-001 | Licensing | Open-source licensing position (network-copyleft dependencies) blocks commercial distribution or forces rework | M | M | M | Legal counsel | Counsel review before distribution (OQ-028); repository boundary discipline maintained | Customer distribution or resale contemplated |
| RSK-TEN-001 | Architecture | Pilot tenant isolation is assumed rather than enforced; "one site per hotel" becomes the only control | H | L | M | Security Engineer | Explicit tenant boundary tests; platform-level enforcement; Phase 2 verification of current controls | Any shared component without a tenant-scope test |
| RSK-AI-001 | AI | AI features present recommendations as authority (rates, availability, balances) or leak data across properties | H | M | **H** | Product Owner / Security | AI governance rules (charter §15; WP 0.7 AI architecture): AI proposes, governed services decide; per-tool authorization; evaluation suite | Any AI output that can change price, availability, balance or permissions without a governed action |
| RSK-PROG-001 | Programme | Key knowledge concentrates in one engineer/AI session; continuity lost | M | M | M | Technical Lead | Documented decisions (this repository), ADRs, runbooks; second-person review; onboarding materials | Any critical area understood by only one person/process |

## 2. Top risks requiring early decisions

1. **RSK-GOV-001 (blueprint drift)** — controls the entire programme's value. Mitigated only by discipline: nothing implemented without an ACCEPTED capability requirement and a transition-architecture decision.
2. **RSK-FIN-001 / RSK-FIN-004 (financial and tax correctness)** — highest consequence, lowest tolerance. Blocked by OQ-011, OQ-012, OQ-021 and Phase 1 tax research.
3. **RSK-SEC-001 (scope leakage)** — designed out at platform level in WP 0.5, tested per surface.
4. **RSK-ARCH-002 (multi-property afterthought)** — the Product Owner has directed an enterprise target with pilot-first delivery; shortcuts in tenancy now become expensive later.
5. **RSK-OPS-001/002 (operational adoption and business-date discipline)** — the most common cause of PMS failure in practice; requires hotel participation, not better software alone.

## 3. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial register with 30 seeded risks | PROPOSED |
