# RADSAFE --- Project Scope

**Document:** Project Scope\
**Version:** 1.0 --- Initial Draft\
**Project duration:** 8 weeks\
**Product type:** Educational and research-oriented web application\
**Team:** Backend Developer, Frontend Developer (UX/UI + Frontend),
Medical Physics & Research Member

------------------------------------------------------------------------

## 1. Project Purpose

RADSAFE is a web-based prototype for processing and analyzing CT
Radiation Dose Structured Reports (RDSR). It is intended to organize
radiation-dose information, support transparent calculations, provide
examination and protocol-level analysis, and present results through
dashboards and reports.

The system is an educational/research prototype. It is not a clinically
approved medical device and must not be used to make patient-care
decisions.

## 2. Problem Being Addressed

CT dose information may be stored in structured DICOM reports. Reviewing
multiple reports manually can be time-consuming and makes it harder to
organize examinations, inspect dose metrics, identify data-quality
problems, and study trends across comparable examination groups.

RADSAFE aims to provide a repeatable workflow to: - Upload supported CT
RDSR files. - Validate and process supported report structures. -
Extract available dose information. - Calculate estimated Effective Dose
when sufficient validated inputs exist. - Store anonymized examination
records. - Review examination history. - Analyze comparable examination
protocols. - Compare appropriate group-level dose statistics with
applicable DRLs. - Generate clear, traceable summaries.

## 3. Project Objectives

1.  Build a working web application for the defined MVP workflow.
2.  Support a documented subset of CT RDSR files.
3.  Extract available CTDIvol and DLP information without inventing
    missing values.
4.  Preserve the distinction between examination-level and
    irradiation-event-level measurements.
5.  Calculate estimated Effective Dose only when the required input and
    a documented conversion factor are available.
6.  Store examination information using a privacy-conscious data model.
7.  Provide history, search, protocol analysis, and a dashboard.
8.  Support scientifically appropriate group-level DRL review.
9.  Provide printable reports with units, methods, references, and
    limitations.
10. Test the software and independently verify representative
    calculations.

## 4. Intended Users

### Primary user

A medical physicist or authorized radiology professional reviewing CT
dose information.

### Secondary user

A researcher, student, or project evaluator using anonymized or
synthetic sample data to demonstrate the workflow.

The MVP does not implement a complex multi-role access-control system.

## 5. Scope of the MVP

### 5.1 Included features

  -----------------------------------------------------------------------
  ID                      Module                  MVP capability
  ----------------------- ----------------------- -----------------------
  S-01                    Dashboard               Show examination
                                                  counts, dose summaries,
                                                  protocol distribution,
                                                  and recent records

  S-02                    Upload                  Select and upload a
                                                  supported CT RDSR file

  S-03                    Validation              Detect unreadable,
                                                  invalid, unsupported,
                                                  or incomplete files

  S-04                    Extraction              Extract supported
                                                  report metadata and
                                                  dose metrics

  S-05                    Calculation             Calculate estimated
                                                  Effective Dose when
                                                  valid inputs and a
                                                  documented k-factor are
                                                  available

  S-06                    Records                 Save anonymized
                                                  examination information
                                                  and processing status

  S-07                    History                 List, search, filter,
                                                  and open examination
                                                  records

  S-08                    Details                 Display extracted
                                                  values, units,
                                                  calculation details,
                                                  and data-quality notes

  S-09                    Protocol analysis       Group comparable
                                                  examinations and
                                                  calculate descriptive
                                                  statistics

  S-10                    DRL review              Compare suitable
                                                  group-level CTDIvol/DLP
                                                  statistics with
                                                  applicable reference
                                                  values

  S-11                    Review indicators       Distinguish
                                                  data-quality flags from
                                                  group-level review
                                                  findings

  S-12                    Reports                 Print or save an
                                                  examination or
                                                  group-analysis summary
                                                  as PDF
  -----------------------------------------------------------------------

### 5.2 Minimum end-to-end workflow

1.  User opens the application.
2.  User selects a CT RDSR file.
3.  The system validates the file and determines whether its structure
    is supported.
4.  The system extracts available examination and dose information.
5.  The system records missing or unsupported fields explicitly.
6.  The system calculates estimated Effective Dose only when permitted
    by the calculation rules.
7.  The system saves the examination record.
8.  The user views the result and can find the examination in History.
9.  The user can view protocol-level summaries when enough comparable
    data exists.
10. The user can review group-level DRL comparisons and generate a
    report.

### 5.3 Out of scope for the MVP

-   PACS integration.
-   Direct connection to CT scanners.
-   Real-time scanner monitoring.
-   Clinical decision support or patient-specific risk prediction.
-   AI or machine-learning features.
-   Advanced organ-dose calculation.
-   SSDE, unless separately scoped, validated, and feasible.
-   Hospital-wide deployment.
-   Multi-tenant architecture.
-   Complex authentication, roles, and permissions.
-   Mobile application.
-   Automated clinical alerts for individual patients based on DRLs.
-   Retaining original patient-identifiable files as a default behavior.

## 6. Scientific and Product Constraints

### 6.1 CTDIvol

CTDIvol is a standardized CT scanner output index. It is not a direct
measurement of an individual patient's absorbed dose.

### 6.2 DLP

DLP is generally expressed in mGy·cm and represents CTDIvol multiplied
by scan length for the relevant acquisition. RDSR may represent multiple
irradiation events, so the implementation must not assume every
examination contains only one value.

### 6.3 Estimated Effective Dose

Estimated Effective Dose may be calculated using:

`Estimated Effective Dose (mSv) = DLP (mGy·cm) × k-factor`

The k-factor must be selected using a documented source and an
appropriate examination region/protocol category. The result is an
estimate for broad comparison and must not be presented as an exact
patient-specific risk or organ dose.

### 6.4 Diagnostic Reference Levels (DRLs)

DRLs are optimization tools for representative groups of examinations.
They are not individual patient dose limits and must not be used as
automatic alarms for individual examinations.

For CT, the system should compare appropriate quantities such as CTDIvol
and DLP at a suitable group/protocol level, using a documented
methodology and applicable reference category. Effective Dose is not a
standard CT DRL comparison quantity.

### 6.5 Missing and unsupported data

The system must distinguish: - Value present and parsed. - Value missing
in the source report. - Value present but not supported by the parser. -
Value rejected as invalid. - Value derived by a documented calculation.

The system must never silently substitute invented values.

## 7. Data and Privacy Scope

-   Use public, synthetic, or properly anonymized sample files for
    development and demonstration.
-   Do not commit identifiable patient data to a public GitHub
    repository.
-   Define which DICOM attributes are retained, removed, or transformed.
-   Use an anonymized/pseudonymized identifier in the application.
-   Avoid exposing unnecessary DICOM metadata to the browser.
-   Decide whether uploaded files are processed temporarily or retained.
-   Document file retention and deletion behavior.
-   Treat DICOM UIDs and metadata as potentially identifying until
    reviewed.

## 8. High-Level Modules

### Dashboard

Overview of processed examinations, dose summaries, protocol
distribution, recent records, and group review status.

### Upload & Processing

File selection, upload, validation, processing feedback, and extraction
result.

### Examination History

Searchable and filterable list of processed examinations.

### Examination Details

Available metadata, dose metrics, calculation provenance, and
data-quality information.

### Protocol Analysis

Descriptive statistics for comparable examination groups.

### DRL Review

Group-level comparison of appropriate dose metrics against documented
reference values.

### Reports

Printable examination and analysis summaries.

## 9. Proposed Technology Direction

  -----------------------------------------------------------------------
  Layer                               Initial proposal
  ----------------------------------- -----------------------------------
  Backend                             Java 21, Spring Boot, Maven

  DICOM processing                    dcm4che, subject to
                                      proof-of-concept validation

  Persistence                         Spring Data JPA

  Database                            SQLite initially; reassess if
                                      requirements or integration justify
                                      PostgreSQL

  Frontend                            HTML, CSS, JavaScript

  UX/UI                               Figma

  Charts                              Chart.js

  Testing                             JUnit 5, Mockito, Postman, manual
                                      end-to-end tests

  Version control                     Git and GitHub

  Reports                             Browser print / Save as PDF for MVP
  -----------------------------------------------------------------------

Technology selection is provisional until the first RDSR proof of
concept and database test are complete.

## 10. Success Criteria

The MVP is successful when: - The application starts and its main pages
are accessible. - A supported CT RDSR can be uploaded. - Invalid and
unsupported files are handled without crashing. - The parser extracts
the supported fields from the agreed sample set. - CTDIvol and DLP are
displayed with correct units and provenance. - Estimated Effective Dose
is calculated only when inputs and conversion factor are valid. -
Records are stored and can be retrieved. - Search and examination
details work. - Protocol analysis uses documented grouping rules. - DRL
review is group-level and scientifically qualified. - A report can be
printed or saved. - Core calculations and APIs have tests. - Privacy
limitations and known parser limitations are documented.

## 11. Assumptions and Dependencies

-   The team can obtain a small, representative set of CT RDSR samples
    early.
-   The supported RDSR subset will be explicitly defined.
-   Scientific references for k-factors and DRLs will be selected and
    documented.
-   The Frontend and Backend will agree on an API contract before
    integration.
-   The project has approximately eight weeks; scope must be reduced if
    the team cannot meet the core workflow milestones.

## 12. Open Decisions

The following must be agreed before implementation is considered
stable: 1. Exact CT RDSR structures and sample files supported. 2.
Whether source files are retained or deleted after processing. 3.
Anonymization/pseudonymization method. 4. k-factor source, categories,
and handling of unsupported categories. 5. DRL source, population,
examination categories, and comparison statistic. 6. Final database
choice. 7. Whether login is excluded entirely from the MVP. 8. Report
generation method. 9. Exact API request/response schemas. 10. Supported
browser and desktop viewport targets.

## 13. Change Control

Any new feature must be reviewed against: - Scientific validity. -
Development effort. - Data availability. - Privacy implications. -
Impact on the eight-week schedule.

Changes to scope, calculations, supported file structures, or database
fields must be documented in the project decision log before
implementation.
