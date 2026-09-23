---
doc-id: ADR-010
title: Integration, event delivery and idempotency contract
status: PROPOSED
version: 0.2
date: 2026-09-23
owner: Integration Architect (drafted); Principal Architect (review); Product Owner (approval)
applies-to: full enterprise target
depends-on: [ADR-001, ADR-003, ADR-005, ARCH-DOMAIN]
supersedes: rules on DP-ADR-003
---

# ADR-010: Integration, event delivery and idempotency contract

## Context

DP-ADR-003 proposed a transactional outbox with versioned events and durable idempotency, with proof incomplete. The target architecture already depends on events for housekeeping/service flows, reporting read models, CRM projection and external integrations, and the financial architecture already requires idempotent postings. This ADR rules on the contract that makes those guarantees real.

## Problem

How do state changes produce observable, reliable outside effects — internal consumers and external providers — without duplicate or missing effects, and with reconciliation as the final backstop?

## Target-state requirement

- Events are written in the same transaction as the state change; nothing is announced that did not happen (BR-INT-001/002).
- Duplicate work is expected under retry; duplicate effects are prohibited (INV-ACC-1, BR-ACC-002).
- Uncertain external outcomes (timeout after submission) are resolvable without guessing (BR-ACC-003).
- Consumers can be replayed, retried and inspected; failures are visible and owned.
- Provider specifics never leak into domain logic (ADR-001 §4).
- The contract must be testable under crash, retry, concurrency and partial failure.

## Options

| Option | Summary | Assessment |
|---|---|---|
| A. Synchronous in-process calls only | Modules call each other and providers inline | Rejected: couples availability, loses effects on crash, provides no replay or evidence |
| B. Fire-and-forget events without durability | Emit after commit, best effort | Rejected: loses effects on crash between commit and emit; no recovery |
| C. Transactional outbox, versioned events, idempotent consumers, reconciliation backstop | Durability in the same transaction; at-least-once delivery; idempotent effects | **Chosen** |
| D. External message broker as the primary durability mechanism | Broker-first eventing | Rejected at pilot scale: adds operational surface without changing the core guarantee; the outbox pattern is database-local and sufficient. The design does not preclude adding a broker later as a transport, because consumers are already idempotent and ordered per aggregate |

## Decision

1. **Transactional outbox with separate dispatch state.** Every governed state change writes its event(s) to an outbox table in the same database transaction as the state change. The event row is **immutable**; delivery workers claim, publish and record attempts in a **separate dispatch/attempt table** (claim, visibility deadline, outcome, next attempt). Replay and evidence derive from dispatch state, never by mutating events.
2. **Versioned envelope.** Every event carries: event identity (durable, unique), event type and version, occurrence time and business date, tenant/property scope, aggregate type and identity, actor/causation, correlation identity, and payload. Schema evolution is additive; breaking changes require a new version and a migration plan (ADR-003 §5).
3. **At-least-once, with idempotent consumers.** Consumers deduplicate by event identity (inbox records) and by domain idempotency keys. The system claims *observable exactly-once effects*, never *exactly-once delivery*.
4. **Write-ahead intent for outbound provider calls.** Before calling a provider, the intent is persisted (identity, payload hash, attempt); the outcome — success, failure, or uncertain — is recorded. Uncertain outcomes are resolved by status query or reconciliation, never by blind re-submission (BR-ACC-003).
5. **Failure model (mandatory per interface).** Each interface documents and implements: timeout, retry with bounded backoff, duplicate suppression, partial failure, authentication failure, rate limiting, external-success/local-failure and local-success/external-failure; reconciliation for every money-touching interface (BR-INT-003).
6. **Adapter isolation.** Adapters translate between provider protocols and provider-neutral domain contracts; adapters hold no domain rules and are the only place provider-specific data exists (ADR-001 §4, INT capability family).
7. **Event registry.** Events are registered configuration: type, version, owner, payload contract, consumers. Unregistered events cannot be emitted; registry changes follow change control.
8. **Observability and control.** Queue lag, attempt history, dead letters, replay tooling and health are first-class; failure alerts route to owners (BR-INT-002). Dead letters are never silently dropped.
9. **Reconciliation as the backstop.** Even with all the above, every money-touching interface reconciles daily; unexplained differences become owned exceptions (financial architecture §10).
10. **Replay safety.** Replay surfaces exist for support and recovery; replay is idempotent by construction and audited.

## Reasoning

- The outbox pattern converts a distributed-consistency problem into a database-local one, which is exactly right for a modular monolith with a single database per tenant.
- Versioned envelopes with identity and scope make multi-consumer delivery, replay and audit tractable.
- Writing intent before provider calls is what makes the "timeout after submission" case decidable rather than guessed.
- Refusing exactly-once claims keeps the team honest: effects are made idempotent instead of pretending delivery is perfect.

## Trade-offs

- At-least-once means every consumer must be idempotent; this is a permanent design obligation, enforced by tests.
- Outbox tables grow; partitioning/archival per data architecture.
- In-process delivery adds polling latency; acceptable for operational consumers, and read models can subscribe directly where latency matters (still idempotent).
- The registry adds ceremony for new events; accepted for traceability.

## Risks

| Risk | Mitigation |
|---|---|
| Consumer not idempotent (double effect) | Idempotency tests per consumer; reconciliation catches residual |
| Outbox worker crash losing claims | Claim semantics with visibility timeouts; attempts recorded; resumable |
| Event payload drift/breaking change | Versioned registry; additive-only rule; compatibility tests |
| Replay causing external duplicate | Provider call identity + provider idempotency where supported; write-ahead intent |
| Dead letters accumulating unowned | Alerting, ownership, ageing policy |

## Consequences

- Integration architecture (WP 0.7) specifies per-interface contracts on this base.
- Every emitting module must register events; architecture tests enforce declarations.
- Test obligations: crash between commit and publish; duplicate delivery; out-of-order and late delivery; provider timeout/uncertain outcome; replay; dead-letter ageing.

## Implementation impact

No immediate change. Programme P2 audits existing event/outbox practices; Programme P4 plans transition.

## Migration impact

Existing events and integrations migrate to the registry with identities back-filled where possible; unresolvable identities become reconciliation exceptions.

## Review trigger

- Adoption of an external broker, or multi-database topology, changing delivery assumptions.
- Evidence that outbox polling cannot meet latency requirements for a new use case.
- A provider that cannot support idempotent submission or status query (then reconciliation mitigations become mandatory interface terms).

## Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial decision issued with WP 0.7; rules on DP-ADR-003 | PROPOSED |
| 0.2 | 2026-09-23 | P1 resolution: immutable outbox events with separate dispatch/attempt state (TEC-03) | PROPOSED |
