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
├── registry/                       # Trainee management
│   └── trainee_registry.json       # Trainee list
├── exams/                          # Exam files (read/write for all)
│   ├── exam_config_*.json          # Exam configurations
│   └── results_*.json              # Exam submissions
└── reports/                        # Generated reports (admin only)
    └── training_report_*.csv
```

### Folder Naming

The subfolder names **must** be exactly:
- `library`
- `registry`
- `exams`
- `reports`

Both apps derive paths from these standard names.

---

## Permissions

### Recommended Setup

| Folder | Trainees | Educators/Admins |
|--------|----------|------------------|
| `library/` | Read | Read/Write |
| `registry/` | Read | Read/Write |
| `exams/` | Read/Write | Read/Write |
| `reports/` | No access | Read/Write |

### Why These Permissions?

- **library/**: Trainees need to read DICOM files; only educators should modify cases
- **registry/**: Trainees need to read names for dropdown; only admins should edit
- **exams/**: Trainees must write their exam submissions
- **reports/**: Contains administrative data; not for trainee access

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
   - Registry: `department_root/registry/trainee_registry.json`
   - Exams: `department_root/exams`
   - Reports: `department_root/reports`
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

Before trainees can take exams with name selection, create a trainee registry.

### Using Muninn Admin

1. Open Muninn Admin
2. Go to **Department** > **Registry** tab
3. Click **Create Registry**
4. Enter department name
5. Add trainees manually or import from CSV

### CSV Import Format

```csv
name,email,level,supervisor
John Smith,j.smith@hospital.nhs.uk,ST3,Dr A. Jones
Jane Doe,j.doe@hospital.nhs.uk,ST2,Dr B. Williams
```

### Registry File Format

```json
{
  "department": "St Mary's Radiology",
  "updated_at": "2026-02-09T10:00:00Z",
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

Save this file as `department_root/registry/trainee_registry.json`.

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

- Check trainee has write access to `exams/` folder
- Verify network connection is stable
- Check disk space on network share

### "Trainees not appearing in dropdown"

- Verify `registry/trainee_registry.json` exists
- Check trainee has read access to `registry/` folder
- Ensure trainees are marked as `"active": true`

### "Trainee identification not prompting"

- Ensure the department folder is configured (check Settings)
- Verify the trainee registry exists at `department_root/registry/trainee_registry.json`
- The prompt only appears after department setup is complete

### "Cases not loading in exam"

- Verify case paths in exam config are absolute paths
- Check paths are accessible from trainee machines
- Ensure case folders contain valid DICOM files

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

---

## Maintenance

### Regular Tasks

- **Update trainee registry** when staff rotate
- **Archive old exam results** after marking
- **Back up case library** regularly
- **Generate training reports** periodically

### Storage Considerations

DICOM files are large. Plan for:
- ~50-500 MB per case (depending on modality)
- ~1-10 GB per course
- Results and registry files are small (< 1 MB each)

---

## Deployment Checklist

- [ ] Create shared network folder
- [ ] Set up folder structure (library, registry, exams, reports)
- [ ] Configure permissions (see table above)
- [ ] Create trainee registry
- [ ] Add anonymized cases to library
- [ ] Test access from educator machine (Muninn Admin)
- [ ] Test access from trainee machine (Muninn)
- [ ] Document department folder path for users
- [ ] Create test exam and verify submission
