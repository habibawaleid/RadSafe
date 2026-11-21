# RADSAFE — Project Scope

**Project Type:** Medical Physics + Software Engineering  
**System:** CT Radiation Dose Monitoring & Analysis System  
**Version:** MVP

## 1. Project Overview

RADSAFE is a web-based application designed to help Medical Physicists organize, review, monitor, and analyze CT radiation dose information extracted from DICOM Radiation Dose Structured Report (RDSR) files.

The system processes RDSR files, extracts relevant patient, examination, scanner, irradiation-event, and dose information, validates the extracted data, maintains patient examination history, provides dose analytics and suitable DRL comparisons, and generates PDF reports.

RADSAFE is a dose-monitoring and analysis support tool. It does not replace professional Medical Physics judgment or clinical decision-making.

## 2. Problem Statement

CT radiation dose information may be distributed across multiple examination records and RDSR files. Manual review can make it difficult to maintain organized patient histories, identify missing or inconsistent data, and analyze dose indicators across examination groups.

RADSAFE aims to provide a centralized and structured workflow for importing RDSR files, reviewing extracted information, maintaining examination records, and supporting dose analysis.

## 3. MVP Objectives

1. Upload and validate DICOM RDSR files.
2. Extract supported patient, examination, scanner, irradiation-event, and dose information.
3. Allow Medical Physicists to review extracted data.
4. Match examinations with the correct patient record within the same hospital.
5. Maintain patient examination history.
6. Display supported dose indicators such as CTDIvol and DLP.
7. Provide dose analytics and suitable DRL comparisons.
8. Generate PDF reports.
9. Enforce secure hospital-level data isolation.

## 4. MVP Users and Deployment Scenario

The initial MVP will support:

- Two demo hospitals.
- One System Admin.
- One Medical Physicist account per hospital.
- No public registration.
- No Patient Portal.

### 4.1 System Admin

The System Admin is responsible for:

- Creating and managing the two demo hospitals.
- Creating Medical Physicist accounts.
- Assigning each Medical Physicist to a hospital.
- Activating and deactivating accounts.
- Managing basic account permissions.

### 4.2 Medical Physicist

The Medical Physicist can:

- Log in using an account created by the System Admin.
- Upload RDSR files.
- Review extracted data and processing warnings.
- Confirm patient matching or review uncertain matches.
- View patient examination history.
- Review dose metrics and analytics.
- Compare suitable examination groups with DRL references.
- Generate and export PDF reports.

Each Medical Physicist is assigned to one hospital and can access only that hospital's data.

### 4.3 Patient

Patients are represented as records in the system but are not application users in the MVP.

## 5. Functional Scope

| Module | Main Functions |
|---|---|
| Authentication | Login, logout, role-based access |
| Hospital Management | Create and manage hospitals |
| User Management | Create Medical Physicist accounts and assign hospitals |
| Dashboard | Display system summaries and recent activities |
| RDSR Upload | Upload and validate DICOM RDSR files |
| RDSR Processing | Extract supported data and record processing status |
| Patient Management | Create patient records and review matching |
| Examination History | Display patient examinations chronologically |
| Dose Analytics | Display CTDIvol, DLP, and supported estimates |
| DRL Comparison | Compare suitable examination groups with references |
| Reports | Generate PDF reports |
| Audit Logs | Record important system activities |

## 6. Out of Scope for MVP

The following features are planned for possible future versions:

- Patient Portal.
- Public registration.
- PACS/RIS integration.
- Direct scanner connectivity.
- AI-based diagnosis or prediction.
- Automated clinical alerts.
- CT equipment quality-assurance management.
- Real-time monitoring.
- Complex hospital department hierarchies.

## 7. Multi-Hospital Architecture

RADSAFE will follow a multi-hospital architecture from the beginning.

The MVP contains two demo hospitals, but the database and backend must support adding more hospitals later.

Mandatory rules:

1. Every Medical Physicist must belong to one hospital.
2. Every hospital-owned record must be associated with a `hospital_id`, directly or through a validated ownership relationship.
3. The backend must determine the user's authorized hospital from the authenticated account.
4. The backend must validate hospital ownership for every data-access operation.
5. A user from Hospital A must never access Hospital B records.
6. Patient matching must happen within the same hospital only.
7. Similar patient names or identifiers across different hospitals must never cause automatic merging.
8. Unauthorized access attempts should be rejected and recorded where appropriate.

Frontend filtering is not sufficient for security. Hospital isolation must be enforced in the backend.

## 8. Patient Identification and Matching

- Each patient receives an internal `radsafe_patient_id` within their hospital.
- Patient matching should use reliable identifiers and available examination information.
- Patient name alone must never be used as the only matching key.
- Uncertain matches must be reviewed by an authorized Medical Physicist.
- The system must not create a new patient record for every uploaded file automatically.
- Patient records must never be automatically merged across hospitals.

## 9. RDSR Data Processing

RDSR is a DICOM Structured Report, not an ordinary PDF report.

The system should investigate and support relevant DICOM CT Radiation Dose SR content, including:

- TID 10011 — CT Radiation Dose.
- TID 10012 — CT Accumulated Dose Data.
- TID 10013 — CT Irradiation Event Data.

Supported extracted data may include:

### Patient Information
- Patient ID.
- Patient Name.
- Birth Date.
- Sex, if available and appropriate.

### Examination Information
- Study Instance UID.
- Study Date/Time.
- Accession Number.
- Modality.
- Study Description.
- Body Region.
- Protocol Name.

### Scanner Information
- Manufacturer.
- Model.
- Serial Number.
- Station Name.

### Dose Information
- CTDIvol (mGy).
- DLP (mGy·cm).
- Phantom Type (16 cm / 32 cm).
- Number of Irradiation Events.
- Total DLP when appropriately calculated.

### Acquisition Parameters
- kVp.
- mA.
- Exposure Time.
- Pitch.
- Collimation Width.
- Scan Length.
- Scan Type.

Not all RDSR files contain every field. Missing values must remain missing and must never be replaced with zero.

## 10. Dose Calculation Rules

- Prefer source-reported dose values when available.
- Preserve units and event context.
- Do not sum CTDIvol values across events as cumulative patient dose.
- DLP aggregation must account for event context and phantom type.
- Effective Dose may be estimated using an appropriate documented conversion coefficient:

`E = DLP × k`

The coefficient, reference, anatomical region, method, and unit must be recorded.

Effective Dose is an estimate for radiation protection purposes. It is not a direct organ-dose measurement or an individualized risk prediction.

DRL comparisons must be performed on suitable groups of comparable examinations, not used as individual patient dose limits.

## 11. Technology Stack

| Layer | Technology |
|---|---|
| Backend | Java 21 + Spring Boot |
| DICOM Processing | dcm4che |
| Frontend | HTML + CSS + JavaScript |
| UI Framework | Bootstrap 5 |
| Charts | Chart.js |
| Database | PostgreSQL |
| File Storage | Server-side file storage |
| Reports | PDF generation |

**Database decision:** PostgreSQL is the selected database for the MVP. SQLite may be used for isolated experiments only and is not the agreed MVP database.

The dcm4che integration must be validated with representative RDSR files before claiming that parsing is fully supported.

## 12. Data Sources and Privacy

- Prefer de-identified or synthetic sample data.
- Verify the source and usage permissions of public sample files.
- Never upload identifiable patient data to a public repository.
- Never commit passwords, secrets, or private patient reports.
- Store original RDSR files in protected server-side storage.
- Keep sample data separate from application source code.

## 13. MVP Acceptance Criteria

The MVP should demonstrate that:

1. The System Admin can create two demo hospitals.
2. The System Admin can create one Medical Physicist account for each hospital.
3. Each Medical Physicist can log in successfully.
4. Each Medical Physicist can access only their assigned hospital's data.
5. Supported RDSR files can be uploaded and validated.
6. Extracted data can be reviewed before acceptance.
7. Patient matching works within the same hospital.
8. Examination records appear in the correct patient history.
9. Dose metrics are displayed with correct units and context.
10. DRL comparisons use appropriate examination groups and references.
11. PDF reports can be generated.
12. Tests demonstrate that cross-hospital access is rejected.

## 14. Risks and Validation Requirements

- RDSR files may differ between scanner vendors.
- Some data elements may be missing or encoded differently.
- dcm4che compatibility must be verified with representative sample files.
- Dose calculations and conversion coefficients require Medical Physics review.
- PostgreSQL configuration and hospital-level authorization must be tested.

## 15. Guiding Principle

RADSAFE prioritizes accurate data extraction, reliable patient identification, secure hospital-level data isolation, and transparent dose analysis over unnecessary features outside the MVP.
