---
doc-id: GOV-INPUTS
title: Inputs Register — Accepted Requirements and Proposed Architecture Inputs
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Product Owner (accountable)
applies-to: all blueprint work
---

# Inputs Register

This register records the external inputs the blueprint is allowed to treat as requirements, and the architecture inputs it must validate rather than inherit. It exists so that "the requirement" and "the current proposal" are never confused.

## 1. Rules

1. **Accepted business requirements** (level 1 of the source-of-truth hierarchy) may constrain the blueprint. Each is recorded with its source document and a content hash taken at registration time.
2. **Proposed architecture inputs** (level 5 or below) may be read, tested against the target design and confirmed, modified, replaced or rejected. They may not constrain the blueprint while PROPOSED.
3. The blueprint may add requirements that the inputs do not contain. It may not silently drop or weaken an accepted business requirement. Any conflict is raised as an open question or ADR.
4. When a source document changes, this register is updated with a new version entry; hashes pin the version consulted.

## 2. Source provenance

| Ref | Document | Location | Hash (SHA-256) at registration |
|---|---|---|---|
| SRC-001 | Product Owner Kickoff Decision Log v0.2 (20 Aug 2026) | programme machine `/home/suiteflow/docs/` | `58aa916c86e92bf8d9a1bd506ffc595203db0f06292fc924e6940164a95cf7b1` |
| SRC-002 | Architecture and Financial Control Decision Pack v0.1 (20 Aug 2026) | programme machine `/home/suiteflow/docs/` | `e1404f7e59247f3d69f917f1314aa57cfa4befd5e1e2110defe684b86de27998` |
| SRC-003 | Product Owner Baseline (20 Aug 2026) | `Highlandfury/SuiteFlow` → `docs/` | `55beb43a6cd5b6b3264db27cecf3f069be2d3e83690c72f62901ee9eab5caf3d` |
| SRC-004 | Implementation-repository ADR statuses observed 23 Sep 2026 | `Highlandfury/SuiteFlow` → `docs/adr/` (working tree, branch `fix/real-checkin-ooo-workflow`) | not hashed; working tree |

## 3. Accepted business requirements

### 3.1 Pilot operating profile (SRC-001 §2, SRC-003)

| Req ID | Requirement | Status | Confidence |
|---|---|---|---|
| BR-PILOT-001 | Pilot hotel: Golfview Suites and Conference Center, GRA Ikeja, Lagos, Nigeria | Confirmed | VERIFIED (recorded decision) |
| BR-PILOT-002 | One property, approximately 200 rooms | Confirmed | VERIFIED |
| BR-PILOT-003 | Timezone West Africa Time (UTC+1); all business dates and cutoff rules use hotel-local time, never server UTC | Confirmed | VERIFIED |
| BR-PILOT-004 | Base currency Nigerian Naira (NGN) | Confirmed | VERIFIED |
| BR-PILOT-005 | Delivery target: 24 weeks to pilot production rollout, gated by finance, audit, security, restore and hotel acceptance | Confirmed | VERIFIED |
| BR-PILOT-006 | Registered legal entity name and tax identifiers | **Open** (PO-001A) | UNVERIFIED |
| BR-PILOT-007 | Named Finance Controller / pilot Finance representative | **Open** (PO-001B) | UNVERIFIED |
| BR-PILOT-008 | Named Hotel Operations representative and hotel acceptance approver | **Open** (PO-001C) | UNVERIFIED |

Operational interpretation recorded with the baseline: the one-property pilot does not require cross-property operational workflows. The blueprint nonetheless designs the enterprise target including multi-property (Product Owner direction, 23 Sep 2026) with pilot-first delivery phasing.

### 3.2 Payment and settlement scope (SRC-001 §3)

| Req ID | Requirement | Status |
|---|---|---|
| BR-PAY-001 | Cash is included: cashier receipt, cash-in-transit/house-bank control, closure, over/short, bank deposit reconciliation | Confirmed |
| BR-PAY-002 | POS card terminal is included: record terminal, bank/acquirer, transaction reference, amount, settlement batch; **no card data stored** | Confirmed |
| BR-PAY-003 | Bank transfer is included: record bank reference and payer evidence; confirmation via approved bank record or authorized verification, not guest-provided evidence alone | Confirmed |
| BR-PAY-004 | Cheque is included: record cheque details; clearing status; dishonour/reversal; approval controls; not treated as cleared cash on receipt | Confirmed |
| BR-PAY-005 | Cash receipt, cashier closure and bank deposit are distinct events | Confirmed |
| BR-PAY-006 | POS authorization slip is not bank settlement; POS clearing remains open until matched to acquirer/bank settlement | Confirmed |
| BR-PAY-007 | SuiteFlow stores POS/transfer/cheque/settlement references, never PAN or CVV | Confirmed |
| BR-PAY-008 | UBA and Wema Bank are candidates; exact roles (operating bank, POS acquirer, transfer destination, settlement bank, API provider) are **open** (PAY-001) | Open |
| BR-PAY-009 | Guest-facing online payment links: requirement **open** (PAY-002) | Open |
| BR-PAY-010 | POS terminal/acquirer and settlement-report details **open** (PAY-003) | Open |
| BR-PAY-011 | Bank statement formats and access/integration constraints **open** (PAY-004) | Open |

### 3.3 Accepted MVP boundaries (SRC-001 §4)

| Req ID | Capability | MVP decision |
|---|---|---|
| BR-MVP-001 | Kamra hotel operations | Include |
| BR-MVP-002 | ERPNext accounting, revenue, tax and corporate AR | Include |
| BR-MVP-003 | Night audit and income-audit controls | Include |
| BR-MVP-004 | Cash, POS, bank transfer and cheque controls | Include |
| BR-MVP-005 | Payment integration | Include one proven provider/acquirer path if required; otherwise controlled reference and reconciliation workflow |
| BR-MVP-006 | CRM | Minimal consent, withdrawal and suppression projection only |
| BR-MVP-007 | HRMS | Employee/user identity boundary only |
| BR-MVP-008 | Payroll | Defer unless separately approved as pilot-critical |
| BR-MVP-009 | Advanced CRM automation | Defer |
| BR-MVP-010 | Advanced analytics | Defer |
| BR-MVP-011 | External POS business integration | Defer; payment-terminal references remain included |
| BR-MVP-012 | Foreign currency | Defer; NGN only for MVP |
| BR-MVP-013 | Multi-property reporting | Defer for the one-property pilot |

Note on terminology: "Kamra" and "ERPNext" appear in the accepted boundary as the systems the Product Owner expects to fulfil the hotel-operations and accounting roles. The blueprint specifies the **roles and target ownership** by domain; whether those named products satisfy the target role is validated in Phase 1–3. Naming a product in the MVP boundary does not make its design authoritative (see §66 of the programme mandate).

### 3.4 Availability, support and recovery (SRC-001 §5)

| Req ID | Requirement | Status |
|---|---|---|
| BR-REL-001 | 24×7 support coverage with named on-call rota, severity model, escalation contacts, monitoring and runbooks before pilot | Confirmed |
| BR-REL-002 | RPO 1 hour — requires continuous database recovery (binlog/PITR or equivalent replication) and frequent file/config replication | Confirmed |
| BR-REL-003 | RTO 1 hour — requires warm recovery capacity, automated restore, current image/config/secrets, rehearsed failover | Confirmed |
| BR-REL-004 | Service objective 99.5% remains the planning baseline; confirmation or replacement **open** (REL-001) | Pending |
| BR-REL-005 | Daily close support within 24×7; financial-integrity and queue alerts must page the on-call owner around the close window | Confirmed |
| BR-REL-006 | Periodic four-hour backups are incompatible with the 1-hour RPO | Confirmed |
| BR-REL-007 | Restored workers must not duplicate external payments or ERP documents | Confirmed |
| BR-REL-008 | Cost/staffing consequences of 24×7 and 1-hour RPO/RTO require approval (REL-002) | Open |

### 3.5 Authorized discovery (SRC-001 §7)

Accepted as authorized: pinned candidate manifest; Frappe v16 all-app install/migration compatibility; Kamra Folio/Company/Payment/Night Audit contract inventory; synthetic golden hotel-day outline for a 200-room NGN/WAT property; payment lifecycle design for cash/POS/transfer/cheque; UBA/Wema interface comparison after hotel inputs; recovery proof design for 1-hour RPO/RTO.

Explicitly gated: bank API implementation, gateway adapters, final accounting documents and hotel configuration, pending ADR evidence and approvals.

## 4. Proposed architecture inputs (to validate, not inherit)

### 4.1 Decision Pack v0.1 (SRC-002) — recorded status at 20 Aug 2026

| Input | Proposed direction | Recorded status | Blueprint treatment |
|---|---|---|---|
| DP-ADR-001 | Daily guest-ledger control posting plus zero-revenue direct-bill transfer | Proposed; proof incomplete | Validate during WP 0.4; record CONFIRMED / MODIFIED / REPLACED / REJECTED |
| DP-ADR-002 | Hotel Business Day approval gate enforced around night audit | Proposed; hook/rollback proof incomplete | Validate during WP 0.3/0.4 |
| DP-ADR-003 | Transactional outbox with versioned events and durable idempotency | Proposed; contract/concurrency proof incomplete | Validate during WP 0.7 |
| DP-ADR-004 | Provider-neutral payments adapter; provider chosen by evidence matrix | Open | Validate during WP 0.7 (integration) |
| DP-ADR-005 | Guest deposits remain liabilities until application, refund or approved forfeiture | Proposed; tax-point decision incomplete | Validate during WP 0.4 |
| DP-CTX-001 | Modular monolith on Frappe; one isolated site/database per hotel; signed image promoted without rebuild | Proposed | Validate during WP 0.2 |
| DP-CTX-002 | Kamra authoritative for hotel operations; ERPNext authoritative for accounting and corporate AR; `hotel_integration` as durable control plane | Proposed | Validate during WP 0.2 domain ownership analysis |
| DP-CTX-003 | Revenue recognized once during approved business-day posting; settlements are balance-sheet movements; guest-ledger control account connects folio subledger to ERPNext | Proposed | Validate during WP 0.4 |

### 4.2 Implementation-repository ADRs (SRC-004) — observed 23 Sep 2026

| Input | Title | Observed status | Blueprint treatment |
|---|---|---|---|
| IMPL-ADR-001 | Revenue and direct bill | Proposed Phase 1 accounting contract — proof and approval required | As above |
| IMPL-ADR-002 | Reservation accounts, ledgers, cashiering, EOD and income audit | Proposed Phase 1 operational-financial close contract | As above |
| IMPL-ADR-003 | Governed outlet inventory and consumption reconciliation | Accepted for Phase C (1 Sep 2026) | Level-5 acceptance; blueprint validation required before it is cited as target architecture |
| IMPL-ADR-004 | Guest financial ledgers, documents and business-date reporting | Accepted for implementation (11 Sep 2026) | Level-5 acceptance; blueprint validation required |

Observed statuses are working-tree observations and are recorded here for planning only. Phase 2 re-verifies them with commit evidence.

## 5. Input conflicts carried forward

| Conflict | Statement A | Statement B | Handling |
|---|---|---|---|
| Product breadth | Accepted MVP defers multi-property, foreign currency and advanced analytics for the pilot (BR-MVP-012/013) | Product Owner direction (23 Sep 2026): enterprise target includes multi-property/chain, designed now, delivered pilot-first | Reconciled by scope.md: enterprise target vs pilot release scope; no capability dropped from the target |
| Product naming | MVP boundary names Kamra/ERPNext as included systems | Blueprint must define target ownership independently of existing products | Named systems treated as candidate implementations of target roles; validation in Phase 1–3 |
| Accounting authority | Decision pack proposes ERPNext as accounting book of record | Blueprint must determine the correct financial architecture on its own merits | WP 0.4 validation with explicit ADR outcome |
| Service objective | 99.5% planning baseline | Not confirmed (REL-001) | Open question OQ-004 |

## 6. Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial register: SRCS-001…004; BR requirements; proposed inputs | PROPOSED |
