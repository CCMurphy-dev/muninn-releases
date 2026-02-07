# Muninn Releases

Downloads for Muninn and Muninn Loader - radiology training tools for local DICOM libraries.

## Downloads

Check the [Releases](https://github.com/CCMurphy-dev/muninn-releases/releases) page for the latest versions.

### Muninn

Desktop application for practicing radiology with your own local DICOM case library.

**Features:**
- Practice radiology cases with local DICOM files
- AI-powered feedback (Cerebras, Google AI, OpenAI, Anthropic, Ollama)
- Track progress and self-assessment ratings
- Key slice navigation with windowing presets
- Exam mode for structured assessments
- Multiple themes (Midnight, Dark, Slate, Emerald, Light)

### Muninn Loader

Companion tool for creating case metadata from DICOM folders.

**Features:**
- Scan DICOM headers to auto-populate series info
- Form UI for diagnosis, history, difficulty, and differentials
- Mark key findings with slice annotations and window settings
- Markdown editor for study notes and model answers
- Generates `case_data.json`, `key_slices.json`, and `STUDY_NOTES.md`

## Installation

Download the appropriate file for your platform:

| Platform | Muninn | Muninn Loader |
|----------|--------|---------------|
| macOS (Apple Silicon) | `.dmg` ending in `aarch64` | `.dmg` ending in `aarch64` |
| macOS (Intel) | `.dmg` ending in `x86_64` | `.dmg` ending in `x86_64` |
| Windows | `.msi` or `.exe` installer | `.msi` or `.exe` installer |
| Linux | `.AppImage` or `.deb` | `.AppImage` or `.deb` |

## Workflow

1. **Prepare cases** - Organize anonymized DICOM files into folders
2. **Create metadata** - Use Muninn Loader to create case_data.json, key_slices.json, and STUDY_NOTES.md
3. **Practice** - Open cases in Muninn, write findings, and get AI feedback

## Data Structure

Muninn expects a folder structure like:

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
    └── brain-mri/
        └── 01_Acute_Stroke/
            └── ...
```

## Documentation

- [Dataset Creation Guide](docs/DATASET_CREATION_GUIDE.md) - How to create case folders, JSON files, and study notes
- [Exam Mode Guide](docs/EXAM_MODE_GUIDE.md) - Setting up centralized exams for multiple trainees

## Requirements

- Anonymized DICOM files organized in folders
- Your own API keys for AI feedback (optional)

## License

Private - Not for distribution

## Author

Dr Christopher Murphy
