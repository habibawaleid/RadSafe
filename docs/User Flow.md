# End-to-End User Flow Specification
## RADSAFE — Patient-Centric Longitudinal CT Radiation Dose Tracking System

---

## 1. Primary User Journeys

### Journey A: Uploading a New CT RDSR File (New or Returning Patient)
1. **Entry:** User opens RADSAFE and navigates to **Upload & Analysis** (or clicks `+ Upload RDSR` from the top bar).
2. **File Selection:** User drags and drops a `.dcm` RDSR file (and optionally confirms/enters their `Unique Patient ID` if overriding or verifying).
3. **Backend Processing:**
   * System validates DICOM RDSR SOP Class UID.
   * System extracts `Patient ID`, `Patient Name`, `Institution Name`, `Scanner Manufacturer`, `Study Date`, `Protocol`, $\text{CTDI}_{\text{vol}}$, and $\text{DLP}$.
   * System checks `StudyInstanceUID` to ensure the report is not a duplicate upload.
   * System computes `Effective Dose (mSv)` using the corresponding anatomical $k$-factor.
   * System links the scan to the patient's `Unique Patient ID` in the database and recalculates their **Cumulative Effective Dose**.
4. **Immediate Result View (`Analysis Results`):**
   * Displays a confirmation banner: *"Examination linked to Patient: [Name] (ID: [Unique ID])"*.
   * Displays current scan metrics ($\text{CTDI}_{\text{vol}}$, $\text{DLP}$, `Effective Dose`) alongside the updated **Lifetime Cumulative Effective Dose ($\text{mSv}$)**.
   * Provides quick action buttons: **View Full Patient Dose History** or **Export PDF Passport**.

---

### Journey B: Reviewing a Returning Patient's Longitudinal History
1. **Search:** A physician or patient enters the **Unique Patient ID** in the **Dashboard** search bar (or navigates to **Patient Dose Profile**).
2. **Profile Overview:**
   * Displays Patient Summary Card (`Unique ID`, `Full Name`, `Age/Sex`, `Total CT Scans Count`, `Lifetime Cumulative Effective Dose (mSv)`, and `Current Year Cumulative Dose (mSv)`).
3. **Visual Timeline & Charts:**
   * **Annual Dose Bar Chart:** Shows total $\text{mSv}$ received per calendar year (e.g., 2024, 2025, 2026).
   * **Cumulative Exposure Line Chart:** Plots the upward trajectory of cumulative dose across every scan date.
4. **Multi-Facility Examination Table:**
   * Lists every scan chronologically with columns: `Date` | `Hospital / Institution` | `Scanner Vendor` | `Protocol` | `CTDIvol (mGy)` | `DLP (mGy.cm)` | `Effective Dose (mSv)` | `DRL Status`.
5. **PDF Export:** User clicks **Download Patient Radiation Passport (PDF)** to generate a formal multi-scan report for their doctor.

---

### Journey C: Error & Exception Flows
* **Non-RDSR DICOM File Uploaded (e.g., standard CT slice image):** System displays an error alert: *"Invalid File Type: The uploaded DICOM file is a standard image slice, not a Radiation Dose Structured Report (RDSR)."*
* **Duplicate RDSR Uploaded:** System prevents duplicate database insertion and displays: *"Duplicate Report: This exact CT examination (Study UID) is already recorded under Patient ID [ID]."*
* **Unrecognized Protocol / Missing $k$-factor:** System saves $\text{CTDI}_{\text{vol}}$ and $\text{DLP}$, flags `Effective Dose` as `Requires Manual k-factor Selection`, and prompts selection of the anatomical region (`Head`, `Neck`, `Chest`, `Abdomen`, `Pelvis`).
