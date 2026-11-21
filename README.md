# RADSAFE — Project Documentation

**Patient-Centric Longitudinal CT Radiation Dose Tracking & Cumulative Exposure Analysis System**

RADSAFE is a web-based medical-informatics project intended to organize CT Radiation Dose Structured Reports (RDSR), associate examinations with a longitudinal patient record, calculate documented dose indicators, and present a traceable radiation-exposure history.

## Core idea

One patient → one internal RADSAFE Patient ID → many CT examinations over time.

The system should let an authorized user review the patient's recorded examination history, the dose indicators extracted from each examination, estimated effective-dose values, and cumulative trends.

## Important identity and privacy rule

A DICOM Patient ID is not guaranteed to be globally unique across hospitals. RADSAFE must not automatically merge records from different institutions based only on Patient ID or patient name. The system should create an internal RADSAFE Patient ID and use a controlled matching workflow. Uncertain matches must be flagged for authorized review. The default interface should display the RADSAFE ID and minimize exposure of direct identifiers.

## Technology direction

- Backend: Java 21, Spring Boot, REST API, dcm4che feasibility-tested for DICOM RDSR.
- Frontend: HTML5, CSS3, vanilla JavaScript; Figma for design. Do not switch to React.
- Database: SQLite for the initial proof of concept; assess PostgreSQL for a later multi-user deployment.
- Visualization: Chart.js.
- Repository: `backend/`, `frontend/`, `docs/`, `sample-data/`.

## Documentation

| File | Purpose |
|---|---|
| `docs/project-scope.md` | Problem, goals, boundaries, assumptions and safety |
| `docs/SRS.md` | Complete software requirements |
| `docs/development-plan.md` | Phases, milestones, deliverables and acceptance gates |
| `docs/backend-plan.md` | Backend architecture, database, APIs, parsing and tests |
| `docs/frontend-plan.md` | Pages, components, API integration and frontend tasks |
| `docs/ui-ux-guidelines.md` | Visual system, layouts, states and accessibility |
| `docs/medical-physics-research-plan.md` | Scientific research, RDSR dictionary, dose methodology and validation |
| `docs/user-flow.md` | End-to-end user and patient-matching flows |
| `docs/team-work-plan.md` | Role ownership, handoffs and first 10-day sprint |

## Current status

Phase 1 — Foundation and Proof of Concept. All proposed capabilities must be treated as planned until implemented and tested. The first technical gate is proving that representative, de-identified CT RDSR files can be parsed and their relevant values independently verified.

## Safety notice

RADSAFE is an academic development project, not a certified medical device or a substitute for clinical judgment. Effective dose is an estimate used for radiation-protection comparisons; it is not an individualized risk prediction or a patient-specific absorbed-dose measurement. Do not use the prototype to make clinical decisions.
