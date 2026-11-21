# UI/UX Design Guidelines & Screen Architecture
## RADSAFE — Patient-Centric Longitudinal CT Radiation Dose Tracking System

---

## 1. Design Philosophy
* **Clarity & Trust:** Clinical, clean, light-mode interface designed for high readability of numerical dose metrics, units ($\text{mGy}$, $\text{mGy}\cdot\text{cm}$, $\text{mSv}$), and multi-year timelines.
* **Patient-Centric Navigation:** Every screen emphasizes how individual scans connect to a patient's longitudinal radiation history across different hospitals and scanner vendors.

---

## 2. Visual Identity & Design Tokens

| Token | Hex Code | Usage |
| :--- | :--- | :--- |
| **Background Primary** | `#F8FAFC` | Main workspace background (reduces eye strain) |
| **Surface / Cards** | `#FFFFFF` | KPI cards, charts, tables, and sidebar (`1px solid #E2E8F0`) |
| **Primary Brand Blue** | `#0284C7` | Primary action buttons (`+ Upload RDSR`), active navigation, chart bars |
| **Text Primary** | `#0F172A` | Headings, patient names, bold numerical dose values |
| **Text Secondary** | `#475569` | Labels, units, timestamps, scientific disclaimers |
| **Success Green** | `#16A34A` | Verified RDSR status, within-DRL reference badge |
| **Advisory Amber** | `#D97706` | Above-DRL protocol advisory badge |
| **Error Red** | `#DC2626` | Invalid file format or corrupted DICOM alerts |

* **Typography:** `Inter` or `Roboto` with tabular numerals (`font-variant-numeric: tabular-nums`) so dose numbers align cleanly in tables and cards.

---

## 3. Master Layout Structure
1. **Left Sidebar (Fixed `240px`):**
   * **RADSAFE** logo & subtitle (*Patient Dose Tracker*)
   * Navigation Links:
     1. `Dashboard & Patient Search`
     2. `Upload RDSR Report`
     3. `Patient Dose Passport` (Longitudinal History)
     4. `Protocol & DRL Reference`
     5. `Export PDF Reports`
2. **Top Header (`64px`):**
   * Active Patient Context Pill (e.g., *Active Patient: Ahmed Hassan — ID: 29801150104422*)
   * Quick Action Button: **`+ Upload RDSR`**

---

## 4. Core Screens (MVP Wireframe Blueprint)

### Screen 1: `Dashboard & Patient Search`
* **Hero Search Bar:** Prominent input field: *"Enter Unique Patient ID to load longitudinal radiation history..."*
* **Top KPI Row (4 Cards):**
  1. `Total Tracked Patients`
  2. `Total Recorded CT Scans`
  3. `Supported Vendors` (e.g., *Siemens, Philips, GE, Canon*)
  4. `Recent Uploads (This Month)`
* **Recent Patient Activity Table:** Quick-access list of recently updated patient profiles with `Unique Patient ID`, `Patient Name`, `Total Scans`, `Cumulative Dose (mSv)`, and `Last Scan Date`.

### Screen 2: `Upload RDSR Report`
* **Drag-and-Drop Zone:** Dashed container accepting `.dcm` files with clear explanation of vendor-neutral parsing.
* **Optional Patient ID Override Input:** Allows confirming or linking the report to a specific `Unique Patient ID` if hospital IDs differ across facilities.
* **UI States:** Idle $\rightarrow$ Validating & Parsing DICOM $\rightarrow$ Success Summary (showing current scan dose + updated cumulative dose) or Error Alert.

### Screen 3: `Patient Dose Passport` (Longitudinal Profile)
* **Patient Header Banner:** Displays `Full Name`, `Unique Patient ID`, `Age/Sex`, and a primary **`Export PDF Passport`** button.
* **Cumulative Dose KPI Row (3 Highlight Cards):**
  1. `Lifetime Cumulative Effective Dose` (e.g., **`24.6 mSv`** across 4 scans)
  2. `Current Year Cumulative Dose (2026)` (e.g., **`8.2 mSv`**)
  3. `Facilities & Scanners Visited` (e.g., *3 Hospitals / 2 Vendors*)
* **Charts Row (2 Side-by-Side Charts via `Chart.js`):**
  1. **Cumulative Dose Progression (Line Chart):** X-axis = Scan Dates across years; Y-axis = Cumulative $\text{mSv}$.
  2. **Annual Exposure Breakdown (Bar Chart):** Total $\text{mSv}$ per year.
* **Chronological Examination History Table:**
  * Columns: `Scan Date` | `Hospital / Facility` | `Scanner Vendor` | `Protocol` | `CTDIvol (mGy)` | `DLP (mGy.cm)` | `Effective Dose (mSv)` | `DRL Reference`.
* **Scientific Disclaimer Box:** Clear footer note explaining that Effective Dose is a population-based estimate and that no statutory dose limit applies to medically justified patient imaging.