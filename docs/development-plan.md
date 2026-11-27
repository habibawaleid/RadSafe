# RADSAFE — Complete Development Plan

**Version:** 2.0 | **Date:** 27 November 2026

## 1. Delivery strategy

Deliver RADSAFE incrementally, prioritizing a verified CT RDSR ingestion and patient-history vertical slice. Scientific correctness, safe patient matching and traceability take priority over feature count. The team should not build complex analytics or polished report exports before confirming the RDSR fields and database model.

## 2. Workstreams

1. **Product & coordination:** requirements, GitHub issues, milestones, integration schedule and scope control.
2. **Backend & database:** Spring Boot, MySQL, upload/parser services, domain APIs, calculations and report generation.
3. **Frontend & UX:** Figma-aligned HTML/CSS/JS screens, API integration, validation and responsive behavior.
4. **Medical Physics validation:** DICOM field mapping, units, coefficient applicability, DRL sources, independent calculation checks and report language.

## 3. Phases

### Phase 1 — Foundation and RDSR proof of concept (Days 1–10)
- Freeze MVP scope and document exclusions.
- Set up GitHub structure, Java 21, Maven, Spring Boot, MySQL and frontend skeleton.
- Confirm the current physical database schema and back it up before changes.
- Obtain authorized de-identified CT RDSR files with CTDIvol/DLP present where possible.
- Test dcm4che and document extractable fields, units and vendor limitations.
- Finalize data dictionary, patient matching rules and source register for factors/DRLs.
- Complete Figma handoff and static HTML prototype for core screens.

**Exit gate:** a representative RDSR can be inspected and the supported extraction contract is documented; otherwise the blocker and revised scope are explicit.

### Phase 2 — Backend foundation and persistence (Weeks 2–3)
- Implement application configuration, authentication/authorization baseline and health endpoint.
- Create versioned additive MySQL migrations after inspecting existing schema.
- Implement upload, validation, hash/duplicate detection and processing statuses.
- Implement parser adapter and normalized DTO.
- Persist patient, examination, RDSR document and irradiation-event records.
- Implement patient matching review and patient-history APIs.

**Exit gate:** a test file can be uploaded, parsed, linked to a patient through the approved workflow, saved and retrieved.

### Phase 3 — Frontend core and integration (Weeks 3–5)
- Build shared layout and five-item sidebar.
- Implement Dashboard, Upload / Sync, Patients & Exams, Patient Profile and Examination Details.
- Connect to backend APIs and remove mock data from integrated workflows.
- Add all loading, empty, validation, warning and error states.

**Exit gate:** a user can complete the core upload-to-history workflow from the browser.

### Phase 4 — Dose calculations and DRL comparison (Weeks 5–6)
- Implement eligible Effective Dose estimation with source/version provenance.
- Validate results against independently calculated examples.
- Implement compatible-group filtering and group statistics.
- Add selected reference values and DRL comparison table.
- Add group-level exceedance summary and no-eligible-group empty state.

**Exit gate:** calculations and group comparisons pass reviewed test cases; unsupported cases remain unavailable.

### Phase 5 — Reports (Weeks 6–7)
- Implement `EXAMINATION_DOSE` for one examination.
- Implement `PATIENT_DOSE_SUMMARY` with one-patient-first and multiple-exam selection.
- Implement `DOSE_ANALYTICS_DRL` for compatible groups.
- Implement backend scope validation, report snapshots, preview/review, PDF export and history.
- Verify generated/reviewed/approved metadata remain separate.

**Exit gate:** all three report types generate correct content and invalid cross-patient selection is rejected.

### Phase 6 — Testing, hardening and presentation (Weeks 7–8 / remaining schedule)
- Run unit, integration and end-to-end tests.
- Test invalid/unsupported DICOM and malformed data.
- Review privacy, role access, institution boundaries, upload restrictions and Git hygiene.
- Prepare clearly labelled synthetic/de-identified demo dataset.
- Finalize screenshots, documentation, known limitations and presentation.

**Exit gate:** critical acceptance scenarios pass and the team can explain the system's limitations.

## 4. Priority matrix

| Priority | Features |
|---|---|
| P0 — Must have | RDSR validation/PoC, manual upload, patient/exam/event persistence, safe matching, patient history, event-level dose display, missing-data handling |
| P1 — MVP required | Eligible Effective Dose estimate, Dashboard, DRL group comparison, three report types, PDF export, role checks, provenance |
| P2 — If time permits | richer audit history, improved report styling, additional chart filters, usability refinements |
| Deferred | PACS/auto-sync, patient portal, real-time alerts, organ-dose/risk modelling, multi-modality expansion, production deployment |

## 5. First ten working days

| Day | Backend / coordination | Frontend / UX | Medical Physics / research | Deliverable |
|---|---|---|---|---|
| 1 | Repo, Java/Maven/MySQL setup | Inspect Figma and repo | Define RDSR sample needs | Environment checklist |
| 2 | Spring Boot skeleton, DB backup/schema inspection | Shared layout skeleton | RDSR/DICOM field checklist | Running skeleton |
| 3 | dcm4che read-file PoC | Login and sidebar | Inspect sample file structure | Parser feasibility notes |
| 4 | Extract metadata DTO | Dashboard static layout | Identify dose fields/units | Field mapping draft |
| 5 | Event extraction experiment | Upload screen states | Validate CTDIvol/DLP interpretation | Parser sample output |
| 6 | Draft schema/migration review | Patients & Exams tables | Patient matching/data dictionary | Domain model draft |
| 7 | Persist parser output in test DB | Patient Profile | Verify event multiplicity and units | Vertical-slice plan |
| 8 | Patient/exam APIs | Examination Details | Review missing-data rules | API contract v1 |
| 9 | Dose eligibility service skeleton | API integration start | Research applicable factors/DRLs | Validation test cases |
| 10 | Demo upload-to-history flow | Core screens connected | Scientific review and blockers | Phase 1 review |

This is a planning sequence, not a guarantee that all tasks fit ten days. If RDSR access or parser feasibility is delayed, prioritize proving the data path over adding UI features.

## 6. Team operating rhythm

- Short daily sync: completed work, next task, blocker.
- Each task should have one owner and a clear acceptance criterion.
- Integrate through small PRs; avoid editing the same files concurrently without coordination.
- Review scientific calculations and DRL assumptions with Member 3 before merging.
- Demo working increments at the end of each phase.
- Update `SRS.md`, `user-flow.md` and technical plans when decisions change.

## 7. Testing strategy

- **Unit:** parser mapping, validation, matching, calculation eligibility, DRL grouping, report scope.
- **Integration:** upload/parse/persist, patient history, DRL API, report creation/export.
- **End-to-end:** login → upload → patient association → profile → examination details → report.
- **Negative:** unsupported/corrupt files, duplicate uploads, missing values, invalid units, unauthorized access, cross-patient report request.
- **Scientific:** independent hand calculations, factor applicability review, DRL source/matching review.
- **UI:** responsive layout, keyboard navigation, empty/loading/error states and fullscreen preview restoration.

## 8. Release checklist

- All source code and documentation are committed; no credentials or patient-identifiable files.
- `.env.example` contains placeholders only.
- Database migrations are versioned and tested on a copy.
- README and setup instructions match the actual implementation.
- Test evidence and known limitations are documented.
- Demo records are clearly labelled synthetic/de-identified.
- No clinical-readiness claims are made.
