---
doc-id: DEP-ARCH
title: Target Deployment and Operations Architecture
status: PROPOSED
version: 0.3
date: 2026-09-23
owner: DevOps / SRE (drafted); Product Owner (approval; OQ-010 open; OQ-038 deferred to first property; OQ-026 closed)
applies-to: pilot and enterprise target
depends-on: [ADR-001, ADR-002, ARCH-NFR, SEC-MODEL, ARCH-RULES]
---

# Target Deployment and Operations Architecture

## 1. Purpose and principles

How SuiteFlow is deployed, promoted, observed, recovered and operated at 24×7 for a production hotel. Principles:

1. **One versioned artefact per release**, promoted without rebuild (ADR-002 §8); configuration is data, not code.
2. **One deployment per tenant** (ADR-002); the pilot is a one-property tenant.
3. **Recovery is designed, rehearsed and timed**, not aspirational: 1-hour RPO/RTO (BR-REL-002/003).
4. **Financial degradation never stops guest operations** (ADR-006 §7); the inverse is also true — operations must not corrupt financial state.
5. **Observability is a feature**: every governed transition, integration attempt and posting is traceable end-to-end.
6. **No direct production modifications**: all change flows through versioned migration and release process.

## 2. Environments and promotion

| Environment | Purpose | Data | Access |
|---|---|---|---|
| Development | Build, unit/module tests | Synthetic | Engineering |
| Test/CI | Automated suites, performance at scale | Synthetic (production-scale) | Engineering + QA (scoped) |
| UAT | Scripted acceptance, migration rehearsal, hotel walkthroughs | Production-like synthetic | Programme + hotel participants |
| Production | Live operation | Real | Least privilege; break-glass governed |

Promotion: artefact built once, promoted by digest; migrations run as versioned, restartable steps; environment differences are configuration records with effective dates. A release cannot be promoted on an environment where a required migration or configuration state is absent.

## 3. Topology

```text
Users (desk, tablet, phone)
   │  TLS
Edge (reverse proxy / WAF as applicable)
   │
Application nodes (stateless)  ←→  Cache
   │
Database (primary; PITR continuous)   ← read replica (reporting)
   │
Workers (outbox, integrations, close jobs)  ←→  Queue
   │
Adapters → external providers (bank, acquirer, channels, messaging, fiscal)
```

- Stateless application nodes scale horizontally if needed; state lives in the database and queue.
- Workers are separated from request path; close, posting and integration jobs have dedicated concurrency controls (single-runner semantics per property for close).
- Reporting reads from the read model/replica, never the primary under load (RPT-ARCH §1).
- Backups, replica lag and queue depth are monitored, not assumed.

## 4. Connectivity, power and degraded operation

The synthetic reference pilot has no site; it runs on a hosted reference environment. The requirements below describe first-property deployment conditions (OQ-038 deferred to the First-Property Deployment Gate) and the degradation behaviour the product must support; D-1/D-2 become mandatory at the first property, D-3…D-6 apply to the product regardless of site:

| # | Requirement | Label |
|---|---|---|
| D-1 | Dual-path WAN at the property (primary wired + secondary mobile/failover) with automatic failover | REQUIRED |
| D-2 | UPS coverage for front desk, cashier and network equipment sufficient for generator start/bridge | REQUIRED |
| D-3 | Desk devices on wired network where possible; Wi-Fi for tablets/phones | TARGET |
| D-4 | Degraded mode: read from last-known state with explicit staleness labelling; non-financial captures queue locally and sync with conflict flags; financial actions blocked with clear UI and escalation | REQUIRED (UX-ARCH §12) |
| D-5 | Manual fallback runbook (paper capture of check-ins/payments) with same-day reconciliation procedure when connectivity is fully lost | REQUIRED |
| D-6 | Full offline financial operation | NOT OFFERED at pilot (deliberate; correctness over false availability) |

## 5. Recovery architecture

| Element | Design |
|---|---|
| RPO 1 h (REQUIRED) | Continuous database PITR (binlog/WAL) with frequent file/config replication; encrypted offsite copies |
| RTO 1 h (REQUIRED) | Warm recovery capacity with current image/migrations/secrets; automated restore procedure; rehearsed failover |
| Restore integrity | Timed restore followed by financial reconciliation (last closed day, control accounts) within the hour (BR-REL-007) |
| Worker safety | Restored workers must not duplicate external or accounting effects: duplicate-effect protection proven by drill (TO-REL-002) |
| Drill cadence | Pre-release and quarterly; evidence recorded; failure to meet 1 h blocks go-live (OQ-issues aside) |
| Data loss disclosure | Any recovery that loses committed data beyond RPO is an incident with financial reconciliation and customer notification procedure |

## 6. Observability

| Surface | Content | Alerting |
|---|---|---|
| Outbox/queue | Lag, claims, attempts, dead letters | Lag > 5 min (NFR O-1) |
| Close | Run state, step progress, failures, duration | Page on failure within 5 min, 24×7 (O-2) |
| Integrations | Per-interface health, lag, error classes, reconciliation status | Auth failures and dead letters page owners |
| Database | Availability, replica lag, PITR status, storage | Capacity thresholds |
| Security | Auth failures/lockouts, denials, break-glass, class-A reads anomalies | Security owner |
| Business KPIs | Occupancy, arrivals outstanding, unclosed sessions (operational, not just technical) | Close-window dashboards |

Correlation identities flow from UI action → transition → event → posting → reconciliation; support can reconstruct an incident without guesswork.

## 7. Release management

| Stage | Requirement |
|---|---|
| Versioning | Every release versioned; migrations forward-only with restart capability; rollback plan documented and rehearsed |
| Change window | Default low-occupancy window agreed with the hotel; close-window changes require finance agreement |
| Feature flags | New behaviours can be enabled per property; flags are configuration with audit |
| Rollback | Application rollback + data compatibility guaranteed for the defined window; if a migration is irreversible, the plan states the recovery path before release |
| Evidence | Release evidence pack (QA gate §5) attached to the release; includes SBOM and dependency/image scan results with the vulnerability-gate disposition (SEC-14) |
| Communication | Release notes to operations and finance per documentation architecture |

## 8. Secrets and environment separation

Per SEC-MODEL §11: central secret management; no production secrets outside production; rotation without downtime; recovery procedures include secrets restoration; environment parity verified (a release tested without its secrets path is not tested).

## 9. Capacity and cost

- Pilot capacity: 200 rooms; 50 concurrent sessions; 1–3M folio items/year; reporting replica handles analytics.
- Recovery capacity must be warm enough to meet RTO at pilot volume; capacity evidence recorded.
- Cost/staffing consequences of 24×7 and 1-hour recovery require Product Owner approval (REL-002).

## 10. Runbook catalogue (minimum)

| Runbook | Owner |
|---|---|
| Close failure and recovery (WF-NA-002) | Night ops + on-call |
| Post-restore reconciliation and worker safety | SRE + Finance |
| Integration outage per interface (channel, payments, banking) | Integration + service desk |
| Authorization outage / degraded mode | Platform + security |
| Cashier discrepancy escalation | Finance |
| Security incident (scope leak, credential compromise, break-glass misuse) | Security |
| Database failover and replica promotion | SRE |
| Certificate/credential rotation | Platform |

## 11. Open items

| Item | Owner | Effect |
|---|---|---|
| OQ-038 connectivity/power facts | Product Owner | D-1/D-2 validation and cost at the first property; **deferred to the First-Property Deployment Gate** |
| OQ-010 support and recovery cost approval | Product Owner | 24×7 rota, warm capacity |
| OQ-026 (closed) hosting region | Product Owner | Topology, residency, latency |
| OQ-009 (closed) availability objective | Product Owner | A-1 measurement |
| Managed support partner decision | Product Owner | 24×7 sustainability (RSK-DEP-001) |

## 12. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial deployment and operations architecture issued with WP 0.7 | PROPOSED |
| 0.2 | 2026-09-23 | Synthetic reference pilot: connectivity/power requirements reframed for first-property deployment (OQ-038 deferred); reference pilot runs hosted | PROPOSED |
| 0.3 | 2026-09-23 | P1 resolution: release evidence includes SBOM and vulnerability-gate disposition (SEC-14) | PROPOSED |
