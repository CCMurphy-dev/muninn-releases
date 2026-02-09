# Muninn Admin User Guide

Muninn Admin is the administrative companion application for Muninn. It provides tools for creating case content, building exams, grading submissions, and managing department training programs.

## First Launch Setup

On first launch, Muninn Admin prompts you to select your **Department Root Folder**. This is the shared network folder that contains all your department's training resources.

### Setting Up

1. Launch Muninn Admin for the first time
2. The "Welcome to Muninn Admin" dialog appears
3. Click **Browse** and select your department's shared folder
4. The app shows the derived paths that will be used:
   - `library/` - Case library for DICOM courses
   - `registry/` - Trainee registry files
   - `exams/` - Exam configurations and results
   - `reports/` - Generated training reports
5. Click **Continue** to save the configuration

You can also click **Skip for Now** to configure this later via the Department Settings.

### Changing the Department Folder

To change the department folder after initial setup:

1. Go to **Department** mode
2. Click the settings gear icon
3. Update paths as needed

---

## Overview

Muninn Admin has five main modes:

| Mode | Purpose |
|------|---------|
| **Organizer** | Sort messy DICOM exports into organized series folders |
| **Case Loader** | Create metadata for DICOM cases |
| **Exam Builder** | Create exam configurations |
| **Exam Marking** | Grade trainee exam submissions |
| **Department** | Manage trainee registries and generate reports |

---

## DICOM Organizer

Organize messy DICOM exports from PACS systems into structured series folders.

### Why Use This?

DICOM exports from PACS viewers often come as:
- Flat folders with hundreds of files
- Files named with UUIDs or random numbers
- No logical grouping by series
- Files not sorted by slice position

Muninn expects cases organized by series folders with numbered slices.

### Workflow

1. **Select source folder** - The folder containing your messy DICOM export
2. **Select output folder** - Where to create the organized structure
3. **Choose copy or move**:
   - **Copy**: Keeps original files (safer, uses more disk space)
   - **Move**: Deletes originals after organizing (faster, saves space)
4. **Click Scan** - The organizer reads DICOM headers to identify series
5. **Review detected series** - Check/uncheck series to include
6. **Click Organize** - Files are sorted into series folders

### Output Structure

```
output_folder/
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

Files are:
- Grouped by SeriesInstanceUID
- Folders named by modality and series description
- Numbered sequentially by slice position

### Batch Mode

For processing multiple cases at once:

1. Click **Batch** to enable batch mode
2. Select a folder containing multiple case subfolders
3. Each subfolder should contain raw DICOM exports
4. Process each case in turn with the Previous/Next buttons
5. Rename organized folders to match naming convention (`NN_ID_Diagnosis`)

> **Tip**: For the complete scan import workflow from PACS to Muninn, see the [Scan Import Guide](SCAN_IMPORT_GUIDE.md).

---

## Case Loader

Create the metadata files that Muninn needs to display cases.

### What It Creates

For each case folder, the Case Loader generates:

| File | Description |
|------|-------------|
| `case_data.json` | Case metadata (diagnosis, history, modality, demographics) |
| `key_slices.json` | Annotated findings with slice numbers and window settings |
| `STUDY_NOTES.md` | Model answer in Markdown format |

### Workflow

1. **Select a case folder** - Browse to a folder containing DICOM series subfolders
2. **Review detected series** - The app scans and lists all DICOM series
3. **Fill in case metadata**:
   - Diagnosis (required)
   - Clinical history (required)
   - Modality (CT, MR, XR, etc.)
   - Body region
   - Patient demographics (age, gender)
   - Difficulty level
   - Differential diagnoses
4. **Mark key findings**:
   - Scroll through images in the DICOM viewer
   - Click "Mark Finding" to annotate important slices
   - Each finding captures: slice number, series, window/level, description
5. **Write study notes**:
   - Use the markdown editor
   - Include: explanation, differential considerations, management, teaching points
6. **Save** - Generates all three files in the case folder

### Batch Processing

Process multiple cases in a folder:

1. Click **Batch** button
2. Select a parent folder containing multiple case subfolders
3. Navigate between cases using Previous/Next buttons
4. Progress indicator shows completion status

### Course Metadata (course.json)

In addition to case-level metadata, you can create course-level metadata to control how courses appear to trainees and enable filtering.

**Create a `course.json` file** in each course folder:

```json
{
  "name": "CT Abdomen Emergencies",
  "description": "On-call CT abdomen cases for junior trainees",
  "modality": "CT",
  "specialty": ["abdomen", "oncall"],
  "min_level": "ST1",
  "recommended_levels": ["ST1", "ST2", "ST3"]
}
```

**Fields:**

| Field | Description |
|-------|-------------|
| `name` | Display name (overrides folder name) |
| `description` | Short description shown to trainees |
| `modality` | CT, MRI, XR, US, or "mixed" |
| `specialty` | Array of specialties: general, oncall, neuro, msk, chest, abdomen, paeds, breast, cardiac, ir |
| `min_level` | Minimum trainee level (ST1-ST5, Fellow, Consultant) |
| `recommended_levels` | Array of levels this course is designed for |

**Behavior:**

- If `course.json` exists, its metadata is used for filtering
- If not, the app infers modality from folder names (current behavior)
- Courses without `min_level` have no level restrictions
- Trainees can filter courses by modality, specialty, and their training level

**Example folder structure:**

```
library/
├── on-call-preparation/
│   ├── ct-abdomen/
│   │   ├── course.json          ← Course metadata
│   │   ├── 01_Appendicitis/
│   │   └── 02_Diverticulitis/
│   └── plain-films/
│       ├── course.json
│       └── 01_Pneumothorax/
└── frcr-2b-preparation/
    └── mock-exam-1/
        ├── course.json          ← min_level: "ST4"
        └── 01_Complex_Case/
```

### Multi-Component Cases

For cases with multiple studies (e.g., pre/post contrast, different time points):

- The loader automatically detects component subfolders
- Switch between components using tabs
- Each component can have its own key slices
- Metadata is saved per component

---

## Exam Builder

Create exam configuration files for Muninn's exam mode.

### What It Creates

A JSON configuration file containing:
- Exam name
- Results file path (for trainee submissions)
- Optional trainee registry path
- List of cases with paths and diagnoses

### Workflow

1. **Browse your case library**:
   - Click Browse and select the root folder of your DICOM library
   - The scanner recursively finds all DICOM cases at any folder depth
   - Cases are grouped by course/folder structure
   - Modality is auto-detected from metadata or folder names

2. **Preview cases**:
   - Click any case to view it in the built-in DICOM viewer
   - Use series buttons to switch between sequences (MRI)
   - Verify the case is appropriate for your exam

3. **Select cases**:
   - Check the checkbox next to each case to include it
   - Use "Select All" / "Clear" buttons for bulk selection
   - Selected cases appear in the right panel

4. **Order cases**:
   - Use up/down arrows to reorder cases
   - Cases appear to trainees in this exact order

5. **Configure exam settings**:
   - **Exam Name**: Displayed to trainees and in results
   - **Results Path**: Where trainee submissions are saved (use network share for multi-trainee exams)
   - **Include trainee registry**: Links a registry for trainee name selection

6. **Save**:
   - Click "Save Exam Config"
   - Choose save location
   - Distribute the config file to trainees

### Example Exam Config

```json
{
  "exam_name": "ST1 On-Call Exam Feb 2026",
  "results_path": "/Volumes/Radiology/Exams/results.json",
  "trainee_registry_path": "/Volumes/Radiology/registry.json",
  "name": "ST1 On-Call Exam Feb 2026",
  "course_type": "exam",
  "modality": "mixed",
  "cases": [
    {
      "id": "01_Acute_Appendicitis",
      "path": "/Volumes/Radiology/Library/Abdomen/01_Acute_Appendicitis",
      "diagnosis": "Acute appendicitis"
    }
  ]
}
```

---

## Exam Marking

Grade trainee submissions from completed exams.

### Workflow

1. **Load exam**:
   - Click "Load Exam"
   - Select the exam config JSON file
   - Results are automatically loaded from the results_path

2. **Select candidate**:
   - Choose a trainee from the dropdown
   - Their submissions for all cases are loaded

3. **Review submissions**:
   - Navigate through cases using the case list
   - View the DICOM images alongside their written answers
   - See their: findings, diagnosis, differential, management (if applicable)
   - See time taken per case

4. **Mark each answer**:
   - Score: 0-10 scale
   - Feedback: Written comments for the trainee
   - Marks are saved automatically

5. **Generate summary**:
   - Click "Generate Summary" for per-candidate statistics
   - Shows: total score, average, time taken, per-case breakdown

6. **Export**:
   - **Save Marks**: JSON file with all marks and feedback
   - **Export CSV**: Spreadsheet format for reporting

### Marking File Format

```json
{
  "exam_name": "ST1 On-Call Exam",
  "marked_at": "2026-02-08T10:30:00Z",
  "marks": [
    {
      "username": "John Smith",
      "case_id": "case_01",
      "score": 8,
      "feedback": "Good identification of findings. Consider mentioning...",
      "marked_at": "2026-02-08T10:25:00Z"
    }
  ]
}
```

---

## Department Training

Manage trainee records and generate training reports.

### Trainee Registry

Create and maintain a list of trainees in your department.

#### Creating a Registry

1. Go to Department → Registry tab
2. Click "Create Registry"
3. Enter department name
4. Choose save location

#### Adding Trainees

**Manually:**
1. Click "Add Trainee"
2. Fill in details:
   - Name (required)
   - Email
   - Training level (ST1-ST5, Fellow, Consultant)
   - Supervisor
   - Active status
3. Click Save

**From CSV:**
1. Click "Import CSV"
2. Select a CSV file with columns: `name`, `email`, `level`, `supervisor`
3. Trainees are added to the registry

#### Registry Format

```json
{
  "department": "St Mary's Radiology",
  "updated_at": "2026-02-08T09:00:00Z",
  "trainees": [
    {
      "id": "jsmith",
      "name": "John Smith",
      "email": "j.smith@hospital.nhs.uk",
      "level": "ST3",
      "supervisor": "Dr A. Jones",
      "active": true
    }
  ]
}
```

### Training Dashboard

View department-wide statistics and individual progress.

#### Generating Reports

1. Go to Department → Dashboard tab
2. Set the date range (from/to)
3. Click "Generate Report"
4. The report scans all exam results in the configured exams folder

#### Report Contents

- **Summary statistics**: Active trainees, total cases, total exams, average score
- **Per-trainee breakdown**:
  - Cases completed
  - Exams taken
  - Average exam score
  - Last activity date
  - Modality breakdown

#### Viewing Individual Progress

1. Click on a trainee row
2. Modal shows their detailed history:
   - All exams taken
   - Scores per exam
   - Time spent
   - Progress over time

#### Exporting Reports

- **Save Report**: JSON format for programmatic analysis
- **Export CSV**: Spreadsheet format for sharing

---

## Integration with Muninn

### Data Flow

```
MUNINN ADMIN                           MUNINN (Trainee)
─────────────────────────────────────────────────────────
Case Loader                            Practice Mode
  └─▶ case_data.json ─────────────────▶ Case display
  └─▶ key_slices.json ────────────────▶ Key findings
  └─▶ STUDY_NOTES.md ─────────────────▶ Model answers

Exam Builder                           Exam Mode
  └─▶ exam_config.json ───────────────▶ Exam loading
                                          │
Exam Marking ◀────────────────────────────┘
  └─◀ results.json                     Submissions

Department
  └─▶ trainee_registry.json ──────────▶ Name dropdown
```

### Network Setup for Exams

For multi-trainee exams, use your department's shared folder:

1. **Configure department folder** on first launch (or via Department Settings)
2. **Exam configs** are saved to `department_root/exams/`
3. **Results files** are saved to `department_root/exams/`
4. **Trainee registry** is loaded from `department_root/registry/trainee_registry.json`

All trainees selecting the same department folder will:
- See the same case library
- Submit to the same results files
- Appear in the trainee dropdown

Example paths:
- macOS: `/Volumes/RadiologyShare/` (department root)
- Windows: `\\server\RadiologyShare\` (department root)

---

## Tips

### For Case Creation

- Use consistent naming: `NN_Diagnosis` (e.g., `01_Acute_Appendicitis`)
- Include clinical history that guides the trainee
- Mark key findings that demonstrate the diagnosis
- Write study notes as a model answer, not just facts

### For Course Organization

- Create `course.json` files to enable filtering by specialty and level
- Set `min_level` for advanced courses (e.g., FRCR 2B prep) to prevent junior trainees from being overwhelmed
- Use meaningful `specialty` tags so trainees can find relevant courses
- Courses without metadata are shown to all trainees (no restrictions)

### For Exam Building

- Test the exam config yourself before distributing
- Use absolute paths that work on all trainee machines
- Include a mix of difficulties
- Consider using a trainee registry for consistent identification

### For Marking

- Provide constructive feedback, not just scores
- Be consistent in scoring criteria across candidates
- Use the summary feature to identify patterns

### For Department Management

- Keep the trainee registry up to date
- Generate reports regularly to track progress
- Archive old exam results for longitudinal tracking
