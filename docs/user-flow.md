# RADSAFE — User Flow

**Version:** 2.0 | **Date:** 27 November 2026

## 1. Main navigation and user journey

Main sidebar: **Dashboard → Upload / Sync → Patients & Exams → DRL Comparison → Reports**. Login is a separate entry screen. Patient Profile, Examination Details, Reports Configuration and Report Preview/Review are supporting views, not additional sidebar sections.

```text
Staff Login
    |
    v
Dashboard
    |--------------------------|
    v                          v
Upload / Sync             Patients & Exams
    |                          |
Select RDSR                    Search / Filter
    |                          |
Upload                         v
    |                     Patient Profile
Validate DICOM                  |
    |                     Examination History
Parse supported RDSR             |
    |                          v
Extract metadata/events    Examination Details
    |                          |
Patient matching/review <------+
    |
Persist examination + irradiation events
    |
Eligible calculations (if valid inputs)
    |
Return to Patient Profile / Examination Details
    |
Reports or DRL Comparison
```

## 2. Login flow

1. User opens RADSAFE login.
2. User enters credentials.
3. Backend authenticates and resolves role/institution permissions.
4. On success, user is routed to Dashboard.
5. On failure, show a generic safe error; do not disclose whether a particular account exists.

## 3. Manual RDSR upload flow

1. Authorized user opens **Upload / Sync**.
2. User selects or drops a CT RDSR file.
3. Frontend checks basic client-side constraints and sends multipart upload.
4. Backend checks upload size, DICOM readability, supported SOP Class/content and duplicate hash.
5. Parser extracts supported patient/study/scanner/protocol metadata and irradiation events.
6. System records processing status and returns a compact result: patient identifier where permitted, examination date, body region, event count, CTDIvol/DLP and eligible Effective Dose estimate.
7. Missing or unsupported values display as **Not Available**; no value is fabricated.
8. On failure or warning, show the reason and preserve a safe processing record where appropriate.

Suggested visible statuses: Uploaded, Processing, Processed Successfully, Failed, Validation Warning. These are workflow statuses, not dose safety alerts.

## 4. Patient matching flow

Patient identifiers from DICOM must not be treated as universally unique. Matching is institution-aware and uses an explicitly approved policy.

- **Strong permitted match:** system proposes an existing RADSAFE Patient ID. Auto-association is allowed only if the team explicitly approves and tests the matching policy; otherwise ask for confirmation.
- **Ambiguous match:** authorized user reviews available evidence and chooses an existing patient, creates a new patient record, or defers the decision.
- **No candidate:** authorized user creates a new RADSAFE patient record or defers.
- **Duplicate/uncertain file:** flag for review; do not create duplicate examination/event data silently.

After confirmation, persist the examination and its source document/event records. Never merge two patient records automatically on weak evidence.

## 5. Patients & Exams flow

1. User opens **Patients & Exams**.
2. Search/filter by RADSAFE Patient ID and permitted fields; results respect institution permissions.
3. Selecting a patient opens **Patient Profile**.
4. Patient Profile shows chronological CT examination history, date range, available summary indicators and data-coverage notes.
5. Selecting an examination opens **Examination Details**.

Patient summary values must distinguish source values, derived values and calculated estimates. A cumulative Effective Dose is shown only when all included contributions are eligible; report coverage and missing-data limitations. CTDIvol is never summed into a cumulative patient dose.

## 6. Examination Details flow

One scrollable page contains:
- Examination Information.
- Dose Summary.
- Irradiation Events (event-level CTDIvol/DLP).
- Acquisition Parameters (only supported fields).
- Data Quality / documented RDSR notifications.
- Medical Physicist Notes (human-authored, separate from automated outputs).

Actions: Back to Profile, Generate Medical Physicist Examination Dose Report, Generate Patient Dose Summary. Do not reintroduce unsupported UI fields such as Philips Dose Index or Image Quality Reference unless source support is proven and scope is formally changed.

## 7. Dashboard flow

Dashboard displays system-level patient/examination/processed-file counts, recent examinations and available dose analytics. Scanner comparisons and dose charts must use compatible cohorts and display N. DRL Exceedance Summary is calculated from eligible matched groups; denominator is eligible groups, and the number of contributing examinations is also shown. If no eligible groups exist, show “No eligible matched groups” rather than 0%.

## 8. DRL Comparison flow

1. User opens **DRL Comparison**.
2. Select date range, body region, protocol/category, patient category and optional scanner.
3. Select an applicable DRL reference.
4. Show reference name, source, year/version, metric, value and unit.
5. Build compatible examination groups and calculate local median/75th percentile where sample size supports it.
6. Display Metric, Local Median, Local 75th Percentile, Selected DRL and Unit, with N and grouping definition.
7. Show group-level exceedance count and percentage. Normally compare the local 75th percentile with the matched reference; percentage denominator is eligible matched groups, not patients.
8. If no comparable groups exist, show an empty state and explain why.
9. User may send the selected analysis to the `DOSE_ANALYTICS_DRL` report workflow.

Never compare unlike metrics/units or show individual patient DRL pass/fail. Effective Dose is not a standard CT DRL metric.

## 9. Reports flow

### 9.1 Shared flow

1. User opens **Reports**.
2. Select one of the three MVP report types.
3. Configure its scope and sections/filters.
4. Backend validates authorization, report type, scope and source-data availability.
5. Generate a preview from source records and eligible calculations.
6. Review the preview; professional approval is a separate action where required.
7. Export PDF and save report history with generation metadata and data snapshot.

### 9.2 Examination Dose (`EXAMINATION_DOSE`)

- Select exactly one examination.
- Show source/RDSR status, event-level CTDIvol/DLP, supported acquisition parameters, eligible estimate and limitations.
- Keep automated output separate from physicist notes, assessment and approval.

### 9.3 Patient Dose Summary (`PATIENT_DOSE_SUMMARY`)

1. Select **one patient** first.
2. Load only that patient's eligible examinations.
3. Select multiple examinations manually or use **Select All Eligible Examinations**.
4. If the patient selection changes, clear all previously selected examinations.
5. Backend verifies every selected examination belongs to the selected patient and is accessible to the user. Reject cross-patient selections.
6. Generate a plain-language summary covering dates/body regions, reported CTDIvol/DLP indicators, eligible cumulative DLP and estimated cumulative Effective Dose only if valid, missing-data notes and explanations.
7. Export through the authorized institutional workflow; there is no patient login/portal.

Do not sum CTDIvol, add diagnosis or treatment advice, or present an individual risk estimate. State that the report is informational.

### 9.4 Dose Analytics & DRL (`DOSE_ANALYTICS_DRL`)

- Select compatible group filters and an applicable DRL reference.
- Include group definitions, sample sizes, metric/unit, local statistics, reference values, source/version and group-level exceedance summary.
- Store included examination IDs and calculation definitions in the report snapshot.

## 10. Report preview and review behavior

The preview is displayed inside a scrollable panel. Fullscreen mode expands the preview and exiting fullscreen returns the user to the previous scroll position. Generated-by, reviewed-by and approved-by are distinct. Patient Dose Summary may be generated/exported by authorized staff without creating a patient account.

## 11. Error, warning and empty states

- Unsupported DICOM/SOP class: explain unsupported file type and do not parse as CT RDSR.
- Missing dose field: show Not Available; do not substitute zero.
- Parser warning: retain traceable warning and display available extracted fields.
- Ambiguous patient match: require user decision.
- No patient examinations: clear empty state.
- No eligible Effective Dose: explain missing/unsupported input or factor.
- No eligible DRL groups: empty state, not 0% exceedance.
- Invalid Patient Dose Summary scope: reject selection and identify that all exams must belong to the selected patient.
- Unauthorized access: deny server-side and return a safe error.
