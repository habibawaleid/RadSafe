<div align="center">

# ☢️ RADSAFE
### **Patient-Centric Longitudinal CT Radiation Dose Tracking & Cumulative Exposure Analysis System**

![Java](https://img.shields.io/badge/Java-21-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring_Boot-3.x-6DB33F?style=for-the-badge&logo=spring-boot&logoColor=white)
![DICOM](https://img.shields.io/badge/DICOM-RDSR_%7C_dcm4che-0078D7?style=for-the-badge&logo=dicom&logoColor=white)
![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-ES6+-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)
![SQLite](https://img.shields.io/badge/SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white)
![Status](https://img.shields.io/badge/Phase_1-Foundation_In_Progress-0284C7?style=for-the-badge)

*A Vendor-Neutral Medical Informatics Platform that unifies CT Radiation Dose Structured Reports (RDSR) across different scanner manufacturers (Siemens, Philips, GE, Toshiba) into a single longitudinal Patient Radiation Passport.*

[Overview](#-project-overview) • [Architecture](#-system-architecture--workflow) • [Roadmap](#-full-project-roadmap) • [Team](#-team--responsibilities) • [Documentation](#-documentation-index)

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
