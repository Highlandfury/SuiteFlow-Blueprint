---
doc-id: ADR-008
title: Invoice and statutory document authority
status: PROPOSED
version: 0.2
date: 2026-09-23
owner: Financial Systems Architect (drafted); Finance Controller + tax adviser (approval; roles open)
applies-to: full enterprise target
depends-on: [ADR-005, ARCH-RULES]
supersedes: none (rules on OQ-011, closed 2026-09-23)
---

# ADR-008: Invoice and statutory document authority

## Context

OQ-011 asked which document is the statutory and customer-facing invoice: the hotel-generated folio invoice or the accounting system's tax invoice. The question has three dimensions: who issues the document, which system holds its authority, and which numbering/particulars regime applies. It was closed by Product Owner adoption of the industry-standard answer on 23 September 2026 (`00-governance/industry-standard-answers.md`): SuiteFlow issues the customer-facing document; a required statutory/fiscal document derives from and links to it. Nigerian VAT invoice particulars and any fiscalisation requirements (OQ-029) still shape the details; tax and legal advice is not yet available.

## Problem

Which system issues which financial document, under what authority and numbering, without creating dual documents for the same supply or contradictions between guest-facing and statutory records?

## Target-state requirement

- Exactly one operative document per supply; no duplicate invoicing.
- Documents are traceable to their underlying folio items and postings.
- Numbering is series-based, unique, never reused; gapless where law requires (ADR-004).
- Tax particulars required by law appear on the operative document.
- Corporate/agent statements reconcile to the AR ledger.
- The design must not block on open tax advice, but must not pretend the advice exists.

## Options

| Option | Summary | Assessment |
|---|---|---|
| A. SuiteFlow issues all guest/corporate documents; Accounting Authority is books-only | Operationally aligned: invoices come from folio truth | **Chosen as the default**, with the statutory-form caveat below |
| B. Accounting Authority issues all invoices | Tax-system alignment | Poor operational fit: guests need folio invoices at the desk; creates latency and duplicate-record risk |
| C. Dual documents (SuiteFlow receipt + accounting tax invoice) | Perceived compliance safety | Rejected as default: two documents for one supply is the classic reconciliation trap; permitted only where law explicitly requires a separate fiscal document, in which case it must be a *derived, linked* document with a one-to-one mapping |

## Decision

1. **SuiteFlow is the issuing authority for guest-facing and corporate financial documents** — folio invoice, pro forma, receipts, in-house statements — produced from folio truth at settlement/checkout, numbered from configured series, and linked to their items.
2. **The Accounting Authority produces statutory accounting documents** (AR invoices/credit notes, journals) where configured; these are **linked one-to-one** to the event and its SuiteFlow document, never an independent second invoice for the same supply.
3. **One supply, one operative document.** Where a separate fiscal document is legally required (OQ-029), it is derived from and linked to the SuiteFlow document; both carry the same series reference and the mapping is testable.
4. **Numbering.** Per-property, per-series, configured (prefix, width, reset, gap policy), never reused; gapless allocation supported for series where law requires it (ADR-004 decision 2).
5. **Particulars.** The document format is configuration carrying the particulars required by law (entity name, tax number, tax rate/amount, date, description, series number). Format changes are configuration changes with effective dates.
6. **Corporate statements** are produced from AR by the Accounting Authority; their content reconciles to folio-level detail held in SuiteFlow; disputes reference the folio evidence.
7. **Proformas are not invoices**: they carry a clear non-statutory marking and no series that implies tax invoicing.
8. **This ADR is PROPOSED pending:**
   - Finance Controller confirmation at acceptance (OQ-002);
   - tax advice on VAT invoice particulars and any fiscalisation regime (OQ-029);
   - legal entity/tax identifiers (OQ-001).
   The issuing-authority question itself is ruled (OQ-011 closed by Product Owner adoption, 23 Sep 2026). No invoicing implementation may be committed before the pending confirmations; the design above is the working basis.

## Reasoning

- Guest-facing invoicing must live at the moment and place of settlement; deriving it from folio truth avoids latency and duplicate records.
- The one-to-one link rule preserves the compliance option without accepting the dual-document trap.
- Making the open decisions explicit keeps the design honest: the *shape* is decidable now; the *particulars* wait on advice.

## Trade-offs

- If Nigerian practice requires a fiscal device or accredited e-invoicing provider, SuiteFlow's role narrows to source-of-truth and the provider becomes an adapter (INT-014) — this is anticipated by the adapter boundary, not by redesign.
- Configuration-managed formats add administration; accepted for multi-property/multi-jurisdiction readiness.

## Risks

| Risk | Mitigation |
|---|---|
| Dual invoicing slips in through parallel manual processes | Single issuing authority rule; reconciliation of document series; audit of AR vs folio links |
| Fiscalisation requirement discovered late | Adapter boundary (INT-014) and OQ-029 tracked as launch gate for invoicing flows |
| Format non-compliance | Tax adviser review of configured templates before go-live |
| Numbering gaps where gapless is required | Series configuration + gap audit; release gate check |

## Consequences

- Folio invoice/pro forma entities and numbering (ARCH-DOMAIN, CAP-FOL-011) stand; their statutory particulars are configuration.
- Finance sign-off on document templates becomes a release gate.
- Test obligations: one operative document per supply; series uniqueness and gap audit; AR-to-folio reconciliation; proforma non-statutory marking.

## Implementation impact

No immediate change. Phase 2 verifies current document behavior; Phase 4 plans transition.

## Migration impact

Historical document series migrate with numbering continuity preserved; no reuse of numbers across the migration boundary.

## Review trigger

- Tax adviser ruling (OQ-029) or Finance confirmation changing the issuing authority.
- A new jurisdiction with different document requirements (multi-property expansion).

## Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial decision issued with WP 0.4; OQ-011 confirmation pending | PROPOSED |
| 0.2 | 2026-09-23 | OQ-011 closed by Product Owner adoption (industry-standard answer); pending items restated (OQ-002, OQ-029, OQ-001) | PROPOSED |
