# RADSAFE — Frontend Development Plan

## 1. Ownership

Frontend owner: **Member 2 (Manar)**

Owns HTML/CSS/JavaScript implementation, responsive behavior, API integration, charts, form validation, frontend error states, and UI testing.

## 2. Technology constraints

- HTML5
- CSS3
- Vanilla JavaScript (ES6+)
- Figma for design handoff
- Chart.js for charts
- Bootstrap 5 may be used selectively if it supports the agreed design.

**Do not replace the agreed HTML/CSS/JavaScript direction with React.**

## 3. Main screens

### Screen 1 — Dashboard
- Total patient records (not a clinical dose threshold).
- Total examinations processed.
- Recent uploads and processing statuses.
- Quick actions: Upload RDSR and Search Patient.
- Clear distinction between system statistics and patient-specific information.

### Screen 2 — Upload RDSR
- File picker and drag/drop area.
- Accepted file guidance.
- Upload progress.
- Processing result: success, unsupported file, parsing warning, or match review required.
- Do not display sensitive patient identifiers in broad notifications.

### Screen 3 — Patient Search
- Search by RADSAFE Patient ID.
- Additional search fields only if authorized and agreed.
- Mask identifiers where appropriate.
- Show match-review state separately from confirmed patient records.

### Screen 4 — Patient Profile
- Prominent RADSAFE Patient ID.
- Patient identity fields hidden/masked by default.
- Summary cards: number of recorded CT examinations, date range, cumulative estimated effective dose (with method/coverage note).
- Chronological examination timeline/table.
- Dose trend chart.
- Clear note that cumulative effective dose is an estimate, not individualized risk.

### Screen 5 — Examination Details
- Study date/time.
- Institution/scanner/protocol metadata where available.
- CTDIvol and DLP with units and source context.
- Effective-dose estimate, coefficient, and calculation status.
- Parser warnings and missing fields.
- Link back to the patient history.

### Screen 6 — Match Review
- Candidate record details using privacy-safe display.
- Evidence used for matching.
- Actions: confirm existing patient, create new patient, or defer/reject candidate.
- Confirmation step for any merge.
- Never imply an uncertain candidate is already the same patient.

### Screen 7 — Analytics / DRL (later)
- Protocol-group comparisons only when categories and reference datasets are appropriate.
- Display reference source, metric, unit, population, and date/version.
- Never show a DRL as a patient dose limit.

### Screen 8 — Report (later)
- Preview and export patient radiation-history report.
- Include generation date, examination coverage, calculation method, and limitations.

## 4. Suggested frontend structure

```text
frontend/
├── index.html
├── pages/
│   ├── dashboard.html
│   ├── upload.html
│   ├── patients.html
│   ├── patient-profile.html
│   ├── examination-details.html
│   └── match-review.html
├── css/
│   ├── main.css
│   ├── components.css
│   └── responsive.css
├── js/
│   ├── api.js
│   ├── dashboard.js
│   ├── upload.js
│   ├── patients.js
│   ├── patient-profile.js
│   ├── examination.js
│   └── match-review.js
└── assets/
```

## 5. API integration

Create one shared `api.js` module for:
- Base API URL.
- Fetch wrapper.
- JSON and multipart upload handling.
- Consistent error parsing.
- Loading and timeout behavior.

Do not hardcode backend response assumptions before the API contract is agreed. Use mock data only during initial layout work, and label it clearly as demo data.

## 6. UX states to implement

Every data-driven screen must cover:
- Loading.
- Success with data.
- Empty state.
- Error state.
- Partial data / missing fields.
- Unauthorized or unavailable state when authentication is introduced.
- Match review required.
- Upload in progress.

Do not show missing dose values as zero. Do not display an estimated value without its unit and calculation status.

## 7. First 10-day tasks

| Day | Task | Deliverable |
|---|---|---|
| 1 | Review user flows and UI guidelines | Screen inventory |
| 2 | Figma dashboard and navigation wireframe | Approved wireframe |
| 3 | Build shared layout, navigation, typography | HTML/CSS shell |
| 4 | Dashboard using clearly labeled mock data | Dashboard prototype |
| 5 | Upload screen and validation states | Upload UI |
| 6 | Patient search and patient profile layout | Profile prototype |
| 7 | Examination details and history table | Examination UI |
| 8 | Chart.js timeline using sample data | Dose trend chart |
| 9 | Connect available backend endpoints or mock API adapter | Integration demo |
| 10 | Responsive checks, accessibility, bug fixes | Frontend sprint demo |

## 8. Frontend acceptance checklist

- Navigation works across core screens.
- Layout works on desktop and tablet widths.
- Forms have labels and understandable validation.
- Upload progress and backend errors are visible.
- Patient history is chronological.
- Units are shown beside dose values.
- Missing/uncertain data is not fabricated.
- Charts explain what is being plotted.
- Sensitive identifiers are not exposed by default.
