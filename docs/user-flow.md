# RADSAFE — User Flow

## 1. Main user journey

```text
Authorized user opens RADSAFE
          |
          v
      Dashboard
          |
          v
      Upload RDSR
          |
          v
 File validation and DICOM parsing
          |
          v
 Extract metadata and dose metrics
          |
          v
 Patient matching decision
       /       |        \
      /        |         \
Confirmed   Uncertain   No candidate
match       candidate   found
  |            |           |
  v            v           v
Link to     Match review  Create new
patient     by authorized patient record
  |          user             |
  +------------+--------------+
               |
               v
      Save examination record
               |
               v
      Calculate eligible estimate
               |
               v
      Patient profile/history
               |
               v
      Review timeline and charts
```

## 2. Upload flow

1. User selects a DICOM file.
2. Frontend sends file to backend.
3. Backend validates file and supported report type.
4. Parser extracts supported metadata and dose metrics.
5. Backend returns parsing status, warnings, and a temporary examination summary.
6. User proceeds to patient matching.

## 3. Patient matching flow

### Case A — Strong permitted match
The system finds a candidate based on configured, institution-aware evidence. It may propose the existing RADSAFE patient record. The policy for automatic association must be explicitly approved; otherwise require confirmation.

### Case B — Uncertain match
The system creates a Match Review item. An authorized user reviews the available evidence and chooses:
- Associate with an existing RADSAFE Patient ID.
- Create a new RADSAFE patient record.
- Defer the decision.

### Case C — No candidate
The system creates a new internal RADSAFE Patient ID and links the examination to it.

### Safety rule
Never merge records solely because names or source Patient IDs look alike, especially across institutions. Every manual merge must be logged and reversible.

## 4. Patient profile flow

1. User searches by RADSAFE Patient ID.
2. System opens the patient profile.
3. Header displays internal patient code.
4. Summary displays recorded exam count, date coverage, and cumulative estimate where calculable.
5. History lists every linked examination chronologically.
6. Selecting an examination opens its detailed metrics, source context, and warnings.
7. Chart shows per-exam or cumulative estimates with clear labels.

## 5. Error and exception flows

- Invalid file → explain file could not be read.
- Unsupported DICOM object → explain supported report type.
- RDSR with missing DLP → save eligible metadata if policy permits; mark dose estimate unavailable.
- Unknown anatomy → do not select a guessed coefficient.
- Ambiguous patient → send to match review.
- Duplicate file → show existing processing record and avoid duplicate examination creation.
- Database failure → do not report success; preserve a safe retry path.

## 6. Core user outcome

The user can open one RADSAFE Patient ID and review the patient's recorded CT examination history in one chronological place, while seeing which values were extracted, which were calculated, and which require review.
