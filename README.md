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
- Browse courses organized by modality
- View DICOM images with window/level controls
- Submit findings, diagnosis, and differentials
- Get AI-powered feedback (Cerebras, Google AI, OpenAI, Anthropic, Ollama)
- Review model answers and key findings
- Track progress over time

**Exam Mode:**
- Take structured exams configured by administrators
- Timed assessments with learning features hidden
- Submit answers to shared results file
- Select name from trainee registry

### Muninn Admin (Educator App)

Administrative tools for creating content and managing training.

**Case Loader:**
- Create metadata for DICOM cases
- Mark key findings with slice annotations
- Write study notes and model answers
- Batch process multiple cases

**Exam Builder:**
- Browse case library with DICOM preview
- Select and order exam cases
- Configure results path and trainee registry
- Generate exam config files

**Exam Marking:**
- Load exam submissions
- Grade answers 0-10 with feedback
- Generate per-candidate summaries
- Export marks to CSV

**Department Training:**
- Manage trainee registries
- Import trainees from CSV
- Generate training reports
- Track individual progress

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
│  Case Loader ──────────────────────────────▶ Practice Mode              │
│  (case_data.json, key_slices,   │            (browse cases, study)      │
│   STUDY_NOTES.md)               │                                       │
│                                 │                                       │
│  Exam Builder ─────────────────────────────▶ Exam Mode                  │
│  (exam_config.json)             │            (timed, no hints)          │
│                                 │                    │                  │
│  Exam Marking ◀────────────────────────────────────────                 │
│  (results.json)                 │            (submissions)              │
│                                 │                                       │
│  Department ───────────────────────────────▶ Trainee Selection          │
│  (trainee_registry.json)        │            (name dropdown)            │
│                                 │                                       │
└─────────────────────────────────┴───────────────────────────────────────┘
```

---

## Documentation

### User Guides
- [Muninn User Guide](docs/MUNINN_USER_GUIDE.md) - For trainees using Muninn
- [Muninn Admin Guide](docs/MUNINN_ADMIN_GUIDE.md) - For educators using Muninn Admin

### Reference
- [Dataset Creation Guide](docs/DATASET_CREATION_GUIDE.md) - Creating case folders and metadata files
- [Exam Mode Guide](docs/EXAM_MODE_GUIDE.md) - Setting up centralized exams

---

## Quick Start

### For Trainees

1. Download and install **Muninn**
2. Set your DICOM library path
3. Scan and browse courses
4. Practice cases and get AI feedback

### For Educators

1. Download and install **Muninn Admin**
2. Use **Case Loader** to create case metadata
3. Use **Exam Builder** to create exam configurations
4. Use **Department** to manage trainees
5. Use **Marking** to grade exam submissions

---

## Data Structure

```
radiology_library/
├── ct-courses/
│   └── ct-abdomen/
│       └── 01_Acute_Appendicitis/
│           ├── case_data.json      # Case metadata
│           ├── STUDY_NOTES.md      # Model answer
│           ├── key_slices.json     # Key findings
│           └── Axial_CT/           # Series folder
│               └── *.dcm           # DICOM files
└── mri-courses/
    └── brain-mri/
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
