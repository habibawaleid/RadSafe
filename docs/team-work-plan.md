# RADSAFE — Team Work Plan

## 1. Team structure

| Member | Role | Primary ownership |
|---|---|---|
| Member 1 — Habiba | Backend Developer & Coordinator | Backend, parser integration, database, APIs, testing, integration and sprint coordination |
| Member 2 — Manar | UI/UX & Frontend Developer | Figma, design system, HTML/CSS/JS screens, API integration and frontend testing |
| Member 3 | Medical Physics Researcher | Scientific research, RDSR field dictionary, coefficient/DRL references, sample data and independent validation |

## 2. Collaboration rules

- One shared GitHub repository.
- Each member works in their assigned folders and communicates changes affecting shared contracts.
- Backend publishes API request/response examples before frontend integration.
- Medical Physics provides scientific mappings and expected values before dose calculations are treated as validated.
- Use de-identified or synthetic data only.
- Every task must have an owner, deliverable, and acceptance check.
- Commit small, understandable changes and use descriptive commit messages.
- Raise blockers early; do not silently invent missing requirements.

## 3. First 10-day sprint goal

**Sprint goal:** prove the core concept end-to-end at prototype level: a supported sample CT RDSR can be inspected, its relevant fields can be understood and checked, the backend can begin processing it, and the team can demonstrate the first patient-history interface using clearly labeled sample/mock data.

This sprint is a foundation/PoC sprint, not a promise of a production-ready medical system.

## 4. Deliverables by member

### Member 1 — Backend
- Running Java 21/Spring Boot project.
- dcm4che feasibility test.
- One sample RDSR parsing output.
- Initial normalized extraction DTO.
- Draft patient/examination data model.
- Initial upload/API contract.
- Basic unit tests and known-limitations list.

### Member 2 — UI/UX + Frontend
- Figma wireframes for Dashboard, Upload, Patient Search/Profile, Examination Details.
- Shared light-theme design system.
- Responsive HTML/CSS shell.
- Upload and patient profile prototypes.
- Sample-data patient history and Chart.js chart.
- API integration plan.

### Member 3 — Medical Physics Research
- CTDIvol/DLP/effective-dose terminology notes.
- RDSR sample inventory and field dictionary.
- Manually verified CTDIvol/DLP examples.
- k-factor evidence table draft with exact references and applicability.
- DRL research table draft.
- Independent dose-calculation test cases.
- List of scientifically unsupported/ambiguous cases.

## 5. Day-by-day coordination

| Day | Backend | Frontend/UI | Medical Physics | Team checkpoint |
|---|---|---|---|---|
| 1 | Project setup | Screen inventory | Terminology | Confirm scope and folder ownership |
| 2 | DICOM structure/dependencies | Figma wireframe | Dose metrics | Agree data fields |
| 3 | Parse sample RDSR | Shared layout | Sample inventory | Compare sample availability |
| 4 | Metadata extraction | Dashboard prototype | RDSR dictionary | Review field names |
| 5 | CTDIvol/DLP extraction | Upload screen | Verify dose values | Compare parser vs source |
| 6 | Data model | Patient profile | k-factor research | Agree calculation inputs |
| 7 | Persistence | History table | Anatomy mapping | Review unknown cases |
| 8 | API draft | Chart prototype | DRL research | Agree API/data contract |
| 9 | Tests and errors | Integration/mock adapter | Validation cases | Review end-to-end gaps |
| 10 | Demo flow | Responsive polish | Scientific report | Sprint demo and next backlog |

## 6. Definition of done for sprint

- Each member has delivered the listed artifacts.
- The team can explain the complete user journey.
- One sample RDSR has been inspected; parser feasibility and limitations are documented.
- Dose values have source evidence and manual checks.
- The UI clearly labels mock data.
- No uncertain patient match is silently merged.
- Next sprint backlog is prioritized.

## 7. Meeting rhythm

- Short daily async update: yesterday / today / blocker.
- Two focused integration reviews during the sprint (around Day 5 and Day 9).
- Day 10 demo and retrospective.
- Keep decisions in repository documentation so new team members can understand them.

## 8. Weekly progress template

```text
Member:
Completed:
Evidence / link:
In progress:
Blockers:
Decision needed:
Next deliverable:
```
