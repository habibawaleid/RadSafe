# RADSAFE — SRS Implementation & Development Plan

**Version:** 1.0  | **Date:** 27 November 2026  | **Companion:** `SRS.md`

## 1. Purpose

This document converts the SRS into an implementation sequence, ownership model, verification strategy and delivery gates. `SRS.md` remains the authoritative source for system requirements; this file explains how the team will build and demonstrate them.

## 2. Delivery principles

- Build a narrow, working vertical slice before adding secondary features.
- Prove RDSR parsing with real, authorized, anonymized representative files before committing to detailed parser mappings.
- Keep source data, derived summaries and calculated estimates distinct.
- Treat UI mock data as mock data and label it clearly.
- Implement only the agreed MVP; defer PACS, auto-sync, patient portal and advanced alerts.
- Use AI tools for scaffolding and review, but the team must understand, run and test generated code.

## 3. Team responsibilities

| Role | Primary responsibilities | Deliverables |
|---|---|---|
| Member 1 — Backend / Coordination | Java, Spring Boot, MySQL, DICOM integration, APIs, integration planning, backend tests and coordination | Backend services, schema/migrations, API contract, test evidence |
| Member 2 — Frontend / UI | Figma handoff, HTML/CSS/JS, forms, tables, charts, API integration and UI tests | Responsive screens, reusable components, UI validation |
| Member 3 — Medical Physics / Research | RDSR field review, dose definitions, conversion-factor research, DRL sources, scientific validation and report wording | Data dictionary, source register, calculation validation cases, scientific review notes |

Responsibilities can overlap; final decisions and changes should be recorded in GitHub issues/PRs. No separate medical-physics-only software role is assumed beyond Member 3's research/validation responsibilities.

## 4. Development phases and gates

### Phase 0 — Repository and environment (Days 1–2)
- Confirm repository structure, branch/PR conventions and issue board.
- Install/configure JDK 21, Maven, MySQL 8, VS Code/IDE and browser tooling.
- Create backend/frontend skeletons and environment configuration examples.
- Add `.gitignore`; exclude `.env`, credentials, database dumps and all patient-identifiable DICOM/RDSR files.
- Agree API error format and naming conventions.

**Gate:** all three team members can run their part locally; no secrets or patient files are tracked.

### Phase 1 — RDSR proof of concept and data contract (Days 3–7)
- Obtain authorized de-identified CT RDSR test files with known CTDIvol/DLP content.
- Inspect DICOM metadata and confirm SOP Class / modality / content.
- Build a small dcm4che reader to identify relevant sequences and irradiation events.
- Record which fields are reliably extractable, units, vendor variation and unsupported cases.
- Draft parser DTO and field-level data dictionary.
- Research and register applicable DRL sources and conversion-factor sources; do not promote demo values as official.

**Gate:** at least one representative file can be parsed reproducibly, or a documented blocker/unsupported limitation is agreed before full feature development.

### Phase 2 — Database and backend vertical slice (Weeks 2–3)
- Inspect the actual MySQL schema before migration; do not assume the proposed database plan equals the executed schema.
- Write additive, versioned migrations and test them on a database copy.
- Implement upload, validation, hash/duplicate check, parser adapter and processing status.
- Implement institution/user/patient/examination/document/event persistence.
- Implement patient matching review rules and patient history retrieval.
- Add calculation service with explicit eligibility checks and provenance.

**Gate:** upload → validate → parse → review/link patient → persist → retrieve patient history works for agreed fixtures.

### Phase 3 — Frontend core and API integration (Weeks 3–5)
- Implement Login, Dashboard, Upload / Sync, Patients & Exams, Patient Profile and Examination Details.
- Connect forms and tables to API endpoints.
- Add loading, empty, warning, validation and error states.
- Add accessible labels, keyboard navigation and responsive behavior.

**Gate:** a user can complete the core workflow without relying on hardcoded demo data for the tested vertical slice.

### Phase 4 — DRL analysis and reports (Weeks 5–6)
- Implement compatible-group filtering and group statistics.
- Implement selected DRL reference details and comparison table.
- Implement the three report types and report-selection validation.
- Implement one-patient-then-multiple-examinations selection for Patient Dose Summary.
- Implement preview, review/approval separation, PDF export and report history.

**Gate:** all report scopes are validated server-side; saved reports preserve selected exam membership and generation snapshot.

### Phase 5 — Verification, hardening and presentation (Weeks 7–8 or remaining schedule)
- Run parser fixtures, unit tests, integration tests and end-to-end acceptance scenarios.
- Validate calculations independently against hand-checked examples.
- Review DRL applicability, source citations, group definitions and sample sizes.
- Test authorization, upload restrictions, duplicate handling and privacy controls.
- Prepare anonymized demo dataset, screenshots, user guide and final presentation.

**Gate:** critical acceptance scenarios pass; known limitations and unimplemented features are documented.

## 5. Requirement-to-test traceability

| Area | Minimum verification |
|---|---|
| Login / authorization | Valid login, invalid login, role-denied API request, institution boundary check |
| Upload / validation | Supported RDSR, non-DICOM, unsupported SOP class, corrupted file, oversized file, duplicate file |
| Parsing | Required tags/sequences present, missing optional fields, multiple irradiation events, unit preservation |
| Patient matching | Confirmed match, ambiguous candidate, no candidate, user defers, institution isolation |
| Dose calculation | Valid input, missing DLP, unknown unit, unsupported region, wrong patient category, factor version traceability |
| Patient history | Chronological ordering, multiple exams, missing values, no exam empty state |
| DRL | Compatible group, incompatible unit, wrong body region/category, insufficient N, no eligible group, exceedance denominator |
| Reports | One-exam report, one-patient/multi-exam summary, reject cross-patient selection, DRL group report, preview/export/history |
| Security / privacy | Unauthorized file access, secrets excluded from Git, de-identified fixture check, audit metadata |

## 6. Definition of Done

A feature is done only when: requirements and edge cases are understood; code is committed through a reviewable PR; it runs locally from documented instructions; relevant tests pass; errors and empty states are handled; no secrets or identifiable patient data are included; API/UI behavior agrees; and any scientific assumptions have a source or explicit limitation.

## 7. GitHub workflow

- Use `main` for stable integration and short feature branches such as `feature/rdsr-upload` or `feature/patient-dose-summary`.
- Open small PRs with purpose, screenshots/API examples, test evidence and known limitations.
- Track decisions and blockers as issues; link related PRs.
- Update docs whenever a requirement, API, schema or screen changes.
- Tag milestones only after the relevant gate is met.

## 8. Risk register

| Risk | Mitigation |
|---|---|
| No suitable RDSR files | Secure authorized anonymized samples early; build parser PoC before full UI/backend expansion |
| Vendor-specific RDSR variation | Preserve raw source context; parse defensively; document unsupported variants |
| Uncertain patient identity | Use institution-aware matching and human review; never silently merge uncertain records |
| Incorrect conversion factor | Maintain source/version/applicability register; require Medical Physics review; keep result unavailable if unsupported |
| Misleading DRL comparison | Match metric/unit/category; show N; compare group statistic, not individual patients |
| Database schema drift | Inspect actual MySQL schema and use additive versioned migrations |
| Scope growth | Defer excluded features and require explicit team approval for scope changes |
| AI-generated code defects | Review generated code, run tests, and understand dependencies before merge |

## 9. Demonstration scenario

Use a synthetic or authorized de-identified patient with at least two CT examinations, where available data permits. Upload supported RDSR files; demonstrate processing; show event-level dose values and any eligible estimate; open patient history; create a Patient Dose Summary after selecting one patient and multiple of that patient's examinations; show a separate one-examination physicist report; and run DRL analysis only if the dataset contains enough comparable records and a valid reference. Clearly label unavailable data and demo records.
