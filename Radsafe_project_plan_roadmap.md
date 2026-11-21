# RADSAFE — Intelligent CT Radiation Dose Monitoring & Analysis System

![Status](https://img.shields.io/badge/Status-In%20Development-blue)
![Tech Stack](https://img.shields.io/badge/Backend-Java%2021%20%7C%20Spring%20Boot-brightgreen)
![Frontend](https://img.shields.io/badge/Frontend-HTML%20%7C%20CSS%20%7C%20JS%20%7C%20Chart.js-orange)
![DICOM](https://img.shields.io/badge/Standard-DICOM%20RDSR-purple)

**RADSAFE** is a web-based clinical decision support and quality assurance system designed to automate the extraction, analysis, and benchmarking of Computed Tomography (CT) radiation dose data from **DICOM Radiation Dose Structured Reports (RDSR)**.

---

## 1. System Overview & Core Workflow

Instead of manually inspecting complex radiation dose reports, RADSAFE streamlines the workflow into 9 automated steps:

1. **Upload:** User uploads a DICOM `.dcm` (RDSR) file.
2. **Validation:** The system verifies file integrity and confirms it is a valid CT RDSR object.
3. **Extraction:** The backend parses the DICOM tree to extract key dose metrics (`CTDIvol`, `DLP`, scan protocols, etc.).
4. **Dose Calculation:** Calculates the **Estimated Effective Dose** ($E$) using verified anatomical $k$-factors ($E = \text{DLP} \times k$).
5. **Anonymization & Storage:** Strips Protected Health Information (PHI) and stores anonymized examination records.
6. **Results Visualization:** Displays individual examination metrics on the Analysis Results screen.
7. **Protocol Aggregation:** Groups examinations by clinical protocol (e.g., CT Head, CT Chest, CT Abdomen/Pelvis).
8. **DRL Benchmarking:** Compares group median/75th-percentile doses against established **Diagnostic Reference Levels (DRLs)**.
9. **Reporting:** Generates dashboards, statistical trends, and printable summary reports.

> **Important Scientific Disclaimer:**  
> *Diagnostic Reference Levels (DRLs) are optimization tools applied to groups of similar examinations—not individual patient dose limits or safety thresholds. Furthermore, Effective Dose is a population-based risk estimate rather than an exact measure of individual organ dose.*

---

## 2. MVP Scope & Core Modules (8-Week Target)

| Module | User Goal | Key MVP Features |
| :--- | :--- | :--- |
| **Main Dashboard** | View system overview | Total examinations, dose averages, protocol distribution charts, recent scans, and DRL review summary. |
| **1. Upload & Analysis** | Upload RDSR files | File selection, DICOM/RDSR validation, processing status indicators, and clear error feedback. |
| **2. Examination Records** | Browse past scans | Anonymized examination history table, search/filter by ID or protocol, and detailed scan view. |
| **3. Dose Analysis** | Inspect dose metrics | Breakdown of $\text{CTDI}_{\text{vol}}$ ($\text{mGy}$), $\text{DLP}$ ($\text{mGy}\cdot\text{cm}$), Effective Dose ($\text{mSv}$), and protocol analytics. |
| **4. DRL Review** | Benchmark dose levels | Group-level comparison against national/international DRLs with statistical review flags. |
| **5. Reports** | Export documentation | Printable examination summaries and protocol analysis reports (Browser Print / Save as PDF). |
| **6. Scientific Settings** | Inspect reference data | Read-only reference tables for anatomical $k$-factors, protocol mappings, and DRL thresholds. |

---

## 3. Technology Stack & Architectural Decisions

| Component | Approved Decision | Notes |
| :--- | :--- | :--- |
| **Backend** | Java 21 + Spring Boot | RESTful API architecture (`Controller` $\rightarrow$ `Service` $\rightarrow$ `Repository`). |
| **DICOM Parser** | `dcm4che` | Used for reading DICOM headers and traversing Structured Report (SR) content items. |
| **Database** | SQLite (MVP) | Lightweight local persistence; designed with JPA to easily migrate to PostgreSQL later. |
| **Frontend** | HTML5 + CSS3 + Vanilla JS | Decoupled client communicating via `Fetch API`, using `Chart.js` for data visualization. |
| **UI/UX Design** | Figma | Wireframes, Design System, and High-Fidelity prototypes created prior to implementation. |
| **Reports** | Browser Print / PDF | Clean CSS print stylesheets for generating examination and audit reports. |
| **Version Control** | Git & GitHub | Feature-branch workflow with Pull Requests into `main`. |
| **Out of MVP Scope** | Auth & PACS Integration | User authentication and direct hospital PACS integration are deferred post-MVP. |

---

## 4. Repository Structure

```text
RADSAFE/
├── radsafe-backend/        # Java 21 Spring Boot application (src/, pom.xml)
├── radsafe-frontend/       # Web UI (index.html, css/, js/, assets/)
├── docs/                   # Project documentation, API Contract, Data Dictionary, Figma links
├── sample-data/            # Anonymized public CT RDSR test files (NO real patient PHI)
└── README.md               # Master project guide & roadmap
```

> **Security Rule:** Uploading real patient data or files containing Protected Health Information (PHI) to this repository is strictly prohibited.

---

## 5. Team Roles & Responsibilities

### Member 1 — Backend Lead & Team Coordinator
* **Core Focus:** System architecture, REST APIs, database modeling, `dcm4che` RDSR parsing, dose calculation engine, and integration testing.
* **Key Deliverables:** Spring Boot backend, `API_Contract.md`, `dcm4che` feasibility report, unit tests (JUnit 5).

### Member 2 — UI/UX Designer & Frontend Developer
* **Core Focus:** User flows, Figma wireframes & UI design system, responsive HTML/CSS layouts, interactive JS logic, and `Chart.js` dashboards.
* **Key Deliverables:** 10-screen Figma prototype (prioritizing *Dashboard*, *Upload*, and *Results*), responsive frontend integrated via `Fetch API`.

### Member 3 — Medical Physics & Research Lead
* **Core Focus:** Clinical accuracy, sourcing valid public CT RDSR datasets (IHE REM / NEMA), defining $k$-factors and DRL benchmarks, and manual validation.
* **Key Deliverables:** RDSR Sample Catalogue, Scientific Data Dictionary, $k$-factor & DRL reference tables, and manual calculation validation sheets.

---

## 6. 8-Week Master Roadmap

| Week | Phase | Backend Lead | UX/UI + Frontend | Medical Physics | Unified Milestone |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Week 1** | **Foundation & Setup** | Spring Boot setup, GitHub repo, initial `dcm4che` trial | User Flow, Figma basics & core wireframes | Gather public CT RDSR samples & core concepts | Working repo, Spring Boot app, wireframes, & RDSR samples |
| **Week 2** | **Design & Data Freeze** | REST APIs, File Upload endpoint, Database design | High-fidelity Figma MVP design & initial HTML/CSS | Scientific Data Dictionary & manual DLP calculations | Finalized Figma MVP, `API_Contract.md`, & Data Dictionary |
| **Week 3** | **Core Parsing MVP** | Build `dcm4che` RDSR Parser & file validation | Build Dashboard, Upload, & Results UI pages | Verify extracted RDSR fields & units | End-to-end file upload & real RDSR data extraction |
| **Week 4** | **Dose Engine** | Effective Dose calculation engine & DB persistence | Connect Upload & Results pages via `Fetch API` | Provide verified $k$-factors & validate calculations | Automated dose calculation & anonymized DB storage |
| **Week 5** | **History & Protocols** | History, Search, & Protocol aggregation APIs | Build Examination History, Details, & Search UI | Define clinical protocol classification rules | Searchable scan history & protocol-level analytics |
| **Week 6** | **DRL Benchmarking** | Group DRL statistical analysis & Dashboard APIs | Integrate `Chart.js` dashboards & DRL Review UI | Provide DRL references & validate group logic | Live analytics dashboard & DRL group comparison |
| **Week 7** | **Reports & Testing** | JUnit 5 tests, error handling, & integration fixes | Printable Reports UI & responsive polish | Full scientific validation of system outputs | Printable reports & verified test suite |
| **Week 8** | **Final Integration** | Bug fixing, final deployment prep, & demo support | Final UI polish & demo walkthrough | Complete scientific documentation & presentation | **Final Integrated RADSAFE System & Demo** |

---

## 7. API Contract Preview

Before frontend-backend integration, both teams adhere to a shared JSON contract:

**Endpoint:** `POST /api/examinations/upload`  
**Content-Type:** `multipart/form-data`

```json
{
  "examinationId": 101,
  "status": "COMPLETED",
  "protocol": "CT Chest",
  "ctdiVol": 12.5,
  "dlp": 450.0,
  "effectiveDose": 6.3,
  "units": {
    "ctdiVol": "mGy",
    "dlp": "mGy*cm",
    "effectiveDose": "mSv"
  }
}
```
