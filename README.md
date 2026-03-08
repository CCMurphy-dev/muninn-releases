<div align="center">
  <img src="docs/muninn-logo.png" alt="Muninn" width="120" height="120">
  <h1>Muninn - Radiology Training Software</h1>
  <p><strong>Practice radiology reporting with local Departmental DICOM cases.</strong></p>

[![License](https://img.shields.io/badge/license-proprietary-blue.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20macOS-lightgrey.svg)]()

</div>

---

## What is Muninn?

**Muninn** is a desktop application suite for radiology training departments to create structured exam practice environments using their own DICOM case libraries.

### The Problem

- FRCR 2B exam preparation requires practicing with real cases
- Commercial question banks don't reflect your department's case mix
- Sharing cases via email/USB is disorganized and hard to track
- No way to run mock exams with proper timing and conditions

### The Solution

Muninn provides:
- **Centralized case library** on your department network
- **Structured practice** with findings, diagnosis, and management fields
- **Mock exam mode** with timing, shuffled cases, and result tracking
- **Progress analytics** for trainees and training programme directors
- **Offline-first** - works on air-gapped NHS networks

---

## Features

### For Trainees (Muninn)

| Feature | Description |
|---------|-------------|
| **DICOM Viewer** | Full windowing, presets, scroll, split-pane comparison |
| **Practice Mode** | Submit findings, diagnosis, differential, management |
| **AI Feedback** | Optional AI-powered feedback (Cerebras, Google, OpenAI, Anthropic, Ollama) |
| **Self-Assessment** | Rate your confidence, track improvement over time |
| **Exam Mode** | Timed assessments, hidden answers, shuffled case order |
| **Progress Tracking** | See your practice history across all sessions |
| **Playlist Learning** | Work through curated case collections |

### For Educators (Muninn Admin)

| Feature | Description |
|---------|-------------|
| **DICOM Organizer** | Sort PACS exports into structured case folders |
| **Case Editor** | Add model answers, key findings, study notes |
| **Playlist Builder** | Create curated collections for different training levels |
| **Exam Builder** | Set up mock exams with time limits, eligibility, and marking scheme |
| **Marking Interface** | Grade submissions with three scoring modes: legacy (0–10), dimension, or RCR CR2B (0–5) |
| **Dimension Analytics** | Per-exam breakdown of detection, description, diagnosis, and management scores |
| **Trainee Management** | Registry, PIN authentication, progress reports |
| **Audit Logging** | Track exam starts, submissions, and marking |
| **Automated Backups** | Daily and weekly backups of tracking database |

---

## Screenshots

*Coming soon*

---

## How It Works

```
┌──────────────────┐     ┌──────────────────┐
│   MUNINN ADMIN   │     │     MUNINN       │
│  (Admin Tool)    │     │  (Trainee Tool)  │
├──────────────────┤     ├──────────────────┤
│                  │     │                  │
│  Import DICOM    │     │                  |
│                  │     |                  | 
│                  │     |                  |
│   Create Cases   │     |   Browse Cases   │
│                  │     |                  |
│  Build Playlists │────▶│  Practice Mode   │
│                  │     │       ↓          │
│                  │     │  Track Progress  │
│                  │     │       ↓          │
│  Create Exams    │────▶│  Take Exams      │
│       ↓          │     │       ↓          │
│  Mark Exams      │◀────│  Submit Results  │
│                  │     │                  │
└────────┬─────────┘     └────────┬─────────┘
         │                        │
         └────────────┬───────────┘
                      ↓
         ┌────────────────────────┐
         │   Shared Network       │
         │   Department Folder    │
         │                        │
         │  • Case Library        │
         │  • Tracking Database   │
         │  • Trainee Registry    │
         │  • Exam Configs        │
         └────────────────────────┘
```

---

## Download

Download the latest version from the [Releases](https://github.com/CCMurphy-dev/muninn-releases/releases) page.

| Platform | Muninn (Trainee) | Muninn Admin |
|----------|------------------|----------------------|
| **Windows** | `.msi` installer | `.msi` installer |
| **macOS (Apple Silicon)** | `.dmg` (`aarch64`) | `.dmg` (`aarch64`) |
| **macOS (Intel)** | `.dmg` (`x64`) | `.dmg` (`x64`) |

### macOS Installation Note

After mounting the DMG and dragging to Applications, you may need to run:
```bash
xattr -cr /Applications/Muninn.app
```
This removes the quarantine flag for unsigned apps.

---

## Licensing

Muninn requires a per-department license. Licenses are:
- **Offline-verified** - no internet required after activation
- **Annual subscription** - with 30-day grace period for renewal
- **Unlimited trainees** - per department, not per seat

**Request a license:** [REDACTED](mailto:REDACTED?subject=%5BMUNINN-LIC%5D%20License%20Inquiry)

---

## Requirements

- **Windows 10/11** or **macOS 12+**
- Anonymized DICOM files (standard NHS PACS export)
- Network share for multi-user deployment (optional)
- AI API key for feedback feature (optional)

---

## Documentation

- [Department Setup](docs/DEPARTMENT_SETUP_GUIDE.md)
- [User Guide (Muninn)](docs/MUNINN_USER_GUIDE.md)
- [Admin Guide (Muninn Admin)](docs/MUNINN_ADMIN_GUIDE.md)
- [Exam Mode Guide](docs/EXAM_MODE_GUIDE.md)
- [Case Creation Guide](docs/DATASET_CREATION_GUIDE.md)
- [Technical Reference](docs/TECHNICAL_REFERENCE.md)

---

## Use Cases

### Mock FRCR 2B Exams
Create timed exam sessions with 6 short cases and 3 long cases. Trainees see shuffled case order, hidden diagnoses, and submit structured reports. Educators mark centrally with written feedback.

### Departmental Teaching Library
Build a searchable library of interesting cases from your PACS. Consultants contribute cases, registrars practice with structured feedback, and TPDs track engagement.

### On-Call Preparation
Curate playlists of "must-know" emergency cases for junior trainees. Track who has completed each playlist before they go on call.

### MDT Case Review
Create playlists around upcoming MDT cases for pre-meeting preparation.

---

## FAQ

**Q: Can trainees cheat in exam mode?**
A: Exam mode hides model answers, shuffles case order per trainee, and supports PIN authentication. All submissions are logged with timestamps. Optional time limits auto-submit the exam when time expires, recording any unanswered cases as not submitted.

**Q: Does it work on hospital networks?**
A: Yes. Muninn is offline-first with no cloud dependencies. All data stays on your local network.

**Q: What DICOM formats are supported?**
A: Standard DICOM files from any modality. Multi-frame, CT, MRI, CR, DX all supported.

**Q: Can I use my own AI provider?**
A: Yes. Supports Cerebras, Google AI, OpenAI, Anthropic, and local Ollama models.

**Q: How is data backed up?**
A: Automated daily and weekly backups of the tracking database, plus manual backup/restore.

---

## About

Created by **Dr Christopher Murphy**, Radiology Registrar, St George's Hospital NHS Foundation Trust.

Built with [Tauri](https://tauri.app), [Svelte](https://svelte.dev), and [Cornerstone.js](https://cornerstonejs.org).

---

## Contact

- **License inquiries:** [REDACTED](mailto:REDACTED?subject=%5BMUNINN-LIC%5D%20License%20Inquiry)
- **Bug reports:** [GitHub Issues](https://github.com/CCMurphy-dev/muninn-releases/issues)
- **Feature requests:** [GitHub Discussions](https://github.com/CCMurphy-dev/muninn-releases/discussions)

---

## Legal

- [Privacy Policy](PRIVACY_POLICY.md)
- [Terms of Use](TERMS_OF_USE.md)
- [Medical Disclaimer](MEDICAL_DISCLAIMER.md)

**Important:** Muninn is a training tool for educational purposes only. It is not intended for clinical diagnosis, does not provide formal accreditation, and does not replace supervised radiology training. See the [Medical Disclaimer](MEDICAL_DISCLAIMER.md) for full details.

---

## Keywords

FRCR 2B, radiology training, DICOM viewer, radiology exam preparation, medical imaging education, UK radiology, NHS radiology training, mock radiology exam, radiology case library, radiology reporting practice
