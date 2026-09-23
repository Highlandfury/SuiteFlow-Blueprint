---
doc-id: INT-ARCH
title: Target Integration Architecture
status: PROPOSED
version: 0.2
date: 2026-09-23
owner: Integration Architect (drafted); Product Owner (approval)
applies-to: full enterprise target; pilot interfaces marked
depends-on: [ADR-010, ADR-011, FIN-ARCH, SEC-MODEL]
---

# Target Integration Architecture

## 1. Purpose

Every external connection is a reliability, security and reconciliation problem before it is a feature. This document applies the ADR-010 contract to each interface: authentication, identity, retry, failure handling, reconciliation, observability and versioning. Interfaces not yet in pilot scope are specified to the same standard so their later addition is configuration and adapter work, not redesign.

## 2. Principles

1. **Contract before vendor.** Domain contracts are provider-neutral; providers are bound through adapters with declared capabilities (ADR-010 §6, ADR-011).
2. **No silent failure.** Every interface has health, lag and failure surfaces with named owners; dead letters age visibly.
3. **Money interfaces reconcile daily.** Volume and value comparisons per interface per business day; differences become owned exceptions (BR-INT-003).
4. **Identity everywhere.** Namespaced external references (ADR-004 §3) with `(provider, reference)` uniqueness; duplicates absorbed idempotently.
5. **Secrets are managed, rotated, never logged** (SEC-MODEL §11).
6. **Replay is a feature.** Support and recovery can replay safely because consumers are idempotent by construction.

## 3. Delivery substrate

Per ADR-010: transactional outbox for outbound effects; inbound endpoints (webhooks/callbacks) authenticate, validate, deduplicate and acknowledge; async workers claim, retry with bounded backoff and dead-letter; all attempts evidenced. Event registry governs types, versions, owners and consumers.

## 4. Interface catalogue

### 4.1 Channel manager / OTA distribution (INT-002) — D13

| Field | Specification |
|---|---|
| Direction | Bidirectional: rates/availability/restrictions out; reservations, modifications, cancellations in |
| Auth | Per-provider credentials, rotatable; allow-listed endpoints |
| Identity | Provider reservation reference (namespaced); outbound ARI batch identity per property/date-range |
| Guarantees | ARI publication is a projection of availability (never an independent source); inbound bookings validated against live restrictions before acceptance |
| Retry/failure | Bounded retries; overbooking protection: inbound booking rejected if inventory no longer available, with provider notification; mismatch case opened |
| Reconciliation | Daily: inbound bookings vs received messages; ARI published vs current inventory; rate/restriction drift report |
| Versioning | Provider API version pinned per adapter; schema changes tested against conformance suite |
| Rate limits | Respect provider quotas; batch/backoff; never retry storms |

### 4.2 Booking engine / web direct (INT-003) — D13

| Field | Specification |
|---|---|
| Direction | Inbound reservations and payments; outbound availability and rate display |
| Auth | Public surface with rate limiting; payment via provider adapter (4.3) |
| Identity | Booking reference + provider payment reference |
| Guarantees | Same validation path as any booking (BR-AVL-002) — the booking engine gets no special shortcuts |
| Failure | Failed payment releases the hold per policy; abandoned bookings release inventory on timeout |
| Reconciliation | Daily: bookings vs payments vs inventory movements |

### 4.3 Payment gateway / acquirer (INT-004) — pilot path per ADR-011

| Field | Specification |
|---|---|
| Direction | Outbound capture/refund/status; inbound settlement files and webhooks |
| Auth | Provider credentials/signature verification; IP allow-list where available |
| Identity | Provider transaction reference; SuiteFlow payment identity; batch identity |
| Guarantees | No card data stored (BR-PAY-007); `PENDING_CLEARANCE` until evidence (BR-CSH-004) |
| Retry/failure | Write-ahead intent (ADR-010 §4); uncertain → status query → reconciliation |
| Reconciliation | Daily acquirer batch matching; unmatched ageing; fees reconciled to fee expense (FIN-ARCH §6) |

### 4.4 Banking and statement ingestion (INT-005) — pilot manual, automation later

| Field | Specification |
|---|---|
| Direction | Inbound statements/confirmations; outbound payment instructions only where a bank API is approved |
| Auth | Bank-specific (API credentials, SFTP, secure portal export) |
| Identity | Bank reference + value date; `(provider, reference)` uniqueness for dedupe |
| Guarantees | Transfer/cheque clearing states change only on bank evidence (BR-CSH-004) |
| Failure | Formats and access constraints are the primary risk (OQ-007); manual ingestion path with dual review |
| Reconciliation | Daily: transfer and cheque clearing vs bank statement entries; cash deposits vs bank credits; acquirer settlement vs terminal batches with fees (FIN-ARCH §10 checks 3, 4, 12, 13) |

### 4.5 External POS (INT-006) — D11

| Field | Specification |
|---|---|
| Direction | Inbound orders/checks, discounts, settlements; outbound room validation and posting confirmations |
| Auth | Per-terminal credentials |
| Identity | POS check identity + SuiteFlow posting identity (idempotent room posting; ADR-010) |
| Guarantees | Room posting validates stay and credit (CAP-POS-008); no duplicate posts on retry |
| Reconciliation | Daily: POS settlements vs folio postings vs outlet revenue (CAP-POS-010) |

### 4.6 Accounting interface (INT-007) — pilot

| Field | Specification |
|---|---|
| Direction | Outbound posting runs (journal documents), inbound document links and balances |
| Auth | Service identity scoped to posting operations only |
| Identity | `(property, business_date, run_type)` posting identity; document links one-to-one (ADR-005/008) |
| Guarantees | Idempotent; one committed posting per source event per dimension (INV-ACC-1) |
| Failure | Uncertain external outcome resolved by reconciliation, never blind re-post (BR-ACC-003) |
| Reconciliation | Daily control accounts and interface value comparison (FIN-ARCH §10) |

### 4.7 CRM projection (INT-008) — D12

| Field | Specification |
|---|---|
| Direction | Outbound guest projection (consented subset), stay/event signals |
| Auth | Service identity; scoped consumer |
| Identity | Guest identity + consent version; suppression list respected prospectively |
| Guarantees | Only consented data leaves; withdrawal propagates on the next projection cycle and is evidenced |
| Reconciliation | Periodic: projected population vs consented population; suppression drift report |

### 4.8 Messaging providers (INT-009) — pilot

| Field | Specification |
|---|---|
| Direction | Outbound email/SMS/WhatsApp; inbound delivery status and guest replies (where supported) |
| Auth | Per-provider credentials; sender identity registered with providers |
| Identity | Message identity + provider message reference |
| Guarantees | Delivery status recorded; failures retried; no guest communication marked sent without acceptance by the provider |
| Reconciliation | Delivery-failure report on the notification surface |

### 4.9 Door locks (INT-010) — OQ-020 closed: manual keys at pilot; integration later

| Field | Specification |
|---|---|
| Direction | Outbound key/access grants and revocations; inbound audit trail |
| Auth | Encoder/gateway credentials |
| Identity | Key/access identity bound to stay |
| Guarantees | Access validity follows stay state (INV-FO-6); checkout revokes |
| Failure | Offline encoder mode documented; revocation queue with alert if unsent |
| Reconciliation | Daily: issued keys vs active stays |

### 4.10 ID scanners and document capture (INT-011) — OQ-019 closed: capture type/number; scanning only if legally required

| Field | Specification |
|---|---|
| Direction | Device → SuiteFlow capture; no cloud dependency required |
| Auth | Local device pairing |
| Guarantees | Class-A handling (SEC-MODEL §5); retention per OQ-024; device images never leave the platform's storage controls except approved exports |

### 4.11 Kiosks / self-service (INT-012) — D16

| Field | Specification |
|---|---|
| Direction | Guest self check-in/out; payments via provider adapter |
| Auth | Kiosk service identity scoped to check-in operations |
| Guarantees | Same gates as desk check-in (BR-FO-001); overrides unavailable; escalation to desk |

### 4.12 Revenue management systems (INT-013) — D13

| Field | Specification |
|---|---|
| Direction | Outbound history/pickup; inbound rate recommendations (advisory) |
| Guarantees | Recommendations are proposals; human approval applies rates (CAP-AI rule, RTM-013) |
| Reconciliation | Applied-rate vs recommended-rate audit trail |

### 4.13 Government / fiscal systems (INT-014) — pending OQ-029

| Field | Specification |
|---|---|
| Direction | Outbound document submission/registration where required |
| Identity | Provider registration number per document |
| Guarantees | Documents derive from the operative SuiteFlow document (ADR-008 §3); one supply, one operative document |
| Failure | Submission retried; unresolved submission surfaced as a compliance exception with owner, never hidden |

### 4.14 External BI (BI-006) — D16

| Field | Specification |
|---|---|
| Direction | Outbound analytical extracts from closed, reconciled data |
| Auth | Consumer identity, scope-bound |
| Guarantees | Scope enforcement at extract definition; no class-A data; provenance (as-of, certification status) carried in extract metadata |

## 5. Global failure model

Mandate §39 scenarios applied uniformly; interface-specific deviations are stated in the catalogue.

| Scenario | Handling |
|---|---|
| Success | Acknowledge, record evidence, reconcile |
| Timeout | Treat as uncertain; status query or reconciliation (never assume failure or success) |
| Retry | Bounded backoff with jitter; attempt history; idempotency identity constant across retries |
| Duplicate request (inbound) | Absorb by `(provider, reference)`/message identity; return the original outcome |
| Duplicate webhook | Deduplicate by message/auth identity; no second effect |
| Partial failure (batch) | Per-item status; partial batches recorded as split outcomes; no all-or-nothing pretence |
| Auth failure | Alert immediately (not retried into lockout); credential rotation path; owner paged |
| Rate limit | Respect quotas with backoff; queue depth alerting |
| External success / local failure | Resolve by status query/reconciliation; local effect applied idempotently once resolved (write-ahead intent) |
| Local success / external failure | Retry; if unresolvable, reconciliation case; never hide the external gap |
| Provider outage | Degrade per interface: channel closes sale or falls back; payments remain manual-reference; reconciliation catches up |
| Schema drift / version change | Adapter conformance suite fails; provider version pinned; upgrade is a controlled change |

## 6. Reconciliation obligations

| Interface | Frequency | Comparison | Owner |
|---|---|---|---|
| Payments/acquirer | Daily | Batch totals, fees, individual references | Finance |
| Banking | Daily | Clearing accounts, deposits, statement lines | Finance |
| Accounting | Daily | Control accounts, posting links, revenue by family | Finance |
| Channel/OTA | Daily | Bookings received vs channel reports; ARI drift | Reservations/Revenue |
| POS | Daily | Settlements vs postings vs outlet revenue | F&B + Finance |
| Messaging | Daily | Delivery failures and queued volume | Front office |
| Locks | Daily | Issued credentials vs active stays | Front office |
| CRM projection | Periodic | Consented population and suppression sync | Sales/Marketing |
| Fiscal | Per submission window | Submissions vs issued documents | Finance |

Each reconciliation produces cases with owner, age and escalation; the financial ones feed the close gate (FIN-ARCH §10).

## 7. Security requirements per interface

Per SEC-MODEL: per-provider principal with least privilege; credential rotation; signature/authenticity verification for inbound; payload validation and size limits; no secrets in logs; class-A data minimisation in outbound payloads; rate limiting; audit of all interface operations with correlation identities.

## 8. Acceptance obligations per interface

Each interface is "done" only with: contract conformance tests; failure-scenario tests (timeout, duplicate, partial, auth, rate limit, both-sided failures); reconciliation test with injected mismatch; observability evidence (health, lag, dead letters); secret rotation rehearsal; and runbook documentation. Money interfaces additionally pass the financial test pack (WP 0.7 QA strategy).

## 9. Open items

| Item | Owner | Affects |
|---|---|---|
| OQ-004/005/006/007 bank and acquirer facts | Product Owner / Finance | Payments and banking interfaces |
| OQ-029 fiscalisation | Legal / Finance | Fiscal adapter necessity |
| OQ-018 (closed) channel strategy | Product Owner | Channel manager timing (D13) |
| OQ-020 (closed) locks | Hotel Ops | Lock interface in/out of pilot |
| OQ-030 (closed) provider capability evidence | Platform / Finance | ADR-011 binding |
| Cross-cutting: processor/transfer gate (SEC-08) | Security/Privacy Adviser (interim: Product Owner) | No new processor or cross-border transfer touching personal data without a recorded agreement, transfer mechanism and register entry (SEC-MODEL §15) |

## 10. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial integration architecture issued with WP 0.7 | PROPOSED |
| 0.2 | 2026-09-23 | P1 resolutions: reconciliation cross-references corrected (FIN-03); processor/transfer enablement gate (SEC-08) | PROPOSED |
