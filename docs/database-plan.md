# RADSAFE --- Database Plan

**Document type:** Technical Planning Document\
**Project:** RADSAFE --- Intelligent CT Radiation Dose Monitoring and
Analysis System\
**Database:** MySQL 8.0+\
**Backend:** Java 21 + Spring Boot + Spring Data JPA\
**DICOM/RDSR processing:** dcm4che (must be validated against real RDSR
samples)\
**Frontend:** HTML, CSS, JavaScript\
**Status:** Proposed Database Blueprint --- Review Required Before Final
SQL\
**Version:** 1.0\
**Last updated:** 23 November 2026

------------------------------------------------------------------------

## 1. Purpose and Scope

This document defines the proposed database architecture for RADSAFE. It
is intended for the full project team and will be used as the baseline
for review, implementation, and testing.

The database is designed for the current Figma workflow and the planned
use of real CT Radiation Dose Structured Report (RDSR) files. It is not
limited to the seven temporary demo cases.

This is a **proposed blueprint**, not a guarantee that every field
listed below exists in every RDSR file. Final fields and relationships
must be validated against the latest Figma frames, representative
authorized RDSR samples, DICOM attributes, and backend requirements.

### Objectives

-   Store institution, user, patient, scanner, protocol, and examination
    information.
-   Track RDSR uploads, validation, and processing.
-   Preserve individual irradiation events and their source documents.
-   Store available dose metrics and acquisition parameters with
    normalized units.
-   Support Dose Analytics and DRL Comparison.
-   Support report configuration, preview, and generated report
    tracking.
-   Preserve source and calculation provenance.
-   Represent missing or unsupported values safely.
-   Separate demo fixtures from real test and production data.

### Initial MVP exclusions

Unless formally approved as a scope change: - PACS or direct scanner
integration. - AI dose prediction. - Advanced SSDE calculations. -
Mobile application support. - Complex enterprise authorization. -
Invented dose-alert thresholds or warnings not documented in RDSR. -
Storing original DICOM/RDSR files as MySQL BLOBs by default.

------------------------------------------------------------------------

## 2. Current Figma Scope and Constraints

The latest agreed design contains these ten pages:

1.  Login
2.  Medical Physicist Dashboard
3.  Upload RDSR
4.  Patients
5.  Patient Profile
6.  Examination Details
7.  Dose Analytics
8.  DRL Comparison
9.  Reports Configuration
10. Report Preview / Review

Shared design direction: - Desktop target: 1440 × 900. - Light medical
SaaS interface. - Shared sidebar and topbar. - Navy, white, and blue
palette.

### Examination Details requirements

The current design includes: - Back navigation to Patient Profile. -
Examination Information. - Irradiation Events. - Acquisition
Parameters. - Patient ID, examination date, accession number, and
scanner details when available. - CTDIvol and DLP when available. -
Phantom type and scan range when available. - kVp and mAs when
available.

Explicitly removed from the current design: - Philips Dose Index. -
Image Quality Reference.

Do not reintroduce these fields unless the team approves a design
change. Warnings must be based only on documented RDSR notification
events. Do not invent generic warning rules or thresholds.

### Temporary demo constraints

The current UI uses seven temporary demo cases: - Demo IDs, institution
IDs, and user IDs must be consistent across screens. - The demo cases do
not represent real RDSR measurements. - CTDIvol and DLP must display as
**Not Available** in demo records. - Never invent dose values or present
demo values as real measurements. - Demo data must be clearly
identifiable and must not be used to validate parser or scientific
calculation correctness.

------------------------------------------------------------------------

## 3. Recommended Database Architecture

The proposed schema is grouped into six logical areas.

  -----------------------------------------------------------------------------
  Area                    Purpose                 Main tables
  ----------------------- ----------------------- -----------------------------
  User and institution    Accounts and            `institutions`, `users`
  management              institution ownership   

  Patient and examination Patients, scanners,     `patients`, `scanners`,
  management              protocols, examinations `protocols`, `examinations`

  RDSR upload and         File tracking,          `rdsr_documents`,
  processing              validation, processing  `processing_logs`
                          history                 

  Dose and acquisition    Events, acquisition     `irradiation_events`,
  data                    parameters, RDSR        `acquisition_parameters`,
                          notifications           `rdsr_notification_events`

  Dose analysis and DRL   Calculation provenance  `dose_conversion_factors`,
                          and DRL references      `dose_calculation_runs`,
                                                  `dose_calculation_results`,
                                                  `drl_references`

  Reports                 Saved report settings   `report_configurations`,
                          and generated reports   `generated_reports`
  -----------------------------------------------------------------------------

Not every UI card, chart, or statistic needs a separate table. Dashboard
and analytics values should generally be produced by SQL queries,
aggregations, or views.

------------------------------------------------------------------------

## 4. Proposed Tables and Fields

Fields below are proposed starting points. Many are nullable because
RDSR content varies by manufacturer, scanner model, and software
version.

### 4.1 `institutions`

Stores hospitals or medical institutions.

  Column               Suggested type   Notes
  -------------------- ---------------- --------------------
  `institution_id`     BIGINT           PK, auto-increment
  `institution_code`   VARCHAR(30)      Unique code
  `institution_name`   VARCHAR(200)     Required
  `address`            VARCHAR(500)     Optional
  `contact_email`      VARCHAR(150)     Optional
  `created_at`         DATETIME         Required
  `is_active`          BOOLEAN          Default true

Purpose: ownership/grouping boundary for users, patients, scanners,
protocols, and local DRL references.

### 4.2 `users`

Stores application accounts.

  Column             Suggested type   Notes
  ------------------ ---------------- -----------------------------------
  `user_id`          BIGINT           PK, auto-increment
  `institution_id`   BIGINT           FK to `institutions`
  `full_name`        VARCHAR(150)     Required
  `email`            VARCHAR(150)     Required, unique
  `password_hash`    VARCHAR(255)     Secure hash only
  `role`             VARCHAR(30)      e.g. `ADMIN`, `MEDICAL_PHYSICIST`
  `is_active`        BOOLEAN          Default true
  `created_at`       DATETIME         Required
  `updated_at`       DATETIME         Optional

**MVP note:** A simple role column may be sufficient initially. Separate
role/permission tables should be introduced only if authorization
becomes more complex.

### 4.3 `patients`

Stores the minimum patient information required by the application.

  Column               Suggested type   Notes
  -------------------- ---------------- ------------------------------
  `patient_id`         BIGINT           PK, auto-increment
  `institution_id`     BIGINT           FK to `institutions`
  `dicom_patient_id`   VARCHAR(100)     DICOM Patient ID, if present
  `patient_name`       VARCHAR(200)     Optional; sensitive
  `birth_date`         DATE             Optional
  `sex`                VARCHAR(20)      Optional
  `created_at`         DATETIME         Required
  `updated_at`         DATETIME         Optional

**Identity rule:** DICOM Patient ID must not be assumed globally unique.
A candidate uniqueness constraint is
`(institution_id, dicom_patient_id)`, subject to the institution's
identity policy and treatment of missing IDs.

Avoid unnecessary personally identifiable information. Use anonymized or
synthetic data for development whenever possible.

### 4.4 `scanners`

Stores CT scanner information.

  ------------------------------------------------------------------------
  Column                   Suggested type          Notes
  ------------------------ ----------------------- -----------------------
  `scanner_id`             BIGINT                  PK, auto-increment

  `institution_id`         BIGINT                  FK to `institutions`

  `manufacturer`           VARCHAR(100)            Optional

  `model_name`             VARCHAR(150)            Optional

  `station_name`           VARCHAR(100)            Optional

  `device_serial_number`   VARCHAR(100)            Optional; potentially
                                                   sensitive

  `department`             VARCHAR(100)            Optional

  `created_at`             DATETIME                Required
  ------------------------------------------------------------------------

Some information may be extracted from DICOM metadata. Matching a parsed
scanner to an existing record requires a documented strategy; do not
rely on model name alone.

### 4.5 `protocols`

Stores protocol classifications for grouping and analysis.

  -----------------------------------------------------------------------
  Column                  Suggested type          Notes
  ----------------------- ----------------------- -----------------------
  `protocol_id`           BIGINT                  PK, auto-increment

  `institution_id`        BIGINT                  FK to `institutions`

  `protocol_name`         VARCHAR(200)            Required

  `body_region`           VARCHAR(100)            Optional

  `patient_category`      VARCHAR(30)             Optional;
                                                  adult/pediatric only
                                                  when validated

  `description`           TEXT                    Optional

  `created_at`            DATETIME                Required
  -----------------------------------------------------------------------

Preserve the original protocol description from the source separately
from any normalized classification. Protocol names may vary between
scanners and institutions.

### 4.6 `examinations`

Represents a CT examination/study linked to a patient.

  -----------------------------------------------------------------------
  Column                  Suggested type          Notes
  ----------------------- ----------------------- -----------------------
  `examination_id`        BIGINT                  PK, auto-increment

  `patient_id`            BIGINT                  FK to `patients`

  `institution_id`        BIGINT                  FK to `institutions`

  `scanner_id`            BIGINT                  Nullable FK to
                                                  `scanners`

  `protocol_id`           BIGINT                  Nullable FK to
                                                  `protocols`

  `accession_number`      VARCHAR(100)            Optional

  `study_instance_uid`    VARCHAR(100)            Optional DICOM Study
                                                  Instance UID

  `study_date`            DATE                    Optional

  `study_time`            TIME                    Optional

  `modality`              VARCHAR(20)             Expected CT for this
                                                  scope

  `study_description`     VARCHAR(250)            Optional

  `body_region`           VARCHAR(100)            Optional

  `examination_status`    VARCHAR(30)             Application workflow
                                                  status

  `created_at`            DATETIME                Required

  `updated_at`            DATETIME                Optional
  -----------------------------------------------------------------------

Patient Profile summaries such as examination count and latest
examination should normally be derived from `examinations`, not
duplicated as stored counters.

The uniqueness strategy for Study Instance UID and accession number must
be reviewed against real data and institution boundaries. Do not assume
accession numbers are globally unique.

### 4.7 `rdsr_documents`

Tracks uploaded RDSR documents. Store the original file in secure file
storage rather than as a MySQL BLOB by default.

  Column                  Suggested type   Notes
  ----------------------- ---------------- --------------------------------------
  `rdsr_document_id`      BIGINT           PK, auto-increment
  `examination_id`        BIGINT           Nullable FK until matching completes
  `uploaded_by`           BIGINT           FK to `users`
  `original_filename`     VARCHAR(255)     Required
  `storage_path`          VARCHAR(500)     Required secure storage reference
  `file_hash`             VARCHAR(128)     Required; document hash algorithm
  `file_size`             BIGINT           Required
  `sop_instance_uid`      VARCHAR(100)     Optional
  `study_instance_uid`    VARCHAR(100)     Optional
  `series_instance_uid`   VARCHAR(100)     Optional
  `transfer_syntax_uid`   VARCHAR(100)     Optional
  `processing_status`     VARCHAR(30)      Workflow status
  `validation_status`     VARCHAR(30)      Workflow status
  `error_message`         TEXT             Optional
  `uploaded_at`           DATETIME         Required
  `processed_at`          DATETIME         Optional

Suggested processing states: - `UPLOADED` - `VALIDATING` -
`PROCESSING` - `PROCESSED` - `FAILED` - `INVALID`

An examination may have multiple RDSR document records or versions. A
document may initially be uploaded before it can be matched to an
examination. Define matching and duplicate policies explicitly.

Store files outside MySQL in access-controlled storage. Keep only file
references, hashes, sizes, and required metadata in the database. Do not
expose internal storage paths to unauthorized users.

### 4.8 `processing_logs`

Stores processing and validation steps.

  Column               Suggested type   Notes
  -------------------- ---------------- ------------------------
  `log_id`             BIGINT           PK, auto-increment
  `rdsr_document_id`   BIGINT           FK to `rdsr_documents`
  `processing_step`    VARCHAR(100)     Required
  `status`             VARCHAR(30)      Required
  `message`            TEXT             Optional
  `created_at`         DATETIME         Required

Use this table to identify failures during validation, parsing,
extraction, mapping, or persistence. Avoid logging patient identifiers
or full sensitive DICOM content.

### 4.9 `irradiation_events`

Stores individual irradiation events extracted from RDSR. This is a
central table in RADSAFE.

  Column               Suggested type   Notes
  -------------------- ---------------- -----------------------------------------
  `event_id`           BIGINT           PK, auto-increment
  `examination_id`     BIGINT           FK to `examinations`
  `rdsr_document_id`   BIGINT           FK to `rdsr_documents`
  `event_uid`          VARCHAR(100)     Optional source event identifier
  `event_number`       INT              Event order within source, if available
  `event_type`         VARCHAR(100)     Optional
  `event_datetime`     DATETIME         Optional
  `protocol_name`      VARCHAR(200)     Original source protocol name
  `target_region`      VARCHAR(100)     Optional
  `ctdi_vol_mgy`       DECIMAL(12,5)    Nullable; CTDIvol in mGy
  `dlp_mgy_cm`         DECIMAL(14,5)    Nullable; DLP in mGy·cm
  `phantom_type`       VARCHAR(30)      Optional
  `source_reference`   VARCHAR(255)     Optional parser/source reference
  `created_at`         DATETIME         Required

**Critical rule:** Do not assume one CTDIvol or one DLP value per
examination. An RDSR may contain multiple irradiation events, each with
its own measurements and context. Store event-level values and derive
examination summaries only through validated aggregation rules.

Event identity and duplicate detection must be validated against real
samples. `event_number` is not necessarily globally unique.

### 4.10 `acquisition_parameters`

Stores acquisition parameters associated with an irradiation event.

  -----------------------------------------------------------------------
  Column                  Suggested type          Notes
  ----------------------- ----------------------- -----------------------
  `acquisition_id`        BIGINT                  PK, auto-increment

  `event_id`              BIGINT                  FK to
                                                  `irradiation_events`;
                                                  unique only if
                                                  one-to-one is confirmed

  `kvp`                   DECIMAL(10,3)           Nullable

  `tube_current_ma`       DECIMAL(10,3)           Nullable

  `exposure_time_ms`      DECIMAL(12,3)           Nullable

  `pitch_factor`          DECIMAL(10,5)           Nullable

  `rotation_time_sec`     DECIMAL(10,5)           Nullable

  `collimation_mm`        DECIMAL(10,5)           Nullable

  `scan_length_mm`        DECIMAL(12,3)           Nullable

  `table_feed_mm`         DECIMAL(12,3)           Nullable

  `start_location`        VARCHAR(100)            Nullable

  `end_location`          VARCHAR(100)            Nullable

  `created_at`            DATETIME                Required
  -----------------------------------------------------------------------

These are candidate fields, not a guarantee that every RDSR contains
them. The parser mapping must follow actual supported DICOM content.
Confirm whether multiple parameter groups can occur within one event
before enforcing a one-to-one relationship.

### 4.11 `rdsr_notification_events`

Stores documented notification events contained in RDSR.

  Column                       Suggested type   Notes
  ---------------------------- ---------------- -------------------------------------
  `notification_id`            BIGINT           PK, auto-increment
  `examination_id`             BIGINT           FK to `examinations`
  `event_id`                   BIGINT           Nullable FK to `irradiation_events`
  `rdsr_document_id`           BIGINT           FK to `rdsr_documents`
  `notification_type`          VARCHAR(100)     Required when available
  `notification_description`   TEXT             Optional
  `threshold_value`            DECIMAL(14,5)    Nullable; only if documented
  `actual_value`               DECIMAL(14,5)    Nullable; only if documented
  `unit`                       VARCHAR(30)      Nullable
  `event_datetime`             DATETIME         Nullable
  `created_at`                 DATETIME         Required

This is not a general-purpose alert-rule engine. Do not generate
warnings from invented thresholds or generic assumptions.

### 4.12 `dose_conversion_factors`

Stores scientifically referenced conversion factors for estimated
effective dose calculations.

  Column               Suggested type   Notes
  -------------------- ---------------- ------------------------------
  `factor_id`          BIGINT           PK, auto-increment
  `body_region`        VARCHAR(100)     Required
  `patient_category`   VARCHAR(30)      Nullable
  `factor_value`       DECIMAL(12,8)    Required
  `factor_unit`        VARCHAR(50)      Required
  `reference_name`     VARCHAR(255)     Required
  `reference_year`     SMALLINT         Optional
  `effective_from`     DATE             Optional
  `effective_to`       DATE             Optional
  `is_active`          BOOLEAN          Default false until reviewed
  `created_at`         DATETIME         Required

Only reviewed factors should be active. Document source, applicability,
body region, patient category, and units. Do not insert assumed or
unverified values.

### 4.13 `dose_calculation_runs`

Tracks each calculation execution.

  Column                 Suggested type   Notes
  ---------------------- ---------------- ------------------------
  `calculation_run_id`   BIGINT           PK, auto-increment
  `examination_id`       BIGINT           FK to `examinations`
  `algorithm_version`    VARCHAR(50)      Required
  `calculation_status`   VARCHAR(30)      Required
  `calculated_at`        DATETIME         Required
  `calculated_by`        BIGINT           Nullable FK to `users`

This preserves the history of recalculations and algorithm versions.

### 4.14 `dose_calculation_results`

Stores derived results and provenance.

  Column                 Suggested type   Notes
  ---------------------- ---------------- ------------------------------------------
  `result_id`            BIGINT           PK, auto-increment
  `calculation_run_id`   BIGINT           FK to `dose_calculation_runs`
  `event_id`             BIGINT           Nullable FK to `irradiation_events`
  `metric_code`          VARCHAR(50)      Required calculated metric identifier
  `result_value`         DECIMAL(14,6)    Nullable
  `result_unit`          VARCHAR(30)      Required
  `input_value`          DECIMAL(14,6)    Nullable
  `input_unit`           VARCHAR(30)      Nullable
  `factor_id`            BIGINT           Nullable FK to `dose_conversion_factors`
  `calculation_method`   VARCHAR(255)     Optional
  `calculation_note`     TEXT             Optional
  `created_at`           DATETIME         Required

A possible estimate is:

`Estimated Effective Dose ≈ DLP × region-specific k-factor`

Only calculate when DLP, its unit, the applicable body region, and a
validated conversion factor are available. Label the result as an
**estimate**. It is not a direct measurement of individual absorbed dose
or a precise estimate of individual risk.

Preserve calculation history rather than silently overwriting old
results.

### 4.15 `drl_references`

Stores Diagnostic Reference Level reference values.

  -----------------------------------------------------------------------
  Column                  Suggested type          Notes
  ----------------------- ----------------------- -----------------------
  `drl_id`                BIGINT                  PK, auto-increment

  `institution_id`        BIGINT                  Nullable FK to
                                                  `institutions`; for
                                                  local DRLs

  `reference_name`        VARCHAR(255)            Required

  `reference_type`        VARCHAR(30)             `NATIONAL`, `LOCAL`,
                                                  `INTERNATIONAL`, as
                                                  applicable

  `modality`              VARCHAR(20)             CT for this project
                                                  scope

  `body_region`           VARCHAR(100)            Required

  `protocol_category`     VARCHAR(150)            Optional

  `patient_category`      VARCHAR(30)             Optional

  `metric_code`           VARCHAR(30)             e.g. `CTDIvol`, `DLP`

  `reference_value`       DECIMAL(14,5)           Required

  `unit`                  VARCHAR(30)             Required

  `source_citation`       TEXT                    Required

  `effective_from`        DATE                    Optional

  `effective_to`          DATE                    Optional

  `created_at`            DATETIME                Required
  -----------------------------------------------------------------------

Scientific rules: - DRLs are optimization tools for representative
groups, not individual patient dose limits. - Compare appropriate groups
of similar examinations, protocols, and patient categories. - CTDIvol
and DLP are the relevant common CT dose indices for this comparison. -
Estimated effective dose is not a standard CT DRL comparison metric. -
Every reference value must have a traceable source and documented
applicability.

### 4.16 `report_configurations`

Stores saved report settings.

  Column                 Suggested type   Notes
  ---------------------- ---------------- --------------------
  `configuration_id`     BIGINT           PK, auto-increment
  `user_id`              BIGINT           FK to `users`
  `configuration_name`   VARCHAR(150)     Required
  `report_type`          VARCHAR(50)      Required
  `date_from`            DATE             Nullable
  `date_to`              DATE             Nullable
  `filters_json`         JSON             Nullable
  `selected_sections`    JSON             Nullable
  `created_at`           DATETIME         Required
  `updated_at`           DATETIME         Optional

JSON is proposed for flexible report filters and selected sections.
Validate its structure in the backend; do not put core relational data
into JSON.

### 4.17 `generated_reports`

Tracks reports actually generated and saved.

  Column                Suggested type   Notes
  --------------------- ---------------- ----------------------------------------
  `report_id`           BIGINT           PK, auto-increment
  `configuration_id`    BIGINT           Nullable FK to `report_configurations`
  `generated_by`        BIGINT           FK to `users`
  `report_title`        VARCHAR(200)     Required
  `report_format`       VARCHAR(20)      e.g. PDF
  `storage_path`        VARCHAR(500)     Secure report storage reference
  `generation_status`   VARCHAR(30)      Required
  `generated_at`        DATETIME         Required

A temporary preview does not necessarily need a database record. Persist
a report when the user saves or generates it, according to the final UI
behavior.

------------------------------------------------------------------------

## 5. Relationships and ER Diagram

Core relationships: - One institution has many users, patients,
scanners, and protocols. - One patient can have many examinations. - One
scanner can be associated with many examinations. - One protocol can
classify many examinations. - One examination can have multiple RDSR
documents. - One RDSR document can have multiple processing log entries
and irradiation events. - One examination contains multiple irradiation
events. - One irradiation event may have acquisition parameters and
documented notification events. - One examination may have multiple
calculation runs. - One calculation run may produce multiple calculation
results. - A calculation result may reference a conversion factor. - DRL
references may be institution-specific or external. - A user can save
multiple report configurations and generate multiple reports.

``` mermaid
erDiagram
    INSTITUTIONS ||--o{ USERS : has
    INSTITUTIONS ||--o{ PATIENTS : registers
    INSTITUTIONS ||--o{ SCANNERS : owns
    INSTITUTIONS ||--o{ PROTOCOLS : defines
    PATIENTS ||--o{ EXAMINATIONS : undergoes
    SCANNERS ||--o{ EXAMINATIONS : performs
    PROTOCOLS ||--o{ EXAMINATIONS : classifies
    EXAMINATIONS ||--o{ RDSR_DOCUMENTS : has
    USERS ||--o{ RDSR_DOCUMENTS : uploads
    RDSR_DOCUMENTS ||--o{ PROCESSING_LOGS : records
    EXAMINATIONS ||--o{ IRRADIATION_EVENTS : contains
    RDSR_DOCUMENTS ||--o{ IRRADIATION_EVENTS : source
    IRRADIATION_EVENTS ||--o| ACQUISITION_PARAMETERS : has
    IRRADIATION_EVENTS ||--o{ RDSR_NOTIFICATION_EVENTS : reports
    EXAMINATIONS ||--o{ DOSE_CALCULATION_RUNS : calculates
    DOSE_CALCULATION_RUNS ||--o{ DOSE_CALCULATION_RESULTS : produces
    DOSE_CONVERSION_FACTORS ||--o{ DOSE_CALCULATION_RESULTS : used_in
    INSTITUTIONS ||--o{ DRL_REFERENCES : defines
    USERS ||--o{ REPORT_CONFIGURATIONS : creates
    REPORT_CONFIGURATIONS ||--o{ GENERATED_REPORTS : generates
```

The diagram represents proposed logical relationships. Confirm optional
foreign keys and exact cardinalities during implementation, especially
scanner matching, protocol classification, acquisition parameter
cardinality, and RDSR document-to-examination matching.

------------------------------------------------------------------------

## 6. Mapping the Ten Figma Pages to Database Sources

This is a screen-level mapping. Exact fields, filters, and chart
definitions must be verified against the current Figma frames.

  ----------------------------------------------------------------------------
  Figma page              Main database sources        Expected access pattern
  ----------------------- ---------------------------- -----------------------
  Login                   `users`, `institutions`      Account lookup and
                                                       authentication

  Medical Physicist       `examinations`, `patients`,  Aggregate queries and
  Dashboard               `irradiation_events`,        summary cards
                          `rdsr_documents`             

  Upload RDSR             `rdsr_documents`,            Create upload record;
                          `processing_logs`            update processing state

  Patients                `patients`, `institutions`,  Search, filters,
                          `examinations`               pagination

  Patient Profile         `patients`, `examinations`,  Patient details and
                          `rdsr_documents`             examination history

  Examination Details     `examinations`,              Examination and event
                          `irradiation_events`,        details
                          `acquisition_parameters`,    
                          `rdsr_notification_events`   

  Dose Analytics          `irradiation_events`,        Aggregations, filters,
                          `examinations`, `protocols`  charts

  DRL Comparison          `irradiation_events`,        Grouped comparison
                          `protocols`,                 queries
                          `drl_references`             

  Reports Configuration   `report_configurations` plus Save filters and
                          relevant examination data    selected sections

  Report Preview / Review Examination, event,          Compose preview;
                          calculation, DRL, and        persist report when
                          configuration data           requested
  ----------------------------------------------------------------------------

### 6.1 Stored versus derived versus calculated values

Not every displayed item needs a stored column.

Normally derived through queries: - Patient examination count. - Latest
examination date. - Dashboard totals. - Average/median CTDIvol or DLP
for a defined group. - Charts and grouped analytics. - Examination
summaries calculated from multiple events.

Source values that may be stored: - DICOM identifiers and metadata. -
Event-level CTDIvol and DLP. - Available acquisition parameters. -
Documented notification events. - Scanner and protocol source
information.

Calculated values requiring provenance: - Estimated effective dose. -
Normalized or converted metrics. - Saved calculation results used in a
report.

Every Figma field should be classified as: 1. Source field extracted
from DICOM/RDSR. 2. Relational application field. 3. Derived query
result. 4. Calculated result with method and provenance. 5. UI-only
label or static content. 6. Unavailable/not supported by current source
data.

------------------------------------------------------------------------

## 7. Scientific and Data-Quality Rules

These rules apply to the database, parser, backend, and frontend.

### 7.1 Missing values

-   Use SQL `NULL` when a value is missing, absent, unsupported, or not
    reliably extractable.
-   Never replace missing CTDIvol or DLP with zero.
-   Never invent scan length, kVp, mAs, phantom type, or other
    acquisition values.
-   Render unavailable values in the UI as **Not Available**.
-   Keep missing values distinguishable from a measured zero where zero
    is valid in the source context.

### 7.2 Dose metrics and units

Recommended normalized units:

  Metric                     Normalized unit
  -------------------------- ------------------------------------------------
  CTDIvol                    mGy
  DLP                        mGy·cm
  kVp                        kV
  Tube current               mA
  Scan length                mm
  Exposure time              ms or seconds, consistently defined per column
  Estimated effective dose   mSv

The parser must validate source units and convert only through
documented and tested rules. If the source unit or conversion cannot be
interpreted reliably, do not store a misleading normalized value.

### 7.3 CTDIvol and DLP

-   CTDIvol is a standardized scanner output index, not a direct
    patient-specific absorbed dose.
-   DLP is related to CTDIvol and scan length for a relevant
    acquisition, but RDSR may report event-level dose information
    directly.
-   Do not assume one dose value per examination.
-   Do not sum or average event values without a documented rule that
    accounts for event meaning and avoids double counting.
-   Preserve event-level source values even when examination summaries
    are calculated.

### 7.4 Estimated effective dose

A possible estimate is:

`Estimated Effective Dose ≈ DLP × region-specific k-factor`

Calculate only when DLP is present and valid, units are known, body
region is appropriately classified, and the conversion factor is
validated and applicable. Label the result as an estimate. It is not a
direct measurement of organ dose, patient-specific absorbed dose, or
individual risk.

### 7.5 DRL interpretation

-   DRLs are optimization tools for representative groups, not
    individual patient limits.
-   Compare appropriate groups of similar examinations and protocols.
-   Use CTDIvol and DLP as the applicable common CT metrics.
-   Do not use estimated effective dose as a standard DRL metric.
-   Store the source and applicability of every reference value.

### 7.6 Notification events

Warnings must be supported by documented RDSR notification events.
Preserve source details and units when available. Do not create generic
threshold alerts unless a separate approved requirement is introduced.

------------------------------------------------------------------------

## 8. MySQL Standards and Implementation Conventions

  -----------------------------------------------------------------------
  Setting                             Recommendation
  ----------------------------------- -----------------------------------
  Database                            MySQL 8.0+

  Storage engine                      InnoDB

  Character set                       `utf8mb4`

  Collation                           `utf8mb4_unicode_ci` or a
                                      compatible selected collation

  Primary keys                        `BIGINT AUTO_INCREMENT`

  Foreign keys                        Enforced

  Scientific numeric values           `DECIMAL`, not `FLOAT` for
                                      persisted measurements

  Missing values                      `NULL`

  Date only                           `DATE`

  Time only                           `TIME`

  Timestamp                           `DATETIME` with an agreed timezone
                                      policy

  JSON                                Flexible report configuration only,
                                      not core relational entities
  -----------------------------------------------------------------------

### 8.1 Naming conventions

-   Table and column names use lowercase `snake_case`.
-   Primary key: `<entity>_id`.
-   Foreign key: same name as the referenced primary key.
-   Boolean columns use names such as `is_active`.
-   Timestamps use names such as `created_at`, `updated_at`, and
    `processed_at`.

Agree on conventions before creating the first migration.

### 8.2 Decimal precision

The suggested `DECIMAL` precision values are starting points. Review
them against real RDSR ranges and required precision before finalizing
the schema. Do not round during parsing merely to match UI display
precision.

### 8.3 Candidate indexes and constraints

  ------------------------------------------------------------------------------------
  Table                               Candidate indexes / constraints
  ----------------------------------- ------------------------------------------------
  `institutions`                      Unique `institution_code`

  `users`                             Unique `email`; index `institution_id`

  `patients`                          Index `institution_id`; candidate unique
                                      `(institution_id, dicom_patient_id)` when
                                      appropriate

  `scanners`                          Index `institution_id`; review serial-number
                                      uniqueness

  `protocols`                         Index `(institution_id, protocol_name)`

  `examinations`                      Index `patient_id`, `study_date`,
                                      `study_instance_uid`, `accession_number`

  `rdsr_documents`                    Index `examination_id`, `uploaded_at`; define
                                      hash duplicate policy

  `processing_logs`                   Index `(rdsr_document_id, created_at)`

  `irradiation_events`                Index `examination_id`, `rdsr_document_id`

  `acquisition_parameters`            Unique/index `event_id` only if one-to-one is
                                      confirmed

  `rdsr_notification_events`          Index `examination_id`, `event_id`

  `dose_calculation_runs`             Index `(examination_id, calculated_at)`

  `dose_calculation_results`          Index `calculation_run_id`, `event_id`,
                                      `metric_code`

  `drl_references`                    Index
                                      `(body_region, patient_category, metric_code)`

  `generated_reports`                 Index `(generated_by, generated_at)`
  ------------------------------------------------------------------------------------

Indexes must be confirmed against real query patterns. Avoid indexing
every column without a use case.

### 8.4 Foreign-key deletion policy

-   Avoid cascading deletion of clinical/source data by default.
-   Do not let deletion of a patient or examination silently orphan or
    erase dose records.
-   Prefer controlled archival/deactivation where appropriate.
-   Define retention and deletion policies before handling identifiable
    real data.

### 8.5 Transactions

Use transactions for parser persistence: 1. Validate the document. 2.
Create or match the examination. 3. Persist irradiation events and
acquisition parameters. 4. Persist documented notification events. 5.
Update processing status.

If persistence fails, avoid leaving a partially written event set.
Record the failure safely in processing logs.

------------------------------------------------------------------------

## 9. Demo, Test, and Real Data Environments

Recommended separation:

  -----------------------------------------------------------------------
  Environment             Suggested database      Purpose
  ----------------------- ----------------------- -----------------------
  Development / UI demo   `radsafe_dev`           Seven temporary demo
                                                  cases and UI
                                                  development

  Parser testing          `radsafe_test`          Authorized, anonymized,
                                                  or synthetic RDSR
                                                  samples

  Future production       `radsafe`               Operational data only
                                                  after security and
                                                  validation review
  -----------------------------------------------------------------------

Do not mix demo fixtures and real clinical data in the same working
dataset.

### 9.1 Demo seed data

The seven demo cases are temporary UI fixtures. Keep IDs and
institution/user associations consistent across screens. CTDIvol and DLP
remain unavailable. Do not use fabricated measurements.

### 9.2 Real RDSR test data

When samples become available: 1. Confirm authorization to use them. 2.
Check for direct and indirect identifiers. 3. De-identify them or use an
approved secure environment. 4. Keep source files out of public GitHub.
5. Record sample provenance and permitted use. 6. Validate parser
support and units. 7. Independently review extracted values. 8. Add
parser tests for supported and unsupported structures.

------------------------------------------------------------------------

## 10. Backend and Parser Data Flow

1.  User selects an RDSR file on Upload RDSR.
2.  Backend checks size, content, and safe upload constraints.
3.  Backend stores the file in secure file storage.
4.  Backend calculates a file hash and creates an `rdsr_documents`
    record.
5.  Validation checks whether the file is a supported DICOM object
    containing expected RDSR content.
6.  dcm4che parses supported content.
7.  Parser extracts document identifiers, study metadata, event data,
    dose metrics, acquisition parameters, and documented notification
    events when available.
8.  Backend maps extracted content to normalized entities.
9.  Backend matches or creates patient/examination/scanner/protocol
    records using approved matching rules.
10. Backend persists event-level data transactionally.
11. Backend updates processing and validation statuses.
12. Dashboard, Patient Profile, Examination Details, Analytics, and
    Reports retrieve data through APIs and queries.

### 10.1 Parser feasibility gate

Before implementing the full parser: - Obtain representative authorized
RDSR samples. - Confirm that files are genuine RDSR objects and readable
by the selected library. - Inspect manufacturer and software
variation. - Identify required fields and their actual encoding. - Test
extraction of multiple irradiation events. - Test missing and optional
attributes. - Verify units and coded concepts. - Record unsupported
content rather than silently discarding it.

Do not assume the library supports every desired field from every vendor
without testing.

------------------------------------------------------------------------

## 11. Implementation Roadmap

### Phase 1 --- Database foundation

Create: - `institutions` - `users` - `patients` - `scanners` -
`protocols` - `examinations`

Tasks: - Install and configure MySQL. - Create the development
database. - Define entity relationships and foreign keys. - Add initial
indexes and constraints. - Insert seven demo cases without fabricated
dose values. - Verify Patients and Patient Profile queries.

### Phase 2 --- RDSR upload tracking

Create: - `rdsr_documents` - `processing_logs`

Tasks: - Implement upload API. - Store files outside MySQL. - Calculate
and persist file hash. - Track validation and processing status. -
Handle duplicate upload attempts. - Test invalid files and upload
failures.

### Phase 3 --- Parser and event persistence

Create: - `irradiation_events` - `acquisition_parameters` -
`rdsr_notification_events`

Tasks: - Validate dcm4che against real samples. - Define DICOM/RDSR
extraction mapping. - Preserve source identifiers and units. - Persist
multiple irradiation events per examination. - Represent missing values
with `NULL`. - Test repeat processing and duplicate prevention. -
Confirm notification behavior against source events.

### Phase 4 --- Dose calculation

Create: - `dose_conversion_factors` - `dose_calculation_runs` -
`dose_calculation_results`

Tasks: - Collect and review scientific references. - Approve factors and
applicability. - Implement versioned calculation methods. - Preserve
calculation provenance. - Compare system results with independent manual
calculations. - Label estimates correctly.

### Phase 5 --- DRL comparison

Create: - `drl_references`

Tasks: - Select authoritative references. - Record source, unit,
population, body region, and protocol applicability. - Define group
classification rules. - Implement grouped CTDIvol/DLP comparisons. -
Ensure results are not presented as individual patient limits.

### Phase 6 --- Reports

Create: - `report_configurations` - `generated_reports`

Tasks: - Save report preferences. - Build preview queries. - Generate
reports. - Store generated files securely. - Record generation status
and user.

### Phase 7 --- Integration and validation

Tasks: - Verify all ten Figma pages against implemented APIs. - Test
database relationships and constraints. - Test missing, malformed, and
unsupported RDSR fields. - Test multi-event examinations. - Test parser
reprocessing and duplicate handling. - Validate units and scientific
calculations. - Review privacy, access control, and file storage. -
Document limitations and supported input formats.

------------------------------------------------------------------------

## 12. Suggested Team Responsibilities

### Member 1 --- Backend, database, testing, coordination

-   Own MySQL schema and migrations.
-   Implement Spring Boot entities and repositories.
-   Implement upload and parser integration.
-   Implement APIs for the frontend.
-   Coordinate integration and testing.
-   Maintain this plan and record approved changes.

### Member 2 --- UI/UX and frontend

-   Maintain the approved Figma screens.
-   Implement HTML/CSS/JavaScript pages.
-   Integrate frontend with backend APIs.
-   Display unavailable values as `Not Available`.
-   Verify UI fields and filters against API responses.
-   Report missing or inconsistent backend data requirements.

### Member 3 --- Medical Physics and research

-   Review scientific meaning of CTDIvol, DLP, and RDSR fields.
-   Help identify representative authorized RDSR samples.
-   Prepare a data dictionary for relevant RDSR elements.
-   Review units and source interpretation.
-   Research and document conversion-factor references.
-   Research DRL references and population groupings.
-   Prepare manual calculation examples and scientific test cases.
-   Review scientific wording in dashboards and reports.

Scientific assumptions and references must be documented and reviewed
before implementation.

------------------------------------------------------------------------

## 13. Testing Strategy

### Database tests

-   Primary key and foreign key constraints.
-   Unique constraints and duplicate handling.
-   Nullable field behavior.
-   Referential integrity.
-   Transaction rollback.
-   Query performance for patient and examination lists.
-   Institution-level data separation where applicable.

### Upload and parser tests

-   Valid RDSR file.
-   Non-DICOM file.
-   DICOM file that is not an RDSR.
-   Unsupported or malformed RDSR.
-   Repeated upload of the same file.
-   File with multiple irradiation events.
-   File with missing CTDIvol or DLP.
-   File with missing acquisition parameters.
-   File containing documented notification events.
-   Different manufacturers/software versions when samples are
    available.

### Scientific validation tests

-   CTDIvol and DLP extraction and units.
-   Event-level versus examination-level aggregation.
-   Scan length interpretation.
-   Conversion-factor applicability.
-   Estimated effective dose calculation.
-   DRL group classification and comparison.
-   Missing or invalid source values.
-   Agreement with independent manual calculations.

### UI integration tests

-   Login and role access.
-   Dashboard summaries.
-   Patient search and profile.
-   Examination Details sections.
-   `Not Available` behavior.
-   Dose Analytics filters and charts.
-   DRL Comparison grouping.
-   Report configuration and preview.
-   Report generation and access permissions.

------------------------------------------------------------------------

## 14. Security, Privacy, and Data Governance

RDSR and associated DICOM metadata may contain direct or indirect
patient identifiers. A file may contain sensitive information even if
the patient name is absent.

Minimum safeguards: - Use synthetic, public, or properly anonymized
files for development. - Never commit identifiable DICOM/RDSR files to a
public repository. - Keep secrets, database passwords, and storage
credentials out of Git. - Hash passwords securely; never store
plain-text passwords. - Restrict access to uploaded files and generated
reports. - Validate upload content and impose file-size limits. - Avoid
logging patient names, IDs, or full DICOM content. - Define backup,
retention, deletion, and access policies before production. - Apply
institution-level authorization checks to relevant API queries. - Use
HTTPS and secure configuration in deployed environments.

RADSAFE is an educational/research prototype unless and until it
undergoes required clinical, regulatory, security, and institutional
validation. It must not be presented as clinically approved or used to
make patient-care decisions.

------------------------------------------------------------------------

## 15. Suggested GitHub Repository Structure

``` text
RADSAFE/
├── backend/
├── frontend/
├── database/
│   ├── README.md
│   ├── database-plan.md
│   ├── migrations/
│   ├── seed/
│   │   └── demo-data.sql
│   └── diagrams/
│       └── database-erd.md
├── docs/
│   ├── project-scope.md
│   ├── user-flow.md
│   ├── ui-ux-guidelines.md
│   ├── medical-physics-research-plan.md
│   ├── api-contract.md
│   └── testing-plan.md
├── .gitignore
└── README.md
```

Recommended location for this document:

`database/database-plan.md`

Related files: - `database/migrations/`: version-controlled schema
changes. - `database/seed/demo-data.sql`: demo fixtures only. -
`database/diagrams/database-erd.md`: ERD source. - `database/README.md`:
local database setup instructions.

Never commit production data, real RDSR files, passwords, `.env` files,
or generated patient reports.

------------------------------------------------------------------------

## 16. Open Decisions Before Final SQL

  ---------------------------------------------------------------------------
  Decision                Why it matters              Required confirmation
  ----------------------- --------------------------- -----------------------
  Exact Figma fields on   Avoid missing or            Review all current
  every page              unnecessary columns         frames

  RDSR sample structure   Determines parser mapping   Inspect representative
                          and field support           real samples

  Patient matching        Avoid duplicate or          Agree
  strategy                incorrectly linked patients institution-specific
                                                      matching rules

  Examination matching    Study UID/accession         Review real sample
  strategy                behavior can vary           identifiers

  Scanner matching        Manufacturer/model/serial   Define stable matching
  strategy                data may vary               attributes

  Protocol normalization  Needed for meaningful       Medical Physics review
                          analytics and DRL grouping  

  Acquisition parameter   One event may require       Validate RDSR structure
  cardinality             multiple parameter groups   

  Event identity and      Prevent duplicate records   Test real samples
  duplicate handling      on reprocessing             

  Dose aggregation rules  Prevent incorrect           Medical Physics
                          examination summaries       approval

  Conversion-factor       Needed for estimated        Approve source and
  references              effective dose              applicability

  DRL reference selection Depends on region,          Research authoritative
                          protocol, and population    sources

  Report persistence      Determines when report      Confirm Report Preview
  behavior                rows/files are created      workflow

  Authentication and role Determines whether simple   Confirm access model
  requirements            roles are sufficient        

  Data retention and      Required before             Team/institution review
  privacy                 identifiable real data      
  ---------------------------------------------------------------------------

------------------------------------------------------------------------

## 17. Change Management

This document is a shared baseline and may change as the project
evolves.

For every proposed database change: 1. Identify the Figma page, API,
parser requirement, or scientific requirement motivating it. 2. Explain
whether it affects a source field, derived value, calculation, or
UI-only element. 3. Review it with the relevant team member. 4. Update
the schema and ERD together. 5. Add a migration rather than editing an
already-applied migration. 6. Update parser mapping and tests if
applicable. 7. Record the change in the revision history.

### Revision history

  -----------------------------------------------------------------------
  Version                 Date                    Changes
  ----------------------- ----------------------- -----------------------
  1.0                     23 Nov 2026             Initial proposed
                                                  blueprint aligned with
                                                  the current ten-page
                                                  Figma scope and agreed
                                                  RDSR/data-quality
                                                  constraints

  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 18. Final Recommendation and Next Steps

Use this document as the **proposed database blueprint**. Do not
implement every table without review.

Recommended next steps: 1. Review the ten current Figma pages and map
every displayed field. 2. Obtain representative authorized RDSR samples.
3. Validate dcm4che parsing feasibility and actual source attributes. 4.
Confirm entity relationships, optional fields, uniqueness rules, and
event cardinality. 5. Approve the final ERD with the full team. 6.
Create MySQL migrations in implementation phases. 7. Seed only the seven
clearly marked demo cases in the development environment. 8. Test real
RDSR processing separately using approved test data.

The database must preserve source data accurately, make derived results
traceable, and never turn missing information into fabricated
measurements.
