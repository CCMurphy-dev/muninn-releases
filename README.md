# Muninn Releases

Downloads and documentation for Muninn and Muninn Admin - radiology training tools for local DICOM libraries.

## Downloads

Check the [Releases](https://github.com/CCMurphy-dev/muninn-releases/releases) page for the latest versions.

| Platform | Muninn | Muninn Admin |
|----------|--------|--------------|
| macOS (Apple Silicon) | `.dmg` ending in `aarch64` | `.dmg` ending in `aarch64` |
| macOS (Intel) | `.dmg` ending in `x86_64` | `.dmg` ending in `x86_64` |
| Windows | `.msi` or `.exe` installer | `.msi` or `.exe` installer |
| Linux | `.AppImage` or `.deb` | `.AppImage` or `.deb` |

---

## The Apps

### Muninn (Trainee App)

Desktop application for practicing radiology with local DICOM cases.

**Practice Mode:**
- Browse courses organized by modality and specialty
- Filter by training level (ST1-ST5, Fellow, Consultant)
- Search cases by diagnosis, body region, or clinical history
- View DICOM images with window/level controls and presets
- Split-pane view for comparing series side-by-side
- Submit findings, diagnosis, differential, and management
- Get AI-powered feedback (Cerebras, Google AI, OpenAI, Anthropic, Ollama)
- Review model answers, study notes, and key findings
- Self-rate performance and track progress over time

**Exam Mode:**
- Load exam configurations created by administrators
- Shuffled case order for fair assessment (each trainee gets a unique order)
- Sequential component unlock for multi-study cases (e.g., view CXR before CTPA unlocks)
- Timed assessments with learning features hidden
- Flag cases for review before finishing
- Review and edit answers before final submission
- Submit to centralized results file
- Select name from department trainee registry

**Department Integration:**
- Connect to shared department folder for case library
- Automatic trainee identification from registry
- Practice activity tracking (opt-in)
- Personal library option for additional cases
- Library switcher between department and personal content

### Muninn Admin (Educator App)

Administrative tools for creating content and managing training.

**DICOM Organizer:**
- Sort PACS exports into organized case folders
- Auto-generate unique Case IDs
- Extract and cache series metadata
- Batch process multiple cases
- Handle multi-study cases (e.g., CXR + CTPA)

**Case Loader:**
- Create metadata for DICOM cases
- Mark key findings with slice annotations
- Write study notes and model answers
- Batch process multiple cases
- Auto-populate from DICOM headers

**Exam Builder:**
- Browse case library with DICOM preview
- Select and reorder exam cases
- Configure results path and trainee registry
- Set trainee eligibility by level or specific IDs
- Generate exam config files

**Exam Marking:**
- Load exam submissions from results file
- View trainee answers alongside DICOM images
- Grade answers 0-10 with written feedback
- Generate per-candidate summaries
- Export marks to JSON or CSV

**Department Training:**
- Manage trainee registries
- Import trainees from CSV
- Build and browse search index
- Generate training reports for date ranges
- Track individual trainee progress
- View practice activity summaries

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         MUNINN ECOSYSTEM                                │
├─────────────────────────────────┬───────────────────────────────────────┤
│         MUNINN ADMIN            │              MUNINN                   │
│       [Educator Tool]           │         [Trainee Tool]                │
├─────────────────────────────────┼───────────────────────────────────────┤
│                                 │                                       │
│  DICOM Organizer                │  Local SQLite DB (muninn.db)          │
│  (sort PACS, generate CaseID)   │  (attempts, progress, history)        │
│            │                    │                                       │
│            ▼                    │                                       │
│  Case Loader ──────────────────────────────▶ Practice Mode              │
│  (case_data.json, key_slices,   │            (browse, search, study)    │
│   STUDY_NOTES.md)               │                                       │
│                                 │                                       │
│  Search Index ─────────────────────────────▶ Full-text Search           │
│  (.muninn/case_index.db)        │            (SQLite FTS5)              │
│                                 │                                       │
│  Exam Builder ─────────────────────────────▶ Exam Mode                  │
│  (exam_config.json)             │            (shuffled, timed,          │
│                                 │             sequential unlock)        │
│                                 │                    │                  │
│  Exam Marking ◀────────────────────────────────────────                 │
│  (muninn_tracking.db)           │            (submissions to DB)        │
│                                 │                                       │
│  Department ───────────────────────────────▶ Trainee Selection          │
│  (muninn_tracking.db)           │            (name dropdown, tracking)  │
│                                 │                                       │
└─────────────────────────────────┴───────────────────────────────────────┘
```

**Storage:**
- **Local**: SQLite database (`muninn.db`) for attempt history, ratings, and personal progress
- **Shared (Network)**:
  - Case metadata: JSON files (`case_data.json`, `key_slices.json`, `STUDY_NOTES.md`)
  - Exam configs: JSON (`exam_config.json`)
  - Department tracking: SQLite (`muninn_tracking.db`) for trainees, practice logs, exam submissions
- **Search Index**: SQLite FTS5 (`.muninn/case_index.db`) built by Admin, used by both apps

---

## Documentation

### User Guides
- [Muninn User Guide](docs/MUNINN_USER_GUIDE.md) - For trainees using Muninn
- [Muninn Admin Guide](docs/MUNINN_ADMIN_GUIDE.md) - For educators using Muninn Admin

### Reference
- [Scan Import Guide](docs/SCAN_IMPORT_GUIDE.md) - Complete workflow for importing DICOM scans
- [Dataset Creation Guide](docs/DATASET_CREATION_GUIDE.md) - Creating case folders and metadata files
- [Exam Mode Guide](docs/EXAM_MODE_GUIDE.md) - Setting up centralized exams
- [Department Setup Guide](docs/DEPARTMENT_SETUP_GUIDE.md) - Network deployment for departments

---

## Quick Start

### For Trainees

1. Download and install **Muninn**
2. On first launch, select your department's shared folder (provided by your training coordinator)
3. Select your name from the trainee registry
4. Browse courses and practice cases
5. Take exams when assigned

### For Educators

1. Download and install **Muninn Admin**
2. On first launch, select your department's shared folder
3. Use **Organizer** to sort PACS exports into case folders
4. Use **Case Loader** to add teaching metadata
5. Click **Rebuild Search Index** on the home page to enable search
6. Use **Exam Builder** to create exam configurations
7. Use **Department** to manage trainees
8. Use **Marking** to grade exam submissions

### For IT/Administrators

See [Department Setup Guide](docs/DEPARTMENT_SETUP_GUIDE.md) for network deployment instructions.

---

## Data Structure

### Department Folder (Network Deployment)

For multi-user deployments, both apps use a shared department folder:

```
department_root/                    # Shared network folder
├── library/                        # DICOM case library
│   ├── .muninn/                    # Search index (auto-generated)
│   │   └── case_index.db           # SQLite FTS database
│   ├── ct-courses/
│   │   └── ct-abdomen/
│   │       ├── course.json         # Course-level metadata
│   │       └── 01_Acute_Appendicitis/
│   │           ├── case_data.json
│   │           ├── STUDY_NOTES.md
│   │           ├── key_slices.json
│   │           └── Axial_CT/
│   │               └── *.dcm
│   └── mri-courses/
│       └── ...
├── exams/                          # Exam configurations
│   └── exam_config_2026_01.json
├── reports/                        # Generated training reports
│   └── training_report_*.csv
└── tracking/                       # Central tracking database
    └── muninn_tracking.db          # SQLite: trainees, practice, exam submissions
```

### Multi-Component Cases

For cases with multiple imaging studies (e.g., initial CXR followed by CTPA):

```
multi_study_case/
├── case_data.json
├── 01_Initial_CXR/               # Component 1 (always visible in exams)
│   └── *.dcm
└── 02_CTPA/                      # Component 2 (unlocked after viewing component 1)
    └── *.dcm
```

In exam mode, components unlock sequentially - trainees must view the CXR before the CTPA becomes accessible. This prevents "spoilers" from later imaging.

### Single-User Setup

For standalone use, cases can be in any folder:

```
radiology_library/
├── ct-courses/
│   └── ct-abdomen/
│       └── 01_Acute_Appendicitis/
│           ├── case_data.json
│           ├── STUDY_NOTES.md
│           ├── key_slices.json
│           └── Axial_CT/
│               └── *.dcm
└── mri-courses/
    └── ...
```

---

## Requirements

- Anonymized DICOM files organized in folders
- API keys for AI feedback (optional, for Muninn)

## License

Private - Not for distribution

## Author

Dr Christopher Murphy
