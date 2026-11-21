# Medical Physics & Scientific Validation Plan (Member 3)
## RADSAFE — Longitudinal CT Radiation Dose Tracking System

---

## 1. Scientific Role Overview
**Member 3 (Medical Physics & Research)** ensures that every dose metric extracted, calculated, and displayed by RADSAFE adheres to international radiation protection standards (ICRP, IAEA, AAPM, ACR). No programming is required; deliverables consist of validated reference tables, sample RDSR datasets, manual verification sheets, and scientific documentation.

---

## 2. Core Scientific Objectives

1. **Vendor-Neutral CT RDSR Verification:**
   * Collect genuine, de-identified/test CT RDSR `.dcm` samples from multiple scanner vendors (**Siemens, Philips, GE, Canon/Toshiba**) via public medical physics repositories (e.g., IHE Connectathon, TCIA, NEMA/DICOM test datasets).
   * Verify the exact DICOM tags and Structured Report template IDs (`TID 10011` — CT Radiation Dose) where $\text{CTDI}_{\text{vol}}$ and $\text{DLP}$ are stored across different vendors.
2. **Patient & Facility Metadata Mapping (`Scientific Data Dictionary`):**
   * Document the exact DICOM tags required to link multi-facility scans to a single patient:
     * `PatientID` `(0010,0020)` — Primary longitudinal linking identifier
     * `PatientName` `(0010,0010)`
     * `StudyInstanceUID` `(0020,000D)` — Duplicate scan guard
     * `StudyDate` `(0008,0020)`
     * `InstitutionName` `(0008,0080)` & `Manufacturer` `(0008,0070)`
     * `ProtocolName` `(0018,1030)` / `TargetRegion`
     * Mean $\text{CTDI}_{\text{vol}}$ ($\text{mGy}$) & Total $\text{DLP}$ ($\text{mGy}\cdot\text{cm}$)
3. **Standardized $k$-Factor Reference Table:**
   * Compile and cite authoritative anatomical conversion coefficients ($k$-factors in $\text{mSv}\cdot\text{mGy}^{-1}\cdot\text{cm}^{-1}$) from **ICRP Publication 102 / 103** and **AAPM Report No. 96** for:
     * `Head` ($k \approx 0.0021$)
     * `Neck` ($k \approx 0.0059$)
     * `Chest` ($k \approx 0.014$)
     * `Abdomen` ($k \approx 0.015$)
     * `Pelvis` ($k \approx 0.015$)
     * `Abdomen-Pelvis` ($k \approx 0.015$)
     * `Chest-Abdomen-Pelvis (CAP)` ($k \approx 0.015$)
   * Document pediatric vs. adult $k$-factor considerations and define the default adult reference table for the MVP.
4. **Longitudinal Cumulative Dose & DRL Advisory Benchmarks:**
   * Provide validated Diagnostic Reference Level (DRL) benchmarks ($\text{CTDI}_{\text{vol}}$ and $\text{DLP}$) for standard adult protocols so RADSAFE can flag individual examinations that exceed typical reference ranges.
   * Draft the exact **Clinical & Radiation Protection Disclaimer** for the UI and PDF Patient Radiation Passport:
     * Clarify that **Cumulative Effective Dose** is tracked to support clinical justification and prevent unnecessary repeat scans.
     * State explicitly that **no regulatory dose limit applies to patients** undergoing medically justified diagnostic procedures.
5. **Manual Calculation Validation Sheet (`calculation-validation.xlsx`):**
   * Manually calculate Effective Dose ($\text{mSv}$) and multi-scan Cumulative Effective Dose for at least 5 sample RDSR files across different vendors so the backend team can verify 100% mathematical accuracy.

---

## 3. Member 3 Deliverables Schedule (Days 1–10)

| Phase | Target Day | Deliverable File | Content & Verification Criteria |
| :--- | :--- | :--- | :--- |
| **Phase 1** | **Day 3** | `docs/rdsr-sample-catalogue.md` | Summary of $\text{CTDI}_{\text{vol}}$, $\text{DLP}$, and RDSR structure + list of candidate multi-vendor RDSR samples with verification status. |
| **Phase 2** | **Day 7** | `docs/scientific-data-dictionary.md` | Complete mapping of DICOM tags (Patient ID, Institution, Vendor, $\text{CTDI}_{\text{vol}}$, $\text{DLP}$), units, and `Unavailable` missing-data rules. |
| **Phase 3** | **Day 10** | `docs/k-factor-reference-table.md` & `docs/calculation-validation.xlsx` | Cited $k$-factor lookup table, DRL reference values, clinical disclaimer text, and manual Excel verification sheet for multi-scan cumulative dose. |