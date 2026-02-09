# Scan Import Guide

This guide covers the complete workflow for importing DICOM scans into Muninn, from PACS export to ready-to-use training cases.

## Overview

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        SCAN IMPORT WORKFLOW                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  1. EXPORT          2. ORGANIZE         3. METADATA        4. READY         │
│  ────────────       ────────────        ────────────       ────────────     │
│                                                                              │
│  PACS/Viewer   ──▶  DICOM          ──▶  Case Loader  ──▶  Library          │
│  Export             Organizer           (Metadata)        Browser           │
│                                                                              │
│  Raw DICOM          Sorted by           case_data.json    Practice &        │
│  files              series              key_slices.json   Exam ready        │
│                                         STUDY_NOTES.md                      │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

## Step 1: Export from PACS

### Before You Export

**Patient anonymization is critical.** Ensure all exports are fully anonymized before importing into Muninn.

- Remove patient name, ID, date of birth
- Remove referring physician, institution names
- Consider using a DICOM anonymization tool

### Export Options

Most PACS systems offer these export methods:

| Method | Quality | Notes |
|--------|---------|-------|
| **DICOM Export** | Best | Full metadata, lossless |
| **CD/DVD Burn** | Good | Usually includes DICOM viewer |
| **Network Send** | Best | Direct to folder/DICOM node |

### What You Get

PACS exports typically look like one of these:

```
# Flat dump (common)
export_folder/
├── IMG00001.dcm
├── IMG00002.dcm
├── ...
└── IMG00450.dcm

# UUID-named (common)
export_folder/
├── 1.2.840.113619.2.55.3.604688119.dcm
├── 1.2.840.113619.2.55.3.604688120.dcm
└── ...

# Partially organized (varies by PACS)
export_folder/
├── STUDY_12345/
│   ├── SERIES_001/
│   └── SERIES_002/
└── ...
```

---

## Step 2: Organize with DICOM Organizer

The DICOM Organizer in Muninn Admin sorts messy exports into a clean structure.

### Launch the Organizer

1. Open **Muninn Admin**
2. Click **Organizer** in the sidebar

### Single Case Workflow

For importing one case at a time:

1. **Source folder**: Select the folder containing your PACS export
2. **Output folder**: Select where to create the organized case
   - For new cases: Create a folder like `01_12345_Diagnosis` first
   - Name format: `NN_CaseID_Diagnosis` where NN is a position number
3. **Copy or Move**:
   - **Copy** (recommended): Keeps originals, uses more space
   - **Move**: Deletes originals after organizing
4. Click **Scan** to analyze the DICOM files
5. Review detected series and check/uncheck as needed
6. Click **Organize**

### Output Structure

The organizer creates numbered series folders:

```
01_12345_Acute_Appendicitis/
├── 01_CT_Axial_Abdomen_Pelvis/
│   ├── 001.dcm
│   ├── 002.dcm
│   └── ...
├── 02_CT_Coronal_Reformat/
│   ├── 001.dcm
│   └── ...
└── 03_CT_Sagittal_Reformat/
    └── ...
```

Features:
- Series grouped by SeriesInstanceUID
- Folders named by modality and series description
- Files numbered sequentially by slice position
- Consistent naming across all cases

### Batch Import Workflow

For importing multiple cases at once:

1. Prepare a folder structure with one subfolder per case:
   ```
   batch_import/
   ├── case_001/          # Raw DICOM from PACS
   ├── case_002/
   └── case_003/
   ```
2. In the Organizer, click **Batch**
3. Select the parent folder containing all case subfolders
4. Select an output folder (will create organized subfolders)
5. Review and process each case
6. Rename output folders to proper case names after organizing

---

## Step 3: Add Metadata with Case Loader

The Case Loader creates the metadata files that Muninn needs.

### Required Metadata Files

| File | Purpose |
|------|---------|
| `case_data.json` | Case information (diagnosis, history, modality) |
| `STUDY_NOTES.md` | Model answer in Markdown |
| `key_slices.json` | Annotated findings with slice numbers |

### Launch the Case Loader

1. Open **Muninn Admin**
2. Click **Case Loader** in the sidebar

### Loading a Case

1. Click **Browse** and select the organized case folder
2. The app scans and detects all DICOM series
3. Use the DICOM viewer to review the images

### Filling in Metadata

#### Case Data Tab

| Field | Required | Description |
|-------|----------|-------------|
| Diagnosis | Yes | Primary diagnosis |
| Clinical History | Yes | Presenting complaint, relevant history |
| Modality | Auto | CT, MRI, XR, etc. |
| Body Region | Recommended | Abdomen, Chest, Brain, etc. |
| Difficulty | Recommended | Bread & Butter, Intermediate, Challenging |
| Patient Age | Optional | Age at time of study |
| Patient Gender | Optional | M/F/Other |
| Differential | Recommended | List of differential diagnoses |

Example case_data.json:
```json
{
  "diagnosis": "Acute Appendicitis",
  "clinical_history": "25-year-old male with 24-hour history of RLQ pain, nausea, and fever (38.2°C). WCC 14.2.",
  "modality": "CT",
  "body_region": "Abdomen",
  "difficulty": "Bread & Butter",
  "age": "25",
  "gender": "M",
  "differential": [
    "Acute appendicitis",
    "Mesenteric adenitis",
    "Right ureteric colic",
    "Crohn's disease"
  ]
}
```

#### Study Notes Tab

Write the model answer in Markdown. Include:

1. **Key Findings** - What to see on the images
2. **Diagnosis** - The answer with reasoning
3. **Differential** - Why other diagnoses were excluded
4. **Management** - What happens next
5. **Teaching Points** - Learning objectives

Example STUDY_NOTES.md:
```markdown
# Acute Appendicitis

## Key Findings
- Dilated appendix (12mm diameter) arising from caecal pole
- Periappendiceal fat stranding
- Appendicolith visible at the base
- No free fluid or abscess

## Diagnosis
**Acute uncomplicated appendicitis**

The combination of appendiceal dilation (>6mm), wall thickening,
and periappendiceal inflammatory changes is diagnostic.

## Differential Considerations
- **Mesenteric adenitis**: Would expect enlarged mesenteric lymph nodes,
  normal appendix
- **Crohn's**: No mural stratification or skip lesions seen
- **Ureteric colic**: No hydronephrosis or ureteric calculus

## Management
- Surgical referral for laparoscopic appendicectomy
- IV antibiotics
- NBM, IV fluids

## Teaching Points
1. Always identify the appendix to the tip
2. Normal appendix is <6mm diameter
3. Look for secondary signs: fat stranding, fluid, adenopathy
```

#### Key Slices Tab

Mark important findings on specific slices:

1. Navigate to a slice showing a key finding
2. Adjust window/level for optimal visualization
3. Click **Mark Finding**
4. Add a label describing what to see

Each key slice captures:
- Series index
- Slice number
- Window width/center settings
- Finding label

Example key_slices.json:
```json
[
  {
    "series_index": 0,
    "slice_number": 85,
    "window_width": 400,
    "window_center": 40,
    "finding": "Dilated appendix (12mm) with fat stranding"
  },
  {
    "series_index": 0,
    "slice_number": 82,
    "window_width": 400,
    "window_center": 40,
    "finding": "Appendicolith at appendix base"
  }
]
```

### Saving

Click **Save** to write all three metadata files to the case folder.

---

## Step 4: Organize into Courses

Cases should be organized into courses (folders) within the library structure.

### Library Structure

```
library/
├── on-call-preparation/           # Course type (folder name)
│   ├── ct-abdomenpelvis/          # Course (folder name)
│   │   ├── course.json            # Optional: course metadata
│   │   ├── 01_12345_Appendicitis/
│   │   │   ├── case_data.json
│   │   │   ├── STUDY_NOTES.md
│   │   │   ├── key_slices.json
│   │   │   └── 01_CT_Axial/
│   │   │       └── *.dcm
│   │   └── 02_12346_Diverticulitis/
│   │       └── ...
│   └── plain-films/
│       └── ...
├── frcr-2b-preparation/
│   └── mock-exam-1/
│       └── ...
└── specialty-modules/
    ├── neuro-mri/
    └── msk-xr/
```

### Course Types

Organize courses by training purpose:

| Course Type | Description |
|-------------|-------------|
| `on-call-preparation` | Emergency/on-call cases |
| `frcr-2b-preparation` | Exam preparation |
| `specialty-modules` | Subspecialty training |
| `teaching-files` | Classic teaching cases |

### Course Metadata (Optional)

Add a `course.json` file to set course properties:

```json
{
  "name": "CT Abdomen/Pelvis",
  "description": "On-call CT abdomen cases for registrars",
  "modality": "CT",
  "specialty": ["abdomen", "oncall"],
  "min_level": "ST1",
  "recommended_levels": ["ST1", "ST2", "ST3"]
}
```

Course metadata is set via the **Exam Builder** in Muninn Admin:
1. Open Exam Builder
2. Browse to your library
3. Click the settings icon on a course
4. Set properties and save

---

## Step 5: Verify in Muninn

After importing cases, verify they work correctly:

1. Open **Muninn** (trainee app)
2. Browse to your library folder
3. Click **Scan Library**
4. Find your new course and cases
5. Open a case and verify:
   - Images load correctly
   - All series are detected
   - Clinical history displays
   - Study notes are accessible
   - Key slices navigate correctly

---

## Workflow Summary

### Single Case Import

```
1. Export from PACS (anonymized)
           ↓
2. Create case folder: 01_12345_Diagnosis/
           ↓
3. DICOM Organizer → sort into series folders
           ↓
4. Case Loader → add metadata
           ↓
5. Move to library/course-type/course-name/
           ↓
6. Verify in Muninn
```

### Bulk Import

```
1. Export multiple cases from PACS
           ↓
2. Place in batch folder (one subfolder per case)
           ↓
3. DICOM Organizer (Batch mode) → process all
           ↓
4. Rename output folders to proper case names
           ↓
5. Case Loader (Batch mode) → add metadata to each
           ↓
6. Move batch to library structure
           ↓
7. Verify in Muninn
```

### Creating an Exam from Imported Cases

```
1. Import cases as above
           ↓
2. Exam Builder → select cases
           ↓
3. Order cases for exam
           ↓
4. Save exam config
           ↓
5. Distribute config to trainees
```

---

## Troubleshooting

### Images Don't Load

- Verify DICOM files have `.dcm` extension or are numeric-named
- Check file permissions
- Try re-organizing with the DICOM Organizer

### Case Not Detected

- Ensure folder name matches pattern: `NN_ID_Diagnosis`
- Check for hidden files/folders
- Verify case is inside a course folder

### Metadata Not Showing

- Check `case_data.json` exists and is valid JSON
- Verify file encoding is UTF-8
- Re-save from Case Loader

### Series Order Wrong

- The Organizer numbers series by SeriesNumber DICOM tag
- Manually rename series folders to reorder (01_, 02_, etc.)

---

## Best Practices

### Naming Conventions

- Case folders: `NN_CaseID_Diagnosis` (e.g., `01_12345_Acute_Appendicitis`)
- Keep diagnoses short but descriptive
- Use underscores, not spaces

### Anonymization

- Always anonymize before import
- Double-check burned-in annotations on images
- Remove all patient identifiers from folder names

### Organization

- Group related cases in courses
- Use consistent modality labeling
- Add course metadata for filtering

### Metadata Quality

- Write clinical histories that guide learning
- Include realistic differentials
- Mark all key findings, not just the diagnosis
- Write teaching-focused study notes
