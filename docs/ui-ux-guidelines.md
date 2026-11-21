# RADSAFE — UI/UX Guidelines

## 1. Product experience

RADSAFE should feel like a clear, trustworthy medical-informatics dashboard: professional, calm, light, structured, and easy to scan. It is not a consumer fitness app and should not use alarming visuals to imply patient danger.

## 2. Agreed visual direction

- Theme: **Light**
- Design approach: **Hybrid** — clean dashboard structure with focused data visualizations and clinical tables.
- Frontend implementation: HTML/CSS/JavaScript.
- Design tool: Figma.

### Color palette

| Purpose | Color |
|---|---|
| Main background | `#F5F7FB` |
| Primary navy | `#193B63` |
| Primary action blue | `#3478C8` |
| Accent teal | `#20A69A` |
| Surface | `#FFFFFF` |
| Main text | `#1F2937` |
| Secondary text | `#64748B` |
| Border | `#E2E8F0` |
| Warning | `#D97706` |
| Error | `#DC2626` |

Use warning/error colors only for meaningful states. Do not use red to suggest that a patient's cumulative dose is inherently dangerous.

## 3. Typography

Use Inter or Roboto with a consistent hierarchy:
- Page title: 24–28 px.
- Section title: 18–20 px.
- Card title: 14–16 px.
- Body: 14–16 px.
- Table/helper text: 12–14 px.

Maintain readable line height and avoid dense blocks of text.

## 4. Navigation

Suggested main navigation:
1. Dashboard
2. Upload RDSR
3. Patients
4. Analytics
5. Match Review (show only when relevant/authorized)
6. Settings (later)

Keep the main action, Upload RDSR, easy to locate.

## 5. Dashboard layout

- Top header with RADSAFE identity and user/session area.
- Sidebar or compact navigation.
- Main content with summary cards.
- Recent upload/activity table.
- Shortcuts to upload and search.
- Avoid presenting a global cumulative-dose number across unrelated patients as if it were clinically meaningful.

## 6. Patient profile layout

The patient profile is the core screen.

### Header
- RADSAFE Patient ID as the primary visible identifier.
- Status: confirmed record / review required.
- Do not show full name or direct identifiers by default.

### Summary
- Number of recorded CT examinations.
- First and latest recorded examination dates.
- Cumulative estimated effective dose, with coverage and method note.

### History
A chronological table or timeline with:
- Date/time.
- Examination / anatomical region.
- Institution/scanner (if available).
- CTDIvol (mGy).
- DLP (mGy·cm).
- Estimated effective dose (mSv).
- Parsing/validation status.

### Chart
- X-axis: examination date.
- Y-axis: estimated effective dose per examination or cumulative estimated effective dose; label the selected view clearly.
- Tooltip includes date, exam, value, and unit.
- Missing values must remain missing, not plotted as zero.

## 7. Upload experience

Show:
- What file type is expected.
- File selection/drop zone.
- Upload progress.
- Processing steps: received → validating → parsing → matching → saved.
- Clear result and next action.

Possible statuses:
- Uploaded successfully.
- Unsupported DICOM object.
- RDSR parsed; patient match required.
- Examination saved to existing patient.
- New patient record created.
- Partial extraction; review warnings.

## 8. Patient matching experience

This is a safety-critical workflow:
- Display candidate matches without exposing unnecessary identity information.
- Explain which matching evidence is available.
- Use neutral wording such as “Possible match” rather than “Same patient”.
- Require explicit confirmation for uncertain matches.
- Offer “Create new patient” and “Review later”.
- Confirm before merging records and explain the consequences.
- Keep a visible audit history for decisions.

## 9. Dose and clinical language

Prefer:
- “Estimated effective dose”
- “Recorded CT examinations”
- “Cumulative estimate for available records”
- “Reference comparison”

Avoid:
- “Patient's exact radiation dose”
- “Cancer risk score”
- “Safe/unsafe patient dose”
- “Exceeded limit” unless a properly defined, applicable rule exists (the MVP should not make such claims).

## 10. Components

Design reusable:
- Metric cards.
- Status badges.
- Data tables.
- Search field.
- Upload drop zone.
- Timeline.
- Chart container.
- Warning/empty/error panels.
- Confirmation modal.
- Pagination (if needed).

## 11. Responsive and accessibility requirements

- Desktop-first for data-heavy workflows, but support tablet and mobile widths.
- Keyboard-operable controls.
- Visible focus states.
- Sufficient text/background contrast.
- Labels for form fields and chart descriptions.
- Do not rely on color alone to communicate status.
- Tables should remain readable; use horizontal scrolling only when necessary.

## 12. Figma handoff

Each screen should include:
- Screen name and purpose.
- Desktop and tablet layout.
- Component states.
- Spacing and typography tokens.
- Empty/loading/error examples.
- Notes for responsive behavior.
- Redlines or clear implementation notes.

## 13. Design acceptance checklist

The UI is ready for handoff when the core screens share a consistent design system, patient identity is privacy-conscious, missing/uncertain values are clearly represented, and a first-time user can complete upload → review → patient history without guidance.
