# RADSAFE — Complete Development Plan

## 1. Delivery strategy

Build RADSAFE incrementally. First prove that the team can read representative CT RDSR data and interpret it correctly. Then build the patient/examination data model and dose engine. Only after those foundations are reliable should the team expand the UI, analytics, reports, and deployment.

## 2. Proposed phases

### Phase 1 — Foundation & Proof of Concept (Days 1–10)
- Confirm scope, privacy principles, and architecture.
- Set up repository and development environments.
- Test dcm4che against representative CT RDSR samples.
- Draft data dictionary and scientific references.
- Design core screens in Figma.
- Build HTML/CSS prototype with clearly labeled mock data.

**Gate:** at least one sample report can be inspected and relevant fields are understood; limitations are documented.

### Phase 2 — Core Backend & Data Model (Weeks 3–4)
- Implement upload and validation.
- Implement supported RDSR parsing.
- Normalize extracted metadata and dose metrics.
- Implement Patient, PatientIdentifier, Examination, DoseMetric, DoseEstimate.
- Implement RADSAFE Patient ID and match-review workflow.
- Implement calculation service using approved coefficient configuration.
- Add REST endpoints and automated tests.

**Gate:** upload → parse → match decision → persist → retrieve history works for agreed test fixtures.

### Phase 3 — Frontend & Integration (Weeks 5–6)
- Complete dashboard, upload, search, patient profile, examination details.
- Connect frontend to backend APIs.
- Implement patient timeline and dose charts.
- Add empty/loading/error/warning/match-review states.
- Test responsive layout and accessibility.

**Gate:** a user can process a test report and inspect the linked patient's examination history.

### Phase 4 — Scientific Validation, Reports & QA (Weeks 7–8)
- Run parser fixture tests and independent dose validation.
- Resolve extraction and calculation discrepancies.
- Add DRL comparison only for scientifically comparable categories.
- Implement PDF history report if time and validation permit.
- Run end-to-end and negative tests.
- Prepare deployment and final demonstration.

**Gate:** known limitations are documented; all demo calculations have traceable inputs and independently checked expected outputs.

### Phase 5 — Extension (After MVP)
- PostgreSQL and multi-user deployment.
- Authentication and detailed role-based permissions.
- Stronger audit logging, backup, retention, and security controls.
- Additional vendor RDSR coverage.
- PACS/RIS integration assessment.
- Expanded DRL dataset and report customization.

## 3. Workstream dependencies

| Work item | Depends on |
|---|---|
| Dose engine | Medical Physics-approved metric mapping, units, anatomy mapping and coefficient source |
| Patient matching | Privacy decisions and source identifier analysis |
| Patient profile UI | Patient/examination API contract |
| Dose charts | Validated dose estimate semantics and sample data |
| DRL comparison | Valid reference dataset and comparable exam categories |
| PDF report | Stable patient history, calculation provenance, and approved wording |

## 4. Risk register

| Risk | Impact | Mitigation |
|---|---|---|
| RDSR structures vary by vendor | Parsing errors or incomplete data | Start with representative samples; document supported structures; fixture tests |
| Source Patient IDs collide across institutions | Wrong patient histories | Institution-aware matching; manual review; immutable internal ID |
| Incorrect k-factor selection | Misleading estimate | Scientific approval, source/version tracking, no calculation when uncertain |
| Missing/ambiguous DLP | Incomplete calculation | Explicit missing status; never replace missing with zero |
| Real patient data exposure | Privacy breach | De-identified/synthetic samples, access controls, minimal data retention |
| Frontend/backend mismatch | Integration delays | Agree API contract early and version endpoints |
| Scope expansion | Missed MVP | Defer PACS, AI, advanced QA and nonessential features |

## 5. Release criteria

Before presenting an MVP as complete:
- Core upload and history flow works on agreed test data.
- Extraction values and units are checked against source reports.
- Dose calculations are independently validated.
- Patient matching safeguards are demonstrated.
- Privacy limitations and security gaps are disclosed.
- Mock/demo data is clearly identified.
- Known unsupported RDSR variants are documented.
