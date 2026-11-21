# RADSAFE --- User Flow

**Document:** User Flow and Interaction Requirements\
**Version:** 1.0 --- Initial Draft\
**Related documents:** `project-scope.md`, `ui-ux-guidelines.md`

------------------------------------------------------------------------

## 1. Purpose

This document describes how a user moves through RADSAFE, what actions
are available, what the system should display, and how the application
should respond to successful, empty, invalid, or failed operations.

The flow is based on the MVP. Authentication and advanced administration
are not included.

## 2. Primary User

The primary user is a medical physicist or authorized radiology
professional who wants to review CT dose information from RDSR files and
analyze stored examination data.

## 3. Main Navigation

Recommended application navigation:

1.  Dashboard
2.  Upload & Analysis
3.  Examination History
4.  Protocol Analysis
5.  DRL Review
6.  Reports

Settings may be included only if required for managing reference tables
or application configuration.

## 4. Global Navigation Flow

``` text
Application Start
      |
      v
   Dashboard
      |
      +----> Upload & Analysis
      |           |
      |           v
      |      Select RDSR File
      |           |
      |           v
      |      Validate File
      |           |
      |      +----+----+
      |      |         |
      |    Invalid    Valid
      |      |         |
      |      v         v
      |    Error    Process RDSR
      |                |
      |                v
      |          Extract Data
      |                |
      |                v
      |          Calculate Dose
      |                |
      |                v
      |          Save Record
      |                |
      |                v
      |          Results Page
      |                |
      |                v
      |          Examination Details
      |
      +----> Examination History
      |           |
      |           v
      |       Search / Filter
      |           |
      |           v
      |       Select Record
      |           |
      |           v
      |       Examination Details
      |
      +----> Protocol Analysis
      |           |
      |           v
      |       Select Protocol / Date Range
      |           |
      |           v
      |       View Group Statistics
      |
      +----> DRL Review
      |           |
      |           v
      |       Select Applicable Group
      |           |
      |           v
      |       View Comparison and Reference
      |
      +----> Reports
                  |
                  v
             Select Report
                  |
                  v
             Preview
                  |
                  v
             Print / Save as PDF
```

## 5. Flow A --- Dashboard

### Entry point

The user opens RADSAFE and lands on the Dashboard.

### User actions

-   Review summary cards.
-   Inspect recent examinations.
-   Review protocol distribution.
-   View group-level review summary.
-   Navigate to another module.
-   Open a recent examination.

### System behavior

-   Retrieve summary data from the Backend.
-   Display loading feedback while data is being retrieved.
-   Display an empty state if no examinations exist.
-   Display an error state if the Dashboard API fails.
-   Avoid showing misleading zero values when data is unavailable.

### Dashboard states

1.  Loading.
2.  Populated.
3.  Empty database.
4.  Partial data unavailable.
5.  API/network error.

## 6. Flow B --- Upload & Analysis

### Step 1: Open Upload page

The user selects Upload & Analysis from the navigation.

### Step 2: Select a file

The user chooses a file from their device or uses the file selection
area.

### Step 3: Client-side checks

The Frontend may check: - Whether a file was selected. - Whether the
file is empty. - Whether the file exceeds the configured size limit. -
Whether its extension is among the expected types.

Client-side checks are for user feedback only. The Backend must perform
authoritative validation.

### Step 4: Upload

The Frontend sends the file to the Backend using the agreed multipart
upload endpoint.

### Step 5: Backend validation

The Backend checks: - File readability. - DICOM validity. - Whether the
object is a supported RDSR. - Whether the report structure is
supported. - Whether required information is available for the requested
processing.

### Step 6: Processing

The Backend: - Parses supported report content. - Extracts available
examination information. - Extracts supported dose values. - Records
missing or unsupported fields. - Calculates estimated Effective Dose
only when allowed. - Saves the record according to the agreed storage
policy.

### Step 7: Result

If successful, the user is taken to the Analysis Results page or
receives a clear link/action to open it.

### Upload success state

Display: - Processing status. - Examination identifier. - Available
protocol information. - CTDIvol and unit. - DLP and unit. - Estimated
Effective Dose and unit, if calculated. - Missing/unsupported data
notes. - View Details action.

### Upload error state

Display a human-readable message and, where appropriate, an error code.

Examples: - File is empty. - File is not readable. - File is not
DICOM. - DICOM object is not an RDSR. - RDSR structure is not
supported. - Required dose data is missing. - Processing failed.

Do not expose stack traces or internal server details to the user.

## 7. Flow C --- Examination History

### Entry point

The user selects Examination History.

### Display

A table or list of stored examinations.

Recommended columns: - Examination ID. - Anonymized Patient ID. - Study
Date. - Protocol. - Body Region. - Processing Status. - Available dose
summary. - Action.

### User actions

-   Search by anonymized identifier.
-   Filter by date range.
-   Filter by protocol or body region.
-   Sort available columns.
-   Open examination details.

### Empty and error states

-   No examinations have been stored.
-   No results match the filters.
-   Data could not be loaded.

## 8. Flow D --- Examination Details

### Entry point

The user opens a record from Results or History.

### Display

-   Examination metadata.
-   Anonymized identifier.
-   Protocol and body region when available.
-   Study date.
-   Dose metrics and units.
-   Irradiation-event details when supported.
-   Calculation method.
-   k-factor and source when applicable.
-   Missing/unsupported field notes.
-   Processing status.

### User actions

-   Return to History.
-   Open or print the examination report.
-   Navigate to related protocol analysis when supported.

The interface must distinguish source-reported values from values
calculated by RADSAFE.

## 9. Flow E --- Protocol Analysis

### Entry point

The user selects Protocol Analysis.

### User actions

1.  Select an examination category or protocol.
2.  Select a date range if available.
3.  Apply filters.
4.  View the number of included examinations.
5.  Review descriptive statistics and charts.

### System behavior

-   Retrieve only records matching the agreed grouping rules.
-   Show the number of records included.
-   Calculate supported statistics.
-   Identify insufficient sample sizes.
-   Clearly label the statistic and unit.

### Suggested displayed statistics

-   Examination count.
-   Median CTDIvol.
-   Median DLP.
-   Mean values where useful and clearly labeled.
-   Minimum and maximum.
-   Distribution chart.

The grouping methodology must be documented. Protocol names that differ
should not automatically be treated as equivalent.

## 10. Flow F --- DRL Review

### Entry point

The user selects DRL Review or opens a group-level review item.

### User actions

1.  Select or open an applicable examination group.
2.  Review the category and reference source.
3.  Inspect the group statistic.
4.  Compare the appropriate CTDIvol/DLP metric with the applicable DRL
    reference.
5.  Read the interpretation and limitations.

### System behavior

-   Match the group to a documented reference category.
-   Show the applicable DRL source and population.
-   Compare only appropriate metrics and units.
-   Indicate whether the group requires further review according to the
    adopted methodology.
-   Show insufficient-data or unmatched-category states.

### Important interpretation rules

-   A DRL is not an individual patient dose limit.
-   Do not create an individual-patient DRL alarm.
-   Do not compare Effective Dose against CT DRL values as though it
    were a standard DRL quantity.
-   A group-level finding is a prompt for review, not an automatic
    clinical judgment.

## 11. Flow G --- Reports

### User actions

1.  Open Reports.
2.  Choose an examination report or group-analysis report.
3.  Select the relevant record or group.
4.  Preview the report.
5.  Print or save as PDF.

### Report contents

-   Report title and generated date.
-   Examination or group identifier.
-   Relevant source data.
-   Dose metrics and units.
-   Calculation method and k-factor reference when applicable.
-   DRL reference and comparison method when applicable.
-   Limitations and interpretation notes.

### Error states

-   No record selected.
-   Report data unavailable.
-   Print/preview failure.

## 12. Cross-Flow Rules

-   Every dose value must have a unit.
-   Source-reported and calculated values must be distinguishable.
-   Missing values must be labeled as unavailable, not replaced with
    zero.
-   Loading, empty, success, and error states must be designed.
-   The user must be able to return to the previous relevant page.
-   The Frontend must not infer scientific meaning from color alone.
-   The Backend is the source of truth for validation and calculations.

## 13. Flow Acceptance Criteria

The user-flow design is ready for implementation when: - All MVP modules
have an entry point. - The main upload-to-results journey is mapped. -
Invalid and failed upload paths are mapped. - Empty and error states are
included. - History-to-details navigation is clear. - Protocol analysis
and DRL review are separated. - Report generation is represented. -
Backend and Frontend agree on the data required by each screen.
