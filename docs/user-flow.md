# RADSAFE — User Flow

## 1. User Roles

### System Admin
Creates demo hospitals, creates Medical Physicist accounts, assigns accounts to hospitals, and manages account status.

### Medical Physicist
Uploads and reviews RDSR files, manages patient examination records, analyzes dose information, compares examination groups with DRLs, and generates reports.

### Patient
A patient is a record in the system, not an application user in the MVP.

## 2. MVP Setup

The MVP includes:

- Hospital A — one Medical Physicist.
- Hospital B — one Medical Physicist.
- One System Admin.

There is no public registration.

## 3. System Admin Flow

```text
Open RADSAFE
    |
Login as System Admin
    |
Open Administration
    |
Create Hospital A and Hospital B
    |
Create Medical Physicist Accounts
    |
Assign Each Account to Its Hospital
    |
Activate Accounts
    |
Logout
```

## 4. Medical Physicist Main Flow

```text
Open RADSAFE
    |
Login
    |
Backend Identifies User and Assigned Hospital
    |
Dashboard
    |
Upload RDSR
    |
Validate File
    |
Parse RDSR Content
    |
Review Extracted Data
    |
Patient Matching Within Same Hospital
    |
Confirm Match or Request Manual Review
    |
Save Examination and Irradiation Events
    |
Patient Examination History
    |
Dose Analytics
    |
DRL Comparison
    |
Generate PDF Report
```

## 5. RDSR Upload Flow

1. Medical Physicist opens the Upload page.
2. User selects an RDSR file.
3. Backend associates the upload with the authenticated user's hospital.
4. Backend validates file size, file safety, DICOM structure, and supported RDSR content.
5. System calculates a file hash and checks for duplicates.
6. Parser extracts supported patient, study, scanner, irradiation-event, and dose information.
7. System displays processing status, warnings, and missing fields.
8. Medical Physicist reviews extracted data.
9. System searches for possible patient matches within the same hospital.
10. Medical Physicist confirms a reliable match.
11. Uncertain or conflicting matches are sent for authorized manual review.
12. Examination and irradiation events are saved after confirmation.
13. Upload and processing history remain traceable.

## 6. Patient Matching Outcomes

### Confirmed Match
A reliable patient match is found within the same hospital and confirmed.

### No Match
An authorized Medical Physicist may create a new patient record after reviewing the available identifiers.

### Uncertain Match
The system flags the case for manual review. It must not guess or merge automatically.

### Cross-Hospital Similarity
No cross-hospital matching or merging is performed.

## 7. Patient Examination History

1. Medical Physicist opens Patients.
2. Backend returns patients belonging to the user's hospital only.
3. User selects a patient.
4. RADSAFE displays the available examination history chronologically.
5. User opens an examination.
6. RADSAFE displays study information, scanner information, irradiation events, dose values, units, and warnings.
7. Missing values appear as unavailable, not zero.

## 8. Dose Analytics

1. Medical Physicist opens Dose Analytics.
2. User selects an appropriate date range and examination group.
3. Backend retrieves authorized hospital data only.
4. RADSAFE displays available CTDIvol and DLP values.
5. Derived estimates are labeled with their method and reference.
6. Incompatible measurements must not be combined.

## 9. DRL Comparison

1. Medical Physicist opens DRL Comparison.
2. User selects a suitable examination or protocol group.
3. System selects the applicable reference and metric.
4. System compares the group data with the reference.
5. Result displays reference source, value, unit, and group definition.
6. Result is presented as a group-level comparison, not an individual patient dose limit.

## 10. PDF Report

1. Medical Physicist opens a patient or examination record.
2. User selects Generate Report.
3. Backend verifies permissions and hospital ownership.
4. System generates the report using available authorized data.
5. Source values and derived estimates are clearly distinguished.
6. Missing data and limitations are identified.
7. Authorized user downloads the PDF.

## 11. Hospital Data Isolation

Every Medical Physicist request follows this process:

```text
Authenticated User
    |
Resolve Assigned Hospital on Backend
    |
Load Requested Record
    |
Verify Record Ownership
    |
Allow Request OR Reject Unauthorized Access
```

Changing a patient ID, examination ID, upload ID, URL, or request parameter must never grant access to another hospital's records.

## 12. Main Navigation

### Medical Physicist
- Dashboard
- Upload RDSR
- Patients
- Dose Analytics
- DRL Comparison
- Reports
- Profile / Logout

### System Admin
- Dashboard
- Hospitals
- Users
- Account Status
- Profile / Logout

## 13. Upload Statuses

Suggested statuses:

- `UPLOADED`
- `VALIDATING`
- `PROCESSING`
- `REVIEW_REQUIRED`
- `COMPLETED`
- `FAILED`

The interface must explain what each status means and whether user action is required.
