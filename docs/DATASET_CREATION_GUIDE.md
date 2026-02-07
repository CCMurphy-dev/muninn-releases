# Dataset Creation Guide

This guide explains how to create DICOM case datasets for use with Muninn.

## Overview

Muninn expects a specific folder structure with DICOM files and metadata. Each case needs:

1. **DICOM files** - The actual images
2. **case_data.json** - Basic case metadata
3. **STUDY_NOTES.md** - Model answer/teaching points (optional but recommended)
4. **key_slices.json** - Important findings with window/level presets (optional)

## Folder Structure

```
radiology-cases/
├── ct-abdomen/                         # Course/category
│   ├── 01_Acute_Appendicitis/          # Case folder: NN_Diagnosis
│   │   ├── case_data.json
│   │   ├── STUDY_NOTES.md
│   │   ├── key_slices.json             # Optional
│   │   └── Axial_CT/                   # Series folder (any name)
│   │       ├── 0001.dcm
│   │       ├── 0002.dcm
│   │       └── ...
│   └── 02_Small_Bowel_Obstruction/
│       └── ...
└── mri-brain/
    └── 01_Acute_Stroke/
        └── ...
```

## Case Folder Naming

Format: `NN_Diagnosis` or `NN CASEID_Diagnosis`

- `NN` = Position number (01, 02, etc.)
- `Diagnosis` = Primary diagnosis (underscores for spaces)

Examples:
- `01_Acute_Cholecystitis`
- `05_Pancreatitis`
- `12_Liver_Metastases`

## Series Folder Naming

Each series goes in its own subfolder. The folder name becomes the series description shown in the viewer. Use descriptive names:

- `Axial_Abdomen_Pelvis`
- `Coronal_Recon`
- `DWI`
- `T2_FLAIR`
- `Post_Contrast`

---

## Required Files

### 1. case_data.json

#### Minimal Version

```json
{
  "diagnosis": "Acute Appendicitis",
  "history": "RLQ pain, fever",
  "modality": "CT"
}
```

#### Full Version

```json
{
  "diagnosis": "Acute Appendicitis",
  "history": "25-year-old male with 2-day history of RLQ pain, anorexia, low-grade fever",
  "modality": "CT",
  "body_region": "Abdomen",
  "age": "25",
  "gender": "M",
  "difficulty": "Bread & Butter",
  "differential": [
    "Acute appendicitis",
    "Mesenteric adenitis",
    "Crohn's disease",
    "Right ovarian pathology"
  ]
}
```

#### Field Reference

| Field | Required | Description |
|-------|----------|-------------|
| `diagnosis` | Yes | Primary diagnosis |
| `history` | Recommended | Clinical presentation shown to user |
| `modality` | Recommended | CT, MRI, XR, US, etc. |
| `body_region` | No | Abdomen, Brain, Chest, etc. |
| `age` | No | Patient age |
| `gender` | No | M or F |
| `difficulty` | No | Bread & Butter, Intermediate, Advanced |
| `differential` | No | Array of differential diagnoses |

---

### 2. STUDY_NOTES.md (Model Answer)

This file contains the teaching content and model answer. It's displayed after the user submits their answer and used by AI for feedback comparison.

#### Template

```markdown
# [Diagnosis]

**Clinical History:** [Brief clinical presentation]

---

## Key Findings

- Finding 1
- Finding 2
- Finding 3

## Diagnosis

**[Primary diagnosis]**

[Explanation of why this is the diagnosis based on findings]

## Differential Considerations

1. [Alternative 1] - [Why less likely or how to distinguish]
2. [Alternative 2] - [Why less likely or how to distinguish]

## Management

- [Management point 1]
- [Management point 2]

## Teaching Points

- [Key learning point 1]
- [Key learning point 2]
```

#### Example

```markdown
# Acute Appendicitis

**Clinical History:** 25-year-old male with RLQ pain and fever

---

## Key Findings

- Dilated appendix (12mm diameter, normal <6mm)
- Appendiceal wall thickening and enhancement
- Periappendiceal fat stranding
- No appendicolith identified
- No free fluid or abscess

## Diagnosis

**Uncomplicated acute appendicitis**

The combination of appendiceal dilation >6mm with wall thickening and surrounding fat stranding is diagnostic.

## Differential Considerations

1. Mesenteric adenitis - enlarged lymph nodes, normal appendix
2. Crohn's disease - terminal ileal wall thickening, skip lesions
3. Right ovarian pathology - in females, check adnexa

## Management

- Surgical consultation for appendectomy
- IV antibiotics (cefazolin + metronidazole)
- NPO status
- Pain management

## Teaching Points

- Always measure appendix diameter (>6mm is abnormal)
- Look for secondary signs even if appendix not clearly visualized
- Check for complications: perforation, abscess, phlegmon
- Fat stranding alone is nonspecific - need appendiceal abnormality
```

---

### 3. key_slices.json (Optional)

Annotations for important findings. Allows jumping to specific slices with pre-configured window/level settings. These appear as "Key Findings" after the user submits their answer.

#### Simple Format (Recommended for Manual Creation)

```json
[
  {
    "series_index": 0,
    "slice_number": 85,
    "finding": "Dilated appendix (12mm)",
    "window_width": 400,
    "window_level": 40
  },
  {
    "series_index": 0,
    "slice_number": 90,
    "finding": "Periappendiceal fat stranding",
    "window_width": 400,
    "window_level": 40
  }
]
```

#### Field Reference

| Field | Description |
|-------|-------------|
| `series_index` | 0-based index of the series (order folders appear) |
| `slice_number` | 0-based slice number within the series |
| `finding` | Description of the finding |
| `window_width` | Window width for optimal visualization |
| `window_level` | Window center/level |

#### Common Window Presets

| Study Type | Window Width | Window Level |
|------------|--------------|--------------|
| CT Soft Tissue | 400 | 40 |
| CT Lung | 1500 | -600 |
| CT Bone | 2000 | 400 |
| CT Brain | 80 | 40 |
| CT Liver | 150 | 30 |
| MRI T1/T2 | 400-800 | 200-400 |
| MRI DWI | 300-500 | 150-250 |

---

## DICOM Files

### Requirements

- Standard DICOM format (.dcm extension)
- **Must be anonymized** (remove patient identifiers)
- Files should be numbered sequentially: `0001.dcm`, `0002.dcm`, etc.
- One series per folder

### Anonymization

Before exporting DICOMs, ensure they're anonymized. Remove or replace:

- Patient name
- Patient ID
- Date of birth
- Study date
- Institution name
- Referring physician
- Any other PHI

### Anonymization Tools

- **PACS Export** - Most PACS systems have anonymized export options
- **Horos/OsiriX** - Export with anonymization
- **DICOM Anonymizer** - Standalone tool
- **dcm4che** - Command-line anonymization
- **pydicom** - Python library for scripted anonymization

---

## Quick Start

### Step 1: Create Folder Structure

```
my-cases/
└── ct-abdomen/
    └── 01_Acute_Appendicitis/
        └── Axial_CT/
```

### Step 2: Copy Anonymized DICOMs

Place anonymized DICOM files in the series folder, numbered sequentially.

### Step 3: Create case_data.json

```json
{
  "diagnosis": "Acute Appendicitis",
  "history": "RLQ pain, fever",
  "modality": "CT"
}
```

### Step 4: Create STUDY_NOTES.md

Write the model answer with key findings, diagnosis, and teaching points.

### Step 5: (Optional) Create key_slices.json

Add key findings with slice numbers and window settings.

### Step 6: Test in Muninn

1. Open Muninn
2. Set the library path to your cases folder
3. Scan the library
4. Open the case and verify it displays correctly

---

## Advanced Format (MRI with Slice References)

For MRI cases with multiple sequences, Muninn supports an extended format that enables navigation to specific slices within specific sequences.

### Extended Folder Structure

```
radiology-cases/
├── mri-brain/
│   └── 03_Embolic_Infarcts/
│       ├── case_data.json
│       ├── STUDY_NOTES.md
│       ├── key_slices.json
│       ├── DWI/
│       │   └── *.dcm
│       ├── ADC/
│       │   └── *.dcm
│       ├── T2_FLAIR/
│       │   └── *.dcm
│       └── SWI/
│           └── *.dcm
```

### Extended key_slices.json Format

For cases with multiple sequences, use this format to link findings to specific series and slices:

```json
[
  {
    "dicom_series_uid": "DWI",
    "series_label": "DWI",
    "slice": 19,
    "window_width": 390,
    "window_center": 195,
    "label": "restricted diffusion",
    "section": "Key Findings"
  },
  {
    "dicom_series_uid": "T2_FLAIR",
    "series_label": "T2 FLAIR",
    "slice": 12,
    "window_width": 1035,
    "window_center": 517,
    "label": "FLAIR hypersignal",
    "section": "Key Findings"
  }
]
```

| Field | Description |
|-------|-------------|
| `dicom_series_uid` | Maps to the local series folder name |
| `series_label` | Human-readable sequence name (DWI, ADC, T2 FLAIR, etc.) |
| `slice` | 1-based slice number within the series |
| `window_width/center` | Window settings for optimal visualization |
| `label` | Finding description |
| `section` | Category of finding (Key Findings, Image Findings, etc.) |

### Extended case_data.json Format

For richer metadata including series information:

```json
{
  "case_id": "03548",
  "case_number": "Case 3",
  "history": "Presenting with multifocal neuro deficits",
  "difficulty": "Bread & Butter",
  "diagnosis": "Embolic infarcts",
  "series": [
    {
      "series_uid": "DWI",
      "description": "DWI",
      "slice_count": 32
    },
    {
      "series_uid": "ADC",
      "description": "ADC",
      "slice_count": 32
    },
    {
      "series_uid": "T2_FLAIR",
      "description": "T2 FLAIR",
      "slice_count": 32
    }
  ]
}
```

---

## Troubleshooting

### Case not appearing in library

- Check folder naming follows `NN_Diagnosis` pattern
- Ensure case_data.json exists and is valid JSON
- Verify DICOM files have .dcm extension

### Images not loading

- Confirm DICOM files are valid (try opening in another viewer)
- Check file permissions
- Verify files are numbered sequentially

### Wrong window/level

- Adjust using the viewer controls
- Add appropriate settings to key_slices.json
- Default CT is W:400 L:40 (soft tissue)

### Series in wrong order

- Rename series folders with numeric prefix (01_Axial, 02_Coronal)
- Series are sorted alphabetically by folder name
