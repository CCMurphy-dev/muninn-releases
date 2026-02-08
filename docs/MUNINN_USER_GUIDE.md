# Muninn User Guide

Muninn is a desktop application for practicing radiology with your own local DICOM case library.

## Overview

Muninn has two main modes:

| Mode | Purpose |
|------|---------|
| **Practice Mode** | Self-directed learning with AI feedback and study notes |
| **Exam Mode** | Structured assessments with results submission |

---

## Getting Started

### Setting Up Your Library

1. Launch Muninn
2. Enter the path to your DICOM library folder
3. Click "Scan Library"
4. Your courses and cases will be detected automatically

### Library Structure

Muninn expects cases organized in folders:

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

---

## Practice Mode

### Starting a Practice Session

1. Select a course from the library browser
2. Cases load in the order defined by the course
3. Optionally enable shuffle mode for random order

### Viewing Cases

**DICOM Viewer:**
- Scroll through slices with mouse wheel or arrow keys
- Adjust window/level by clicking and dragging
- Use preset buttons for common window settings (Soft Tissue, Lung, Bone, Brain)

**Case Information:**
- Clinical history is shown at the top
- Modality and difficulty are displayed

### Answering Cases

**Short Form (Plain Films, Rapid Reporting):**
- Findings: Describe what you see
- Diagnosis: Your primary diagnosis
- Differential: Alternative diagnoses to consider

**Long Form (CT/MRI):**
- Findings: Detailed description
- Diagnosis: Primary diagnosis
- Differential: Alternatives with reasoning
- Management: Recommended next steps

### Getting Feedback

After submitting your answer:

1. **AI Feedback** (if configured):
   - Compares your answer to the model answer
   - Highlights what you got right
   - Suggests improvements
   - Provides learning points

2. **Study Notes**:
   - Model answer written by the case author
   - Key findings explained
   - Differential considerations
   - Management recommendations
   - Teaching points

3. **Key Slices**:
   - Jump to important findings
   - Each slice has optimal window/level
   - Labels explain what to look for

### Self-Rating

Rate your performance 1-5:
- 1: Completely wrong
- 2: Major errors
- 3: Partial credit
- 4: Minor errors
- 5: Perfect answer

Ratings are tracked for progress monitoring.

### Progress Tracking

- View your attempt history
- See improvement over time
- Filter by course, modality, or difficulty
- Review previous answers

### AI Providers

Configure AI feedback in Settings:

| Provider | Model | Notes |
|----------|-------|-------|
| Cerebras | Llama 3.3 70B | Fast, free tier available |
| Google AI | Gemini | Good quality feedback |
| Anthropic | Claude | High quality feedback |
| OpenAI | GPT-4 | Industry standard |
| Ollama | Local models | Private, runs on your machine |

---

## Exam Mode

### Taking an Exam

1. Click **Start Exam** in the Library Browser
2. Select your name from the dropdown (or enter manually)
3. Click **Browse** to select the exam config file
4. Click **Begin Exam**

### During the Exam

**What's different from Practice Mode:**
- Cases appear in fixed order (no shuffle)
- Study notes and hints are hidden
- AI feedback is disabled
- Key slices are not shown
- Timer tracks time per case

**Answering:**
1. Review the case and clinical history
2. Enter your findings, diagnosis, and differential
3. Click **Submit**
4. Your answer is saved locally AND to the central results file
5. Click **Next Case** to continue

### After Completing

- Your results are saved to the shared results file
- Your administrator will mark your submissions
- Results may be provided individually or as group feedback

### Troubleshooting Exams

**"Failed to submit to central results"**
- Check your network connection
- Verify you can access the shared folder
- Ask your administrator about write permissions

**Cases not loading**
- Verify the case paths are accessible from your machine
- Check that the DICOM files exist

---

## Settings

### Appearance

- **Theme**: Midnight, Dark, Slate, Emerald, Light
- Themes affect the entire interface

### AI Configuration

- **Provider**: Select your preferred AI service
- **API Key**: Enter your API key (stored locally)
- **Test Connection**: Verify your setup works

### Study Options

- **Hide Spoilers**: Hides diagnosis from case list
- **Show Hints**: Enables hint system (if available)
- **Auto-advance**: Automatically load next case after rating

---

## Keyboard Shortcuts

| Key | Action |
|-----|--------|
| ↑/↓ | Previous/Next slice |
| ←/→ | Previous/Next slice |
| Scroll | Navigate slices |
| Click+Drag | Adjust window/level |

---

## Data Files

Muninn reads these files from each case folder:

### case_data.json

```json
{
  "diagnosis": "Acute Appendicitis",
  "history": "25-year-old with RLQ pain and fever",
  "modality": "CT",
  "body_region": "Abdomen",
  "difficulty": "Bread & Butter",
  "differential": ["Appendicitis", "Mesenteric adenitis", "Crohn's"]
}
```

### key_slices.json

```json
[
  {
    "series_index": 0,
    "slice_number": 85,
    "finding": "Dilated appendix (12mm)",
    "window_width": 400,
    "window_level": 40
  }
]
```

### STUDY_NOTES.md

Markdown file containing:
- Key findings
- Diagnosis explanation
- Differential considerations
- Management recommendations
- Teaching points

---

## Tips for Effective Practice

### For Beginners

- Start with "Bread & Butter" difficulty cases
- Read the clinical history carefully
- Describe findings systematically
- Use a structured reporting approach

### For Intermediate Users

- Challenge yourself with harder cases
- Time yourself to build speed
- Review cases you got wrong
- Focus on pattern recognition

### For Advanced Users

- Practice under exam conditions
- Work on generating differentials
- Focus on management decisions
- Teach others using the study notes

### General Tips

- Practice regularly (daily if possible)
- Review your weak areas
- Use AI feedback to identify knowledge gaps
- Don't rush - accuracy matters more than speed initially
