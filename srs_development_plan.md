# Software Requirements Specification (SRS) & Development Plan
## Project: RADSAFE — Longitudinal CT Radiation Dose Tracking System

**Version:** 2.0 (Patient-Centric Longitudinal Architecture)  
**Target Stack:** Java 21, Spring Boot, dcm4che, SQLite (JPA/Hibernate), HTML5, CSS3, Vanilla JS, Chart.js

---

## 1. Purpose & Scope
**RADSAFE** is a vendor-neutral web application built to solve the problem of fragmented CT radiation dose records across different scanner vendors (Siemens, Philips, GE, Canon) and healthcare facilities. By ingesting standard **DICOM RDSR** files and indexing them under a **Unique Patient ID**, RADSAFE automates Effective Dose calculation, aggregates cumulative exposure over months and years, visualizes multi-year dose history, and generates printable **Patient Radiation Passports (PDF)**.

---

## 2. System Users
1. **Patients:** Upload their CT RDSR files (or view their records via Unique Patient ID) to track their lifetime/annual cumulative radiation exposure and export PDF summaries for clinical consultations.
2. **Referring Physicians & Radiologists:** Review a patient's longitudinal CT dose history before ordering new imaging studies to prevent unnecessary redundant radiation exposure.
3. **Medical Physicists / QA Staff:** Verify dose extraction accuracy, inspect scanner/protocol dose metrics ($\text{CTDI}_{\text{vol}}$, $\text{DLP}$), and compare examination doses against international Diagnostic Reference Levels (DRLs).

---

## 3. Functional Requirements

### 3.1 Ingestion & Validation
* **FR-01 (Vendor-Neutral File Upload):** The system shall allow users to upload DICOM `.dcm` files via a web interface (`POST /api/rdsr/upload`).
* **FR-02 (RDSR Verification):** The backend shall verify that the uploaded file is a valid CT Radiation Dose Structured Report (`SOP Class UID = 1.2.840.10008.5.1.4.1.1.88.67`). Standard CT slice images or non-DICOM files shall be rejected with a clear descriptive error message.

### 3.2 DICOM Parsing & Patient Linking
* **FR-03 (DICOM Tag Extraction):** Using `dcm4che`, the system shall parse and extract:
  * Patient Identifiers: `PatientID` `(0010,0020)`, `PatientName` `(0010,0010)`, `PatientSex` `(0010,0040)`, `PatientBirthDate` `(0010,0030)`.
  * Study & Device Metadata: `StudyInstanceUID` `(0020,000D)`, `StudyDate` `(0008,0020)`, `InstitutionName` `(0008,0080)`, `Manufacturer` `(0008,0070)`, `StationName` `(0008,1010)`.
  * Dose Metrics: `ProtocolName` / `TargetRegion`, $\text{CTDI}_{\text{vol}}$ ($\text{mGy}$), and $\text{DLP}$ ($\text{mGy}\cdot\text{cm}$).
* **FR-04 (Unique Patient ID Resolution):** The system shall match the extracted `PatientID` (or user-provided Unique System ID) against the `Patient` database table. Existing patients shall have the new scan appended to their profile; new patients shall have a profile automatically created.
* **FR-05 (Duplicate Study Prevention):** The system shall check `StudyInstanceUID` to prevent accidental double-counting if the same RDSR file is uploaded twice.

### 3.3 Dose Calculation & Longitudinal Aggregation
* **FR-06 (Effective Dose Computation):** The system shall calculate the estimated Effective Dose ($\text{mSv}$) for each examination using validated anatomical $k$-factors:
  $$E = \text{DLP} \times k$$
* **FR-07 (Cumulative Dose Aggregation):** The system shall dynamically calculate:
  * **Total Cumulative Effective Dose ($\text{mSv}$)** across all recorded examinations.
  * **Annual Cumulative Effective Dose ($\text{mSv}/\text{year}$)** grouped by calendar year.
  * **Total Examination Count** per patient.
* **FR-08 (Missing Data Handling):** If any optional DICOM attribute (e.g., `InstitutionName` or `CTDIvol`) is missing in the file, the system shall display `"Unavailable"` rather than substituting `0`.

### 3.4 Visualization & Reporting
* **FR-09 (Patient Longitudinal Dashboard):** Upon searching by `PatientID` or completing an upload, the UI shall display patient demographics, total cumulative dose, annual dose breakdown chart, multi-vendor scanner history table, and DRL reference indicators.
* **FR-10 (PDF Passport Export):** The system shall allow exporting the complete patient longitudinal dose history and charts into a structured, printable **PDF Patient Radiation Passport**.

---

## 4. Database Schema (Relational Model)

### Table 1: `patients`
| Column | Type | Constraints | Description |
| :--- | :--- | :--- | :--- |
| `id` | `BIGINT` | Primary Key, Auto-Inc | Internal database surrogate key |
| `unique_patient_id` | `VARCHAR(64)` | Unique, Not Null | Unique Patient ID (National ID / Medical Record ID) |
| `full_name` | `VARCHAR(150)` | Not Null | Patient full name |
| `gender` | `VARCHAR(16)` | Nullable | `M`, `F`, or `Other` |
| `birth_date` | `DATE` | Nullable | Date of birth |
| `created_at` | `TIMESTAMP` | Not Null | Profile creation timestamp |

### Table 2: `examinations`
| Column | Type | Constraints | Description |
| :--- | :--- | :--- | :--- |
| `id` | `BIGINT` | Primary Key, Auto-Inc | Examination record ID |
| `patient_id` | `BIGINT` | Foreign Key (`patients.id`) | Links scan to longitudinal patient profile |
| `study_instance_uid` | `VARCHAR(128)` | Unique, Not Null | Prevents duplicate RDSR uploads |
| `study_date` | `DATE` | Not Null | Date the CT scan was performed |
| `institution_name` | `VARCHAR(150)` | Nullable | Hospital or imaging center name |
| `scanner_vendor` | `VARCHAR(64)` | Nullable | Manufacturer (Siemens, Philips, GE, etc.) |
| `protocol_name` | `VARCHAR(100)` | Not Null | Scan protocol / anatomical region |
| `ctdi_vol_mgy` | `DOUBLE` | Nullable | Volume CT Dose Index ($\text{mGy}$) |
| `dlp_mgy_cm` | `DOUBLE` | Not Null | Dose-Length Product ($\text{mGy}\cdot\text{cm}$) |
| `k_factor_used` | `DOUBLE` | Not Null | Conversion coefficient ($\text{mSv}\cdot\text{mGy}^{-1}\cdot\text{cm}^{-1}$) |
| `effective_dose_msv` | `DOUBLE` | Not Null | Calculated Effective Dose ($\text{mSv}$) |
| `drl_reference_msv` | `DOUBLE` | Nullable | Reference benchmark for comparison |
| `uploaded_at` | `TIMESTAMP` | Not Null | Upload timestamp |

---

## 5. Core REST API Endpoints

| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `GET` | `/api/status` | Health check confirming backend availability |
| `POST` | `/api/rdsr/upload` | Uploads `.dcm` RDSR file, parses metrics, links to `Unique Patient ID`, and saves exam |
| `GET` | `/api/patients/{uniquePatientId}` | Retrieves patient profile, cumulative dose totals, annual breakdown, and full scan history |
| `GET` | `/api/patients/{uniquePatientId}/chart` | Returns time-series data for annual/cumulative dose charts |
| `GET` | `/api/examinations/{examId}` | Retrieves detailed metrics for a single CT examination |