# RADSAFE — Backend Implementation Plan

**Backend:** Java 21 + Spring Boot  
**Database:** PostgreSQL  
**DICOM Library:** dcm4che

## 1. Backend Responsibilities

The backend is responsible for:

- Authentication and authorization.
- Hospital and user management.
- Hospital-level data isolation.
- RDSR upload and validation.
- DICOM parsing and data extraction.
- Patient matching.
- Examination and irradiation-event management.
- Dose calculations and analytics.
- DRL comparison.
- PDF report generation.
- Audit logs and processing status.
- Backend testing.

## 2. MVP Environment

The initial demonstration includes:

- Hospital A.
- Hospital B.
- One System Admin.
- One Medical Physicist assigned to Hospital A.
- One Medical Physicist assigned to Hospital B.

There is no public registration.

The backend must support the possibility of adding more hospitals in the future.

## 3. Proposed Architecture

```text
HTTP Request
    |
Spring Security
    |
Controller
    |
Service Layer
    |
Repository Layer
    |
PostgreSQL
```

RDSR processing workflow:

```text
Upload RDSR
    |
File Validation
    |
Duplicate Detection
    |
DICOM Parser (dcm4che)
    |
Data Extraction
    |
Data Validation
    |
Patient Matching
    |
Save Examination and Events
    |
Dose Analytics
    |
PDF Report
```

## 4. Suggested Project Structure

Adapt this structure to the existing `radsafe-backend` project.

```text
radsafe-backend/
├── src/
│   ├── main/
│   │   ├── java/.../radsafe/
│   │   │   ├── config/
│   │   │   ├── security/
│   │   │   ├── auth/
│   │   │   ├── hospital/
│   │   │   ├── user/
│   │   │   ├── patient/
│   │   │   ├── upload/
│   │   │   ├── dicom/
│   │   │   ├── examination/
│   │   │   ├── dose/
│   │   │   ├── drl/
│   │   │   ├── report/
│   │   │   ├── audit/
│   │   │   └── common/
│   │   └── resources/
│   │       ├── application.yml
│   │       └── db/migration/
│   └── test/
├── uploads/
├── pom.xml
└── README.md
```

This is a proposed organization. Do not duplicate existing packages or folders unnecessarily.

## 5. Database

**PostgreSQL is the selected MVP database.**

It provides relational data management, constraints, transactions, and a suitable foundation for multi-hospital data isolation.

SQLite is not the selected MVP database. It may be used for isolated experiments only.

## 6. Main Database Entities

### Hospitals
- `hospital_id`
- `name`
- `code`
- `status`
- `created_at`

### Users
- `user_id`
- `hospital_id`
- `email`
- `password_hash`
- `role`
- `status`
- `created_at`

The System Admin may have a null hospital assignment if implemented as a global administrator.

### Patients
- `patient_id`
- `hospital_id`
- `radsafe_patient_id`
- `source_patient_id`
- `created_at`
- `status`

### UploadedFiles
- `file_id`
- `hospital_id`
- `uploaded_by`
- `original_file_name`
- `file_hash`
- `storage_path`
- `upload_status`
- `uploaded_at`

### Examinations
- `examination_id`
- `hospital_id`
- `patient_id`
- `study_instance_uid`
- `study_date_time`
- `accession_number`
- `modality`
- `study_description`
- `protocol_name`

### IrradiationEvents
- `event_id`
- `examination_id`
- `event_identifier`
- `ctdi_vol`
- `ctdi_vol_unit`
- `dlp`
- `dlp_unit`
- `phantom_type`
- acquisition parameters

### DoseEstimates
- `estimate_id`
- `examination_id`
- `estimate_type`
- `estimated_value`
- `unit`
- `method`
- `coefficient`
- `reference`

### DRLReferences
- `drl_reference_id`
- `modality`
- `anatomical_region`
- `protocol_or_exam_group`
- `metric`
- `reference_value`
- `unit`
- `source`
- `publication_year`

### Reports
- `report_id`
- `hospital_id`
- `patient_id`
- `generated_by`
- `storage_path`
- `created_at`

Additional entities may include Scanner, ProcessingLog, PatientMatchingReview, and AuditLog.

## 7. Hospital-Level Data Isolation

This is a mandatory backend security requirement.

For every request:

1. Authenticate the user.
2. Resolve the user's role and assigned hospital on the backend.
3. Verify that the requested record belongs to the authorized hospital.
4. Apply this check to all relevant endpoints.
5. Reject unauthorized cross-hospital requests.

Never rely on frontend filtering or a hospital ID supplied by the browser as proof of authorization.

Every hospital-owned record must be associated with a hospital through a validated relationship.

Tests must verify that a Medical Physicist from Hospital A cannot read, modify, download, or delete Hospital B data.

## 8. Authentication and User Management

- No public registration endpoint.
- System Admin creates Medical Physicist accounts.
- Passwords must be securely hashed.
- Never store plaintext passwords.
- Implement role-based authorization.
- Support account activation and deactivation.
- Never expose password hashes in API responses.

## 9. RDSR Upload and Processing

### Upload Validation

- Apply configured file-size limits.
- Validate DICOM structure and RDSR content.
- Do not trust the file extension alone.
- Generate safe server-side storage names.
- Keep uploaded files outside publicly served directories.
- Calculate a cryptographic file hash.
- Associate the file with the authenticated user's hospital.

### Processing Status

Suggested statuses:

- `UPLOADED`
- `VALIDATING`
- `PROCESSING`
- `REVIEW_REQUIRED`
- `COMPLETED`
- `FAILED`

### DICOM Parsing

Use dcm4che after validating its compatibility with the selected Java version.

Test the parser using representative RDSR files.

Preserve:
- Original source values.
- Units.
- Event context.
- Missing-value state.
- Parsing warnings.

The integration must not be described as complete until the dependency, parser, and tests are implemented and verified.

## 10. Patient Matching

- Match patients only within the authenticated hospital.
- Do not use patient name alone as a matching key.
- Use reliable identifiers and available examination information.
- Send uncertain matches for manual review.
- Do not create a new patient for every upload.
- Detect duplicate files using hashes and relevant DICOM identifiers.
- Do not assume one RDSR file always represents one irradiation event.
- Never merge patients across hospitals.

## 11. Dose Processing

- Preserve original CTDIvol and DLP values when available.
- Preserve their units and phantom/event context.
- Do not sum CTDIvol values as cumulative patient dose.
- Aggregate DLP only within a clearly defined compatible scope.
- Effective Dose is an estimate and must include the method, coefficient, reference, and unit.
- DRL comparison must use suitable groups of examinations.

All dose formulas and references require Medical Physics review.

## 12. Proposed API Modules

- Authentication.
- Hospitals.
- Users.
- Uploads.
- Patients.
- Examinations.
- Dose Analytics.
- DRL References.
- Reports.
- Audit Logs.

Exact routes should follow the existing backend conventions.

## 13. File Storage and Secrets

- Store original RDSR files in protected server-side storage.
- Store metadata, file hash, and storage reference in PostgreSQL.
- Do not expose uploaded files through public static directories.
- Do not commit patient files, credentials, tokens, or secrets.
- Provide an `.env.example` with safe placeholders if environment variables are used.

## 14. Testing Plan

Required tests:

1. Authentication and role authorization.
2. Hospital and user management.
3. Hospital-level data isolation.
4. Cross-hospital access rejection.
5. Valid and invalid RDSR upload.
6. Unsupported and oversized file handling.
7. Duplicate file detection.
8. RDSR parsing using representative samples.
9. Missing-value and unit handling.
10. Patient matching and manual review.
11. Examination and event handling.
12. Dose calculations and aggregation.
13. DRL comparison.
14. PDF report authorization and content.

## 15. Implementation Phases

### Phase 1 — Backend Foundation
- Review existing Spring Boot structure.
- Configure PostgreSQL.
- Implement authentication.
- Implement hospitals and users.
- Implement hospital-level authorization.
- Write isolation tests.

### Phase 2 — RDSR Proof of Concept
- Collect permitted sample RDSR files.
- Validate dcm4che compatibility.
- Parse representative files.
- Document supported fields and limitations.

### Phase 3 — Core Application
- Implement upload and file storage.
- Implement parsing and validation.
- Implement patient matching.
- Save examinations and irradiation events.

### Phase 4 — Analytics and Reports
- Implement reviewed dose calculations.
- Implement DRL comparison.
- Implement PDF reports.

### Phase 5 — Integration
- Connect frontend APIs.
- Run end-to-end tests.
- Verify security and data isolation.
- Update documentation according to verified implementation.

## 16. Completion Rule

A feature is considered implemented only when its code, configuration, and relevant tests have been verified. Being listed in this document does not mean the feature is already complete.
