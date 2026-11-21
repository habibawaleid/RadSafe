# RADSAFE — First 10 Days Execution Plan

**Sprint name:** Foundation & Proof of Concept  
**Duration:** 10 working days  
**Team:** Habiba (Backend & Coordinator), Manar (UI/UX & Frontend), Member 3 (Medical Physics Researcher)

## 1. Sprint objective

At the end of Day 10, the team should be able to demonstrate and explain the first end-to-end concept using de-identified or synthetic sample data:

- A representative CT RDSR has been inspected and parser feasibility documented.
- Relevant metadata and CTDIvol/DLP values have been identified and manually checked.
- The backend project and initial data model exist.
- The frontend has a consistent Figma design and working prototype screens.
- The patient identity and match-review approach is agreed.
- The team has an API/data contract and a prioritized Phase 2 backlog.

**This is a proof-of-concept sprint, not a production-ready clinical release.**

## 2. Before Day 1 — Shared preparation

All members should:
- Have access to the GitHub repository and agree on branch/commit workflow.
- Read the project README, Scope, SRS, and their role-specific plan.
- Use only de-identified or synthetic DICOM examples; do not upload real patient files to GitHub.
- Agree on terminology: RADSAFE Patient ID, source Patient ID, Examination, CTDIvol, DLP, estimated effective dose, DRL.
- Create a shared task board with owner, status, due day, and evidence/deliverable.
- Agree that unknown values remain unknown; no one should guess a scientific value or patient match.

## 3. Daily execution map

### Day 1 — Scope, setup, and team alignment

**Habiba — Backend & Coordination**
- Confirm repository folders and branch strategy.
- Verify Java 21 and Maven installation.
- Create or verify the Spring Boot project skeleton.
- Draft a one-page architecture and list backend dependencies.
- Create a decision log and task board.

**Manar — UI/UX & Frontend**
- Read the scope and user flow.
- List core screens: Dashboard, Upload RDSR, Patient Search, Patient Profile, Examination Details, Match Review.
- Create Figma file and page/frame naming convention.
- Confirm light theme, hybrid dashboard direction, and HTML/CSS/JS implementation.

**Medical Physics Researcher**
- Prepare a glossary for CTDIvol, DLP, Effective Dose, k-factor, RDSR, DRL.
- Identify authoritative source families to consult.
- List the required fields to inspect in a sample CT RDSR.
- Confirm a safe source of de-identified or synthetic examples.

**End-of-day deliverables**
- Team task board and role ownership.
- Confirmed scope and terminology.
- Backend project starts locally (or setup blocker documented).
- Figma file created with screen inventory.
- Medical Physics research checklist.

**Preparation for Day 2:** everyone should have repository access; Backend should have Java/Maven ready; Researcher should identify at least one candidate sample report source.

---

### Day 2 — Technical and scientific discovery

**Habiba**
- Review DICOM and CT RDSR basics relevant to the parser.
- Check dcm4che compatibility with Java 21 and the selected Spring Boot setup.
- Create a minimal test class or command-line experiment to open a DICOM file.
- Record dependency/version issues.

**Manar**
- Create low-fidelity wireframes for Dashboard and Upload RDSR.
- Define navigation and main content hierarchy.
- Sketch upload states: idle, uploading, processing, success, failure, needs review.

**Medical Physics Researcher**
- Inspect available sample RDSR documentation or sample files.
- Identify candidate metadata and dose fields.
- Start a source log with publication title, organization, year, URL/identifier, and relevance.
- Note which scientific details still need verification.

**End-of-day deliverables**
- dcm4che feasibility note (not a claim of full parsing).
- Dashboard and Upload wireframes v1.
- Sample inventory/source log.
- Initial list of RDSR fields to investigate.

**Preparation for Day 3:** Researcher provides a sample or a precise sample acquisition path; Backend prepares a small parser experiment; Frontend prepares the wireframes for review.

---

### Day 3 — First RDSR inspection and visual direction

**Habiba**
- Run a first read attempt on one representative de-identified CT RDSR.
- Inspect SOP Class UID and basic DICOM metadata.
- Save a sanitized parser output example (never commit identifying values).
- Document parse errors or unsupported content.

**Manar**
- Review wireframes with the team.
- Create the light-theme visual direction: colors, typography, spacing, cards, table style.
- Design a reusable header/sidebar/navigation shell.
- Start the Patient Profile wireframe.

**Medical Physics Researcher**
- Inspect the same sample report independently.
- Record where the report identifies examination and dose information.
- Note whether values are report-level, series-level, or irradiation-event-level.
- Identify missing or ambiguous fields.

**End-of-day deliverables**
- First sample inspection result and limitations.
- Agreed visual direction/design tokens v1.
- Patient Profile wireframe v1.
- Initial sample field notes from Researcher.

**Preparation for Day 4:** compare Backend parser output with Researcher observations; agree which fields can safely be extracted in the PoC.

---

### Day 4 — Metadata mapping and dashboard prototype

**Habiba**
- Implement or document extraction of basic metadata that was proven present.
- Define a normalized DTO for patient/study/scanner metadata.
- Keep source values and normalized values distinguishable.
- Add a basic parser test fixture if feasible.

**Manar**
- Build the shared HTML/CSS page shell.
- Implement the Dashboard prototype using clearly labeled mock data.
- Add navigation and reusable cards/table styles.
- Ensure no real patient identifiers appear in the mock UI.

**Medical Physics Researcher**
- Complete the first draft of the RDSR field dictionary.
- For each field, record: meaning, source location/sequence, unit, context, sample presence, and uncertainty.
- Mark fields that need more than one sample to validate.

**End-of-day deliverables**
- Metadata extraction DTO / mapping draft.
- Dashboard prototype v1.
- RDSR field dictionary v1.

**Preparation for Day 5:** select the exact CTDIvol/DLP examples to validate; Frontend prepares the upload page skeleton.

---

### Day 5 — Dose metric extraction and upload interface

**Habiba**
- Investigate CTDIvol and DLP extraction from the selected RDSR structure.
- Preserve units and event/series/report context.
- Add validation for missing or unsupported values.
- Do not calculate effective dose yet unless the coefficient/mapping is approved.

**Manar**
- Build Upload RDSR screen.
- Add file selection/drop area and accepted-file guidance.
- Add mock processing steps and error/status components.
- Make it clear that the prototype uses mock processing if API is not connected.

**Medical Physics Researcher**
- Manually verify the CTDIvol/DLP values in the sample against the report.
- Record units and scope/context.
- Create at least two or three manually checked examples if suitable samples are available.
- Report discrepancies or ambiguous interpretation.

**End-of-day deliverables**
- CTDIvol/DLP extraction proof or documented blocker.
- Upload screen prototype.
- Manually checked dose-metric examples.

**Preparation for Day 6:** agree the patient identity fields and internal ID behavior; Researcher continues coefficient source review.

---

### Day 6 — Patient identity model and scientific method research

**Habiba**
- Draft the data model: Patient, PatientIdentifier, Examination, DoseMetric, DoseEstimate, MatchReview.
- Define internal RADSAFE Patient ID generation.
- Define candidate matching evidence and uncertain-match review state.
- Draft database relationships and constraints.

**Manar**
- Design Patient Search and Patient Profile screens.
- Show RADSAFE Patient ID prominently.
- Design chronological examination history table.
- Include masked/hidden identity fields and “match review required” state.

**Medical Physics Researcher**
- Research effective-dose conversion methodology.
- Review candidate k-factor references and their exact applicability.
- Record population, anatomical coverage, publication version, page/table, and limitations.
- Do not approve initial draft values without checking the primary reference.

**End-of-day deliverables**
- Draft ERD/data model and Patient ID/matching rules.
- Patient Search/Profile wireframes.
- k-factor evidence table draft and unresolved questions.

**Preparation for Day 7:** Backend prepares initial persistence; Frontend prepares sample examination history; Researcher defines when a calculation must be withheld.

---

### Day 7 — Persistence and examination history

**Habiba**
- Implement initial entities/repositories or a working persistence proof.
- Save and retrieve a test patient and linked examination.
- Ensure one examination links to one internal patient record.
- Add a basic duplicate-upload strategy proposal.

**Manar**
- Build Patient Profile and Examination History HTML prototype.
- Add chronological rows using synthetic sample records.
- Build an Examination Details layout with dose metrics and warnings.
- Add empty and missing-data states.

**Medical Physics Researcher**
- Define anatomy/protocol mapping categories for the initial test scope.
- List cases where k-factor selection is uncertain and no estimate should be generated.
- Prepare independent calculation test-case format.

**End-of-day deliverables**
- Patient/examination persistence proof.
- Patient Profile + Examination Details prototype.
- Anatomy mapping and “not calculated” rules draft.

**Preparation for Day 8:** Backend and Frontend agree on patient/history API fields; Researcher drafts first calculation examples.

---

### Day 8 — API contract, chart prototype, and DRL research

**Habiba**
- Draft API endpoints and request/response examples.
- Prioritize upload, patient search, patient profile, examination history, examination details, and dose summary.
- Define stable error codes and upload processing statuses.
- Review API contract with Frontend.

**Manar**
- Build Chart.js prototype for per-examination and cumulative estimated dose trends.
- Clearly label axes and units.
- Use synthetic/mock data and label it as demo data.
- Add tooltip details and handle missing values without plotting them as zero.

**Medical Physics Researcher**
- Research suitable DRL references.
- Record source, year, population, examination category, metric, unit, and conditions for comparison.
- Define why unmatched categories must not be compared.
- Draft independent effective-dose calculations where coefficients are verified.

**End-of-day deliverables**
- API contract v1.
- Patient dose trend chart prototype.
- DRL evidence table draft and initial manual calculation cases.

**Preparation for Day 9:** freeze the first API contract for the sprint demo; prepare an end-to-end demonstration scenario.

---

### Day 9 — Integration rehearsal and validation

**Habiba**
- Connect at least one backend endpoint or prepare a runnable API demonstration.
- Test upload/parser errors and persistence.
- Add unit tests for proven extraction/calculation logic.
- List unsupported RDSR cases and technical blockers.

**Manar**
- Connect to available APIs if ready; otherwise use a clearly separated mock API adapter.
- Test navigation from Dashboard → Upload → Patient Profile → Examination Details.
- Test loading, empty, error, and review-required states.
- Check responsive layout and accessibility basics.

**Medical Physics Researcher**
- Compare extracted values against source report examples.
- Independently recalculate eligible dose test cases.
- Review UI terminology and units.
- Submit a list of scientific corrections and unresolved issues.

**End-of-day deliverables**
- Integration rehearsal result.
- Backend test notes and frontend navigation demo.
- Scientific validation notes and issue list.

**Preparation for Day 10:** each member prepares a 5-minute demonstration and lists what is complete, incomplete, and blocked.

---

### Day 10 — Sprint demonstration and next-phase planning

**Habiba**
- Demonstrate backend setup, sample parsing result, patient/examination data model, and API contract.
- Present technical blockers and Phase 2 backlog.
- Confirm integration responsibilities and priorities.

**Manar**
- Demonstrate Figma designs and frontend prototype.
- Walk through upload, patient search/profile, history, and chart.
- Present UI gaps and next implementation tasks.

**Medical Physics Researcher**
- Present CTDIvol/DLP field dictionary and verified sample values.
- Present scientific references and k-factor/DRL status.
- Demonstrate independent calculations and explain cases that must not be calculated.

**End-of-day deliverables**
- Sprint demo.
- Updated SRS and architecture decisions.
- Parser feasibility report.
- API contract v1.
- Figma screens and frontend prototype.
- Scientific data dictionary and validation pack.
- Prioritized Phase 2 backlog with owners and estimates.

## 4. Daily team check-in

Each member posts:

```text
Day:
Completed yesterday:
Today's target:
Deliverable/evidence:
Blocker:
Help or decision needed:
```

## 5. Sprint acceptance checklist

- [ ] Java/Spring Boot project setup is documented and reproducible.
- [ ] At least one representative de-identified/synthetic CT RDSR has been inspected.
- [ ] Parser feasibility and limitations are written down.
- [ ] CTDIvol/DLP sample values have been manually checked where available.
- [ ] Patient ID and uncertain-match workflow are agreed.
- [ ] Initial data model is reviewed by the team.
- [ ] Dashboard, Upload, Patient Profile, and Examination Details have Figma or HTML prototypes.
- [ ] Chart uses clearly labeled synthetic data.
- [ ] API contract v1 is shared between Backend and Frontend.
- [ ] k-factor and DRL research includes exact sources and applicability notes.
- [ ] No uncertain patient match is silently merged.
- [ ] No missing dose value is represented as zero.
- [ ] Phase 2 tasks have owners and priorities.

## 6. Important scope guardrails for the first 10 days

Do not spend this sprint on:
- Full PACS/RIS integration.
- AI prediction.
- Phantom/equipment QA module.
- Supporting every vendor/report variation.
- Production deployment or real-patient use.
- Polishing PDF reports before the core data flow is validated.

First prove the core chain: **RDSR → validated extraction → safe patient association → examination history → scientifically eligible dose estimate.**
