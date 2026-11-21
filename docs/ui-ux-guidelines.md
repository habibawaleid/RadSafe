# RADSAFE --- UI/UX Guidelines

**Document:** UX/UI Design and Frontend Handoff Guidelines\
**Version:** 1.0 --- Initial Draft\
**Design tool:** Figma\
**Frontend implementation:** HTML, CSS, JavaScript\
**Primary target:** Desktop web application

------------------------------------------------------------------------

## 1. Design Goal

RADSAFE should look and behave like a professional medical data-analysis
application. The interface should prioritize clarity, data accuracy,
readability, and predictable workflows over decoration.

The design must help users: - Understand the state of an uploaded
report. - Read dose metrics and units without ambiguity. - Distinguish
source values from calculated estimates. - Navigate examination records
and analysis. - Interpret group-level review information responsibly. -
Recognize missing, invalid, or unsupported data.

## 2. Design Principles

1.  **Clarity first:** Important values and actions must be easy to
    find.
2.  **Consistency:** Reuse the same patterns for navigation, cards,
    tables, buttons, and status messages.
3.  **Scientific transparency:** Display units, methods, references, and
    limitations.
4.  **Error prevention:** Confirm important actions and validate forms.
5.  **Accessible communication:** Do not communicate status through
    color alone.
6.  **Progressive disclosure:** Show the most important information
    first; place technical details in a secondary section.
7.  **Responsive behavior:** Support common desktop widths and remain
    usable on smaller screens.

## 3. Visual Direction

Recommended initial direction: - Light theme. - Professional
medical/technical appearance. - Neutral background. - Clear white or
lightly tinted content surfaces. - Dark, readable text. - One restrained
primary accent color. - Separate semantic colors for success, warning,
and error. - Minimal shadows and borders. - Consistent spacing and
alignment.

The exact palette must be approved in Figma. Do not use color to imply a
clinical risk level unless the interpretation has been scientifically
defined.

## 4. Information Architecture

Recommended main navigation: - Dashboard - Upload & Analysis -
Examination History - Protocol Analysis - DRL Review - Reports

Optional: - Settings, only if reference tables or other configuration
features are included in the MVP.

Use consistent page titles, active navigation states, and breadcrumbs or
back navigation where helpful.

## 5. Required Screens

### 5.1 Dashboard

Purpose: Give the user a quick overview of available data.

Required components: - Sidebar navigation. - Header and page title. -
Summary cards. - Examination count. - Dose summary cards. - Protocol
distribution chart. - Recent examinations table. - Group-level review
summary.

Rules: - Label every metric with its unit. - Show the period or dataset
represented. - Do not show misleading statistics when no data exists. -
Distinguish unavailable data from a true value of zero.

### 5.2 Upload & Analysis

Purpose: Let the user select and process an RDSR file.

Required components: - Page title and short instruction. - File
selection area. - Supported file information. - Selected file name and
size. - Upload/process action. - Progress or processing state. - Success
and error feedback.

States to design: - No file selected. - File selected. - Uploading. -
Processing. - Completed. - Invalid file. - Unsupported report. -
Processing failed.

Do not imply that checking a filename extension proves that a file is a
valid RDSR. The Backend performs authoritative validation.

### 5.3 Analysis Results

Purpose: Present the information extracted from the uploaded report.

Required components: - Examination summary. - CTDIvol card with mGy. -
DLP card with mGy·cm. - Estimated Effective Dose card with mSv, if
available. - Protocol and body region. - Processing/data-quality
status. - Calculation details and reference. - Link to full examination
details.

Rules: - Label Effective Dose as an estimate. - Identify whether values
came from the report or were calculated. - Show unavailable values as
"Not available" or an equivalent clear label, not zero.

### 5.4 Examination History

Purpose: Find and open stored examination records.

Required components: - Search field. - Date filters. -
Protocol/body-region filters where supported. - Results table. - Sorting
or pagination where needed. - View Details action.

Table rules: - Use readable column headings. - Keep units in headings or
clearly associated with values. - Avoid overcrowding the table. -
Provide an empty state when no records match.

### 5.5 Examination Details

Purpose: Present one examination in more detail.

Required components: - Examination identification. - Available
metadata. - Dose metrics. - Irradiation-event information where
supported. - Calculation provenance. - Data-quality notes. - Report
action.

Use sections or tabs only if they improve comprehension. Do not hide
essential dose values behind unnecessary navigation.

### 5.6 Protocol Analysis

Purpose: Review comparable examination groups.

Required components: - Protocol/category selector. - Date range selector
where supported. - Examination count. - Descriptive statistics. -
CTDIvol and DLP charts. - Grouping explanation.

Rules: - Display the number of examinations included. - Label median,
mean, minimum, and maximum correctly. - State the units. - Make
insufficient sample sizes visible. - Do not combine unlike protocols
without a documented rule.

### 5.7 DRL Review

Purpose: Present a transparent group-level comparison.

Required components: - Examination category. - Reference source. -
Applicable population. - Group statistic. - CTDIvol/DLP reference values
and units. - Comparison visualization. - Review interpretation. -
Limitations.

Rules: - DRLs are not individual patient limits. - Never show an
individual examination as "unsafe" solely because a value exceeds a
DRL. - Separate data-quality flags from group-level review findings. -
Do not compare Effective Dose as though it were a standard CT DRL
metric. - Make unmatched categories and insufficient data explicit.

### 5.8 Reports

Purpose: Preview and print a useful summary.

Required components: - Report type. - Selected examination/group. -
Preview. - Generated date. - Print / Save as PDF action.

Reports should include: - Units. - Calculation method. - Reference
sources. - Scope and limitations. - Clear distinction between source
values and derived estimates.

## 6. Design System Requirements

Create reusable Figma styles and components for:

### Foundations

-   Color styles.
-   Typography styles.
-   Spacing scale.
-   Grid and layout rules.
-   Border radius.
-   Elevation/shadow rules.
-   Icon style.

### Components

-   Primary, secondary, and text buttons.
-   Text inputs.
-   Search input.
-   Dropdown/select.
-   Date filter.
-   File upload area.
-   Summary card.
-   Data table.
-   Status badge.
-   Alert/message.
-   Modal/dialog.
-   Tabs, if required.
-   Chart container.
-   Pagination, if required.
-   Sidebar and header.

Each component should include relevant states such as default, hover,
focus, disabled, loading, error, and selected where applicable.

## 7. Typography and Data Presentation

-   Use a readable sans-serif font.
-   Establish clear heading, body, label, and caption styles.
-   Avoid overly small text for important values.
-   Align numerical values consistently.
-   Use consistent decimal precision.
-   Display units explicitly.
-   Use clear date formatting consistently.
-   Avoid relying on long blocks of text inside dashboard cards.

## 8. Color and Status Semantics

Use restrained and consistent status colors: - Success:
completed/valid. - Warning: requires attention or incomplete
information. - Error: failed/invalid. - Neutral: pending, informational,
or unavailable.

Always pair color with text, an icon, or another non-color indicator.

A group-level DRL review status must be labeled as a review finding, not
a patient-specific safety verdict.

## 9. Interaction and Feedback

Every interactive element must have a clear response.

Required behaviors: - Buttons show an appropriate loading/disabled state
during submission. - File upload shows selected file information. -
Forms provide validation feedback. - Tables show loading and empty
states. - Failed API requests show a recoverable error message. -
Destructive actions, if added, require confirmation. - Navigation
indicates the current page. - Keyboard focus must be visible.

## 10. Responsive Guidelines

Desktop is the primary MVP target.

At narrower widths: - Sidebar may collapse or become a menu. - Summary
cards should reflow into fewer columns. - Tables should remain readable
or provide controlled horizontal scrolling. - Charts should resize
without clipping labels. - Upload controls should remain usable. - Avoid
horizontal overflow across the entire page.

The Frontend Developer should define target breakpoints in the Figma
handoff and implement them consistently.

## 11. Accessibility and Usability

-   Maintain sufficient text/background contrast.
-   Use labels for all form inputs.
-   Provide keyboard-accessible controls.
-   Use meaningful button text.
-   Provide visible focus states.
-   Do not rely only on color to communicate errors or review findings.
-   Use understandable error messages.
-   Avoid unexplained abbreviations; provide labels or tooltips where
    useful.
-   Ensure charts have titles, legends, and text summaries where
    practical.

## 12. Figma Workflow and Deliverables

The Frontend Developer should deliver:

1.  Basic UX research notes.
2.  User personas or a concise primary-user profile.
3.  User Flow.
4.  Low-fidelity wireframes.
5.  Approved page structure.
6.  High-fidelity MVP screens.
7.  Design System.
8.  Reusable components.
9.  Clickable prototype.
10. Responsive behavior notes.
11. Developer handoff notes.

Recommended Figma pages: - `00 — Cover & Project Notes` -
`01 — User Flow` - `02 — Wireframes` - `03 — Design System` -
`04 — Components` - `05 — High-Fidelity Screens` - `06 — Prototype` -
`07 — Handoff`

## 13. Backend Coordination and Handoff

Before implementing a screen that depends on live data, the Frontend and
Backend developers must agree on: - Endpoint. - Request parameters. -
Response fields. - Data types. - Units. - Loading behavior. - Empty
behavior. - Error response. - Optional/missing fields.

Figma annotations should identify: - Which values are dynamic. - Which
values are calculated. - Which values may be unavailable. - Which
actions call an API. - Which states depend on Backend responses.

Use mock data only as temporary UI scaffolding. Replace it with API data
during integration.

## 14. UI Acceptance Checklist

A screen is ready when: - Its purpose is clear. - Its main action is
obvious. - Layout and spacing are consistent. - All relevant states are
designed. - Labels and units are correct. - Missing data is handled. -
Error messages are understandable. - It follows the Design System. - It
has been reviewed against the API contract. - It remains usable at the
agreed viewport sizes.

## 15. Design Completion Criteria

The MVP design is ready for Frontend implementation when: - Main
navigation is approved. - Core user flows are complete. - Dashboard,
Upload, Results, History, Details, Protocol Analysis, DRL Review, and
Reports screens are designed or explicitly deprioritized. - The Design
System exists. - Key components have states. - The clickable prototype
demonstrates the main upload-to-results journey. - Backend data
requirements have been reviewed.
