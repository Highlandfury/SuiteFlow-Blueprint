---
doc-id: GOV-ACTIONS
title: Action Register — Human and Programme Actions
status: PROPOSED
version: 0.12
date: 2026-09-23
owner: Product Owner (accountable); maintained by the programme
applies-to: execution actions arising from GOV-PO-DEC and GOV-REVIEW
depends-on: [GOV-PO-DEC, GOV-REVIEW, GOV-APPOINT, GOV-SITTING, OPS-SUPPORT, GOV-BRIEFS]
---

# Action Register

Every action arising from the delegated Product Owner record and the review resolutions, with owner, due date, trigger and evidence. **Human-only** actions cannot be delegated to the persona.

| ID | Action | Owner | Due / trigger | Evidence | Status |
|---|---|---|---|---|---|
| A-01 | Countersign the three interim appointment instruments (Finance Controller, Technical Lead, Security/Privacy Adviser) | Real Product Owner | Before/at P0 sitting (≤14 Oct 2026) | Signed instruments (`interim-appointments-2026-09-23.md`) | **DONE — countersigned by recorded Product Owner direction, 23 Sep 2026** |
| A-02 | Appoint the human Finance Controller + deputy (OQ-002) — *deferred; not blocking P2* | Real Product Owner | when available | **Candidate pack ready:** `role-appointment-packs/finance-controller.md` (scope, time, invitation draft). Then: appointment minute + charter acceptance + scoped sign-offs | OPEN — pack ready |
| A-03 | Appoint the human Technical Lead + Security/Privacy Adviser + deputies (OQ-033) — *deferred; not blocking P2* | Real Product Owner | when available | **Candidate packs ready:** `role-appointment-packs/technical-lead.md`, `role-appointment-packs/security-privacy-adviser.md`. **Sign-off packs ready:** `signoff-packs/technical.md`, `signoff-packs/security-privacy.md`. Then: appointment minutes + scoped sign-offs + tabletop | OPEN — packs ready |
| A-04 | Issue RFQ for the two 24×7 options; score quotes — *deferred; not blocking P2* | Programme (PO oversight) | when ready | **RFQ pack ready:** `operations/rfq-24x7-support.md` (email draft §7; responses due 7 Oct) | OPEN — send-ready |
| A-05 | Sign the 24×7 contract and commit spend — *deferred; not blocking P2* | Real Product Owner | after quotes | Signed contract; approval block in OPS-SUPPORT | OPEN — human |
| A-06 | Send the three adviser engagement emails; confirm fees, engagement letters, NDAs — *deferred; not blocking P2; tax/legal stay UNVERIFIED until done* | Real Product Owner | when contacts available | **Send-ready:** emails in GOV-BRIEFS §4 + standalone briefs in `adviser-briefs/` | OPEN — human (send-ready) |
| A-07 | Convene and chair the P0 acceptance sitting — *DEFERRED: P0 conditional completion recorded 23 Sep 2026 (Path 2); sitting remains for Path 1 when appointees are seated* | Real Product Owner | when appointees available | Sitting pack v0.2 + role sign-off packs (`signoff-packs/`); minutes per GOV-SITTING §6 | OPEN — human |
| A-08 | Complete Programme P1 evidence collection (R1–R6) | Programme (TL review at appointment) | Inside P1–P2 envelope (by Week 8) | `research-register.md` statuses and citations | IN-PROGRESS — R1 largely evidenced; R2 domain walk + dispositions done (R2-F1…F5 → D15/D10/D11/D17 + docs); R5 volume model pass 1 (R5-F1 corrected); R3 FIRS/CBN access-blocked; R4 deferred; RS-502 + R2 per-capability depth remain (TL appointment) |
| A-09 | Freeze the pilot capability set | Product Owner | **DONE 23 Sep 2026** — freeze effective with conditional completion | Capability map v0.3 (204/76/3); GOV-PO-DEC §G | **DONE** |
| A-10 | Discharge or extend interim arrangements at the sitting | Real Product Owner | At P0 sitting | Sitting minute; GOV-APPOINT status update | OPEN — human |
| A-11 | Execute the Programme P2 current-state audit per the plan | Technical Lead (at appointment); interim: Programme | P2 complete 24 Sep 2026 (pending TL review) | Workspace `/home/suiteflow/suiteflow-audit`: passes 1–2 complete (204/204 mapped; **40 findings — 1 critical, 18 high**); **all passes 1–10 complete 24 Sep** — 204/204 classified; app suite 399 tests (381 OK); **79 findings (2 critical, 34 high)**; audit report issued (`report.md`, pending TL review); obligations OB-01…OB-14 tracked | **COMPLETE — pending TL review** |

**One-page human checklist:** `product-owner-checklist.md`.

## Version history

| Version | Date | Change | Status |
|---|---|---|---|
| 0.1 | 2026-09-23 | Initial action register: 10 actions (human-only marked) from GOV-PO-DEC and the review resolutions | PROPOSED |
| 0.2 | 2026-09-23 | A-08 progress (R1–R2, R5); A-11 added (P2 audit execution, ready) | PROPOSED |
| 0.3 | 2026-09-23 | A-01 complete: interim instruments countersigned by recorded Product Owner direction | PROPOSED |
| 0.4 | 2026-09-23 | Send-ready artifacts: three standalone adviser briefs and the 24x7 RFQ pack | PROPOSED |
| 0.5 | 2026-09-23 | Candidate-facing appointment packs prepared for A-02/A-03 | PROPOSED |
| 0.6 | 2026-09-23 | Role sign-off packs published (finance, technical, security) and linked; freeze-clean corrections applied | PROPOSED |
| 0.7 | 2026-09-23 | Human-action checklist published and cross-linked | PROPOSED |
| 0.8 | 2026-09-23 | P0 conditional completion: human actions deferred (tracked); freeze DONE; P2 in progress | PROPOSED |
| 0.9 | 2026-09-24 | P2 pass 2 complete: 204/204 mapped, 40 findings; pass 3 next | PROPOSED |
| 0.10 | 2026-09-24 | P2 pass 3 partial: 14 modules re-run (76 OK); F-FUNC-01 baseline EOD suite red; F-FO-06 resolved | PROPOSED |
| 0.11 | 2026-09-24 | P2 pass 3 complete (app suite): 50 modules / 399 tests; 5 failing modules recorded; golden day passed | PROPOSED |
| 0.12 | 2026-09-24 | P2 passes 4–10 complete; audit report issued (79 findings); P3 item A-12 added | PROPOSED |
