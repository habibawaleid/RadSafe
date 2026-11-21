# RADSAFE — Project Scope

## 1. Vision

RADSAFE is a patient-centric system for organizing and reviewing longitudinal CT radiation-dose history. Its central purpose is to prevent examination information from remaining fragmented across separate files, scanners, and visits.

**Core concept:** one internal RADSAFE Patient ID can be associated with multiple CT examinations over time, subject to safe identity matching.

## 2. Problem statement

CT examinations generate dose-related information, often stored in DICOM Radiation Dose Structured Reports (RDSR). A patient may undergo examinations at different times, facilities, or scanners. Without a unified record, reviewing the complete history and calculating cumulative indicators can require manual collection and calculation.

## 3. Goals

1. Accept supported CT RDSR files.
2. Validate the uploaded DICOM object and identify whether it is a supported CT RDSR.
3. Extract supported patient, examination, scanner, protocol, and dose information.
4. Associate each examination with a RADSAFE patient record using safe matching rules.
5. Preserve a chronological examination history.
6. Calculate estimated effective dose using reviewed, documented conversion coefficients.
7. Show examination-level and cumulative recorded dose estimates.
8. Provide clear visualizations and an exportable patient radiation-history report.
9. Preserve traceability: show source, extraction status, calculation method, coefficient version, and warnings.

## 4. Users and roles

- **Authorized operator / researcher:** uploads files, reviews parsed information, resolves uncertain patient matches, and views records according to permissions.
- **Medical Physics researcher:** researches dose metrics, validates mappings and coefficients, prepares reference datasets, and independently checks calculations.
- **System administrator (future):** manages users, permissions, reference data, and system configuration.

For the academic MVP, role-based access may be represented simply, but the design must not assume unrestricted access to identifiable patient data.

## 5. In scope for MVP

- Manual RDSR file upload.
- DICOM validation and CT RDSR parsing proof of concept.
- Extraction of supported patient and examination metadata.
- Extraction of CTDIvol and DLP where present and correctly interpreted.
- Internal RADSAFE Patient ID.
- Patient matching workflow with duplicate prevention and manual review for uncertain matches.
- Examination history page.
- Effective-dose estimate using an approved, versioned coefficient table.
- Cumulative sum of recorded examination estimates.
- Basic dashboard, patient profile, examination details, and charts.
- Validation logs and user-visible warnings.
- De-identified/synthetic sample data for development and demonstration.

## 6. Later-phase features

- Protocol-level DRL comparison using appropriately matched reference data.
- PDF Patient Radiation Passport.
- Advanced user roles and audit trails.
- PostgreSQL deployment for multi-user operation.
- Additional DICOM variants and more extensive vendor interoperability testing.
- PACS/RIS integration only after scope, security, standards, and institutional access are established.

## 7. Out of scope for the initial MVP

- Diagnosis or treatment recommendations.
- Predicting an individual patient's cancer risk.
- Declaring a patient has exceeded a universal cumulative dose limit.
- Automatic clinical decisions or scan justification.
- Full PACS integration.
- AI-based interpretation.
- Phantom-based CT equipment QA workflows.
- Claiming support for every vendor's private tags or every RDSR variation before testing.

## 8. Patient identity and privacy

### Internal identifier

Every RADSAFE patient record receives an immutable internal identifier (`radsafePatientId`). It is the primary identifier used by the application.

### Source identifiers

DICOM Patient ID, issuer, institution, and other identifiers may be useful for matching, but their meaning and uniqueness depend on the source institution. They must not be treated as globally unique.

### Matching rules

- Same institution + same issuer + same source Patient ID may be a candidate match, subject to configured policy.
- Different institutions require additional trusted matching evidence or an authorized manual decision.
- Name or date of birth alone must not trigger automatic merging.
- Uncertain matches go to a review queue.
- A merge must be auditable and reversible by an authorized user.
- Never silently merge two patient histories.

### Privacy-by-design

- Display RADSAFE Patient ID by default.
- Mask or omit direct identifiers in general dashboard views.
- Use de-identified or synthetic data for development and demos.
- Restrict access to identifiable data.
- Avoid storing source files indefinitely unless required and approved.
- Define retention, deletion, encryption, and audit policies before real patient deployment.

## 9. Assumptions and dependencies

- The team can obtain representative, legally usable, de-identified or synthetic CT RDSR examples.
- The parser library can read the selected RDSR structures; this must be proven early.
- The scientific team will verify dose metric mappings, units, anatomical mapping, and conversion coefficients.
- The frontend and backend will agree on API contracts before integration.
- SQLite is for the local prototype, not a claim of production readiness.

## 10. Success criteria

The MVP is successful when a test RDSR can be uploaded, validated, parsed, independently checked, linked to the correct test patient record, persisted, and displayed as part of that patient's chronological history without unsafe automatic identity merging.
