---
doc-id: GOV-PO-CHECKLIST
title: Product Owner Checklist — Human Actions (as at 23 Sep 2026)
status: PROPOSED
version: 0.1
date: 2026-09-23
owner: Product Owner
applies-to: human actions A-02…A-07, sitting decisions and optional spikes
depends-on: [GOV-ACTIONS, GOV-SITTING, GOV-BRIEFS, GOV-APPOINT]
---

# Product Owner Checklist — Human Actions

Everything the programme cannot do without you, in date order. Reply after each item so the registers stay current.

## This week — by Saturday 26 September

### 1. Send the three adviser emails (A-06) ⏰ the long-lead item

- **How:** open `adviser-briefs-2026-09-23.md` §4, copy each email; fill `[contact]`; **attach** the matching file from `adviser-briefs/`:
  - Tax → `brief-tax.md` · Legal/privacy → `brief-legal-privacy.md` · OSS licensing → `brief-oss-licensing.md`
- **Who:** tax — your accountant/audit firm's tax desk or a CITN member; legal — a Nigerian firm with data-protection practice; OSS — a tech/IP lawyer.
- **Reply:** "briefs sent 25 Sep to X, Y, Z".

### 2. Approach the three role candidates (A-02/A-03)

- **How:** open `role-appointment-packs/`, use each file's **invitation draft** as a message:
  - `finance-controller.md` → your accountant or audit-firm manager
  - `technical-lead.md` → an independent engineer (not a vendor you'll hire)
  - `security-privacy-adviser.md` → a NDPC-licensed DPCO (`services.ndpc.gov.ng/repo/?flp=dpco`)
- **Goal:** a 30-minute call each; appointment before the sitting.
- **Reply:** names when confirmed — "FC: name (deputy); TL: name; Security: name".

## By Wednesday 30 September

### 3. Send the 24×7 RFQ (A-04)

- **How:** open `operations/rfq-24x7-support.md`, copy the vendor email (§7), attach the pack, send to 2–3 MSP/NOC providers. Responses due 7 Oct.
- **Reply:** "RFQ sent to A, B, C".

## Wednesday 7 October

### 4. Quotes arrive — hand them over (A-05 input)

- **How:** send me the quote details.
- **Reply:** paste the numbers; I score them against the matrix and give you a recommendation.

### 5. Sign the support contract (A-05)

- **How:** your signature/finance process on the selected provider.
- **Reply:** "contract signed with X".

## By Wednesday 14 October

### 6. Convene and chair the P0 acceptance sitting (A-07)

- **How:** pick a date (90 minutes), send the invitation from `p0-sitting-pack.md` §1 with the pre-read list (§8), chair the agenda (§3).
- **Materials ready:** the pack itself, plus the three **sign-off packs** (`signoff-packs/finance.md`, `technical.md`, `security-privacy.md`) containing reviewed documents, conditions and proposed sign-off wording.
- **At the sitting (your decisions):**
  - Freeze the pilot capability set (A-09).
  - Discharge or extend the interim arrangements (A-10).
  - Record the outcome — **Path 1** (full sign-off, if appointees seated) or **Path 2** (conditional completion; scope freeze effective, sign-offs deferred to appointment).
- **Reply:** "sitting held — path 1/2" (+ the minute).

## Optional — decide when you like

### 7. Approve governed proof spikes

If you want technical work (evidence, not product code) before transition: approve one or more spikes — tenancy/scope enforcement, posting idempotency, close recovery, framework-generic surface closure.
**Reply:** "approve spikes: …".

### 8. FIRS/CBN source access

The programme machine cannot reach `firs.gov.ng` / CBN. Try from your browser or another network, or leave it to the tax adviser.
**Reply:** "FIRS access OK" (or leave).

## Deferred — not your action now

- **First-property items** (OQ-001/004/006/007/025/038): entity documents, bank/POS facts, migration data, site survey — all at the First-Property Deployment Gate when a property is secured.
- **Programme work**: P1 completion, P2 audit, P3 gap matrix, P4 transition — run by the programme; P2 starts when the sitting concludes.

## Reply formats at a glance

| Step | One-line reply |
|---|---|
| Briefs | "briefs sent <date> to X, Y, Z" |
| Candidates | "FC: name (deputy); TL: name; Security: name" |
| RFQ | "RFQ sent to A, B, C" |
| Quotes | paste numbers |
| Contract | "contract signed with X" |
| Sitting | "sitting held — path 1/2" + minute |
| Spikes | "approve spikes: tenancy, idempotency, …" |

## Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Consolidated human-action checklist issued | PROPOSED |
