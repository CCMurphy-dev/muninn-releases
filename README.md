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

Desktop application for practicing radiology with curated case playlists.

**Playlist-Based Learning:**
- Browse curated playlists created by your department
- Filter by modality (CT, MRI, XR, US) and specialty
- Level-appropriate content filtering (ST1-Consultant)
- Progress tracking per playlist with resume capability
- Search within playlists

**Practice Mode:**
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
- Submit to centralized tracking database
- Select name from department trainee registry
- **PIN authentication** for secure exam access (if configured)

**Department Integration:**
- Connect to shared department folder for case library
- Automatic trainee identification from registry
- Practice and exam activity tracking
- Personal library option for additional cases

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

**Playlist Builder:**
- Create curated case collections for trainees
- Select cases from anywhere in the library
- Add per-case notes for context
- Set modality, specialty, and level filters
- Reorder cases with drag-and-drop
- Preview playlists before publishing

**Exam Builder:**
- Browse case library with DICOM preview
- Select and reorder exam cases
- Set trainee eligibility by level or specific IDs
- Generate exam config files

**Exam Marking:**
- Load exam submissions from tracking database
- View trainee answers alongside DICOM images
- Grade answers 0-10 with written feedback
- Generate per-candidate summaries
- Export marks to JSON or CSV

**Department Training:**
- Manage trainee registries
- Import trainees from CSV
- **Set PINs for exam authentication**
- Build and browse search index
- Generate training reports for date ranges
- Track individual trainee progress
- View practice activity summaries
- **View audit logs** (exam starts, submissions, marking, PIN failures)
- **Automated database backups** with manual backup/restore

**Case Database:**
- Browse indexed cases with per-case analytics
- View practice count, exam marks, and averages
- See which playlists and exams use each case
- Detailed analytics modal with unique trainees, timing, and score ranges
- Open cases directly in Case Loader
- **Index staleness detection** with rebuild warnings

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
│  (case_data.json, key_slices,   │            (study, feedback)          │
│   STUDY_NOTES.md)               │                                       │
│                                 │                                       │
│  Playlist Builder ─────────────────────────▶ Playlist Browser           │
│  (library/playlists/*.json)     │            (browse, filter, progress) │
│                                 │                                       │
│  Search Index ─────────────────────────────▶ Full-text Search           │
│  (.muninn/case_index.db)        │            (SQLite FTS5)              │
│                                 │                                       │
│  Exam Builder ─────────────────────────────▶ Exam Mode                  │
│  (exams/exam_config.json)       │            (shuffled, timed,          │
│                                 │             sequential unlock)        │
│                                 │                    │                  │
│  Exam Marking ◀────────────────────────────────────────                 │
│  (muninn_tracking.db)           │            (submissions to DB)        │
│                                 │                                       │
│  Department ───────────────────────────────▶ Trainee Selection          │
│  (muninn_tracking.db)           │            (name dropdown, tracking)  │
│                                 │                                       │
│  Case Database ◀───────────────────────────▶ Per-case Analytics         │
│  (practice/exam stats,          │            (unique trainees, timing)  │
│   playlist/exam usage)          │                                       │
│                                 │                                       │
└─────────────────────────────────┴───────────────────────────────────────┘
```

**Storage:**
- **Local**: SQLite database (`muninn.db`) for attempt history, ratings, and personal progress
- **Shared (Network)**:
  - Case metadata: JSON files (`case_data.json`, `key_slices.json`, `STUDY_NOTES.md`)
  - Playlists: JSON files in `library/playlists/` directory
  - Exam configs: JSON files in `exams/` directory
  - Department tracking: SQLite (`tracking/muninn_tracking.db`) with tables:
    - `trainees` - Trainee profiles (id, name, level, email, pin_hash)
    - `practice_attempts` - Practice session records with case, time, rating
    - `exam_submissions` - Raw exam answers submitted by trainees
    - `marking_entries` - Graded marks with feedback from educators
    - `audit_log` - All critical actions (exam starts, submissions, marking, PIN failures)
    - `backup_metadata` - Records of database backups
  - Automated backups: `tracking/backups/` (daily, weekly, manual)
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
4. Browse playlists created by your department
5. Click a playlist to start practicing (progress is saved automatically)
6. Take exams when assigned

### For Educators

1. Download and install **Muninn Admin**
2. On first launch, select your department's shared folder
3. Use **Organizer** to sort PACS exports into case folders
4. Use **Case Loader** to add teaching metadata
5. Click **Rebuild Search Index** on the home page to enable search
6. Use **Playlist Builder** to create curated case collections for trainees
7. Use **Exam Builder** to create exam configurations
8. Use **Department** to manage trainees and **set PINs** for exam access
9. Use **Marking** to grade exam submissions
10. Review **audit logs** and **backups** in Department Settings

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
│   │   └── case_index.db           # SQLite FTS database (schema v8)
│   ├── playlists/                  # Curated case collections
│   │   ├── on-call-essentials.json
│   │   ├── frcr-2b-prep.json
│   │   └── neuro-masterclass.json
│   ├── dr-smith/                   # Consultant's case folder
│   │   └── neuro/
│   │       └── 01_Acute_Stroke/
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
    ├── muninn_tracking.db          # SQLite: trainees, practice_attempts,
    │                               # exam_submissions, marking_entries,
    │                               # audit_log, backup_metadata
    └── backups/                    # Automated database backups
        ├── daily/                  # Last 7 daily backups
        ├── weekly/                 # Last 4 weekly backups
        └── manual/                 # User-created backups
```

### Playlist Format

Playlists are JSON files that reference cases from anywhere in the library:

```json
{
  "name": "On-Call Essentials",
  "description": "Must-know cases for junior trainees on call",
  "author": "Dr Smith",
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

**Path formats:**
- `@library/path/to/case` - Relative to library root (portable)
- `../relative/path` - Relative to playlist file location
- `/absolute/path` - Absolute path (not portable)

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
├── playlists/
│   └── my-cases.json
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

## Access Control

**Case visibility is controlled through playlists.** Cases are only accessible to trainees if they are included in a playlist. This allows:

- **Flexible organization**: Cases can be stored anywhere in the library structure
- **Multiple access paths**: A case can appear in multiple playlists
- **Level-appropriate content**: Playlists can specify minimum training levels
- **Consultant workspaces**: Each consultant can have their own folder; selected cases are published via playlists

---

## Requirements

- Anonymized DICOM files organized in folders
- API keys for AI feedback (optional, for Muninn)

## License

Private - Not for distribution

## Author

Dr Christopher Murphy
