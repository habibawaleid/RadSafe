::: {align="center"}
# ☢️ RADSAFE

### **Patient-Centric Longitudinal CT Radiation Dose Tracking & Cumulative Exposure Analysis System**

![Java](https://img.shields.io/badge/Java-21-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)
![Spring
Boot](https://img.shields.io/badge/Spring_Boot-3.x-6DB33F?style=for-the-badge&logo=spring-boot&logoColor=white)
![DICOM](https://img.shields.io/badge/DICOM-RDSR%20%7C%20dcm4che-0078D7?style=for-the-badge)
![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-ES6%2B-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)
![SQLite](https://img.shields.io/badge/SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white)
![Status](https://img.shields.io/badge/Phase_1-Foundation_In_Progress-0284C7?style=for-the-badge)

*A vendor-neutral medical informatics platform designed to unify CT
Radiation Dose Structured Reports (RDSR) from different scanner
manufacturers into a longitudinal patient radiation history.*

[Overview](#-project-overview) •
[Workflow](#-system-architecture--workflow) •
[Dosimetry](#-scientific-dosimetry-model) • [Roadmap](#-project-roadmap)
• [Setup](#-quick-start--setup) • [Team](#-team--responsibilities) •
[Docs](#-documentation-index)
:::

------------------------------------------------------------------------

## 🎯 Project Overview

In modern radiology workflows, CT scanners from different
manufacturers---such as Siemens, Philips, GE, and
Canon/Toshiba---generate radiation exposure information in DICOM
Radiation Dose Structured Reports (RDSR). When a patient undergoes
multiple CT examinations across different hospitals or scanners over
months or years, their exposure history may become fragmented.

**RADSAFE** aims to provide a vendor-neutral, patient-centric platform
for collecting, organizing, analyzing, and presenting CT radiation dose
information.

### Key Features

-   **DICOM RDSR Ingestion:** Upload and process standard CT Radiation
    Dose Structured Report (`.dcm`) files.
-   **Longitudinal Patient Records:** Associate examinations with a
    consistent patient identifier so that dose history can be viewed
    over time.
-   **Automated Dose Calculations:** Extract dose metrics such as
    `CTDIvol` and `DLP`, then estimate effective dose using documented
    anatomical conversion coefficients (`k-factors`).
-   **Cumulative Exposure Tracking:** Aggregate calculated
    effective-dose estimates across a patient's recorded examinations.
-   **Dose Visualization:** Present examination history and cumulative
    trends through charts and chronological timelines.
-   **DRL Benchmarking:** Compare examination dose indicators with
    suitable Diagnostic Reference Levels (DRLs), where applicable
    reference data is available.
-   **Patient Radiation Passport:** Generate a printable report
    summarizing the patient's recorded CT examination and dose history.

> **Project status:** RADSAFE is under development. The current work
> focuses on the foundation and proof of concept. Features described in
> this README represent the planned system unless explicitly marked as
> implemented.

------------------------------------------------------------------------

## 🏗️ System Architecture & Workflow

### High-Level Architecture

``` text
┌──────────────────────────────┐
│       CT Scanner Systems     │
│   GE / Siemens / Philips /   │
│       Canon / Toshiba        │
└──────────────┬───────────────┘
               │ DICOM RDSR (.dcm)
               ▼
┌────────────────────────────────────────────┐
│              RADSAFE Backend               │
│             Java 21 + Spring Boot          │
│                                            │
│  1. DICOM RDSR validation and parsing      │
│  2. Patient and scanner information        │
│  3. Dose metric extraction                 │
│  4. Effective-dose estimation              │
│  5. Cumulative dose aggregation            │
│  6. REST API                               │
└──────────────────────┬─────────────────────┘
                       │ REST API (JSON)
                       ▼
┌──────────────────────────────┐
│        RADSAFE Frontend      │
│       HTML / CSS / JavaScript│
│                              │
│  • RDSR upload interface     │
│  • Patient dose profile      │
│  • Examination timeline      │
│  • Cumulative dose charts    │
│  • PDF report access         │
└──────────────────────────────┘
                       │
                       ▼
┌────────────────────────────────────────────┐
│               Database Layer               │
│              SQLite / PostgreSQL           │
│                                            │
│  • Patient                                │
│  • Examination                            │
│  • DoseMetric                              │
│  • Scanner / Institution                   │
│  • DRLReference                            │
└────────────────────────────────────────────┘
```

### End-to-End Operational Flow

1.  **Upload:** An authorized user uploads a CT DICOM RDSR file (`.dcm`)
    through the web interface.
2.  **Validate and Parse:** The backend validates the DICOM object and
    its SOP Class, then extracts supported patient, study, scanner,
    protocol, and dose information.
3.  **Calculate:** The dose engine uses the extracted `DLP` and an
    appropriate documented anatomical `k-factor` to estimate effective
    dose.
4.  **Associate and Aggregate:** The examination is linked to the
    appropriate patient record using the project's patient-matching
    rules. The patient's cumulative recorded effective-dose estimate is
    updated.
5.  **Visualize and Export:** The frontend displays the examination
    history, dose trends, applicable DRL comparisons, and available
    patient report.

**DICOM note:** The CT Radiation Dose SR Storage SOP Class UID is
`1.2.840.10008.5.1.4.1.1.88.67`. The parser should validate the actual
DICOM object and supported content rather than assuming every `.dcm`
file is a CT RDSR.

------------------------------------------------------------------------

## 🔬 Scientific Dosimetry Model

RADSAFE's planned effective-dose estimation uses the Dose-Length Product
(`DLP`) and an anatomical conversion coefficient (`k`):

\[ E (`\mathrm{mSv}`{=tex}) =
`\mathrm{DLP}`{=tex} (`\mathrm{mGy \cdot cm}`{=tex})
`\times `{=tex}k (`\mathrm{mSv/(mGy \cdot cm)}`{=tex}) \]

The cumulative estimate across the examinations included in a patient's
record is:

\[ E\_{`\mathrm{cumulative}`{=tex}} = `\sum`{=tex}\_{i=1}\^{n} E_i \]

### Initial Reference Coefficients

  ------------------------------------------------------------------------
  Anatomical Region                 Example k-factor Reference noted in
                                      (mSv/(mGy·cm)) project
  --------------------- ---------------------------- ---------------------
  Head / Brain                              `0.0021` ICRP 102 / AAPM
                                                     Report 96

  Neck                                      `0.0059` ICRP 102 / AAPM
                                                     Report 96

  Chest                                     `0.0140` ICRP 102 / AAPM
                                                     Report 96

  Abdomen & Pelvis                          `0.0150` ICRP 102 / AAPM
                                                     Report 96
  ------------------------------------------------------------------------

> **Scientific validation required:** These coefficients are initial
> project reference values, not universal constants. Before clinical
> use, the team must verify each coefficient against the exact reference
> publication, patient age group, anatomical region, scanner/protocol
> context, and intended application. A single coefficient may not be
> appropriate for every patient or examination.

### Important Dose-Metric Distinction

-   **CTDIvol (mGy):** A standardized CT scanner output index. It is not
    a direct measurement of an individual patient's absorbed dose.
-   **DLP (mGy·cm):** CTDIvol multiplied by scan length; it is commonly
    used as an examination-level dose indicator.
-   **Effective Dose (mSv):** A radiation-protection quantity estimated
    here from DLP and a conversion coefficient. It is useful for broad
    comparisons and population-level radiation protection, but it is not
    an individualized prediction of a patient's health outcome.

> **Clinical disclaimer:** Effective dose is a radiation-protection
> metric and should not be interpreted as an individual patient's exact
> biological risk. Medical imaging decisions must be made by qualified
> healthcare professionals. Diagnostic Reference Levels are optimization
> tools, not dose limits for individual patients.

------------------------------------------------------------------------

## 🗺️ Project Roadmap

The current **10-day sprint** represents **Phase 1: Foundation & Proof
of Concept**. The phases below describe the planned development
sequence.

  -----------------------------------------------------------------------
  Phase             Timeline          Focus Area        Key Milestones
  ----------------- ----------------- ----------------- -----------------
  **Phase 1 ---     Days 1--10        Foundation & PoC  Development
  Current**                                             environment,
                                                        initial DICOM
                                                        RDSR parsing
                                                        test, Figma
                                                        wireframes,
                                                        HTML/CSS
                                                        prototype,
                                                        scientific data
                                                        dictionary and
                                                        initial
                                                        coefficient
                                                        review.

  **Phase 2**       Weeks 3--4        Core Backend &    Patient and
                                      Dose Engine       Examination
                                                        database schema,
                                                        patient-record
                                                        association, dose
                                                        calculation
                                                        module, and REST
                                                        API endpoints.

  **Phase 3**       Weeks 5--6        Frontend &        RDSR upload
                                      Longitudinal      workflow, patient
                                      Charts            dose profile,
                                                        Chart.js
                                                        timelines, and
                                                        DRL comparison
                                                        views.

  **Phase 4**       Weeks 7--8        PDF Passport,     PDF report
                                      Validation & QA   generation,
                                                        validation
                                                        against
                                                        independently
                                                        calculated test
                                                        cases, end-to-end
                                                        testing, and
                                                        deployment
                                                        preparation.
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 🚀 Quick Start & Setup

### Prerequisites

-   **Java Development Kit (JDK):** Version 21 or later.
-   **Maven:** Version 3.9 or later, or use the included Maven Wrapper.
-   **Git:** To clone the repository.
-   **Browser:** A modern browser such as Chrome, Edge, or Firefox.

### Run the Backend Locally

``` bash
# 1. Clone the repository
git clone https://github.com/habibawaleid/RadSafe.git

# 2. Move into the backend directory
cd RadSafe/radsafe-backend

# 3. Run the Spring Boot application
# Windows:
mvnw.cmd spring-boot:run

# macOS / Linux:
./mvnw spring-boot:run
```

When the application starts successfully, the backend is expected to be
available at:

``` text
http://localhost:8080
```

> **Setup note:** These commands assume the repository contains the
> `radsafe-backend` directory and a Maven Wrapper. If the wrapper is not
> included, install Maven and run `mvn spring-boot:run`.

------------------------------------------------------------------------

## 👥 Team & Responsibilities

  -----------------------------------------------------------------------
  Role                    Member                  Core Responsibilities
  ----------------------- ----------------------- -----------------------
  **Backend Developer &   **Member 1 (Habiba)**   Java 21, Spring Boot
  Coordinator**                                   REST APIs, DICOM RDSR
                                                  parsing with dcm4che,
                                                  patient-record
                                                  association, database
                                                  integration, and team
                                                  coordination.

  **UI/UX & Frontend      **Member 2 (Manar)**    Figma wireframes,
  Developer**                                     responsive HTML/CSS
                                                  interface, JavaScript
                                                  integration with
                                                  backend APIs, Chart.js
                                                  visualizations, and
                                                  report interface.

  **Medical Physics &     **Member 3**            CT dosimetry research,
  Research**                                      `CTDIvol` and `DLP`
                                                  interpretation, RDSR
                                                  sample collection,
                                                  `k-factor` references,
                                                  DRL research, and
                                                  independent manual
                                                  validation of dose
                                                  calculations.
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 📂 Repository Structure

``` text
RadSafe/
├── docs/
│   ├── project-scope.md
│   ├── Radsafe_srs_project_development_plan.md
│   ├── user-flow.md
│   ├── ui-ux-guidelines.md
│   └── medical-physics-research-plan.md
│
├── radsafe-backend/
│   └── ...
│
├── radsafe-frontend/
│   └── ...
│
└── README.md
```

------------------------------------------------------------------------

## 📚 Documentation Index

  -----------------------------------------------------------------------------------------
  Document                                              Description
  ----------------------------------------------------- -----------------------------------
  [Project Scope](docs/project-scope.md)                Problem statement, project
                                                        objectives, scope boundaries, and
                                                        clinical safety framing.

  [SRS & Development                                    Functional requirements, system
  Plan](docs/Radsafe_srs_project_development_plan.md)   components, database entities, and
                                                        planned REST API endpoints.

  [User Flow](docs/user-flow.md)                        User and data workflow from RDSR
                                                        upload through patient history and
                                                        report export.

  [UI/UX Guidelines](docs/ui-ux-guidelines.md)          Design system, colors, typography,
                                                        layout, and interface
                                                        specifications.

  [Medical Physics Research                             Dose metrics, scientific
  Plan](docs/medical-physics-research-plan.md)          references, `k-factor`
                                                        verification, DRL research, and
                                                        manual validation methodology.
  -----------------------------------------------------------------------------------------

------------------------------------------------------------------------

## ⚠️ Development & Clinical Use Notice

RADSAFE is an academic development project and is not a certified
medical device or a replacement for clinical judgment. Its calculations,
patient-matching logic, reference coefficients, and reports must be
validated by qualified professionals before any clinical use. The system
should be tested using suitable de-identified or synthetic data and must
protect patient information in accordance with applicable privacy and
security requirements.

------------------------------------------------------------------------

::: {align="center"}
**RADSAFE --- Making CT Dose History Clearer, Connected, and
Traceable.**
:::
