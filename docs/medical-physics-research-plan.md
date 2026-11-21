# RADSAFE --- Member 3 Role & Medical Physics Research Plan

**Role:** Medical Physics & Research Member\
**Project:** RADSAFE --- Intelligent CT Radiation Dose Monitoring and
Analysis System\
**Duration:** 8 weeks\
**Document version:** 1.0 --- Initial Draft

------------------------------------------------------------------------

## 1. Role Purpose

The Medical Physics & Research Member is responsible for the scientific
foundation of RADSAFE.

This role ensures that the software processes meaningful CT
radiation-dose information, uses documented calculation methods, applies
appropriate examination categories, and presents results with
scientifically accurate interpretation.

This role is not limited to collecting articles. It includes defining
the scientific data requirements, obtaining and cataloguing suitable
RDSR samples, validating extracted information, preparing reference
datasets, and reviewing the scientific accuracy of software outputs.

The member does not need to develop the Backend or Frontend. They must,
however, work closely with both developers and provide precise,
structured information that can be implemented and tested.

## 2. Main Responsibilities

1.  Understand the CT RDSR format and its purpose.
2.  Find suitable public or synthetic CT RDSR test files.
3.  Confirm whether each sample is actually an RDSR rather than an
    ordinary CT image or a screenshot of a dose page.
4.  Identify dose-related information represented in the available
    reports.
5.  Define the scientific meaning and units of the fields required by
    the application.
6.  Research CTDIvol and DLP.
7.  Research Effective Dose estimation and suitable k-factors.
8.  Research DRLs and appropriate group-level comparison methods.
9.  Prepare manually calculated reference examples.
10. Review extracted values against source reports.
11. Prepare scientific test scenarios and expected outcomes.
12. Review scientific wording in the UI and generated reports.
13. Prepare the Medical Physics section of the final report and
    presentation.
14. Document limitations, assumptions, and unsupported cases.

## 3. What This Member Must Understand

### 3.1 CT and Radiation Dose Basics

Understand: - What a CT examination is. - What an acquisition/series
is. - What an irradiation event is. - Why a single examination may
contain multiple irradiation events. - What information a CT scanner can
include in a dose report.

### 3.2 DICOM and RDSR

Understand: - DICOM as a standard for medical imaging and related
information. - The difference between DICOM CT Image objects and DICOM
Structured Reports. - The purpose of Radiation Dose Structured
Reports. - The general structure of an SR document. - The difference
between report-level metadata and individual dose-related content
items. - Why RDSR structures may vary between scanner vendors and
software versions.

**Important:** A CT image file is not an RDSR. A screenshot or Secondary
Capture image of a dose page is not equivalent to a structured RDSR.

### 3.3 CTDIvol

-   Meaning: CT Dose Index Volume.
-   Unit: mGy.
-   It is a standardized scanner output index.
-   It is not a direct measurement of the absorbed dose to an individual
    patient.
-   Its interpretation depends on the CT acquisition and phantom
    context.

### 3.4 DLP

-   Meaning: Dose Length Product.
-   Unit: mGy·cm.
-   It represents CTDIvol multiplied by scan length for the relevant
    acquisition.
-   An examination may contain multiple irradiation events, so the
    member must identify whether the report contains event-level and/or
    accumulated values.

### 3.5 Estimated Effective Dose

A commonly used broad estimate is:

`Estimated Effective Dose (mSv) = DLP (mGy·cm) × k-factor`

The member must: - Find a credible source for the conversion factors. -
Identify the relevant anatomical region and examination category. -
Record the factor, unit convention, reference, publication year, and
limitations. - Explain when a factor cannot safely be selected. -
Prepare independent calculation examples.

Effective Dose is a broad protection quantity and an estimate in this
application. It must not be presented as an exact individual patient
risk, organ dose, or direct patient-specific measurement.

### 3.6 Diagnostic Reference Levels (DRLs)

The member must understand: - The purpose of DRLs in optimization. - The
difference between a DRL and a dose limit. - The importance of
comparable examination groups. - The need to use the correct examination
category and population. - The appropriate CT dose quantities, including
CTDIvol and DLP. - The need to document the source and date of each
reference.

**Critical rule:** DRLs are not individual patient dose limits and must
not be implemented as automatic alarms for individual examinations. The
project should use appropriate group-level analysis and review
methodology. Effective Dose must not be treated as a standard CT DRL
quantity.

------------------------------------------------------------------------

## 4. Required Deliverables

The following deliverables are expected from this role.

  ------------------------------------------------------------------------
  ID                Deliverable       Description        Target
  ----------------- ----------------- ------------------ -----------------
  MP-01             RDSR sample       List of candidate  Week 1
                    catalogue         files, source,     
                                      type, modality,    
                                      and suitability    

  MP-02             RDSR structure    Explanation of the Week 1--2
                    notes             fields/content     
                                      relevant to the    
                                      MVP                

  MP-03             Scientific data   Field name,        Week 2
                    dictionary        meaning, unit,     
                                      source,            
                                      optionality, and   
                                      interpretation     

  MP-04             Manual            Verified examples  Week 2--4
                    calculation       for DLP and        
                    workbook          estimated          
                                      Effective Dose     

  MP-05             k-factor          Factors by         Week 4
                    reference table   applicable         
                                      region/category    
                                      with full          
                                      citations and      
                                      limitations        

  MP-06             Protocol          Rules for grouping Week 5
                    classification    comparable CT      
                    rules             examinations       

  MP-07             DRL reference     Source,            Week 5--6
                    table             population,        
                                      category, metric,  
                                      value, unit, and   
                                      methodology        

  MP-08             Scientific test   Test inputs and    Week 6--7
                    cases             independently      
                                      calculated         
                                      expected results   

  MP-09             Validation report Comparison of      Week 7
                                      software output    
                                      with               
                                      manual/reference   
                                      results            

  MP-10             Research          Scientific         Week 8
                    documentation     background,        
                                      methods,           
                                      limitations, and   
                                      references         
  ------------------------------------------------------------------------

## 5. RDSR Sample Collection Requirements

### 5.1 Priority Source

Start with the IHE/HL7 Radiation Exposure Monitoring (REM) sample
material:

-   IHE REM sample folder:
    https://drive.google.com/drive/u/0/folders/1M3OLxdHU25q8vNKQSr-O3Aip\_\_YOYly0
-   HL7 testing and conformance page:
    https://hl7.org/fhir/uv/radiation-dose-summary/en/testing.html

Also use the official DICOM standard for the structure and examples: -
RDSR examples:
https://dicom.nema.org/medical/dicom/current/output/chtml/part17/sect_UUUU.5.html -
RDSR informative annex:
https://dicom.nema.org/medical/dicom/current/output/chtml/part17/chapter_UUUU.html

Additional DICOM sample catalogues may be used for general DICOM reading
tests, but must not be described as RDSR sources unless the downloaded
object is verified as an RDSR.

### 5.2 Sample Catalogue Format

Create a spreadsheet with these columns:

  -----------------------------------------------------------------------
  Column                              Meaning
  ----------------------------------- -----------------------------------
  Sample ID                           Internal sample label,
                                      e.g. RDSR-001

  Source URL                          Exact public source

  Download Date                       Date obtained

  Modality                            CT or other

  DICOM Object Type                   Structured Report, CT Image,
                                      Secondary Capture, etc.

  RDSR Confirmed?                     Yes / No / Uncertain

  CT RDSR?                            Yes / No

  Readable?                           Yes / No / Not tested

  CTDIvol present?                    Yes / No / Uncertain

  DLP present?                        Yes / No / Uncertain

  Irradiation events                  Count if established

  Privacy review                      Reviewed / Pending

  Notes                               Vendor, structure, missing fields,
                                      issues
  -----------------------------------------------------------------------

### 5.3 Minimum Sample Target

Target 3--5 verified CT RDSR files for the initial proof of concept, if
publicly available. Prefer samples with different report structures or
vendors where possible.

If this target cannot be reached: 1. Record every source searched. 2.
Record why candidate files were unsuitable or unavailable. 3. Escalate
the issue in the first-week team meeting. 4. Agree on a clearly labeled
synthetic or standards-example fallback for parser development. 5. Do
not claim that synthetic examples prove compatibility with real
scanner-generated RDSRs.

### 5.4 Safe Handling

-   Use public, synthetic, or properly anonymized data.
-   Do not upload identifiable patient data to public GitHub.
-   Do not assume that removing the patient name alone anonymizes a
    DICOM file.
-   Review identifiers, dates, UIDs, free-text fields, and private
    attributes.
-   Keep original samples in a controlled local location unless
    redistribution is explicitly permitted.
-   Record licensing and permitted use where available.

------------------------------------------------------------------------

## 6. Scientific Data Dictionary

Prepare a data dictionary for every field that the software may extract
or calculate.

Recommended columns:

-   Field name.
-   Display label.
-   Scientific definition.
-   DICOM source/tag or content concept, when verified.
-   Data type.
-   Unit.
-   Required or optional.
-   Source-reported or calculated.
-   Validation rule.
-   Example value.
-   Scientific reference.
-   Known limitations.

### Initial fields to investigate

  --------------------------------------------------------------------------------
  Field             Meaning                   Unit / Format     Notes
  ----------------- ------------------------- ----------------- ------------------
  Patient           Identifier present in     String            Must be
  identifier        source or anonymized form                   privacy-reviewed

  Study date        Examination date          Date              Handle privacy
                                                                policy

  Modality          Imaging modality          Code/string       Confirm CT

  Protocol name     Scanner/report protocol   String            May vary by vendor
                    label                                       

  Body region       Anatomical region         Category          May need mapping

  CTDIvol           CT dose index volume      mGy               Identify event vs
                                                                accumulated
                                                                context

  DLP               Dose length product       mGy·cm            Identify event vs
                                                                accumulated
                                                                context

  Scan length       Irradiated length, if     cm or documented  Verify source and
                    available                 unit              meaning

  Irradiation event Individual                Structured record One exam may
                    irradiation/acquisition                     contain multiple
                    event                                       events

  Scanner details   Available equipment       String            Optional and
                    metadata                                    privacy-reviewed

  k-factor          Conversion factor used    Source-defined    Must be documented
                    for estimate              units             

  Estimated         Derived estimate          mSv               Not a direct
  Effective Dose                                                patient
                                                                measurement
  --------------------------------------------------------------------------------

Do not assume a field is always present. The dictionary must distinguish
required-for-calculation fields from optional metadata.

------------------------------------------------------------------------

## 7. Scientific Calculation Work

### 7.1 DLP Review

For an applicable acquisition:

`DLP = CTDIvol × scan length`

The member must verify: - The CTDIvol value and unit. - The scan-length
value and unit. - That the two values refer to the same
acquisition/event. - Whether the RDSR already provides DLP. - Whether
the calculation is a validation check or a value derived by the
software.

Do not calculate DLP from unrelated examination-level values.

### 7.2 Effective Dose Review

`Estimated Effective Dose = DLP × k-factor`

Prepare a reference table with: - Anatomical region. - Examination
category. - k-factor. - Unit convention. - Source title and
organization. - Publication year. - Page/table/section if available. -
Applicability and limitations.

Do not select a factor solely because a protocol name contains a
familiar word. The category mapping must be justified.

### 7.3 Manual Calculation Workbook

Prepare at least: - One straightforward valid example. - One example
with multiple irradiation events. - One example with missing scan
length. - One example with missing or unsupported k-factor. - One
invalid-value example.

For every example include: 1. Input values. 2. Units. 3. Formula. 4.
Step-by-step calculation. 5. Expected output. 6. Source/reference. 7.
Interpretation and limitations.

The Backend Developer will use these as independent test oracles.

------------------------------------------------------------------------

## 8. Protocol Classification

Protocol names may vary across scanners and sites. The member must
propose a controlled classification approach.

For each category, document: - Canonical category name. - Common source
protocol names. - Anatomical region. - Contrast/non-contrast distinction
if relevant. - Adult/paediatric applicability if relevant to the chosen
DRL. - Inclusion rules. - Exclusion rules. - Ambiguous cases. -
Reference source.

Do not group two examinations merely because their names are similar.
Uncertain cases should remain unclassified until reviewed.

------------------------------------------------------------------------

## 9. DRL Research Requirements

Create a DRL reference table with:

-   Reference ID.
-   Source organization.
-   Publication title.
-   Publication year.
-   Country/region.
-   Adult or paediatric population.
-   Examination category.
-   CTDIvol value and unit, if provided.
-   DLP value and unit, if provided.
-   Statistical basis (e.g. median/percentile) as specified by the
    source.
-   Scanner/protocol context if specified.
-   Page/table/section.
-   Applicability notes.
-   Access URL.
-   Date accessed.

### Scientific review rules

1.  Use an authoritative and traceable reference.
2.  Do not mix adult and paediatric references.
3.  Do not mix unlike examination categories.
4.  Do not invent missing reference values.
5.  Record the statistic used by the source.
6.  Document how the project statistic is calculated.
7.  Explain when a group has insufficient data.
8.  Treat a finding as a prompt for optimization review, not a clinical
    verdict.
9.  Do not implement per-patient DRL alarms.
10. Do not use Effective Dose as a standard CT DRL metric.

Useful starting reference: - ICRP Publication 135:
https://www.icrp.org/publication.asp?id=ICRP+Publication+135

The full publication may not be freely accessible. Do not claim that the
entire publication is free if only its abstract or summary is
accessible. Record exactly which material was reviewed.

Other useful starting points: - IAEA Radiation Protection of Patients:
https://www.iaea.org/resources/rpop - AAPM Reports:
https://www.aapm.org/pubs/reports/ - DICOM standard:
https://www.dicomstandard.org/

------------------------------------------------------------------------

## 10. Eight-Week Work Roadmap

### Week 1 --- Understand RDSR and Find Samples

**Learning** - CT dose basics. - DICOM vs DICOM SR vs RDSR. - CTDIvol
and DLP definitions. - RDSR purpose and general structure.

**Tasks** - Read the official DICOM RDSR examples. - Explore the IHE/HL7
REM sample folder. - Download candidate files where permitted. - Verify
whether each candidate is a CT RDSR. - Start the sample catalogue. -
Identify the first fields needed by the MVP.

**Deliverables** - Sample catalogue v1. - RDSR vs CT Image
explanation. - Initial field list. - Source list. - Blocker report if no
usable sample is found.

**Team interaction** - Explain to Backend which sample should be used
for the first parser test. - Explain which dose fields appear to be
available. - Tell Frontend which values can be shown in the initial
Results screen and which are optional.

### Week 2 --- Data Dictionary and Manual Calculations

**Learning** - RDSR content organization. - CTDIvol and DLP units and
interpretation. - Irradiation-event concepts. - Effective Dose
estimation basics.

**Tasks** - Inspect available RDSR samples with a suitable DICOM
viewer/parser in coordination with Backend. - Record where target values
are represented. - Build the scientific data dictionary. - Prepare
manual DLP examples. - Find candidate k-factor references. - Identify
fields that may be missing or vendor-dependent.

**Deliverables** - Data Dictionary v1. - Sample inspection notes. -
Manual calculation examples. - k-factor source shortlist. - List of
unsupported/uncertain fields.

**Team interaction** - Review the proposed database fields with
Backend. - Review the Results Page labels and units with Frontend.

### Week 3 --- Validate Extracted Data

**Learning** - How to compare source report values with extracted
software values. - Common parsing and unit interpretation problems.

**Tasks** - Use the sample set agreed by the team. - Compare parsed
fields with the source reports. - Record event-level vs
examination-level values. - Identify missing, repeated, or ambiguous
values. - Prepare expected values for parser tests. - Review how the
application communicates incomplete reports.

**Deliverables** - Extraction verification table. - Parser test
scenarios. - Data-quality rules. - Updated data dictionary.

### Week 4 --- Effective Dose and k-Factors

**Learning** - Effective Dose as a protection quantity. - Limitations of
DLP-to-Effective-Dose conversion. - Region-specific conversion factors
and applicability.

**Tasks** - Select an appropriate source for each MVP category. - Record
exact factors and citations. - Verify units and calculation
conventions. - Prepare independent calculation cases. - Review Backend
calculation outputs.

**Deliverables** - k-factor reference table. - Manual calculation
workbook. - Calculation validation report v1. - Unsupported-category
rules.

### Week 5 --- Protocol Classification

**Learning** - Why comparable examination groups are important. -
Protocol naming variability. - Factors affecting comparability.

**Tasks** - Propose canonical MVP protocol categories. - Map source
protocol names to canonical categories where justified. - Identify
ambiguous records. - Define grouping inclusion/exclusion rules. - Review
the proposed protocol database model.

**Deliverables** - Protocol classification table. - Mapping rules. -
Ambiguous-case list. - Grouping methodology notes.

### Week 6 --- DRL Research and Group Analysis

**Learning** - DRL purpose and interpretation. - Appropriate CT
metrics. - Group-level statistics. - Limitations of comparing different
populations or protocols.

**Tasks** - Identify authoritative DRL references relevant to the chosen
categories. - Record population and examination category. - Record
CTDIvol/DLP reference values and statistical basis. - Review the
software's group comparison methodology. - Check that individual
examinations are not labeled as exceeding a patient safety limit.

**Deliverables** - DRL reference table. - Source citations. - Group
comparison review. - DRL interpretation text for the UI.

### Week 7 --- Scientific Validation

**Tasks** - Run the agreed sample set through the integrated
application. - Compare extracted values with source reports. -
Independently recalculate DLP/Effective Dose examples. - Check units and
rounding. - Review group statistics and DRL matching. - Record defects
and severity. - Verify report wording and references.

**Deliverables** - Scientific Validation Report. - Completed
expected-results table. - Defect list. - Approved scientific labels and
limitations.

### Week 8 --- Documentation and Presentation

**Tasks** - Finalize scientific background. - Finalize methods and data
sources. - Document supported RDSR subset. - Document limitations and
future work. - Prepare figures/tables for the research book. - Prepare
the Medical Physics part of the final presentation. - Rehearse
explanations of CTDIvol, DLP, Effective Dose, and DRLs.

**Deliverables** - Final scientific documentation. - References list. -
Sample data catalogue. - Validation summary. - Presentation slides. -
Known limitations section.

------------------------------------------------------------------------

## 11. Daily Working Routine

Suggested daily work rhythm:

1.  Review the current week's scientific target.
2.  Complete one focused research or sample-inspection task.
3.  Record the source and exact location of evidence.
4.  Update the relevant table or document.
5.  Send the Backend/Frontend members any implementation-relevant
    findings.
6.  Raise uncertainties rather than guessing.
7.  Prepare a short update for the daily team meeting.

### Daily meeting update format

-   Completed:
-   Evidence/source:
-   Findings:
-   Uncertainties/blockers:
-   What Backend needs:
-   What Frontend needs:
-   Next task:

## 12. Definition of Done for Member 3 Tasks

A research task is complete only when: - The source is identified. - The
relevant statement or value is recorded accurately. - The unit and
context are included. - Applicability is explained. - Limitations are
documented. - The information is communicated to the relevant
developer. - Another team member can understand and reproduce the
finding.

A file is not considered a verified RDSR sample merely because its
filename ends in `.dcm`.

A calculation is not considered validated merely because the software
displays a plausible value. It must be independently checked against a
documented formula and input context.

## 13. Recommended Tools

-   DICOM viewer such as a suitable DICOM inspection tool approved by
    the team.
-   Spreadsheet software for sample catalogues and calculation tables.
-   Official DICOM standard.
-   IHE/HL7 sample resources.
-   ICRP, IAEA, and AAPM references.
-   GitHub for versioned documentation, excluding sensitive data.
-   Figma review access to comment on scientific labels and content.

The Medical Physics member does not need to become a Java or Frontend
developer. They do need to understand enough about the software workflow
to identify where scientific assumptions enter the system.

## 14. Final Handover Package

At the end of the project, Member 3 should hand over:

1.  Verified sample catalogue.
2.  RDSR structure notes.
3.  Scientific data dictionary.
4.  k-factor reference table.
5.  Manual calculation workbook.
6.  Protocol classification rules.
7.  DRL reference table.
8.  Scientific test cases.
9.  Scientific validation report.
10. Scientific references and limitations.
11. Final presentation content.

## 15. Recommended Scientific Sources

### DICOM and RDSR

-   DICOM RDSR examples:
    https://dicom.nema.org/medical/dicom/current/output/chtml/part17/sect_UUUU.5.html
-   DICOM RDSR informative annex:
    https://dicom.nema.org/medical/dicom/current/output/chtml/part17/chapter_UUUU.html
-   IHE/HL7 REM samples:
    https://drive.google.com/drive/u/0/folders/1M3OLxdHU25q8vNKQSr-O3Aip\_\_YOYly0
-   HL7 testing page:
    https://hl7.org/fhir/uv/radiation-dose-summary/en/testing.html

### Dose and Radiation Protection

-   ICRP Publication 135:
    https://www.icrp.org/publication.asp?id=ICRP+Publication+135
-   IAEA Radiation Protection of Patients:
    https://www.iaea.org/resources/rpop
-   AAPM Reports: https://www.aapm.org/pubs/reports/

Use primary or authoritative sources wherever possible. Verify the exact
publication, table, population, and context before transferring any
numerical reference value into the software.
