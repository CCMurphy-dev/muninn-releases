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

### First Launch - Department Setup

On first launch, Muninn asks you to select your department's shared training folder.

1. Launch Muninn for the first time
2. The "Welcome to Muninn" dialog appears
3. Click **Browse** and select the folder provided by your training coordinator
4. Click **Continue**

The app automatically:
- Loads your department's case library
- Connects to the trainee registry (for exam name selection)
- Configures paths for exam submissions

> **Note**: If you're not sure which folder to select, ask your training coordinator or IT department. It's typically a network drive or shared folder.

You can click **Skip for Now** to configure this later, but you'll need to manually enter the library path.

### Trainee Identification

After setting up your department folder, Muninn prompts you to identify yourself from the trainee registry.

1. A "Who are you?" dialog appears
2. Search for your name or scroll through the list
3. Select your name and click **Continue**

Your trainee profile is used to:
- **Personalize recommendations**: Filter courses appropriate for your training level
- **Track your progress**: Link practice attempts and exam results to your profile
- **Pre-fill exam details**: Your name appears automatically when taking exams

Your name and training level appear in the app header. To change your profile, go to **Settings** > **Trainee Profile** > **Change**.

> **Note**: If your name doesn't appear in the registry, contact your training coordinator to be added.

### Manual Library Setup

If you're not using a department folder, or want to use a different library:

1. Enter the path to your DICOM library folder in the text field
2. Click "Scan Library"
3. Your courses and cases will be detected automatically

### Personal Library (Optional)

In addition to your department library, you can configure a personal library for your own cases:

1. Go to **Settings**
2. Scroll to the **Personal Library** section
3. Click **Browse** and select your personal case folder
4. Click **Save Settings**

Once configured, a library switcher appears in the Library Browser header:

```
[Department] [Personal]
```

Click either button to switch between libraries. Your personal library can contain:
- Cases you've collected independently
- Study material from courses you've purchased
- Cases shared by colleagues outside your department

> **Note**: Trainee identification is linked to your department. When using your personal library, you remain identified as your department trainee profile.

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

### Filtering Courses

The Library Browser includes filters to help you find appropriate courses:

**Modality Filter:**
- Click modality buttons (CT, MRI, XR, US) to show only courses of that type
- Click "All" to show all modalities

**Specialty Filter:**
- Use the dropdown to filter by specialty (Neuro, MSK, Chest, Abdomen, etc.)
- Select "All Specialties" to show everything

**Level Filter:**
- If you've identified yourself as a trainee, you can check **"Hide courses above my level"**
- This hides advanced courses (e.g., FRCR 2B preparation) from trainees who aren't yet at that level
- Courses without level restrictions are always shown

Filters combine with AND logic - selecting CT modality and Neuro specialty shows only CT Neuro courses.

### Starting a Practice Session

1. Select a course from the library browser (use filters to narrow down)
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
2. Select your name from the dropdown (if your department uses a trainee registry) or enter it manually
3. Click **Browse** to select the exam config file (your examiner will provide this location)
4. Click **Begin Exam**

> **Tip**: If you've configured your department folder, the trainee dropdown should show your name. If not, type your name exactly as it appears in departmental records.

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

### Trainee Profile

Your identified trainee profile is shown here:
- **Name**: Your name as it appears in the registry
- **Level**: Your training level (ST1-ST5, Fellow, Consultant)
- **Change**: Opens the trainee selector to switch profiles (useful on shared workstations)

### Department

- **Department Folder**: Path to your department's shared folder
- Click **Browse** to change the department folder

### Personal Library

- **Personal Library Path**: Optional path to your own case library
- Click **Browse** to set up a personal library
- Click **Remove** to clear the personal library configuration

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
