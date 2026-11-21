<div align="center">

# ☢️ RADSAFE
### **Patient-Centric Longitudinal CT Radiation Dose Tracking & Cumulative Exposure Analysis System**

[![Java](https://img.shields.io/badge/Java-21-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)](https://www.oracle.com/java/)
[![Spring Boot](https://img.shields.io/badge/Spring_Boot-3.x-6DB33F?style=for-the-badge&logo=spring-boot&logoColor=white)](https://spring.io/projects/spring-boot)
[![DICOM](https://img.shields.io/badge/DICOM-RDSR_%7C_dcm4che-0078D7?style=for-the-badge&logo=dicom&logoColor=white)](https://www.dcm4che.org/)
[![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white)](https://developer.mozilla.org/en-US/docs/Web/HTML)
[![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white)](https://developer.mozilla.org/en-US/docs/Web/CSS)
[![JavaScript](https://img.shields.io/badge/JavaScript-ES6+-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
[![SQLite](https://img.shields.io/badge/SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white)](https://www.sqlite.org/)
[![Status](https://img.shields.io/badge/Phase_1-Foundation_In_Progress-0284C7?style=for-the-badge)](#)

*A Vendor-Neutral Medical Informatics Platform that unifies CT Radiation Dose Structured Reports (RDSR) across different scanner manufacturers (Siemens, Philips, GE, Toshiba) into a single longitudinal Patient Radiation Passport.*

[Overview](#-project-overview) • [Architecture](#-system-architecture) • [Roadmap](#-full-project-roadmap) • [Team](#-team--responsibilities) • [Documentation](#-documentation-index)

</div>

---

## 🎯 Project Overview

In modern radiology workflows, CT scanner vendors (Siemens, Philips, GE, Canon/Toshiba) store radiation exposure data inside proprietary reporting systems. When a patient undergoes multiple CT scans across different hospitals or scanners over months and years, their dose history becomes fragmented, requiring tedious manual calculations to estimate cumulative radiation exposure.

**RADSAFE** solves this challenge by providing a **Vendor-Neutral, Patient-Centric** platform:
* **Universal DICOM RDSR Ingestion:** Automatically parses standard CT Radiation Dose Structured Reports (`.dcm`) regardless of the scanner manufacturer.
* **Unique Patient ID Linking:** Maps every uploaded scan to a unified `Unique Patient ID`, aggregating historical and new examinations over time.
* **Automated Scientific Dose Calculation:** Extracts `CTDIvol` (mGy) and `DLP` (mGy·cm), applies anatomical region-specific `k-factors` (ICRP/AAPM), and computes both per-exam and **Cumulative Effective Dose (mSv)**.
* **Longitudinal Trend Visualization:** Tracks cumulative radiation exposure across months and years through interactive charts and timelines.
* **Patient Radiation Passport (PDF):** Exports a comprehensive, standardized PDF dose history report for physicians and patients.

---

## 🏗️ System Architecture & Workflow

```text
┌─────────────────────────┐      ┌──────────────────────────────────────────┐
│  CT Scanners (Multi-    │      │           RADSAFE Backend                │
│  Vendor: GE, Siemens,   │─────▶│         (Java 21 + Spring Boot)          │
│  Philips, Toshiba)      │ RDSR │                                          │
└─────────────────────────┘ File │  1. dcm4che DICOM RDSR Parser            │
                                 │  2. Patient ID & Scanner Extraction      │
                                 │  3. k-Factor Effective Dose Engine       │
                                 │  4. Cumulative Dose Aggregator           │
                                 └────────────────────┬─────────────────────┘
                                                      │ REST API (JSON)
                                                      ▼
┌─────────────────────────┐      ┌──────────────────────────────────────────┐
│  RADSAFE Frontend       │◀─────│           Database Layer                 │
│  (HTML5 / CSS3 / JS)    │      │       (SQLite / PostgreSQL)              │
│                         │      │                                          │
│  • RDSR Upload Portal   │      │  • Patient Profile (Unique Patient ID)   │
│  • Patient Dose Profile │      │  • Examination Timeline & Scanner Info   │
│  • Cumulative Charts    │      │  • Dose Metrics & k-Factor Reference     │
│  • PDF Dose Passport    │      │  • DRL Reference Benchmarks              │
└─────────────────────────┘      └──────────────────────────────────────────┘
🗺️ Full Project RoadmapNote: The current 10-Day Sprint represents Phase 1 (Foundation & Proof of Concept)—focused on tool onboarding, validating DICOM RDSR parsing, and establishing the core data pipeline before full-scale implementation.PhaseTimelineFocus AreaKey MilestonesPhase 1 (Current)Days 1–10Foundation & PoCSpring Boot setup, dcm4che RDSR parsing test, Figma wireframes, HTML/CSS prototype, k-factor & RDSR scientific dictionary.Phase 2Weeks 3–4Core Backend & Dose EngineFull Patient & Examination database schema, automated Unique Patient ID linking, k-factor calculation engine, and REST APIs.Phase 3Weeks 5–6Frontend & Longitudinal ChartsInteractive RDSR upload flow, Patient Dose Profile, Chart.js cumulative dose timelines, and DRL reference comparison views.Phase 4Weeks 7–8PDF Passport, Validation & QAPDF Patient Radiation Passport generator, end-to-end validation against manual physics calculations, and final deployment.👥 Team & ResponsibilitiesRoleMemberCore ResponsibilitiesBackend Developer & CoordinatorMember 1 (Habiba)Java 21, Spring Boot REST APIs, dcm4che RDSR parsing, Patient ID linking, SQLite/JPA database, and team coordination.UI/UX & Frontend DeveloperMember 2 (Manar)Figma wireframes, HTML5/CSS3 responsive layout, JavaScript Fetch API integration, Chart.js timelines, and PDF view.Medical Physics & ResearchMember 3CT dosimetry (CTDIvol, DLP, Effective Dose), RDSR sample curation, k-factor tables, DRL benchmarks, and manual validation.📂 Repository StructurePlaintextRadSafe/
├── docs/                                         # Project & Scientific Documentation
│   ├── project-scope.md                          # System scope, boundaries & clinical framing
│   ├── Radsafe_srs_project_development_plan.md   # Software Requirements Specification (SRS)
│   ├── user-flow.md                              # End-to-end user & data workflows
│   ├── ui-ux-guidelines.md                       # Design system, screens & visual guidelines
│   └── medical-physics-research-plan.md          # Dosimetry formulas, k-factors & validation plan
├── radsafe-backend/                              # Java 21 + Spring Boot Backend Application
└── README.md                                     # Project overview & roadmap
📚 Documentation IndexProject Scope: Problem statement, vendor-neutral goals, and clinical safety disclaimers.SRS & Development Plan: Functional requirements, database entities (Patient, Examination, DoseMetric), and API specifications.User Flow: Step-by-step operational workflow from RDSR file upload to PDF Radiation Passport export.UI/UX Guidelines: Color palette, typography, and layout specifications for the Patient Dose Profile and Dashboard.Medical Physics Research Plan: CTDIvol, DLP, k-factor sources, cumulative dose methodology, and manual verification protocol.

