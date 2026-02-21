# Department Setup Guide

This guide is for IT administrators and training coordinators setting up Muninn for department-wide use.

## Overview

Muninn and Muninn Admin can be deployed across a department using a shared network folder. This allows:

- **Centralized case library** - All trainees access the same cases
- **Shared trainee registry** - Names appear in exam dropdowns automatically
- **Unified exam management** - Results submitted to a central location
- **Department-wide reporting** - Track all trainee progress

---

## Network Folder Structure

Create a shared folder with the following structure:

```
department_root/
├── library/                        # Case library (read access for all)
│   ├── .muninn/                    # Search index (auto-generated)
│   │   └── case_index.db           # SQLite FTS database
│   ├── ct-courses/
│   │   └── ct-abdomen/
│   │       └── 01_Case_Name/
│   │           ├── case_data.json
│   │           ├── key_slices.json
│   │           ├── STUDY_NOTES.md
│   │           └── Series_Folder/
│   │               └── *.dcm
│   └── mri-courses/
│       └── ...
├── exams/                          # Exam configurations (read for trainees)
│   └── exam_config_*.json          # Exam configurations
├── reports/                        # Generated reports (admin only)
│   └── training_report_*.csv
└── tracking/                       # Central tracking database
    └── muninn_tracking.db          # SQLite: trainees, practice, exams
```

### Folder Naming

The subfolder names **must** be exactly:
- `library`
- `exams`
- `reports`
- `tracking`

Both apps derive paths from these standard names. The trainee registry is stored within `muninn_tracking.db`, not as a separate JSON file.

---

## Permissions

### Recommended Setup

| Folder | Trainees | Educators/Admins |
|--------|----------|------------------|
| `library/` | Read | Read/Write |
| `exams/` | Read | Read/Write |
| `reports/` | No access | Read/Write |
| `tracking/` | Read/Write | Read/Write |

### Why These Permissions?

- **library/**: Trainees need to read DICOM files; only educators should modify cases
- **exams/**: Trainees need to read exam configurations; only educators should create exams
- **reports/**: Contains administrative data; not for trainee access
- **tracking/**: Contains `muninn_tracking.db` - trainees need write access to submit practice logs and exam results; they also read from it for trainee selection

---

## Platform-Specific Paths

### macOS

Mount the network share and use the mounted path:

```
/Volumes/RadiologyTraining/
```

Or use SMB path (if supported):
```
smb://server/RadiologyTraining/
```

### Windows

Use UNC paths:
```
\\server\RadiologyTraining\
```

Or mapped drive:
```
Z:\RadiologyTraining\
```

### Linux

Mount point path:
```
/mnt/radiology_training/
```

---

## First-Time Setup

### For Educators (Muninn Admin)

1. Install Muninn Admin
2. On first launch, the setup dialog appears
3. Browse to the department folder
4. The app shows derived paths for verification:
   - Library: `department_root/library`
   - Exams: `department_root/exams`
   - Reports: `department_root/reports`
   - Tracking DB: `department_root/tracking/muninn_tracking.db`
5. Click **Continue**

### For Trainees (Muninn)

1. Install Muninn
2. On first launch, the setup dialog appears
3. Browse to the department folder
4. Click **Continue**
5. The trainee identification dialog appears
6. Select your name from the registry list
7. Click **Continue**

The trainee only needs to know the department folder location - the app handles the rest.

### Trainee Identification

After department setup, trainees must identify themselves from the registry. This:

- Links their practice sessions and exam results to their profile
- Enables level-based course filtering (e.g., hiding FRCR 2B courses from ST1 trainees)
- Pre-fills their name when taking exams

The trainee's name and level appear in the app header. They can change their profile in Settings if needed (useful for shared workstations).

---

## Creating the Trainee Registry

Before trainees can take exams with name selection, create a trainee registry. Trainees are stored in the `muninn_tracking.db` database.

### Using Muninn Admin

1. Open Muninn Admin
2. Go to **Department** > **Registry** tab
3. Click **Initialize Department** (creates the tracking database)
4. Enter department name
5. Add trainees manually or import from CSV

### CSV Import Format

```csv
name,email,level,supervisor
John Smith,j.smith@hospital.nhs.uk,ST3,Dr A. Jones
Jane Doe,j.doe@hospital.nhs.uk,ST2,Dr B. Williams
```

### Tracking Database Structure

The trainee registry and all activity data are stored in `tracking/muninn_tracking.db`:

| Table | Purpose |
|-------|---------|
| `trainees` | Trainee profiles (id, name, level, email, supervisor, active, pin_hash) |
| `practice_attempts` | Practice session records with case, time, rating |
| `exam_submissions` | Raw exam answers submitted by trainees |
| `marking_entries` | Graded marks with feedback from educators |
| `audit_log` | All critical actions (exam starts, submissions, marking, PIN failures) |
| `backup_metadata` | Records of database backups |

---

## PIN Authentication

For formal assessments (ARCP evidence), you may require trainees to enter a PIN before starting exams.

### Setting Up PINs

1. Open **Muninn Admin** > **Department** > **Registry** tab
2. Find the trainee in the list
3. Click the **PIN** column button (shows "Not Set" or "Set")
4. Enter a 4-6 digit PIN and confirm it
5. Click **Save PIN**

### How PIN Authentication Works

- **Practice mode**: No PIN required - trainees can practice freely
- **Exam mode**: If a trainee has a PIN set, they must enter it before starting any exam
- **Failed attempts**: All failed PIN attempts are logged to the audit trail

### PIN Security

- PINs are stored as Argon2 hashes (not plain text)
- Original PINs cannot be recovered - only reset by administrators
- Use unique PINs per trainee; don't share PINs

### When to Use PINs

| Scenario | Recommendation |
|----------|----------------|
| Formal ARCP assessments | **Required** - ensures identity verification |
| Formative exams | Optional - depends on policy |
| Practice sessions | Not applicable - PINs only apply to exams |

---

## Setting Up Exams

### Exam Workflow

1. **Educator creates exam** in Muninn Admin:
   - Go to **Exams** mode
   - Select cases from library
   - Configure exam name
   - Save to `department_root/exams/exam_config_*.json`

2. **Trainees take exam** in Muninn:
   - Click **Start Exam**
   - Select their name from dropdown
   - Browse to exam config file
   - Complete cases
   - Submissions saved to results file

3. **Educator marks exam** in Muninn Admin:
   - Go to **Marking** mode
   - Load exam config
   - Results are loaded automatically
   - Grade each submission
   - Export marks

### Results File

Exam submissions are appended to a shared results file. Multiple trainees can submit simultaneously - the file format supports concurrent writes.

---

## Troubleshooting

### "Failed to scan library"

- Check trainee has read access to `library/` folder
- Verify DICOM files exist in series subfolders
- Ensure path is accessible from trainee's machine

### "Failed to submit exam results"

- Check trainee has write access to `tracking/` folder
- Verify `muninn_tracking.db` exists and is not locked
- Verify network connection is stable
- Check disk space on network share

### "Trainees not appearing in dropdown"

- Verify `tracking/muninn_tracking.db` exists
- Check trainee has read access to `tracking/` folder
- Ensure trainees are marked as active in the database

### "Trainee identification not prompting"

- Ensure the department folder is configured (check Settings)
- Verify the tracking database exists at `department_root/tracking/muninn_tracking.db`
- The prompt only appears after department setup is complete

### "Cases not loading in exam"

- Verify case paths in exam config are absolute paths
- Check paths are accessible from trainee machines
- Ensure case folders contain valid DICOM files

### "PIN entry failing"

- PINs are 4-6 digits only (no letters or symbols)
- Trainee may be entering the wrong PIN
- Check audit log for `pin_failed` entries
- Administrator can reset the PIN in Muninn Admin

### "Database backup not created"

- Verify write permissions to `tracking/backups/` folder
- Check available disk space
- Manual backup can be created via Department Settings

### Different path on different platforms

If macOS and Windows users access the same share with different paths:

- macOS: `/Volumes/RadiologyTraining/`
- Windows: `\\server\RadiologyTraining\`

Each user should select their platform's path during first-launch setup. The exam configs should use paths accessible to all platforms, or create platform-specific configs.

---

## Security Considerations

### DICOM Data

- All DICOM files should be **fully anonymized** before adding to the library
- Remove patient names, IDs, dates of birth, and other PHI
- Consider using DICOM anonymization tools before import

### Network Access

- Restrict `reports/` folder to administrative users only
- Consider read-only access to `library/` for trainees
- Use your institution's standard network security policies

### Exam Integrity

- Exam configs are not encrypted - trainees could read correct answers
- For high-stakes exams, consider controlled exam environments
- Results files can be viewed by anyone with access to `exams/` folder
- **Use PIN authentication** for formal assessments to verify trainee identity

### Audit Logging

All critical actions are automatically logged to the `audit_log` table in the tracking database:

| Action | What's Logged |
|--------|---------------|
| `exam_start` | Trainee started an exam |
| `exam_submit` | Trainee submitted a case answer |
| `pin_failed` | Failed PIN verification attempt |
| `mark_entered` | Examiner marked a submission |
| `trainee_added/modified/removed` | Registry changes |
| `pin_changed` | PIN set or cleared |

Export audit logs for compliance evidence:
1. **Muninn Admin** > **Department** > Settings gear
2. Scroll to **Audit Log** section
3. Filter by date range and action type
4. Click **Export CSV**

---

## Search Index

Muninn supports full-text search across all cases in the library. The search index is built by Muninn Admin and stored in the library folder.

### Building the Index

1. Open **Muninn Admin**
2. On the home page, verify the library path is correct
3. Click **Rebuild Search Index**
4. Wait for indexing to complete (shows case count when done)

The index is stored at `library/.muninn/case_index.db` (SQLite with FTS5).

### What's Indexed

- Case ID
- Diagnosis
- Clinical history
- Body region
- Course name

### When to Rebuild

Rebuild the index when:
- New cases are added to the library
- Case metadata is updated
- Cases are deleted or moved

Trainees cannot rebuild the index - this is an admin-only operation.

### Index Staleness Detection

Muninn Admin automatically detects when indexed cases are out of date:

- **Modified cases**: `case_data.json` files edited since indexing
- **Missing cases**: Case folders deleted or moved

On startup, if stale cases are detected, an amber warning banner appears:
> "X case(s) in the index may be outdated."

Click **Rebuild Index** to update the index.

### Using Search (Trainees)

When the index exists, a search bar appears in Muninn's library browser. Trainees can:
- Search by diagnosis, body region, or clinical history
- Click a result to load that specific case directly
- Filter results by modality

---

## Maintenance

### Database Backups

The tracking database (`muninn_tracking.db`) contains all trainee records, exam submissions, and marks. Protect it with regular backups.

**Automatic Backups:**
- Created daily on Muninn Admin startup (if none exists for today)
- Stored in `tracking/backups/daily/`
- Last 7 daily + 4 weekly backups retained

**Manual Backups:**
1. **Muninn Admin** > **Department** > Settings gear
2. Scroll to **Database Backups** section
3. Click **Create Backup**
4. Backup saved to `tracking/backups/manual/`

**Restoring from Backup:**
1. Open the backup list in Department Settings
2. Select a backup and click **Restore**
3. Confirm the restore action

**When to Create Manual Backups:**
- Before bulk trainee imports
- Before major exam marking sessions
- Before software updates
- Before migrating to a new server

### Regular Tasks

- **Update trainee registry** when staff rotate
- **Review audit logs** periodically for security
- **Set PINs** for trainees taking formal assessments
- **Generate training reports** periodically
- **Rebuild search index** after adding new cases
- **Verify backups** are being created automatically

### Storage Considerations

DICOM files are large. Plan for:
- ~50-500 MB per case (depending on modality)
- ~1-10 GB per course
- Results and registry files are small (< 1 MB each)

---

## Deployment Checklist

- [ ] Create shared network folder
- [ ] Set up folder structure (library, exams, reports, tracking)
- [ ] Configure permissions (see table above)
- [ ] Initialize department and create trainee registry (Muninn Admin > Department)
- [ ] Add anonymized cases to library
- [ ] Build search index (Muninn Admin home page)
- [ ] **Set PINs for trainees who will take formal exams**
- [ ] Test access from educator machine (Muninn Admin)
- [ ] Test access from trainee machine (Muninn)
- [ ] Verify search works in Muninn
- [ ] Document department folder path for users
- [ ] Create test exam and verify submission
- [ ] **Test PIN authentication flow** (if using PINs)
- [ ] Test practice tracking (enable in trainee settings, complete a case, verify in Case Database analytics)
- [ ] **Verify automatic backups** are being created in `tracking/backups/`
- [ ] **Review audit log** to confirm actions are being recorded
