# Muninn Admin User Guide

Muninn Admin is the administrative companion application for Muninn. It provides tools for creating case content, building exams, grading submissions, and managing department training programs.

## Overview

Muninn Admin has four main modes:

| Mode | Purpose |
|------|---------|
| **Case Loader** | Create metadata for DICOM cases |
| **Exam Builder** | Create exam configurations |
| **Exam Marking** | Grade trainee exam submissions |
| **Department** | Manage trainee registries and generate reports |

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

For multi-trainee exams, use a shared network location:

1. **Create shared folder** accessible to all trainees
2. **Place exam config** in the shared folder
3. **Set results_path** to the shared folder
4. **Ensure write permissions** for all trainees

Example paths:
- macOS: `/Volumes/RadiologyShare/Exams/`
- Windows: `\\server\RadiologyShare\Exams\`

---

## Tips

### For Case Creation

- Use consistent naming: `NN_Diagnosis` (e.g., `01_Acute_Appendicitis`)
- Include clinical history that guides the trainee
- Mark key findings that demonstrate the diagnosis
- Write study notes as a model answer, not just facts

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
