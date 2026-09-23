# Scope of Work — Open-Source Licensing Counsel

**Programme:** SuiteFlow hotel management platform — Nigerian pilot preparation
**Prepared:** 23 September 2026 · **Status:** engagement enquiry

## 1. Context

The programme is building a hotel management platform on an open-source stack. It will be operated **SaaS-hosted** for the pilot and may later be **deployed on-premise at hotel properties**. Before any distribution, the programme requires an OSS licensing opinion on the stack and its dependencies in both operating models.

## 2. Stack inventory (as observed on the pinned pilot environment, 23 September 2026)

| Component | Version | Licence declared |
|---|---|---|
| Frappe Framework | 16.31.0 | MIT |
| ERPNext | 16.32.3 | GPL-3.0 |
| Kamra (hotel operations) | 2.5.0 | AGPL-3.0 |
| HRMS | 16.16.0 | GPL-3.0 |
| CRM | 1.81.2 | GPL-3.0 |
| Payments | 0.0.1 | MIT |
| `hotel_integration` (programme application) | 0.1.0.dev0 | **undeclared** |

Transitive dependencies and bundled assets are not yet fully inventoried; an SBOM can be generated as part of the engagement.

## 3. Scope

1. Licence classification of each component and its dependencies.
2. Network-copyleft (AGPL) implications for SaaS operation, and the point at which distribution obligations are triggered.
3. Source-offer and attribution duties for on-premise distribution, including combined-work considerations (GPL/AGPL components alongside MIT components).
4. Bundling/compatibility of the planned components, and any licence conflicts.
5. Required notices and attribution pack for a commercial release.
6. Trademark constraints (project names and marks).
7. An OSS policy and pre-distribution release-gate controls (SBOM and licence scanning in CI; review before each distribution).

## 4. Deliverables

- Licence inventory and risk opinion.
- OSS policy (allowed/conditional/prohibited licences, review process).
- Pre-distribution gate checklist.
- Citations suitable for the programme's release pack.

## 5. Inputs provided by the programme

- The stack inventory above; SBOM generation on request.
- Description of the intended operating and distribution models.

## 6. Timing and commercial

- Acknowledgement requested within **3 business days** of engagement.
- Draft within **3 weeks**; final within **6 weeks** of engagement (to be fixed in the engagement letter).
- Fee quote, engagement letter and mutual NDA are handled by the Product Owner's office.
- Scheduling contact: [name / email / phone].

*This document is an engagement enquiry, not a contract; no programme commitment exists before a countersigned engagement letter.*
