# RADSAFE — UI/UX Guidelines

## 1. Product Overview

RADSAFE is a role-based medical web application designed for Medical Physicists.

The interface should support:

- RDSR file upload.
- Extracted data review.
- Patient management.
- Examination history.
- Dose analytics.
- DRL comparison.
- PDF reports.
- Hospital and user administration.

## 2. Visual Direction

### Theme
- Light theme.
- White and light-gray backgrounds.
- Navy and dark-blue primary colors.
- Light-blue accents.
- Restrained status colors.

### Layout
- Left sidebar navigation.
- Top bar for page title and user information.
- Main content area.
- Summary cards.
- Tables for patients and examinations.
- Charts for dose analytics.

The design should be clean, modern, professional, and easy to use.

## 3. Frontend Technology

The agreed MVP frontend:

- HTML.
- CSS.
- JavaScript.
- Bootstrap 5.
- Chart.js.

Figma will be used for wireframes and UI design.

A free UI Kit or Bootstrap-based template can be used as a starting point and customized for RADSAFE.

React is not part of the current agreed MVP stack.

## 4. User Roles and Navigation

### Medical Physicist
1. Dashboard
2. Upload RDSR
3. Patients
4. Dose Analytics
5. DRL Comparison
6. Reports
7. Profile / Logout

### System Admin
1. Dashboard
2. Hospitals
3. Users
4. Account Status
5. Profile / Logout

Patients do not have a portal or login in the MVP.

## 5. Main Screens

### 5.1 Login
- Email input.
- Password input.
- Login button.
- Clear validation and error messages.
- No public registration link.

### 5.2 Medical Physicist Dashboard
Display:
- Number of processed examinations.
- Number of patients in the assigned hospital.
- Recent uploads.
- Processing statuses.
- Items requiring review.
- Quick access to Upload RDSR and Patients.

Dashboard data must belong to the authenticated user's hospital only.

### 5.3 System Admin Dashboard
Display:
- Registered hospitals.
- Medical Physicist accounts.
- Account activation status.
- Administrative shortcuts.

Avoid unnecessary exposure of patient dose information.

### 5.4 Upload RDSR
Include:
- Upload area.
- Supported file guidance.
- File restrictions.
- Upload progress.
- Processing status.
- Validation errors.
- Parsing warnings.
- Review extracted data action.

A `.dcm` extension alone does not prove that a file is a valid RDSR.

### 5.5 Patient List
Use a searchable table containing appropriate patient identifiers and summary information.

Include:
- Loading state.
- Empty state.
- Error state.
- Clear patient details action.

### 5.6 Patient Profile
Display:
- RADSAFE Patient ID.
- Available patient identifiers with suitable privacy controls.
- Examination history.
- Examination dates and descriptions.
- Links to examination details.

### 5.7 Examination Details
Organize information into:
- Study Information.
- Scanner Information.
- Irradiation Events.
- Dose Values and Units.
- Acquisition Parameters.
- Source and Processing Warnings.

Clearly distinguish source-reported values from calculated estimates.

### 5.8 Dose Analytics
- Use Chart.js.
- Display units and axis labels.
- Allow suitable date-range and examination-group filtering.
- Avoid combining incompatible measurements.
- Label estimated values and their references.

### 5.9 DRL Comparison
Display:
- Examination group.
- Metric and unit.
- DRL reference value.
- Source.
- Comparison result.
- Method and group definition.

Include a note that DRLs are group-level optimization references, not individual patient dose limits.

### 5.10 Reports
- List authorized reports.
- Display report date and scope.
- Provide download action.
- Restrict access to the user's hospital.

### 5.11 Hospital Management
System Admin can:
- Create hospitals.
- Edit hospital information.
- View hospital status.

### 5.12 User Management
System Admin can:
- Create Medical Physicist accounts.
- Assign accounts to hospitals.
- Activate/deactivate accounts.
- View role and hospital assignment.

## 6. UI States

Every data-driven screen should support:

- Loading.
- Empty.
- Success.
- Validation Error.
- Server Error.
- Unauthorized / Forbidden.
- Review Required.

Use understandable messages. Do not expose raw backend exceptions or stack traces.

## 7. Responsive Design

Prioritize desktop and laptop layouts because the application contains detailed tables and examination data.

Also support:
- Collapsible sidebar.
- Responsive tables.
- Vertically stacked cards on mobile.
- Readable forms.
- Responsive charts.

## 8. Accessibility

- Use readable font sizes.
- Maintain sufficient contrast.
- Add labels to form controls.
- Support keyboard navigation.
- Do not rely on color alone.
- Use consistent terminology.

## 9. Figma Design Workflow

1. Create the design system.
2. Define colors, typography, spacing, buttons, and inputs.
3. Design the shared sidebar and top bar.
4. Design Login.
5. Design Medical Physicist Dashboard.
6. Design Upload RDSR.
7. Design Patient List and Patient Profile.
8. Design Examination Details.
9. Design Dose Analytics.
10. Design DRL Comparison.
11. Design Reports.
12. Design System Admin pages.

Start with the shared layout and the core workflow instead of designing all screens at once.

## 10. Frontend Integration Rules

- Fetch real application data through backend APIs.
- Never depend on frontend filtering for security.
- Handle loading, empty, and error responses.
- Do not invent missing dose values.
- Always show units and data context.
- Distinguish source values from estimates.
- Do not label demo data as real hospital data.

## 11. Design Acceptance Checklist

- [ ] Medical Physicist and System Admin have different navigation.
- [ ] No Patient Portal or public registration.
- [ ] Medical Physicists see only their hospital's data.
- [ ] Upload and processing statuses are clear.
- [ ] Patient matching uncertainty is visible.
- [ ] Dose values display units.
- [ ] Estimates are distinguished from source values.
- [ ] DRL comparison is explained as group-level.
- [ ] Tables and charts are responsive.
- [ ] Loading, empty, success, and error states are designed.
