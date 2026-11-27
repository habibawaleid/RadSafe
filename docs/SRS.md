# RADSAFE — Software Requirements Specification (SRS)

**Version:** 2.0 — Aligned with RADSAFE UI and database decisions  
**Date:** 27 November 2026  
**Status:** Approved planning baseline; implementation and verification remain pending.

## 1. Purpose

This document defines the functional and non-functional requirements for the RADSAFE MVP: a web application for importing supported CT DICOM Radiation Dose Structured Reports (RDSR), organizing patient-centred examination histories, presenting dose indicators and eligible estimates, comparing compatible examination groups with applicable Diagnostic Reference Levels (DRLs), and generating reports.

## 2. Product scope and perspective

RADSAFE is an academic Medical Physics and software graduation-project prototype. It consists of:
- Frontend: HTML5, CSS3 and vanilla JavaScript.
- Backend: Java 21, Spring Boot 3.x and Maven.
- DICOM/RDSR processing: dcm4che, subject to a representative-file proof of concept.
- Database: MySQL 8.0+ with InnoDB and utf8mb4.
- Reporting: server-generated report data and PDF export in the MVP, using a suitable Java PDF library selected during implementation.

The MVP uses manual RDSR upload. PACS integration, scanner auto-sync, patient portal, mobile application and production clinical deployment are out of scope.

## 3. Intended users and roles

- **Administrator:** manages institutional setup and authorized user accounts in the prototype.
- **Medical Physicist:** uploads and reviews RDSR records, examines dose data, reviews calculations and DRL analyses, and generates professional reports.
- **Authorized Staff:** performs permitted upload, patient/examination lookup and Patient Dose Summary generation where granted.
- **Management viewer:** views permitted aggregate analytics and reports; cannot change source measurements.

The backend must enforce authorization. Hiding a UI control is not sufficient access control. No patient account or patient login is provided in the MVP.

## 4. Terms

- **RDSR:** DICOM Radiation Dose Structured Report.
- **CTDIvol:** standardized CT scanner output index, commonly mGy; not a direct patient-specific absorbed dose.
- **DLP:** Dose-Length Product, commonly mGy·cm.
- **Irradiation event:** a separately represented irradiation/acquisition event in an RDSR.
- **Estimated Effective Dose:** an estimate calculated from eligible DLP and an applicable, validated conversion coefficient; not an individual risk estimate.
- **DRL:** optimization benchmark for representative groups of comparable examinations, not an individual patient limit.
- **RADSAFE Patient ID:** internal patient identifier used by RADSAFE.

## 5. Functional requirements

### 5.1 Authentication and authorization
- **FR-01:** The system shall provide a login screen for authorized application users.
- **FR-02:** The backend shall validate credentials and enforce role/institution permissions for protected operations.
- **FR-03:** Administrators shall be able to create and manage authorized Medical Physicist accounts within the implemented administrative scope.

### 5.2 RDSR upload and processing
- **FR-04:** An authorized user shall be able to upload a CT RDSR file manually through a file picker or drag-and-drop area.
- **FR-05:** The backend shall validate file size, DICOM readability, supported SOP Class/content and required metadata; it shall not trust file extension or browser MIME type alone.
- **FR-06:** The system shall show processing states: Uploaded, Processing/Validation, Processed Successfully, Failed, or Validation Warning.
- **FR-07:** The parser shall extract only fields it can reliably interpret and shall preserve source context, original units and provenance.
- **FR-08:** The system shall identify duplicate uploads using a file fingerprint/hash and a documented duplicate policy.
- **FR-09:** The system shall present a processing summary and actionable validation messages without exposing unnecessary patient identifiers in broad notifications.
- **FR-10:** The MVP shall not claim PACS or scanner synchronization; all ingestion is user-initiated manual upload.

### 5.3 Patient and examination records
- **FR-11:** The system shall maintain an internal RADSAFE Patient ID and separate source identifiers.
- **FR-12:** Each accepted examination shall be associated with one patient and an institution context.
- **FR-13:** Authorized users shall be able to search and filter patient records and examinations.
- **FR-14:** A Patient Profile shall show the patient's chronological CT examination history and available summary indicators.
- **FR-15:** Patient matching shall follow a documented, institution-aware policy. Ambiguous matches shall require authorized review; the system shall not silently merge uncertain identities.
- **FR-16:** The system shall retain event-level irradiation data. An examination may contain multiple irradiation events and source documents.

### 5.4 Dose information and calculations
- **FR-17:** The system shall display CTDIvol and DLP with units and source context when available.
- **FR-18:** Missing, unsupported or unreliable values shall remain NULL in storage and appear as “Not Available”; they shall not be replaced with zero.
- **FR-19:** CTDIvol shall be represented at its source/event level as applicable; the system shall not sum CTDIvol across events or examinations as a cumulative patient dose.
- **FR-20:** Cumulative DLP may be displayed only when the contributing event values, units, duplicate handling and inclusion rules are valid and documented.
- **FR-21:** Estimated Effective Dose may be calculated only when DLP, units, body-region classification, patient category and a validated applicable conversion factor are available. The result shall be labelled “estimated”.
- **FR-22:** The system shall record the calculation method, coefficient source/version, inputs, execution time and result status for each saved calculation.
- **FR-23:** Unsupported calculations shall return a non-success/Not Available state, not a numeric zero. Adult coefficients shall not be applied to pediatric cases.

### 5.5 Dashboard and analytics
- **FR-24:** The Dashboard shall show system-level counts for patients, examinations and processed RDSR files, plus recent examination activity.
- **FR-25:** Dose charts and scanner comparisons shall use compatible data groups and show the sample size (N) and grouping context.
- **FR-26:** The Dashboard shall include a DRL Exceedance Summary derived from eligible matched groups. It shall show the number and percentage of eligible groups above the selected applicable reference and the number of contributing examinations.
- **FR-27:** If there are no eligible matched groups, the Dashboard shall show an explanatory empty state rather than a misleading 0% result.

### 5.6 DRL comparison
- **FR-28:** Authorized users shall filter comparison data by date range, body region, protocol/category, patient category and optionally scanner.
- **FR-29:** The system shall display the selected DRL reference's name, source, version/year, metric, value and unit where available.
- **FR-30:** Local group statistics shall be compared only with a DRL matching metric, unit, body region/protocol category and patient category as appropriate.
- **FR-31:** The comparison shall report local median and local 75th percentile where sufficient eligible data exists, along with N.
- **FR-32:** The exceedance summary shall compare the defined local group statistic (normally the local 75th percentile) with the matched reference. Its denominator shall be eligible matched groups, not patients.
- **FR-33:** Estimated Effective Dose shall not be used as a standard CT DRL comparison metric. No individual patient shall be labelled as passing/failing a DRL.

### 5.7 Reports
- **FR-34:** The MVP shall provide exactly these selectable report types: `EXAMINATION_DOSE`, `PATIENT_DOSE_SUMMARY`, and `DOSE_ANALYTICS_DRL`.
- **FR-35:** `EXAMINATION_DOSE` shall cover one examination and target the Medical Physicist/authorized professional.
- **FR-36:** `PATIENT_DOSE_SUMMARY` shall target a patient-friendly informational summary covering multiple selected examinations belonging to one patient.
- **FR-37:** For Patient Dose Summary, the user shall select one patient first, then select multiple examinations belonging only to that patient. “Select All Eligible Examinations” and manual selection shall be supported.
- **FR-38:** The backend shall validate that all selected examinations belong to the selected patient and that the user is authorized to access them. If the patient changes in the UI, prior examination selections shall be cleared.
- **FR-39:** `DOSE_ANALYTICS_DRL` shall cover a compatible group of examinations and target Medical Physicists/management.
- **FR-40:** Reports shall support configuration, preview/review, PDF export and report history within the MVP.
- **FR-41:** Reports shall distinguish automated output from human-authored notes, assessment and approval. `generated_by` shall not imply `approved_by`.
- **FR-42:** A saved report shall retain its selected examination membership, generation metadata and a data snapshot sufficient to explain the generated result.
- **FR-43:** Patient Dose Summary shall not include diagnosis, full medical history, treatment advice or unsupported individual risk estimates. It shall include plain-language explanations, missing-data notes and an informational disclaimer.

## 6. Non-functional requirements

- **NFR-01 Security:** authenticate protected operations; enforce role and institution access on the server; store password hashes, never plaintext passwords.
- **NFR-02 Privacy:** use authorized de-identified or synthetic test data; minimize identifiers in exports; restrict access to uploaded DICOM files; never commit identifiable patient files or secrets to GitHub.
- **NFR-03 Integrity:** use relational constraints and transactions for patient, examination, event and report membership.
- **NFR-04 Traceability:** retain source-document references, hashes, extraction status, units, calculation versions and report snapshots.
- **NFR-05 Reliability:** malformed or unsupported RDSR files shall fail safely with useful error states.
- **NFR-06 Maintainability:** use layered backend architecture and separate parser/calculation logic from controllers.
- **NFR-07 Usability:** provide loading, empty, success, warning and error states; use clear medical terminology and responsive layouts.
- **NFR-08 Performance:** define configurable upload limits and avoid loading large RDSR files fully into browser memory; establish measurable response targets after sample-data profiling.
- **NFR-09 Compatibility:** target modern desktop browsers; responsive support is required for the agreed web interface.

## 7. Data and scientific constraints

1. Preserve event-level CTDIvol/DLP and original units; normalize only through documented tested conversions.
2. Do not sum CTDIvol across events or examinations.
3. Sum DLP only for valid, non-duplicated eligible records with consistent units and documented rules.
4. Effective Dose is an estimate, not a direct measurement or individual risk prediction.
5. DRLs are group optimization tools. Compare like with like and display N.
6. Missing/unsupported values remain NULL / Not Available.
7. Only show notifications documented in the RDSR or explicitly defined validation outcomes; do not invent alert thresholds.
8. Keep demo, authorized de-identified test and future production data separated.

## 8. MVP boundaries and exclusions

Included: login for staff, manual CT RDSR upload, validation/parsing PoC, patient/examination records, longitudinal history, event-level dose display, eligible estimate calculation, Dashboard, grouped DRL comparison, three report types, PDF export and basic audit/provenance.

Excluded: patient portal, patient self-registration, PACS integration, scanner auto-sync, real-time alerts, treatment/diagnostic recommendations, organ-dose modelling, individual cancer-risk prediction, multi-modality dose tracking, certified clinical deployment and unvalidated automated patient merging.

## 9. Acceptance criteria

The MVP is demonstrable when an authorized user can upload a supported anonymized CT RDSR; see a clear validation/extraction result; review extracted event-level values with units and missing-data handling; associate the examination with a patient under the approved matching policy; view chronological patient history; obtain only eligible calculations with provenance; compare a sufficiently populated compatible group against a sourced DRL; generate each of the three report types with correct scope validation; and export a traceable PDF. Unsupported files and invalid report selections must fail safely.

## 10. Limitations and disclaimer

RADSAFE is an academic prototype, not a certified medical device. It is not a substitute for qualified Medical Physics review, clinical judgement, institutional governance or validated clinical systems. No clinical use is intended without independent validation, security review and institutional approval.
