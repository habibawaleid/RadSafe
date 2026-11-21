# RADSAFE — Medical Physics Research & Validation Plan

**Role owner:** Member 3 — Medical Physics Researcher  
**Role nature:** Scientific research, DICOM/RDSR domain interpretation, dose methodology, reference-data curation, and independent validation.

## 1. Mission

Ensure that RADSAFE's extracted dose metrics, anatomical classifications, conversion coefficients, DRL references, and displayed scientific explanations are accurate, traceable, and appropriately limited.

The researcher does not simply provide numbers. They provide the scientific rules and evidence that the software team must implement and test.

## 2. Main responsibilities

1. Understand CT dose quantities and their units.
2. Study the DICOM CT RDSR structure and identify relevant content.
3. Collect suitable de-identified or synthetic RDSR samples and document their provenance.
4. Build a data dictionary mapping report concepts to expected application fields.
5. Identify how CTDIvol and DLP are represented, including units and context.
6. Research effective-dose conversion methodology and appropriate coefficient sources.
7. Define anatomical/protocol mapping rules and unresolved cases.
8. Research DRLs and define valid comparison categories.
9. Independently calculate expected results for test cases.
10. Review scientific wording in the interface and exported reports.
11. Document limitations and cases that must not be calculated automatically.

## 3. Research workstreams

### Workstream A — CT dose fundamentals

Prepare concise explanations and references for:
- CTDI and CTDIvol.
- DLP.
- Scan length and the relationship between CTDIvol and DLP.
- Effective dose and its intended use.
- Difference between scanner output indices and patient-specific absorbed dose.
- Difference between dose optimization and dose limits.

**Deliverable:** `ct-dose-metrics.md` or a section in this research plan.

### Workstream B — RDSR structure and data dictionary

For each sample RDSR, record:
- SOP Class UID and modality.
- Study and series identifiers needed for traceability.
- Patient identifiers present (use de-identified values only).
- Institution, station, manufacturer/model when present.
- Protocol/anatomical region.
- CTDIvol value and unit.
- DLP value and unit.
- Whether the value is per irradiation event, series, or total report.
- Missing, repeated, or ambiguous content.
- How the backend parser should interpret it.

**Deliverable:** RDSR field dictionary spreadsheet/CSV plus sample notes.

### Workstream C — Effective-dose methodology

The project may use the relationship:

`Estimated Effective Dose (mSv) = DLP (mGy·cm) × k (mSv/(mGy·cm))`

But the researcher must not approve a coefficient table just because values appear in an initial draft.

For every coefficient, document:
- Exact publication/reference and edition.
- Table/page/section where the value appears.
- Anatomical region and scan coverage.
- Adult or pediatric applicability.
- Whether the coefficient is suitable for the chosen DLP definition.
- Any limitations or assumptions.
- Approval status and version.

Do not assume one k-factor applies to all ages, scanners, protocols, or anatomical coverage. If the region cannot be mapped confidently, return “not calculated — review required”.

**Deliverable:** versioned `k-factor-reference-table.csv` and evidence notes.

### Workstream D — DRL research

DRLs are used for optimization of defined groups of examinations. They are not patient-specific dose limits.

For each proposed reference, capture:
- Organization/country and publication.
- Publication year/version.
- Examination/protocol category.
- Adult/pediatric population.
- Metric (CTDIvol or DLP).
- Value and unit.
- Phantom/population/context where specified.
- Conditions for a valid comparison.

Do not compare an individual examination to a mismatched category or treat a DRL exceedance as proof of harm.

**Deliverable:** `drl-reference-table.csv` and comparison eligibility rules.

### Workstream E — Independent validation

Create test cases with:
- Input DLP and unit.
- Anatomical region.
- Selected coefficient and source.
- Expected effective-dose estimate.
- Rounding rule.
- Expected warnings.
- Reviewer and verification date.

Manually calculate results independently from backend code. Compare backend outputs to expected values and document discrepancies.

**Deliverable:** `dose-validation-cases.csv` and signed-off validation notes.

## 4. Scientific acceptance rules

A calculation is eligible only if:
- DLP is present.
- The unit is known and correctly interpreted.
- The anatomical mapping is supported.
- The coefficient is approved for the intended population/context.
- The coefficient source and version are recorded.

If any required condition is not met, the system must not invent a value. It should show a clear “Not calculated” or “Needs review” status and explain why.

## 5. First 10-day plan

| Day | Task | Deliverable |
|---|---|---|
| 1 | Agree project definitions and scope with team | Scientific terminology sheet |
| 2 | Study CTDIvol, DLP, effective dose and units | CT dose metrics notes |
| 3 | Find and assess de-identified/synthetic CT RDSR samples | Sample inventory |
| 4 | Inspect sample report fields and sequences | RDSR field dictionary draft |
| 5 | Identify CTDIvol/DLP values and their context | Manually checked extraction examples |
| 6 | Research k-factor sources and applicability | Evidence-backed coefficient table draft |
| 7 | Define anatomical mapping and “not calculable” cases | Mapping rules |
| 8 | Research DRL references and comparison conditions | DRL table draft |
| 9 | Prepare independent calculation test cases | Validation dataset |
| 10 | Review parser output with backend and present findings | Scientific review report and open-issues list |

## 6. Handoffs to backend

Provide:
- Exact field mapping and sample evidence.
- Units and conversion rules.
- Expected extraction values for fixtures.
- Approved coefficient table with version.
- Expected calculation results and tolerance.
- Unsupported/ambiguous cases and required warnings.

Backend must not independently guess scientific mappings or coefficients.

## 7. Handoffs to frontend/UI

Provide:
- Approved names and definitions for dose metrics.
- Required units.
- Explanation text for estimates and limitations.
- DRL labels and reference metadata.
- Wording for missing, uncertain, and invalid data states.

## 8. Weekly reporting format

At each team check-in, report:
1. What was researched/completed?
2. Which sources were used?
3. Which values/fields were verified?
4. What remains uncertain?
5. What does backend need next?
6. What should not yet be implemented?

## 9. Recommended source families to investigate

Use primary or authoritative publications and record exact editions/pages:
- DICOM Standard, especially the relevant CT Radiation Dose SR content.
- ICRP publications relevant to effective dose and radiation protection.
- AAPM reports relevant to CT dose metrics and conversion coefficients.
- National/regional DRL publications and recognized radiology/radiation-protection bodies.

The exact reference and its applicability must be checked before a value is approved. A citation to an organization alone is not sufficient evidence for a specific coefficient.
