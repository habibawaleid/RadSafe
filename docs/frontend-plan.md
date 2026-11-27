# RADSAFE — Frontend Development Plan

**Version:** 2.0 | **Stack:** HTML5, CSS3, Vanilla JavaScript (ES6+)

## 1. Ownership and responsibilities

Frontend owner (Member 2) is responsible for Figma handoff, page structure, reusable UI components, responsive behavior, API integration, charts, form validation, loading/error/empty states and frontend testing. Backend and Medical Physics members review API contracts and scientific wording.

## 2. Technology rules

- HTML5, CSS3 and vanilla JavaScript.
- Figma as the approved design reference.
- Chart.js may be used for charts.
- Bootstrap 5 may be used selectively only if it does not undermine the agreed custom visual style.
- Do not switch the agreed implementation to React or another framework without explicit team scope approval.
- Keep API base URL/configuration separate from page code; do not hardcode credentials or patient data.

## 3. Design and navigation baseline

Use the approved light medical SaaS style, hybrid design approach and 1440×900 desktop-first reference frame, with responsive behavior for smaller screens. Shared layout: sidebar, top bar, content area, consistent cards/tables/forms and accessible status messaging.

Main sidebar contains exactly five items:
1. Dashboard
2. Upload / Sync
3. Patients & Exams
4. DRL Comparison
5. Reports

Login is separate. Patient Profile, Examination Details, Reports Configuration and Report Preview/Review are supporting views, not extra sidebar items. Dose Analytics is integrated into Dashboard and DRL Comparison. No patient portal/login.

## 4. Screens and requirements

### 4.1 Login
- Email/username and password fields, submit/loading state and safe error message.
- Do not reveal whether an account exists.
- Redirect authorized users to Dashboard after successful authentication.

### 4.2 Dashboard
- Summary cards: Total Patients, Total Examinations, Processed RDSR Files and Dose Review Required (only if this label is defined by actual review workflow; do not invent alert logic).
- Recent five examinations with date, patient display identifier subject to permissions, body region and processing/review status.
- Dose charts by compatible body region/protocol and scanner comparisons only when groups are comparable; show N.
- **DRL Exceedance Summary:** eligible matched group count, groups above selected reference, percentage of eligible groups and number of contributing examinations. Clearly identify the metric/grouping. If no eligible groups, show an empty state, not 0%.
- Do not show patient-level DRL pass/fail.

### 4.3 Upload / Sync
- Drag/drop zone and Browse button for manual CT RDSR upload.
- Show accepted file guidance, progress and processing state.
- Workflow display: Uploaded → Validation → Extraction → Examination Record Created.
- Status labels: Uploaded, Processing, Processed Successfully, Failed, Validation Warning.
- Result summary may show Patient ID, date, body region, event count, event-level CTDIvol/DLP and estimated Effective Dose only if valid.
- Missing values display Not Available. Do not imply PACS/scanner auto-sync; “Sync” is a navigation label only in MVP.

### 4.4 Patients & Exams
- Searchable/filterable patient and examination tables.
- Respect user permissions and mask unnecessary identifiers.
- Selecting a patient opens Patient Profile; selecting an examination opens Examination Details.
- Provide a Patient Dose Summary action from patient context.

### 4.5 Patient Profile
- Patient identity summary with RADSAFE Patient ID.
- Chronological examination history and date range.
- Available cumulative indicators with coverage and missing-data notes.
- Effective Dose summary only when each included contribution is eligible; label as estimated.
- Never display a cumulative CTDIvol value.

### 4.6 Examination Details
One scrollable page with:
- Examination Information.
- Dose Summary.
- Irradiation Events table with event-level CTDIvol/DLP and units.
- Acquisition Parameters, only when supported by source.
- Data Quality / documented notifications.
- Physicist Notes, visually distinct from automated extraction/calculation.

Actions: Back to Profile, Generate Medical Physicist Report, Generate Patient Dose Summary. Do not add unsupported Philips Dose Index or Image Quality Reference fields.

### 4.7 DRL Comparison
- Filters: date range, body region, protocol/category, patient category and optional scanner.
- Selected DRL Reference Values: reference name, source, year/version, metric, value and unit.
- Comparison table: Metric, Local Median, Local 75th Percentile, Selected DRL, Unit, N.
- Group-level exceedance count/percentage; denominator is eligible matched groups, not patients.
- Use local 75th percentile normally, when data support it; clarify local statistic is not itself an official national DRL.
- No Effective Dose DRL comparison and no patient-level pass/fail.
- Export through `DOSE_ANALYTICS_DRL` report flow.

### 4.8 Reports
Show exactly three MVP report types:
- Examination Dose — one examination, Medical Physicist.
- Patient Dose Summary — multiple examinations belonging to one patient, patient-friendly informational output.
- Dose Analytics & DRL — compatible examination group, Medical Physicist/Management.

**Patient Dose Summary selection UI:** Step 1 select one patient; Step 2 load/select multiple examinations belonging to that patient. Include Select All Eligible Examinations and manual selection. Changing patient clears selected exams. Backend validation is still mandatory.

### 4.9 Report Preview / Review
- Preview inside a scrollable panel.
- Fullscreen control; exit returns to previous scroll position.
- Review/approval actions only for authorized roles.
- Clearly separate Generated by, Reviewed by and Approved by.
- PDF export and report history.

## 5. Reusable components and states

Build reusable components for sidebar, topbar, page header, summary card, searchable table, filter bar, status chip, empty state, loading skeleton, validation message, modal/confirmation, dose metric display, event table and report preview.

Every data-driven screen must support:
- Initial/loading state.
- Success/populated state.
- Empty state.
- Validation warning / partial-data state.
- Error state with retry where appropriate.
- Unauthorized/forbidden state.

Do not encode clinical meaning through color alone; pair status color with text/icon and ensure contrast.

## 6. API integration guidance

- Use a shared API helper for base URL, headers, JSON parsing and error handling.
- Use `FormData` for RDSR upload; do not manually set multipart boundary.
- Render backend data using safe DOM APIs; avoid injecting untrusted strings as HTML.
- Keep patient identifiers out of local storage unless explicitly justified and secured.
- Use server response as source of truth for report scope and authorization.
- Disable duplicate submissions while upload/report generation is in progress.

## 7. Accessibility and responsive behavior

- Semantic headings, labels, buttons and table headers.
- Keyboard-accessible navigation, dialogs and controls.
- Visible focus state and sufficient text/background contrast.
- Tables may scroll horizontally on small screens; preserve key identifiers and actions.
- Charts require text summaries or accessible labels; do not rely only on color.
- Confirm destructive or irreversible actions.

## 8. Frontend implementation order

1. Project shell, shared layout and login.
2. Dashboard with mock data clearly labelled.
3. Upload form and processing states.
4. Patients & Exams tables and Patient Profile.
5. Examination Details and event table.
6. API wiring for core patient/upload flow.
7. DRL Comparison filters/table/empty states.
8. Reports configuration including patient-first selection.
9. Report preview/fullscreen/export/history.
10. Responsive/accessibility pass and end-to-end testing.

## 9. Frontend acceptance checklist

- Sidebar has exactly five agreed items.
- No patient portal or automatic PACS sync claims.
- All screens have loading, empty, warning and error states.
- Missing dose values show Not Available, never fabricated zero.
- CTDIvol is event-level and never presented as cumulative patient dose.
- Patient Dose Summary requires one patient followed by multiple same-patient exams.
- DRL summary is group-level and shows denominator/context/N.
- Report preview scroll position is restored after fullscreen exit.
- API authorization is respected; frontend hiding is not treated as security.
