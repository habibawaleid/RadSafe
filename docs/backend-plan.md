# RADSAFE — Backend Development Plan

**Version:** 2.0 | **Stack:** Java 21, Spring Boot 3.x, Maven, MySQL 8.0+, dcm4che

## 1. Responsibilities

Backend work includes application architecture, authentication/authorization, DICOM ingestion, parser integration, domain services, MySQL persistence/migrations, REST APIs, calculation logic, report generation, audit/provenance and automated tests.

## 2. Technology decisions

- Java 21 LTS.
- Spring Boot 3.x with Maven.
- Spring Web, Spring Validation, Spring Data JPA and Spring Security as appropriate.
- MySQL 8.0+ (InnoDB, utf8mb4); do not use SQLite as the project database baseline.
- dcm4che for DICOM/RDSR parsing, only after a working proof of concept against representative authorized files.
- JUnit 5, Spring Boot Test and integration testing tools.
- A Java PDF library may be selected for report export after a small evaluation; keep report content generation independent from PDF rendering.

## 3. Architecture

Use a layered modular monolith for the MVP. Keep controllers thin and isolate DICOM parsing, patient matching, dose calculations, DRL analysis and report rendering in services.

```text
backend/
├── pom.xml
└── src/
    ├── main/java/.../radsafe/
    │   ├── config/
    │   ├── security/
    │   ├── controller/
    │   ├── dto/request/
    │   ├── dto/response/
    │   ├── entity/
    │   ├── repository/
    │   ├── service/
    │   │   ├── upload/
    │   │   ├── dicom/
    │   │   ├── patient/
    │   │   ├── examination/
    │   │   ├── dose/
    │   │   ├── drl/
    │   │   └── report/
    │   ├── validation/
    │   └── exception/
    └── test/java/.../
```

## 4. Core modules

### 4.1 Authentication and authorization
- Authenticate staff users; store only strong password hashes.
- Enforce role and institution access at service/API level.
- Suggested roles: `ADMIN`, `MEDICAL_PHYSICIST`, `AUTHORIZED_STAFF`, `MANAGEMENT_VIEWER`.
- Keep authorization decisions centralized; do not rely on frontend route hiding.

### 4.2 Upload and validation
- Accept multipart file upload with configurable maximum size.
- Validate actual DICOM structure and supported CT RDSR SOP Class/content; extension and MIME type are not sufficient.
- Compute SHA-256 (or documented hash) for duplicate detection.
- Store original files outside MySQL in access-controlled storage; database stores secure storage reference, hash, size and metadata.
- Track statuses: `UPLOADED`, `VALIDATING`, `PROCESSING`, `PROCESSED`, `FAILED`, `VALIDATION_WARNING` (map to agreed UI labels consistently).
- Return safe structured errors and preserve processing trace.

### 4.3 DICOM/RDSR parser
- Start with a parser proof of concept before full domain mapping.
- Confirm SOP Class, modality, study/series identifiers and relevant RDSR content.
- Extract only supported patient/study/scanner/protocol metadata and irradiation-event measurements.
- Handle multiple irradiation events; preserve event identity, source units and original values.
- Do not assume all manufacturers encode every field identically.
- Missing/unknown values become null with a field-level extraction status or warning.
- Keep parser output in versioned internal DTOs so parser changes do not leak into controllers/entities.

### 4.4 Patient identity and matching
- Generate an immutable internal RADSAFE Patient ID.
- Keep source Patient ID and issuer/institution context separate.
- Never assume a source Patient ID is globally unique.
- Implement deterministic institution-aware matching rules and a review path for uncertain candidates.
- Do not silently merge uncertain patient records.
- Audit manual match decisions and any later correction.

### 4.5 Domain persistence
The logical data model should cover institutions, users, patients, scanners, protocols, examinations, RDSR documents, irradiation events, acquisition parameters, dose conversion factors, calculation runs/results, DRL references, report configurations, generated reports and report-to-examination membership. Optional processing/audit tables may be added where useful.

**Important migration rule:** the database plan is a proposed logical blueprint, not proof of the current physical schema. Before changing the existing MySQL database:
1. Back it up.
2. Run `SHOW TABLES` and inspect `SHOW CREATE TABLE` for existing tables.
3. Compare actual columns, indexes and foreign keys with the approved plan.
4. Write additive, versioned migrations; do not drop/recreate tables or overwrite user data.
5. Test migrations on a copy and verify rollback/recovery strategy.

### 4.6 Dose calculation service
- Keep calculation logic independent of controllers and persistence.
- Use `Estimated Effective Dose = DLP × applicable k-factor` only when input DLP/unit, body region, patient category and coefficient are valid.
- Do not apply adult factors to pediatric cases.
- Persist input values, input units, factor ID/source/version, method version, result unit, status and limitation note.
- Unsupported/missing input returns NULL result and non-success status, not zero.
- Preserve source values; normalized values require documented tested conversion.
- Never sum CTDIvol across irradiation events/examinations as cumulative dose.
- Cumulative DLP must apply documented event inclusion and duplicate rules.

### 4.7 DRL analysis service
- Build compatible groups by body region, protocol/category, patient category/size where applicable, metric, unit and selected DRL reference.
- Calculate local median and 75th percentile only where sample size and data quality support it.
- Compare local group statistic (normally local 75th percentile) with matching reference value.
- Denominator for exceedance percentage is eligible matched groups, not patients; include contributing examination count.
- Do not use Effective Dose as a standard CT DRL metric.
- Do not generate individual patient pass/fail states.
- Persist selected reference IDs, grouping criteria, statistic definition, eligible group count, exceedance count/percentage and included examination IDs in saved report snapshot.

### 4.8 Reporting service
MVP report codes:
- `EXAMINATION_DOSE`: exactly one examination.
- `PATIENT_DOSE_SUMMARY`: one selected patient and one or more examinations belonging to that patient.
- `DOSE_ANALYTICS_DRL`: compatible examination group and applicable reference.

For Patient Dose Summary, the request should carry `patientId` plus selected `examinationIds`. Validate that every examination belongs to that patient and is accessible to the caller. A patient change in the UI must clear selected exams, but backend validation remains mandatory.

Separate report data assembly from HTML/PDF rendering. Store generated report metadata, creator, timestamp, status, snapshot, patient where applicable, and exact examination membership. `generated_by`, `reviewed_by` and `approved_by` are separate fields/actions.

## 5. Suggested REST API contract

Base path: `/api/v1`. Names are proposed and may be adjusted consistently during implementation.

| Method | Endpoint | Purpose |
|---|---|---|
| POST | `/auth/login` | Staff login |
| GET | `/auth/me` | Current user and permissions |
| POST | `/rdsr-uploads` | Upload RDSR and start processing |
| GET | `/rdsr-uploads/{id}` | Processing status/details |
| GET | `/patients` | Search/list authorized patients |
| POST | `/patients` | Create patient record when permitted |
| GET | `/patients/{id}` | Patient profile |
| GET | `/patients/{id}/examinations` | Patient examination history |
| GET | `/examinations` | Search/filter examinations |
| GET | `/examinations/{id}` | Examination and event details |
| GET | `/dashboard/summary` | Counts/recent activity/group summary |
| GET | `/drl-references` | List/filter DRL references |
| POST | `/drl-analyses` | Run compatible group comparison |
| POST | `/report-configurations` | Save report configuration |
| POST | `/reports/preview` | Validate scope and build preview data |
| POST | `/reports` | Generate and save report |
| GET | `/reports` | Report history |
| GET | `/reports/{id}` | Report details/status |
| GET | `/reports/{id}/pdf` | Download authorized PDF |

Use DTOs rather than exposing JPA entities. Validate request bodies and return consistent error payloads with a machine-readable code, human-readable message and correlation/request ID.

## 6. Database conventions

- MySQL 8.0+, InnoDB, utf8mb4.
- Use PK/FK constraints and indexes for common patient/date, examination, upload hash and report membership queries.
- Store absent/unsupported measurements as NULL.
- Preserve original unit/value and any normalized value with conversion provenance.
- Use DECIMAL for scientific measurements; avoid binary floating-point persistence for reported dose values.
- Keep uploaded file bytes outside MySQL unless a documented reason requires otherwise.
- Use schema migration tooling (e.g., Flyway) and never rely on automatic destructive schema update in shared environments.

## 7. Security and privacy

- Enforce institution-aware access and authorization on every protected endpoint.
- Validate upload content and size; use generated storage names, not user-provided paths.
- Restrict download access to authorized users.
- Do not log full patient identifiers, credentials or file contents.
- Keep secrets in environment variables/local secret configuration, not Git.
- Exclude raw DICOM/RDSR and database dumps from GitHub.
- Use de-identified/synthetic fixtures and document anonymization limitations.

## 8. Backend testing

- Unit tests: parser mapping, unit handling, matching, dose eligibility, DRL grouping, report scope validation.
- Integration tests: upload → parse → persist; patient history; report creation; PDF authorization.
- Negative tests: invalid DICOM, unsupported SOP Class, missing sequences, duplicate hash, invalid units, pediatric factor mismatch, cross-patient report selection, unauthorized institution access.
- Regression fixtures: retain sanitized representative RDSR files only where licensing/authorization permits; otherwise use synthetic parser DTO fixtures.

## 9. Implementation order

1. Spring Boot skeleton and health endpoint.
2. MySQL connection and safe migration baseline.
3. Authentication/authorization skeleton.
4. dcm4che parser proof of concept and normalized DTO.
5. Upload/validation/status/hash.
6. Patient/examination/event persistence and matching review.
7. History/details APIs.
8. Dose calculation and provenance.
9. Dashboard and DRL analysis APIs.
10. Report validation, preview, persistence and PDF export.
11. Integration, security and regression tests.
