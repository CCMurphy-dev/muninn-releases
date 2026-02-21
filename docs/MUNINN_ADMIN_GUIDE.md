# Muninn Admin User Guide

Muninn Admin is the administrative companion application for Muninn. It provides tools for creating case content, building playlists and exams, grading submissions, and managing department training programs.

## First Launch Setup

On first launch, Muninn Admin prompts you to select your **Department Root Folder**. This is the shared network folder that contains all your department's training resources.

### Setting Up

1. Launch Muninn Admin for the first time
2. The "Welcome to Muninn Admin" dialog appears
3. Click **Browse** and select your department's shared folder
4. The app shows the derived paths that will be used:
   - `library/` - Case library for DICOM courses
   - `library/playlists/` - Curated case collections for trainees
   - `exams/` - Exam configurations
   - `reports/` - Generated training reports
   - `tracking/` - SQLite database for trainees, practice, and exam submissions
5. Click **Continue** to save the configuration

You can also click **Skip for Now** to configure this later via the Department Settings.

### Changing the Department Folder

To change the department folder after initial setup:

1. Go to **Department** mode
2. Click the settings gear icon
3. Update paths as needed

---

## Overview

Muninn Admin has seven main modes:

| Mode | Purpose |
|------|---------|
| **Organizer** | Sort messy DICOM exports into organized series folders |
| **Case Loader** | Create metadata for DICOM cases |
| **Playlist Builder** | Create curated case collections for trainees |
| **Exam Builder** | Create exam configurations |
| **Exam Marking** | Grade trainee exam submissions |
| **Department** | Manage trainee registries, PINs, audit logs, backups, and reports |
| **Case Database** | View per-case analytics from practice and exam activity |

### Key Features

- **PIN Authentication**: Require PINs for exam access to verify trainee identity
- **Audit Logging**: All critical actions are logged for compliance and troubleshooting
- **Automated Backups**: Daily backups with manual backup/restore capability
- **Index Staleness Detection**: Warns when case metadata is outdated

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
├── case_data.json                 ← Series metadata (auto-generated)
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

The organizer automatically creates `case_data.json` containing:
- Series metadata (UIDs, descriptions, slice counts)
- Window/level defaults from DICOM headers
- Image dimensions and bit depth
- Original source path and timestamp

This enables instant case loading in Muninn without re-reading DICOM headers.

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

For each case folder, the Case Loader generates or updates:

| File | Description |
|------|-------------|
| `case_data.json` | Case metadata (diagnosis, history, modality, demographics) + preserved series data |
| `key_slices.json` | Annotated findings with slice numbers and window settings |
| `STUDY_NOTES.md` | Model answer in Markdown format |

> **Note:** If the case was imported via the DICOM Organizer, `case_data.json` already contains series metadata. The Case Loader preserves this data when adding teaching content (diagnosis, history, key slices, etc.).

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
   - Subspecialty (for categorization)
   - Author (your name)
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
5. Subspecialty and author persist across cases in batch mode

### Multi-Component Cases

For cases with multiple studies (e.g., pre/post contrast, different time points):

- The loader automatically detects component subfolders
- Switch between components using tabs
- Each component can have its own key slices
- Metadata is saved per component

---

## Playlist Builder

Create curated case collections that trainees can access. **Playlists are the primary way trainees access cases** - they provide a cleaner interface than folder browsing and allow flexible organization.

### Why Use Playlists?

- **Flexible organization**: Cases can live anywhere in the library (consultant folders, course folders, etc.)
- **Multiple access paths**: A case can appear in multiple playlists
- **Level-appropriate content**: Set minimum training levels per playlist
- **Curated experience**: Add per-case notes and control the order
- **No duplication**: Cases stay in their original location; playlists just reference them

### Accessing Playlist Builder

Click **Playlists** in the navigation bar to enter Playlist Builder mode.

### Creating a Playlist

1. **Click "New Playlist"** or select an existing playlist to edit

2. **Fill in playlist metadata**:
   - **Name** (required): Display name shown to trainees
   - **Description**: What this playlist covers
   - **Author**: Your name
   - **Modality**: CT, MRI, XR, US, or "mixed"
   - **Specialty**: Array of specialties (oncall, neuro, msk, chest, abdomen, etc.)
   - **Min Level**: Minimum trainee level (ST1-ST5, Fellow, Consultant)
   - **Recommended Levels**: Array of levels this playlist is designed for

3. **Add cases from the Case Database**:
   - The left panel shows all indexed cases
   - Use filters to narrow down (modality, subspecialty, author)
   - Click a case to preview it
   - Click **Add to Playlist** to include it

4. **Organize cases**:
   - Drag to reorder cases
   - Add per-case notes (optional) - these appear to trainees as context
   - Remove cases by clicking the X

5. **Save**:
   - Click **Save Playlist**
   - Playlists are saved to `library/playlists/`

### Playlist File Format

Playlists are stored as JSON files in `library/playlists/`:

```json
{
  "name": "On-Call Essentials",
  "description": "Must-know cases for junior trainees on call",
  "author": "Dr Smith",
  "created_at": "2026-02-14T10:00:00Z",
  "modality": "mixed",
  "specialty": ["emergency", "oncall"],
  "min_level": "ST1",
  "recommended_levels": ["ST1", "ST2"],
  "cases": [
    {
      "path": "@library/dr-smith/neuro/01_Acute_Stroke",
      "notes": "Classic MCA territory infarct"
    },
    {
      "path": "@library/ct-courses/ct-abdomen/01_Acute_Appendicitis"
    }
  ]
}
```

### Path Formats

Playlist case paths support three formats:

| Format | Example | Best For |
|--------|---------|----------|
| `@library/...` | `@library/ct-courses/case_01` | Portable across departments |
| `../...` | `../shared/case_01` | Relative to playlist file |
| Absolute | `/Volumes/Radiology/case_01` | Local use only |

**Recommendation**: Use `@library/` paths for portability. These resolve to the library root regardless of where the department folder is mounted.

### Access Control via Playlists

**Case visibility is controlled through playlists.** Cases are only accessible to trainees if they appear in at least one playlist. This enables:

- **Private consultant folders**: Store cases in `library/dr-smith/` - they remain invisible until added to a playlist
- **Staged publishing**: Create cases, review them, then add to playlists when ready
- **Multiple audiences**: Same case can appear in "ST1 Basics" and "FRCR 2B Prep" playlists

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
   - Cases appear to trainees in this exact order (shuffled during exam)

5. **Configure exam settings**:
   - **Exam Name**: Displayed to trainees and in results
   - **Eligible Levels**: Which trainee levels can take this exam
   - Results are saved to the tracking database automatically

6. **Save**:
   - Click "Save Exam Config"
   - Exams are saved to `department_root/exams/`
   - Share the exam name with trainees

### Example Exam Config

```json
{
  "exam_name": "ST1 On-Call Exam Feb 2026",
  "results_path": "/Volumes/Radiology/tracking/muninn_tracking.db",
  "name": "ST1 On-Call Exam Feb 2026",
  "course_type": "exam",
  "modality": "mixed",
  "eligible_levels": ["ST1", "ST2"],
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
   - Results are automatically loaded from the tracking database

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
   - Marks are saved automatically to the tracking database

5. **Generate summary**:
   - Click "Generate Summary" for per-candidate statistics
   - Shows: total score, average, time taken, per-case breakdown

6. **Export**:
   - **Save Marks**: JSON file with all marks and feedback
   - **Export CSV**: Spreadsheet format for reporting

---

## Department Training

Manage trainee records and generate training reports.

### Trainee Registry

Create and maintain a list of trainees in your department.

#### Creating a Registry

The registry is automatically created when you set up your department folder. Trainees are stored in `tracking/muninn_tracking.db`.

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

#### Trainee Data

Trainees are stored in the `trainees` table of `tracking/muninn_tracking.db`:

| Column | Description |
|--------|-------------|
| `id` | Unique identifier (auto-generated or from CSV) |
| `name` | Display name |
| `email` | Contact email |
| `level` | Training level (ST1-ST5, Fellow, Consultant) |
| `supervisor` | Supervising consultant |
| `active` | Whether trainee appears in selection dropdowns |
| `pin_hash` | Hashed PIN for exam authentication (optional) |

#### Managing Trainee PINs

PINs provide exam access control. When a trainee has a PIN set, they must enter it before starting any exam.

**Setting a PIN:**
1. In the Trainee Registry, find the trainee row
2. Click the **PIN** column button (shows "Not Set" or "Set")
3. Enter a 4-6 digit PIN in the dialog
4. Confirm the PIN by entering it again
5. Click **Save PIN**

**Clearing a PIN:**
1. Open the PIN dialog for the trainee
2. Click **Clear PIN**
3. Confirm the action

**When to use PINs:**
- **Formal assessments** (ARCP evidence): Require PINs to ensure identity verification
- **Practice mode**: PINs are not required for practice, only exams
- **Mixed departments**: Set PINs only for trainees taking formal exams

> **Security note:** PINs are stored as Argon2 hashes in the database, not in plain text. The original PIN cannot be recovered - only reset by an administrator.

### Training Dashboard

View department-wide statistics and individual progress.

#### Generating Reports

1. Go to Department → Dashboard tab
2. Set the date range (from/to)
3. Click "Generate Report"
4. The report queries activity data from `tracking/muninn_tracking.db`:
   - Practice attempts from the `practice_attempts` table
   - Exam submissions and marks from the `exam_submissions` and `marking_entries` tables

#### Report Contents

- **Summary statistics**: Active trainees, total cases (practice + exam), total exams, average score
- **Per-trainee breakdown**:
  - Cases completed (total)
  - Practice cases (with self-rating if provided)
  - Exam cases
  - Exams taken
  - Average exam score
  - Total time spent
  - Last activity date

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

## Case Database

View per-case analytics across your entire library. This shows how cases are being used in practice and exams.

### Accessing Case Database

Click **Database** in the navigation bar to enter Case Database mode.

### Building the Index

Before using the Case Database, build the search index:

1. Click **Rebuild Index** on the home page
2. The scanner finds all cases in your library
3. Index is stored at `library/.muninn/case_index.db`

### Analytics Columns

The table displays:

| Column | Description |
|--------|-------------|
| **Case ID** | Folder name of the case |
| **Diagnosis** | Case diagnosis from metadata |
| **Mod** | Modality (CT, MR, XR, etc.) |
| **Status** | Metadata completeness indicator (D=case_data, N=notes, K=key_slices) |
| **Practice** | Number of practice attempts |
| **Exams** | Number of graded exam marks |
| **Avg Prac** | Average practice self-rating |
| **Avg Exam** | Average exam mark (0-10 scale) |
| **Range** | Min-max exam mark range |

### Detailed Analytics Modal

Click any case row to open the detailed analytics modal:

**Case Information:**
- Case ID and diagnosis
- Modality and subspecialty

**Practice Statistics:**
- Total practice attempts
- Unique trainees who practiced
- Average time spent
- Average self-rating percentage

**Exam Statistics:**
- Total graded marks
- Unique trainees marked
- Average exam mark
- Score range (min-max)

**Timeline:**
- First activity date
- Last activity date

**Used in Playlists:**
- Lists all playlists that include this case
- Shows playlist author

**Used in Exams:**
- Lists all exams that include this case

### Opening Cases

From the analytics modal, click **Open in Loader** to edit the case metadata in Case Loader mode.

### Index Staleness Detection

The Case Database monitors for outdated index entries. When you modify `case_data.json` files outside of Muninn Admin (e.g., via scripts or manual editing), the index becomes stale.

**On startup**, Muninn Admin checks if any indexed cases have changed:
- **Modified cases**: The `case_data.json` file has been edited since indexing
- **Missing cases**: The case folder has been deleted or moved

If stale cases are detected, an amber warning banner appears:
> "X case(s) in the index may be outdated. Some case_data.json files have been modified."

**Actions:**
- Click **Rebuild Index** to update all case metadata
- Click **X** to dismiss the warning (index remains stale until rebuilt)

**Best practice:** Rebuild the index after bulk imports or when editing case files via scripts.

---

## Audit Logging

All critical actions in Muninn and Muninn Admin are logged to an audit trail for compliance and troubleshooting.

### What's Logged

| Action | Description | Logged By |
|--------|-------------|-----------|
| `exam_start` | Trainee began an exam | Muninn |
| `exam_submit` | Trainee submitted a case answer | Muninn |
| `pin_failed` | Failed PIN verification attempt | Muninn |
| `mark_entered` | Examiner marked a submission | Muninn Admin |
| `trainee_added` | New trainee added to registry | Muninn Admin |
| `trainee_modified` | Trainee details updated | Muninn Admin |
| `trainee_removed` | Trainee removed from registry | Muninn Admin |
| `pin_changed` | Trainee PIN set or cleared | Muninn Admin |

### Audit Log Schema

Logs are stored in the `audit_log` table of `tracking/muninn_tracking.db`:

| Column | Description |
|--------|-------------|
| `timestamp` | When the action occurred (UTC) |
| `actor` | Trainee ID or "admin" |
| `action` | Action type (see above) |
| `target_type` | What was affected (exam, trainee, submission) |
| `target_id` | Specific identifier (exam name, trainee ID, case ID) |
| `details` | JSON blob with additional context |
| `app_version` | Version of the app that logged the action |

### Viewing Audit Logs

1. Go to **Department** mode
2. Click the settings gear icon
3. Scroll to the **Audit Log** section
4. Filter by date range, actor, or action type
5. Click **Export CSV** to download for external review

### Use Cases

- **ARCP evidence**: Export exam activity logs showing when trainees took exams
- **Security review**: Check for failed PIN attempts
- **Troubleshooting**: Track when data was modified and by whom
- **Compliance**: Demonstrate chain of custody for assessment data

---

## Backup & Restore

Protect your training data with automated and manual backups.

### Backup Location

Backups are stored in:
```
{department_root}/tracking/backups/
├── daily/                  # Last 7 days
│   └── 2026-02-16_muninn_tracking.db
├── weekly/                 # Last 4 weeks
│   └── 2026-W07_muninn_tracking.db
└── manual/                 # User-created, no limit
    └── 2026-02-16_1430_manual_backup.db
```

### Automatic Backups

**Daily backups** are created automatically:
- On app startup, if no backup exists for today
- Stored in `backups/daily/`
- Last 7 daily backups are retained; older ones are deleted

### Manual Backups

To create a backup manually:

1. Go to **Department** mode
2. Click the settings gear icon
3. Scroll to the **Database Backups** section
4. Click **Create Backup**
5. Backup is saved to `backups/manual/`

**When to create manual backups:**
- Before major changes (bulk trainee imports, exam marking sessions)
- Before software updates
- Before migrating to a new server

### Restoring from Backup

To restore a backup:

1. Go to **Department** mode
2. Click the settings gear icon
3. Find the backup in the **Database Backups** list
4. Click **Restore** next to the backup
5. Confirm the restore action

**Warning:** Restoring replaces the current database with the backup. Any data created after the backup was made will be lost.

### What's Backed Up

The backup includes `muninn_tracking.db` which contains:
- Trainee registry
- Practice attempts
- Exam submissions
- Marking entries
- Audit logs
- PIN hashes

**Not backed up:**
- Case files (DICOM, JSON, markdown) - these live in the library folder
- Playlist and exam config files - these are separate JSON files
- The case index (`case_index.db`) - this can be rebuilt from library files

### Disaster Recovery

If the tracking database becomes corrupted:

1. Stop all Muninn and Muninn Admin instances
2. Open Muninn Admin on any machine with access to the department folder
3. Go to Department → Settings → Database Backups
4. Select the most recent valid backup
5. Click **Restore**
6. Verify data by checking the Trainee Registry and Training Dashboard

---

## Integration with Muninn

### Data Flow

```
MUNINN ADMIN                           MUNINN (Trainee)
─────────────────────────────────────────────────────────
DICOM Organizer
  └─▶ case_data.json (series[]) ──────┐
                                       ▼
Case Loader                            Practice Mode
  └─▶ case_data.json ─────────────────▶ Case display (fast load)
      (preserves series[], adds        │
       diagnosis, history, etc.)       │
  └─▶ key_slices.json ────────────────▶ Key findings
  └─▶ STUDY_NOTES.md ─────────────────▶ Model answers

Playlist Builder                       Playlist Browser
  └─▶ library/playlists/*.json ───────▶ Browse, filter, progress

Exam Builder                           Exam Mode
  └─▶ exams/exam_config.json ─────────▶ Exam loading
                                          │
                                          ▼
         muninn_tracking.db ◀─────────────┴───── Submissions
         ├── trainees ────────────────────────▶ Name dropdown
         ├── practice_attempts ◀──────────────── Practice activity
         ├── exam_submissions ◀───────────────── Exam answers
         └── marking_entries ──────────────────▶ Graded marks

Department
  └─◀ muninn_tracking.db ──────────── Reports, analytics

Case Database
  └─◀ muninn_tracking.db ──────────── Per-case analytics
  └─◀ case_index.db ───────────────── Case metadata
```

**Performance note:** The `series[]` array in `case_data.json` contains all DICOM metadata needed for display (UIDs, descriptions, window/level, dimensions). Muninn reads this single JSON file instead of scanning DICOM headers from each series folder, enabling instant case loading even on network drives.

### Network Setup

For multi-user deployments, all apps use the same department folder:

1. **Configure department folder** on first launch (or via Department Settings)
2. **Playlists** are saved to `department_root/library/playlists/`
3. **Exam configs** are saved to `department_root/exams/`
4. **All tracking data** is stored in `department_root/tracking/muninn_tracking.db`
   - Trainee registry
   - Practice attempts
   - Exam submissions
   - Graded marks

All trainees selecting the same department folder will:
- See the same playlists
- Submit to the same tracking database
- Appear in the trainee dropdown

Example paths:
- macOS: `/Volumes/RadiologyShare/` (department root)
- Windows: `\\server\RadiologyShare\` (department root)

---

## Tips

### For Case Creation

- Use consistent naming: `NN_CaseID_Diagnosis` (e.g., `01_12345_Acute_Appendicitis`)
- Include clinical history that guides the trainee
- Mark key findings that demonstrate the diagnosis
- Write study notes as a model answer, not just facts
- Set subspecialty and author for better organization

### For Playlist Creation

- Create playlists for different trainee levels (ST1 Basics, ST3 Advanced, etc.)
- Use per-case notes to provide context ("Compare with case 3...")
- Include a mix of difficulties within each playlist
- Update playlists regularly as you add new cases
- Consider creating specialty-specific playlists (Neuro On-Call, Chest Emergencies, etc.)

### For Exam Building

- Test the exam config yourself before distributing
- Use cases from multiple subspecialties for comprehensive assessment
- Include a mix of difficulties
- Set eligible levels to control who can take the exam

### For Marking

- Provide constructive feedback, not just scores
- Be consistent in scoring criteria across candidates
- Use the summary feature to identify patterns
- Marks are saved automatically - no need to manually save

### For Department Management

- Keep the trainee registry up to date
- Generate reports regularly to track progress
- Archive old exam results for longitudinal tracking
- Use the Case Database to identify underused or problematic cases

### For Security & Compliance

- **Set PINs for formal assessments**: Require PINs for any exam used as ARCP evidence
- **Review audit logs regularly**: Check for failed PIN attempts or unusual activity
- **Create manual backups before major events**: Backup before exam marking sessions
- **Export audit logs for ARCP**: Include exam_start and exam_submit logs as evidence
- **Rebuild the index after bulk imports**: Ensure case metadata is current
