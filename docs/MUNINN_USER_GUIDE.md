# Muninn User Guide

Muninn is a desktop application for practicing radiology with curated case playlists.

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
- Loads your department's playlists
- Connects to the trainee registry (for progress tracking and exam name selection)
- Configures paths for exam submissions

> **Note**: If you're not sure which folder to select, ask your training coordinator or IT department. It's typically a network drive or shared folder.

You can click **Skip for Now** to configure this later, but you'll need to manually enter the library path.

### Trainee Identification

After setting up your department folder, Muninn prompts you to identify yourself from the trainee registry.

1. A "Who are you?" dialog appears
2. Search for your name or scroll through the list
3. Select your name and click **Continue**

Your trainee profile is used to:
- **Personalize content**: Filter playlists appropriate for your training level
- **Track your progress**: Link practice attempts and exam results to your profile
- **Resume where you left off**: Each playlist remembers your position
- **Pre-fill exam details**: Your name appears automatically when taking exams

Your name and training level appear in the app header. To change your profile, go to **Settings** > **Trainee Profile** > **Change**.

> **Note**: If your name doesn't appear in the registry, contact your training coordinator to be added.

### Personal Library (Optional)

In addition to your department library, you can configure a personal library for your own cases:

1. Go to **Settings**
2. Scroll to the **Personal Library** section
3. Click **Browse** and select your personal case folder
4. Click **Save Settings**

Once configured, a library switcher appears in the header. Your personal library can contain:
- Cases you've collected independently
- Study material from courses you've purchased
- Cases shared by colleagues outside your department

> **Note**: Trainee identification is linked to your department. When using your personal library, you remain identified as your department trainee profile.

---

## Practice Mode

### Browsing Playlists

When you open Muninn, you'll see a list of playlists created by your department. Each playlist card shows:

- **Name**: The playlist title
- **Description**: What the playlist covers
- **Author**: Who created the playlist
- **Case count**: How many cases are included
- **Progress**: How many cases you've completed (e.g., "5/12")
- **Modality**: CT, MRI, XR, US, or mixed
- **Level**: Recommended training level

### Filtering Playlists

Use the filters at the top to find relevant playlists:

**Modality Filter:**
- Click modality buttons (CT, MRI, XR, US) to show only playlists of that type
- Click "All" to show all modalities

**Specialty Filter:**
- Use the dropdown to filter by specialty (Neuro, MSK, Chest, Abdomen, etc.)
- Select "All Specialties" to show everything

**Search:**
- Type in the search box to filter by playlist name or description

**Level Filter:**
- If you've identified yourself as a trainee, playlists above your level may be hidden
- This prevents junior trainees from being overwhelmed by advanced content

### Starting Practice

1. Click on a playlist card
2. The playlist loads and starts from your last position (or the first case if you're starting fresh)
3. Progress is saved automatically as you complete cases

When you finish a playlist, it loops back to the first case so you can continue practicing.

### Viewing Cases

**DICOM Viewer:**
- Scroll through slices with mouse wheel or arrow keys
- Adjust window/level by clicking and dragging
- Use preset buttons for common window settings (Soft Tissue, Lung, Bone, Brain)

**Case Information:**
- Clinical history is shown at the top
- Modality and difficulty are displayed
- Playlist-specific notes may appear (added by the playlist author)

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

- Progress is tracked per playlist
- View your attempt history in Settings
- See improvement over time
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

1. Click **Start Exam** in the header
2. Select your name from the dropdown (if your department uses a trainee registry) or enter it manually
3. **Enter your PIN** (if required by your department):
   - A PIN entry dialog appears after selecting your name
   - Enter your 4-6 digit PIN
   - Click **Continue**
   - If incorrect, try again or contact your supervisor
4. Click **Browse** to select the exam config file (your examiner will provide this location)
5. Click **Begin Exam**

> **Tip**: If you've configured your department folder, the trainee dropdown should show your name. If not, type your name exactly as it appears in departmental records.

> **Note**: PINs are only required if your department has configured them for exam access. Practice mode never requires a PIN.

### During the Exam

**What's different from Practice Mode:**
- Cases are shuffled into a random order (each trainee gets a unique order)
- Study notes and hints are hidden
- AI feedback is disabled
- Key slices are not shown
- Timer tracks time per case
- Case navigation shows "Case 1, 2, 3..." instead of diagnoses

**For Multi-Component Cases (e.g., CXR + CTPA):**
- Components unlock sequentially
- You must view Component 1 (e.g., CXR) before Component 2 (e.g., CTPA) becomes accessible
- This prevents "spoilers" from later imaging
- Locked components show a lock icon on their tab

**Answering:**
1. Review the case and clinical history
2. Enter your findings, diagnosis, and differential
3. Click **Submit**
4. Your answer is saved locally AND to the central tracking database
5. Review your submitted answer
6. **Edit** your answer if needed before moving on
7. **Flag** the case if you want to review it later
8. Click **Next Case** to continue (or **Show Summary** on the last case)

### Exam Review and Summary

After answering cases, you can review your progress:

**Flagging Cases:**
- Click the flag icon on any case to mark it for review
- Flagged cases appear with an amber indicator
- Use flags to mark cases you're uncertain about

**Exam Summary Screen:**
- Shows all cases with their status (Completed, Skipped, Pending)
- Highlights flagged cases that need review
- Click any case to go back and edit your answer
- **Go Back** returns to the current case
- **Finish Exam** completes the session

### After Completing

- Your results are saved to the department tracking database
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

**Component tabs showing lock icons**
- This is normal - view the first component before later ones unlock
- Scroll through the images in Component 1 to unlock Component 2

**PIN entry failing**
- Make sure you're entering the correct PIN (4-6 digits only)
- If you've forgotten your PIN, contact your training coordinator
- Your coordinator can reset your PIN in Muninn Admin

**PIN entry not appearing**
- PIN authentication is optional - your department may not use it
- If you expected a PIN prompt, check with your administrator

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

### History

- **Clear History**: Reset your practice progress
- This clears your local attempt history and playlist progress
- Use with caution - progress cannot be recovered

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

- Start with playlists designed for your level (ST1/ST2)
- Read the clinical history carefully
- Describe findings systematically
- Use a structured reporting approach

### For Intermediate Users

- Challenge yourself with harder playlists
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
- Complete entire playlists rather than jumping around
- Use AI feedback to identify knowledge gaps
- Don't rush - accuracy matters more than speed initially
