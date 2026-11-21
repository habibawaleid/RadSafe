# RADSAFE — Software Requirements Specification (SRS) & Project Development Plan

| Attribute | Details |
| :--- | :--- |
| **Project Name** | RADSAFE |
| **Project Full Name** | Intelligent CT Radiation Dose Monitoring and Analysis System |
| **Document Type** | Software Requirements Specification + UX/UI Requirements + Technical Design + Development Roadmap |
| **Version** | 1.0 - Initial Draft |
| **Project Duration** | 8 Weeks |
| **Development Team** | 2 Members: Backend Developer & Frontend Developer |

---

## 1. Project Overview

### 1.1 Project Description
**RADSAFE** is a web-based software system designed to process, monitor, analyze, and visualize radiation dose information obtained from **CT Radiation Dose Structured Reports (RDSR)**.

The system allows users to upload DICOM RDSR files, extract relevant radiation dose parameters, calculate estimated effective dose, store examination records, analyze dose information, compare examination groups against applicable Diagnostic Reference Levels (DRLs), and generate reports. The system aims to transform radiation dose information into structured, accessible, and useful data through an integrated web application.

### 1.2 Problem Statement
Radiation dose information generated during CT examinations is commonly stored in structured reports. Reviewing and analyzing these reports manually can be time-consuming and may make it difficult to monitor trends across multiple examinations and protocols.

RADSAFE aims to provide a centralized software solution that helps users:
* Process CT Radiation Dose Structured Reports.
* Extract and organize radiation dose parameters.
* Calculate estimated effective dose.
* Maintain anonymized examination records.
* Analyze dose distributions across examination protocols.
* Compare appropriate group-level dose metrics with applicable DRLs.
* Visualize information through dashboards and charts.
* Generate structured reports.

### 1.3 Project Objectives
1. Develop a web-based CT radiation dose analysis application.
2. Support uploading and processing of compatible DICOM RDSR files.
3. Extract $\text{CTDI}_{\text{vol}}$ and $\text{DLP}$ values.
4. Calculate estimated Effective Dose using documented conversion factors.
5. Store examination data in a structured database.
6. Provide examination history and search functionality.
7. Provide protocol-based dose analysis.
8. Support appropriate group-level DRL comparison.
9. Provide a dashboard for visualizing dose information.
10. Generate printable analysis reports.
11. Maintain data quality, traceability, and basic security.

### 1.4 Target Users
* **Primary User:** Medical physicist or authorized radiology staff member responsible for reviewing CT radiation dose information.
* **Secondary User:** Project administrator or researcher using the application for demonstration, analysis, and evaluation.

> **Note:** The initial MVP will not implement a complex multi-role permission system.

---

## 2. Project Scope

### 2.1 In Scope (MVP)
The initial release will include:
1. Main Dashboard.
2. RDSR File Upload.
3. File Validation.
4. RDSR Data Extraction.
5. Dose Calculation.
6. Examination Records.
7. Examination Details.
8. Examination Search.
9. Protocol-Based Analysis.
10. Group-Level DRL Comparison.
11. Basic Review Indicators.
12. Printable Reports.
13. Basic Error Handling.
14. Automated and Manual Testing.

### 2.2 Out of Scope (Initial Version)
The following features are excluded from the first release and may be considered for future versions:
* PACS integration.
* Direct connection to CT scanners.
* AI-based diagnosis or prediction.
* Advanced machine learning.
* Automatic clinical decision-making.
* Advanced patient-specific organ dose calculation.
* SSDE implementation unless time and validated requirements permit.
* Complex authentication and role management.
* Multi-hospital deployment.
* Cloud deployment and enterprise infrastructure.
* Real-time scanner monitoring.

### 2.3 MVP Priorities

* **Priority 1 — Core Functionality:**
  * Upload RDSR.
  * Validate files.
  * Extract $\text{CTDI}_{\text{vol}}$ and $\text{DLP}$.
  * Calculate estimated Effective Dose.
  * Save examination information.
  * Display analysis results.
* **Priority 2 — Analysis:**
  * Examination History.
  * Search and Filtering.
  * Protocol Analysis.
  * Group-Level DRL Comparison.
* **Priority 3 — Presentation:**
  * Dashboard.
  * Charts.
  * Reports.
  * UI Improvements.

> **Constraint:** The scientific core must take priority over advanced visual features if the development timeline becomes limited.

---

## 3. Functional Requirements

### FR-01: Main Dashboard
The system shall provide a main dashboard displaying an overview of the available examination data.
* **Features:**
  * Display total number of processed examinations.
  * Display available examination statistics.
  * Display average $\text{CTDI}_{\text{vol}}$ and average $\text{DLP}$.
  * Display estimated Effective Dose statistics.
  * Display examination distribution by protocol.
  * Display recent examinations and group-level review summaries.
  * Provide navigation to other application modules.
* **Dashboard Components:** Summary Cards, Dose Distribution Charts, Protocol Distribution Chart, Recent Examinations Table, Group Review Summary.
* **Acceptance Criteria:**
  * Dashboard loads successfully and statistics are retrieved from stored examination records.
  * Empty database states are handled correctly.
  * Charts display meaningful data and displayed statistics match backend calculations.

### FR-02: RDSR File Upload
The system shall allow users to upload compatible CT DICOM RDSR files.
* **Features:** File selection, file upload, upload progress or processing state, file type & size validation, processing status, and clear success/error messages.
* **Expected Workflow:**
  1. User opens Upload & Analysis.
  2. User selects an RDSR file.
  3. Frontend sends the file to the Backend.
  4. Backend validates the uploaded file.
  5. Backend processes the DICOM Structured Report.
  6. Backend extracts relevant data.
  7. Backend performs required calculations.
  8. Backend saves the examination record.
  9. Frontend displays the result.
* **Acceptance Criteria:**
  * Valid supported RDSR files can be uploaded; unsupported/invalid files are rejected.
  * Incomplete or invalid examination records are not saved.
  * The user receives a clear processing result, and processing errors do not crash the application.

### FR-03: File Validation
The system shall validate uploaded files before processing.
* **Validation Requirements:**
  * Check whether the uploaded file can be read and is a valid DICOM file.
  * Identify whether the content represents a supported Radiation Dose Structured Report.
  * Verify required dose information is available and handle missing or malformed data.
  * Avoid accepting ordinary CT images as RDSR files.
* **Error Cases:** Empty file, unsupported file format, invalid DICOM file, non-RDSR DICOM file, unsupported RDSR structure, missing $\text{CTDI}_{\text{vol}}$, missing $\text{DLP}$, invalid numerical values, incomplete report data.

### FR-04: RDSR Data Extraction
The system shall extract relevant information from compatible RDSR files.

| Field | Description |
| :--- | :--- |
| **Patient ID** | Anonymized or pseudonymized identifier |
| **Study Date** | Examination date |
| **Study Instance UID** | DICOM study identifier, subject to privacy policy |
| **Modality** | Examination modality (`CT`) |
| **Protocol Name** | CT examination protocol |
| **Body Region** | Available anatomical region |
| **$\text{CTDI}_{\text{vol}}$** | CT dose index volume |
| **$\text{DLP}$** | Dose Length Product |
| **Scan Length** | When available or derivable |
| **Number of Irradiation Events** | When available |
| **Scanner Information** | When available |
| **Report Information** | Available RDSR metadata |

> **Data Integrity Rule:** Not all fields are guaranteed to exist in every RDSR file. The system must distinguish between *Available values*, *Missing values*, *Unsupported values*, and *Derived values*. The Backend must not silently invent missing data.

### FR-05: Dose Calculation
The system shall calculate estimated Effective Dose when sufficient validated input data is available.
* **Required Formulas:**
  $$\text{DLP} = \text{CTDI}_{\text{vol}} \times \text{Scan Length}$$
  $$\text{Effective Dose } (\text{mSv}) = \text{DLP } (\text{mGy}\cdot\text{cm}) \times k\text{-factor}$$
* **Requirements:**
  * Store $\text{CTDI}_{\text{vol}}$ in $\text{mGy}$, $\text{DLP}$ in $\text{mGy}\cdot\text{cm}$, and Effective Dose in $\text{mSv}$.
  * Use a documented $k$-factor source associated with the relevant examination region or protocol, recording the factor and reference used.
  * Handle missing or unsupported $k$-factors and avoid calculating results when required input data is invalid.
  * Effective Dose must be labeled as an **estimate** and must not be presented as a precise individual patient risk measurement.

### FR-06: Examination Records
The system shall store processed examination records including examination metadata, extracted dose parameters, calculation results, processing status, source file reference or file hash (when appropriate), creation and processing timestamps, and anonymized examination identifiers.

### FR-07: Examination History
The system shall provide a page listing previously processed examinations with a structured table, search by anonymized Patient ID, filtering by examination date, protocol, and body region, record sorting, navigation to examination details, and empty-result handling.

### FR-08: Examination Details
The system shall provide a detailed view of an individual examination displaying examination identifier, anonymized patient identifier, study date, protocol, body region, $\text{CTDI}_{\text{vol}}$, $\text{DLP}$, estimated Effective Dose, calculation factor and reference, available scan information, and processing status.

### FR-09: Protocol-Based Analysis
The system shall group examinations according to their protocol or relevant examination category, displaying examination counts, statistical summaries (average, median, minimum, and maximum dose values where appropriate), date-range filtering, and dose distribution visualizations.
> **Constraint:** Protocol classification must be consistent. Different protocols should not be combined simply because their names look similar.

### FR-10: DRL Comparison and Review
The system shall support comparing appropriate dose indicators across representative examination groups with applicable DRLs.
* **Requirements:** Store documented DRL reference values, identify the applicable examination category, compare appropriate $\text{CTDI}_{\text{vol}}$ and $\text{DLP}$ metrics at the group level, display comparison results, highlight groups requiring review, and display the reference source and applicable category.
* **Scientific Constraints:**
  * DRLs are optimization tools, **not** individual patient safety limits or automatic alarms for individual examinations.
  * Comparisons must use appropriate examination groups and comparable protocols.
  * Effective Dose must not be treated as a standard CT DRL quantity.
  * Group-level statistics and their interpretation must be documented.

### FR-11: Review Indicators
The system shall provide basic indicators for data quality and group-level review (e.g., missing required dose information, invalid values, unsupported examination category, insufficient data for group analysis, group-level DRL review required). The interface must clearly distinguish data-quality errors from group-level dose review findings.

### FR-12: Reports
The system shall provide printable examination and analysis reports (**Individual Examination Report**, **Protocol Analysis Report**, and **Group-Level DRL Review Summary**) containing report title, generation date, examination/group identification, relevant dose parameters, calculation methodology, reference values, tables/charts, and scientific limitations/interpretation notes.
> **Implementation Note:** The MVP may use browser `Print / Save as PDF` instead of a dedicated PDF generation engine.

---

## 4. Non-Functional Requirements

* **NFR-01: Performance:** Normal application pages should load efficiently under the expected dataset; file processing should provide clear status feedback; large files must not freeze the application; database queries should avoid unnecessary full-table scans.
* **NFR-02: Reliability:** Invalid files must not crash the application; failed processing must return understandable errors; database records must not be partially saved; calculations must be reproducible; errors should be logged for debugging.
* **NFR-03: Security and Privacy:** Patient-identifying information (PHI) must be removed or protected before storage; sensitive DICOM metadata must not be exposed through frontend responses; file types and sizes must be restricted; no patient data or sensitive files may be committed to public repositories; anonymized test data must be used for development and demonstration.
* **NFR-04: Usability:** Consistent navigation; dose values must include units; errors must explain what happened and next steps; readable tables and charts; important information must not rely on color alone.
* **NFR-05: Maintainability:** Layered backend architecture; modular frontend components; consistent API naming conventions; automated tests for core calculations; maintained technical documentation.
* **NFR-06: Compatibility:** Compatible with modern desktop browsers and standard desktop screen sizes, with responsive behavior implemented where feasible.
* **NFR-07: Scientific Accuracy:** Documented formulas; traceable $k$-factor references; valid DRL categories and metrics; no invented values for missing data; visible scientific limitations on relevant screens and reports.
* **NFR-08: Data Integrity:** Unique examination identifiers; valid relational constraints; appropriate numerical data types and precision; consistent database updates.

---

## 5. UX/UI Requirements

The Frontend Developer is responsible for both **UX/UI Design** and **Frontend Implementation**. The design process must be completed before implementing the full set of application pages.

### 5.1 UX Research & Primary User Journey
Before designing screens, define primary users, goals, pain points, workflows, key data points, and edge cases.

**Primary User Journey:**
1. Open Dashboard $\rightarrow$ 2. Navigate to Upload $\rightarrow$ 3. Select RDSR file $\rightarrow$ 4. Review processing status $\rightarrow$ 5. View analysis results $\rightarrow$ 6. Open examination details $\rightarrow$ 7. Review protocol statistics $\rightarrow$ 8. View group-level DRL comparison $\rightarrow$ 9. Generate report.

### 5.2 Information Architecture
Recommended navigation structure: `Dashboard` | `Upload & Analysis` | `Examination History` | `Dose Analysis` | `DRL Review` | `Reports` | `Settings (optional in MVP)`.

### 5.3 Required UI Screens

| Screen | Key Components |
| :--- | :--- |
| **Screen 1: Dashboard** | Sidebar Navigation, Header, Summary Cards, Dose Statistics, Protocol Distribution Chart, Recent Examinations Table, Group Review Summary |
| **Screen 2: Upload & Analysis** | Page Title, File Upload Area, Supported File Info, Selected File Details, Upload Button, Processing Indicator, Success/Error Feedback |
| **Screen 3: Analysis Results** | Examination Summary, $\text{CTDI}_{\text{vol}}$ Card, $\text{DLP}$ Card, Estimated Effective Dose Card, Protocol Info, Data Quality Status, View Details Action |
| **Screen 4: Examination History** | Search Input, Date Filters, Protocol Filter, Examinations Table, Pagination/List Navigation, Details Action |
| **Screen 5: Examination Details** | Examination Info, Dose Parameters, Available Scan Details, Calculation Info, Processing Status, Print Report Action |
| **Screen 6: Protocol Analysis** | Protocol Selector, Date Range, Examination Count, $\text{CTDI}_{\text{vol}}$ & $\text{DLP}$ Statistics, Dose Distribution Charts |
| **Screen 7: DRL Review** | Examination Group, Applicable DRL Reference, Group Statistics, Comparison Visualization, Review Status, Reference Source |
| **Screen 8: Reports** | Report Type Selection, Examination/Group Selection, Report Preview, Print / Save as PDF |

### 5.4 Design System & Figma Deliverables
* **Design Direction:** Professional medical software, modern and minimal light theme, clear data hierarchy, consistent spacing, high readability, restrained color palette, avoiding unnecessary visual decoration.
* **Design System Elements:** Color Palette, Typography, Spacing & Grid Systems, Buttons, Input Fields, Dropdowns, Cards, Tables, Charts, Status Indicators, Modal Dialogs, Alerts/Notifications, Sidebar & Navigation.
* **Figma Deliverables (Prior to Full Implementation):**
  1. Low-Fidelity Wireframes.
  2. User Flow.
  3. High-Fidelity MVP Screens.
  4. Design System & Reusable Components.
  5. Clickable Prototype.
  6. Responsive Layout Decisions.
  7. Developer Handoff Notes.

---

## 6. Backend Technical Requirements

### 6.1 Proposed Technology Stack
* **Language & Framework:** Java 21 + Spring Boot (Maven)
* **DICOM Processing:** `dcm4che`
* **Database & Persistence:** SQLite (initially, subject to compatibility testing) + Spring Data JPA
* **Testing & Tools:** JUnit 5, Mockito, Postman, Git & GitHub

### 6.2 Backend Architecture
Layered architecture: `Controller` $\rightarrow$ `Service` $\rightarrow$ `Repository` $\rightarrow$ `Database`

```text
backend/
└── src/main/java/.../
    ├── controller/     # Receive HTTP requests, validate structure, return HTTP responses
    ├── service/        # Business logic, coordinate file processing & workflows
    ├── repository/     # Database operations (CRUD)
    ├── entity/         # JPA database entities
    ├── dto/            # API request/response objects (decoupled from entities)
    ├── exception/      # Global processing & validation error handling
    ├── config/         # Application & CORS configurations
    ├── dicom/          # Read DICOM files, identify RDSR, extract SR content
    └── calculation/    # Calculate Effective Dose, apply k-factors, handle invalid inputs
```

### 6.3 Backend Modules & Responsibilities
* **Modules:** (1) File Upload, (2) File Validation, (3) DICOM/RDSR Processing, (4) Dose Calculation, (5) Examination Management, (6) Protocol Analysis, (7) DRL Comparison, (8) Dashboard Statistics, (9) Report Data.
* **Responsibilities:** Implement REST APIs, define database entities, integrate `dcm4che`, validate uploaded files, extract supported RDSR data, implement calculation formulas, handle errors, write unit/API/integration tests, document API behavior, and coordinate with Frontend during integration.

---

## 7. Frontend Technical Requirements

### 7.1 Technology Stack & Structure
* **Stack:** HTML5, CSS3, Vanilla JavaScript, Chart.js (visualizations), Fetch API (backend communication), Figma (UX/UI). *React is not required for the initial MVP.*

```text
frontend/
├── index.html
├── pages/
├── css/
│   ├── style.css
│   ├── layout.css
│   └── components.css
├── js/
│   ├── app.js
│   ├── api.js
│   ├── dashboard.js
│   ├── upload.js
│   ├── examinations.js
│   ├── analysis.js
│   └── reports.js
└── assets/
```

### 7.2 Frontend States & Responsibilities
Each screen must handle six UI states: **Default**, **Loading**, **Success**, **Empty**, **Error**, and **Invalid Input**. Responsibilities include UX research, Figma prototyping, building the design system, implementing pages and reusable components, integrating APIs, rendering charts/tables/filters, supporting browser printing, and UI testing.

---

## 8. Database Requirements

SQLite is proposed for the initial prototype to reduce setup complexity, with PostgreSQL as a fallback if compatibility or concurrency requirements demand it.

### 8.1 Main Database Entities

#### Entity 1: `Examination`
| Field | Suggested Type | Description |
| :--- | :--- | :--- |
| `id` | `Long` | Primary Key |
| `anonymizedPatientId` | `String` | Anonymized patient identifier |
| `studyDate` | `Date` | Examination date |
| `modality` | `String` | Modality (`CT`) |
| `protocolName` | `String` | Examination protocol |
| `bodyRegion` | `String` | Anatomical region |
| `studyInstanceUid` | `String` | Optional DICOM identifier |
| `processingStatus` | `Enum` | Processing status |
| `createdAt` | `DateTime` | Record creation timestamp |

#### Entity 2: `DoseMetric`
| Field | Suggested Type | Description |
| :--- | :--- | :--- |
| `id` | `Long` | Primary Key |
| `examinationId` | `Long` | Foreign Key $\rightarrow$ `Examination` |
| `ctdiVol` | `Decimal` | $\text{CTDI}_{\text{vol}}$ in $\text{mGy}$ |
| `dlp` | `Decimal` | $\text{DLP}$ in $\text{mGy}\cdot\text{cm}$ |
| `scanLength` | `Decimal` | Scan length when available |
| `effectiveDose` | `Decimal` | Estimated dose in $\text{mSv}$ |
| `kFactor` | `Decimal` | Applied conversion factor |
| `kFactorSource` | `String` | Scientific reference |
| `calculationStatus` | `Enum` | `CALCULATED` / `UNAVAILABLE` |

> **Data Modeling Note:** One examination may have multiple irradiation events or dose measurements. The final data model must preserve this structure rather than assuming every report contains only one $\text{CTDI}_{\text{vol}}$ and one $\text{DLP}$.

#### Entity 3: `Protocol`
| Field | Suggested Type | Description |
| :--- | :--- | :--- |
| `id` | `Long` | Primary Key |
| `protocolName` | `String` | Protocol name |
| `bodyRegion` | `String` | Anatomical region |
| `description` | `String` | Optional description |

#### Entity 4: `DRLReference`
| Field | Suggested Type | Description |
| :--- | :--- | :--- |
| `id` | `Long` | Primary Key |
| `protocolCategory` | `String` | Applicable examination category |
| `bodyRegion` | `String` | Body region |
| `ctdiVolReference` | `Decimal` | $\text{CTDI}_{\text{vol}}$ reference |
| `dlpReference` | `Decimal` | $\text{DLP}$ reference |
| `unit` | `String` | Measurement unit |
| `sourceName` | `String` | Reference source (documented, not invented) |
| `publicationYear` | `Integer` | Publication year |
| `applicablePopulation` | `String` | Applicable population |
| `notes` | `String` | Limitations or notes |

#### Entity 5: `UploadedFile`
| Field | Suggested Type | Description |
| :--- | :--- | :--- |
| `id` | `Long` | Primary Key |
| `examinationId` | `Long` | Foreign Key $\rightarrow$ `Examination` |
| `originalFileName` | `String` | Uploaded filename |
| `fileHash` | `String` | Optional integrity reference |
| `fileSize` | `Long` | File size in bytes |
| `uploadDate` | `DateTime` | Upload timestamp |
| `processingStatus` | `Enum` | Processing status |
| `errorMessage` | `String` | Optional error information |

### 8.2 Relationships & Deliverables
* **Relationships:** `Examination` (1) $\rightarrow$ (N) `DoseMetric`; `Examination` (1) $\rightarrow$ (1..N) `UploadedFile`; `Protocol` (1) $\rightarrow$ (N) `Examination`; `DRLReference` $\rightarrow$ `Protocol Category` (group evaluation).
* **Deliverables:** ER Diagram, Entity Relationship Definitions, PK/FK Constraints, Data Types, Indexing Decisions, Anonymization Strategy, Database Initialization Plan.

---

## 9. API Requirements

Proposed REST API endpoints (to be finalized in `api-contract.md` before integration):

| Method | Endpoint | Purpose |
| :--- | :--- | :--- |
| `POST` | `/api/examinations/upload` | Upload and process RDSR |
| `GET` | `/api/examinations` | Retrieve examination records |
| `GET` | `/api/examinations/{id}` | Retrieve examination details |
| `GET` | `/api/examinations/search` | Search examination records |
| `GET` | `/api/dashboard/summary` | Retrieve dashboard statistics |
| `GET` | `/api/analysis/protocols` | Retrieve protocol analysis |
| `GET` | `/api/analysis/protocols/{id}` | Retrieve selected protocol analysis |
| `GET` | `/api/drl/review` | Retrieve group-level DRL comparison |
| `GET` | `/api/reports/examinations/{id}` | Retrieve examination report data |
| `GET` | `/api/reports/analysis` | Retrieve analysis report data |

### 9.1 Standard API Response Structure

**Success Response Example:**
```json
{
  "success": true,
  "message": "Examination processed successfully",
  "data": {
    "examinationId": 101,
    "protocolName": "CT Chest",
    "ctdiVol": 12.5,
    "dlp": 450.0,
    "effectiveDose": 6.3
  }
}
```

**Error Response Example:**
```json
{
  "success": false,
  "message": "Unsupported DICOM file",
  "errorCode": "UNSUPPORTED_FILE"
}
```

---

## 10. Integration & Testing Requirements

### 10.1 Integration Rules
1. Backend defines endpoints and request/response schemas $\rightarrow$ Frontend reviews $\rightarrow$ Both agree on field names and units.
2. Backend implements endpoint $\rightarrow$ Frontend connects corresponding screen $\rightarrow$ Both test end-to-end.
3. Any API changes must be communicated before implementation changes.

### 10.2 Testing Levels & Key Test Cases
* **Levels:** Unit Testing (JUnit 5), Integration Testing, API Testing (Postman), Frontend UI/State Testing, and End-to-End Workflow Testing.

| Test ID | Scenario | Expected Result |
| :--- | :--- | :--- |
| **TC-01** | Upload valid CT RDSR | File accepted |
| **TC-02** | Upload ordinary CT image | Rejected as non-RDSR |
| **TC-03** | Upload invalid DICOM | Validation error |
| **TC-04** | Upload empty file | Rejected |
| **TC-05** | RDSR missing required value | Incomplete-data status |
| **TC-06** | Calculate DLP | Correct formula result |
| **TC-07** | Calculate Effective Dose | Correct factor and unit |
| **TC-08** | Search by anonymized ID | Matching records displayed |
| **TC-09** | No matching records | Empty state displayed |
| **TC-10** | Retrieve examination details | Correct data displayed |
| **TC-11** | Group protocol analysis | Correct grouping and statistics |
| **TC-12** | Group DRL comparison | Appropriate comparison displayed |
| **TC-13** | Generate report | Correct report data |
| **TC-14** | Backend unavailable | Frontend displays understandable error |

---

## 11. Repository Structure & Branch Strategy

```text
RADSAFE/
├── backend/                # Java 21 Spring Boot project (src/, pom.xml, README.md)
├── frontend/               # Web application (index.html, pages/, css/, js/, assets/)
├── docs/                   # SRS.md, project-scope.md, user-flow.md, ui-ux-guidelines.md,
│                           # api-contract.md, database-design.md, testing-plan.md, technical-decisions.md
├── sample-data/            # Anonymized public test files & README.md (NO PHI)
└── README.md               # Project overview & setup guide
```

* **Branch Strategy:**
  * `main`: Stable, reviewed project version.
  * `backend-dev`: Active backend development branch.
  * `frontend-dev`: Active frontend development branch.
  * `feature/*`: Individual task branches merged via Pull Request after peer review.

---

## 12. Eight-Week Development Roadmap

| Week | Focus & Objective | Backend Developer | Frontend Developer | Key Deliverables |
| :--- | :--- | :--- | :--- | :--- |
| **Week 1** | **Requirements, Planning & Initial Setup** | Review SRS, confirm MVP, set up Java/Spring Boot, create first test API, test DICOM library compatibility | UX research, define user journeys, user flow, low-fidelity wireframes, start Figma design system | Agreed project scope, User Flow, Initial Wireframes, Spring Boot app, GitHub repo, initial technical decisions |
| **Week 2** | **Detailed Design & API Planning** | Define DB entities, ER Diagram, initial REST APIs, file upload foundation, test DICOM reading with samples | Complete core Figma screens, reusable components, colors/typography, clickable prototype, start HTML/CSS | MVP Figma Design, API Contract, ER Diagram, initial upload workflow, RDSR feasibility decision |
| **Week 3** | **Core Processing & Upload Interface** | Implement file validation, RDSR processing foundation, extract $\text{CTDI}_{\text{vol}}$ & $\text{DLP}$, handle incomplete reports, write tests | Implement Dashboard layout, Upload Page, file selection, loading/error states, Results Page layout | Upload interface, backend processing foundation, initial extraction results, validation tests |
| **Week 4** | **Dose Calculation & Results** | Implement calculation service, apply validated $k$-factors, DB persistence, examination details APIs, test calculations | Implement Results Page, connect Upload API, display extracted & calculated data, handle incomplete data | Working upload-to-results workflow, DB persistence, calculation tests, Results interface |
| **Week 5** | **Examination Management** | Implement examination history, search & filtering, protocol classification, protocol analysis APIs | Implement Examination History, Search & Filters, Examination Details, connect history/details APIs | Examination History, Search, Details Page, Initial Protocol Analysis |
| **Week 6** | **Dashboard & DRL Analysis** | Implement group-level statistics, DRL comparisons, review indicators, dashboard statistics APIs | Implement Dashboard charts, connect statistics APIs, Protocol Analysis UI, DRL Review Page | Functional Dashboard, Protocol Analysis, Group-Level DRL Review |
| **Week 7** | **Reports & Testing** | Complete unit & integration tests, test edge cases, fix API/processing bugs, prepare report data APIs | Implement Reports Page, Print / Save as PDF, improve responsiveness, fix UI issues | Printable reports, test results, bug list, updated documentation |
| **Week 8** | **Final Integration & Delivery** | Resolve critical bugs, verify DB consistency & calculation accuracy, finalize API docs | Resolve UI issues, finalize navigation, verify connected pages, prepare demo workflow | Integrated RADSAFE MVP, final documentation, test summary, final demonstration & presentation |

---

## 13. Project Management, Governance & Risks

### 13.1 Team Coordination & Definition of Done
* **Daily Meetings (30–45 min):** (1) Yesterday's completed tasks, (2) Today's planned tasks, (3) Current blockers, (4) Required decisions, (5) Ownership & deadlines.
* **Weekly Review:** Review completed requirements, demo features, review open bugs, update roadmap, confirm next week's scope.
* **Task Tracking Statuses:** `Backlog` $\rightarrow$ `To Do` $\rightarrow$ `In Progress` $\rightarrow$ `In Review` $\rightarrow$ `Testing` $\rightarrow$ `Done` (`Blocked`).
* **Definition of Done (DoD):** Requirements understood $\rightarrow$ Implementation complete $\rightarrow$ Expected behavior verified $\rightarrow$ Errors/edge cases handled $\rightarrow$ API/UI integration works $\rightarrow$ Tests pass $\rightarrow$ Docs updated $\rightarrow$ Peer reviewed $\rightarrow$ Merged to stable branch.

### 13.2 Major Project Risks

| Risk | Impact | Mitigation Strategy |
| :--- | :--- | :--- |
| Lack of real RDSR samples | Medium | Test sample availability during Week 1 |
| Different RDSR structures | High | Support a defined subset and document limitations |
| `dcm4che` integration difficulties | High | Build an early technical proof of concept |
| Incorrect dose calculations | High | Use documented references and independent test cases |
| Unclear DRL categories | High | Define applicable categories before implementation |
| Frontend design delays | High | Approve MVP screens by Week 2 |
| API changes during development | Medium | Maintain a strict shared API Contract |
| Database redesign | Medium | Validate the data model against real samples |
| Limited development time | High | Prioritize core workflow over optional features |

---

## 14. Final Acceptance Criteria & Delivery Checklist

### 14.1 MVP Acceptance Criteria
- [ ] Application starts successfully and main Dashboard loads.
- [ ] Supported CT RDSR files can be uploaded; invalid/unsupported files are handled gracefully.
- [ ] Relevant dose data ($\text{CTDI}_{\text{vol}}$ and $\text{DLP}$) is extracted with correct units.
- [ ] Estimated Effective Dose is calculated using a documented $k$-factor when possible.
- [ ] Examination records are stored, searchable, and viewable in Examination History & Details.
- [ ] Protocol Analysis and Group-Level DRL comparison work accurately on supported data.
- [ ] Printable reports can be generated.
- [ ] Core APIs and end-to-end user workflows pass testing.
- [ ] Scientific limitations are documented and no sensitive patient information (PHI) is exposed.

### 14.2 Final Project Delivery Package
1. Complete Backend Source Code (`backend/`)
2. Complete Frontend Source Code (`frontend/`)
3. Database Schema & ER Diagram
4. API Documentation (`api-contract.md`)
5. Figma Design File
6. Testing Documentation & Results
7. Scientific Calculation References ($k$-factors & DRLs)
8. Sample Data Documentation (`sample-data/README.md`)
9. Project `README.md`
10. Technical Documentation & Known Limitations
11. Final Live Demonstration

> **Project Statement:** *RADSAFE is an educational and research-oriented software prototype for CT radiation dose monitoring and analysis. It is not intended to replace clinical judgment, scanner quality assurance procedures, or an approved clinical medical device.*
