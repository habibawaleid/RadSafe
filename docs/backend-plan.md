# RADSAFE — Backend Development Plan

## 1. Ownership

Backend owner: **Member 1 (Habiba)**

Owns backend architecture, DICOM ingestion, domain services, database, REST APIs, integration coordination, backend testing, and technical progress tracking.

## 2. Technology

- Java 21
- Spring Boot 3.x
- Maven
- dcm4che (only after a parsing proof of concept)
- Spring Data JPA
- SQLite for local MVP; evaluate PostgreSQL for later deployment
- JUnit 5 and appropriate Spring testing tools

## 3. Architecture

Use a layered structure. Keep DICOM parsing and scientific calculations independent of controllers.

```text
backend/
└── src/
    ├── main/java/.../
    │   ├── config/
    │   ├── controller/
    │   ├── dto/
    │   ├── entity/
    │   ├── repository/
    │   ├── service/
    │   │   ├── dicom/
    │   │   ├── patient/
    │   │   ├── examination/
    │   │   └── dose/
    │   ├── validation/
    │   └── exception/
    └── test/java/.../
```

## 4. Main modules

### A. Upload and validation
- Accept multipart file uploads.
- Enforce configurable size limits.
- Validate DICOM readability and supported SOP Class/content.
- Do not trust file extension or browser MIME type alone.
- Generate a file fingerprint for duplicate detection.
- Return structured processing status.

### B. DICOM parser
- Build a small parser PoC before designing the full extraction layer.
- Extract only fields that are present and reliably interpreted.
- Preserve original values, units, and source context.
- Handle missing sequences and vendor variation safely.
- Do not assume all RDSR objects share identical structure.
- Keep parser output in a normalized internal DTO.

### C. Patient identity and matching
- Generate immutable internal RADSAFE Patient ID.
- Keep source identifiers separate from internal IDs.
- Include issuer/institution context in source identifier matching.
- Never auto-merge across institutions based only on Patient ID/name.
- Create a review state for uncertain matches.
- Log match decisions and any merge/split operation.

### D. Dose engine
- Accept normalized dose metrics, not raw unvalidated strings.
- Validate unit and context before calculation.
- Apply only coefficients approved by the Medical Physics researcher and recorded with source/version.
- Calculate `E = DLP × k` only when inputs are eligible.
- Store derived estimates separately from source dose metrics.
- Return warnings for unsupported region, missing DLP, or unapproved coefficient.
- Cumulative total = sum of eligible examination estimates; expose the included records and period.

### E. Persistence
Suggested entities: Patient, PatientIdentifier, Examination, DoseMetric, DoseEstimate, MatchReview, AuditEvent, and later DrlReference.
Use database constraints and transactions to protect integrity.

### F. REST API
Implement the routes listed in `SRS.md`. Agree request/response DTOs with the frontend before coding. Use consistent response shape and validation errors.

Example upload response (illustrative contract):
```json
{
  "status": "NEEDS_MATCH_REVIEW",
  "examinationId": "EXAM-...",
  "candidatePatientId": null,
  "warnings": ["Patient identity could not be matched confidently."]
}
```

## 5. Error handling

Define stable error codes such as:
- `INVALID_FILE`
- `UNSUPPORTED_DICOM_OBJECT`
- `UNSUPPORTED_RDSR_CONTENT`
- `MISSING_REQUIRED_DOSE_METRIC`
- `PATIENT_MATCH_REVIEW_REQUIRED`
- `DUPLICATE_UPLOAD`
- `PERSISTENCE_ERROR`

Do not return stack traces or sensitive DICOM content to the browser.

## 6. Testing responsibilities

- Unit tests for parsing helpers and mapping.
- Fixture-based tests using de-identified/synthetic RDSR files.
- Unit tests for unit conversion and dose calculations.
- Repository tests for patient/examination relationships.
- API tests for upload, errors, history, and dose summary.
- Integration test: upload → parse → match → persist → retrieve history.
- Negative tests: malformed file, missing DLP, unsupported anatomy, duplicate upload, ambiguous patient match.

Medical Physics supplies expected values and independently calculated reference cases. Backend implements the agreed calculations and demonstrates that tests pass.

## 7. Backend deliverables

1. Spring Boot project runs locally.
2. DICOM parsing PoC and sample parsing output.
3. Normalized RDSR extraction DTO.
4. Database schema and repositories.
5. Patient ID and match-review service.
6. Dose calculation service with versioned coefficient input.
7. REST API and API examples.
8. Automated tests and test report.
9. Integration notes and known limitations.

## 8. First 10-day tasks

| Day | Task | Deliverable |
|---|---|---|
| 1 | Create project, Java/Maven setup, Git structure | Backend starts locally |
| 2 | Review DICOM/RDSR structure and dependencies | Parser feasibility note |
| 3 | Parse one representative RDSR | PoC output |
| 4 | Extract patient/study/scanner metadata | Extraction DTO |
| 5 | Locate CTDIvol/DLP structures and units | Dose extraction proof |
| 6 | Draft entities and patient ID strategy | ERD/data model |
| 7 | Implement basic persistence | Save/retrieve test record |
| 8 | Draft upload and patient-history API | API contract + endpoints |
| 9 | Add unit tests and invalid-file handling | Test report |
| 10 | Demonstrate upload-to-persistence flow | Sprint demo and backlog |

## 9. Definition of done

A backend task is done only when code is committed, tested, documented, and its API/data impact is communicated to the frontend and Medical Physics researcher.
