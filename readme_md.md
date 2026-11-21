# RADSAFE — Longitudinal CT Radiation Dose Tracking & Cumulative Exposure Analysis System

**RADSAFE** is a vendor-neutral medical informatics and radiation dose monitoring web application designed to track individual patient radiation exposure across multiple Computed Tomography (CT) examinations, scanners, and healthcare facilities over time.

---

## 1. Problem Statement & Core Value Proposition

* **The Problem (Vendor Lock-in & Fragmented Dose History):** CT scanner manufacturers (Siemens, Philips, GE, Canon/Toshiba) use proprietary reporting environments. When a patient undergoes multiple CT scans across different hospitals or over several years, their radiation exposure records remain fragmented. Calculating the **Effective Dose ($\text{mSv}$)** and aggregating **Cumulative Effective Dose** across years is typically done manually, increasing the risk of unmonitored radiation accumulation and redundant imaging.
* **The Solution (Vendor-Neutral Patient Radiation Passport):** RADSAFE ingests standard **DICOM Radiation Dose Structured Reports (RDSR)** regardless of scanner vendor, links each examination to a **Unique Patient ID**, automatically extracts $\text{CTDI}_{\text{vol}}$ and $\text{DLP}$, computes Effective Dose using anatomical $k$-factors, updates the patient's longitudinal cumulative dose history, visualizes exposure trends over time, and generates a printable **Patient Radiation Passport (PDF)**.

---

## 2. System Workflow (End-to-End Architecture)

1. **RDSR Ingestion & Validation:** The user (patient, physician, or radiology staff) uploads a DICOM `.dcm` file. The Spring Boot backend validates that the file is a valid CT RDSR (`SOP Class UID: 1.2.840.10008.5.1.4.1.1.88.67`).
2. **Metadata & Dose Extraction:** Using `dcm4che`, the backend extracts:
   * **Patient Identifiers:** `Patient ID` (Unique System Key), `Patient Name`, `Patient Sex`, `Patient Birth Date`.
   * **Scanner & Facility Context:** `Institution Name`, `Manufacturer` (Siemens, Philips, GE, etc.), `Station Name`, `Study Date`.
   * **Radiation Metrics:** `Protocol Name / Target Region`, $\text{CTDI}_{\text{vol}}$ ($\text{mGy}$), and $\text{DLP}$ ($\text{mGy}\cdot\text{cm}$).
3. **Scientific Dose Calculation:** The system maps the anatomical scan region to its standardized conversion coefficient ($k$-factor) and computes:
   $$\text{Effective Dose (mSv)} = \text{DLP (mGy}\cdot\text{cm)} \times k\text{-factor}$$
4. **Longitudinal Patient Linking:**
   * The backend queries the database using the **Unique Patient ID**.
   * If the patient already exists (even from a scan performed months or years earlier at another facility), the new examination is linked to their longitudinal record and added to their **Cumulative Effective Dose**.
   * If the patient is new, a dedicated patient profile is automatically created.
5. **Visualization & PDF Reporting:** The frontend displays the current scan results alongside historical examinations, annual/multi-year cumulative dose charts, DRL reference comparisons, and an exportable **PDF Patient Radiation Passport**.

---

## 3. Repository Structure

```text
RadSafe/
├── docs/                                         # Project documentation & specifications
│   ├── Radsafe_srs_project_development_plan.md   # Software Requirements Specification (SRS)
│   ├── project-scope.md                          # Scope boundaries & scientific disclaimers
│   ├── user-flow.md                              # End-to-end user journeys
│   ├── ui-ux-guidelines.md                       # Design system & screen layouts
│   └── medical-physics-research-plan.md          # Medical physics validation & k-factors
├── radsafe-backend/                              # Java 21 + Spring Boot REST API & dcm4che parser
├── radsafe-frontend/                             # HTML5, CSS3, JavaScript & Chart.js UI
└── README.md                                     # Project overview & roadmap
```

---

## 4. Team Roles & Responsibilities

| Role | Member | Core Responsibilities |
| :--- | :--- | :--- |
| **Member 1** | Backend Developer & Coordinator | Java 21, Spring Boot REST APIs, `dcm4che` RDSR parsing, SQLite/JPA database modeling (`Patient` & `Examination`), cumulative dose aggregation, and sprint coordination. |
| **Member 2** | UI/UX Designer & Frontend Dev | Figma wireframes & mockups, responsive HTML/CSS layouts, JavaScript Fetch API integration, `Chart.js` longitudinal dose visualizations, and PDF report styling. |
| **Member 3** | Medical Physics & Research | CT RDSR DICOM tag verification, $k$-factor reference tables (ICRP/AAPM), Diagnostic Reference Levels (DRLs), manual dose validation sheets, and scientific documentation. |

---

## 5. Foundation Phase Roadmap (Days 1–10)

* **Days 1–3 (Setup & Core Understanding):** Environment setup (Java 21, Spring Boot, VS Code, Figma, GitHub), basic REST APIs (`GET`/`POST`), initial HTML/CSS layouts, and DICOM RDSR sample verification.
* **Days 4–7 (Core Execution):** File upload endpoint, SQLite database connection (`Patient` + `Examination` entities), `dcm4che` tag extraction feasibility test, Figma screens (`Dashboard`, `Upload`, `Patient Profile`), and `Scientific Data Dictionary`.
* **Days 8–10 (Integration & Validation):** Standardized JSON API contract, Fetch API frontend-backend integration, manual vs. automated dose calculation cross-check, and Phase 1 progress review.