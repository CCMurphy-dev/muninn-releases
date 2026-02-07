# Exam Mode Guide

Muninn supports a centralized exam mode where multiple trainees can take the same exam and submit their results to a shared file for marking.

## Overview

In exam mode:
- All trainees load the same exam configuration file
- Cases are loaded from existing paths in the DICOM library (no duplication needed)
- Results are submitted to a central JSON file (e.g., on a network share)
- Trainees enter their name, which is recorded with each submission

## Setting Up an Exam (Admin)

### Step 1: Create the Exam Configuration File

Create a JSON file with the exam details and case references:

```json
{
  "exam_name": "ST1 On-Call Exam Jan 2026",
  "results_path": "/Volumes/Radiology/Exams/ST1-OnCall-Jan2026/results.json",

  "name": "ST1 On-Call Exam",
  "course_type": "exam",
  "modality": "mixed",

  "cases": [
    {
      "id": "case_01",
      "path": "/Volumes/Radiology/Library/Neuro/01_Acute_MCA_Infarct",
      "diagnosis": "Acute left MCA territory infarct"
    },
    {
      "id": "case_02",
      "path": "/Volumes/Radiology/Library/Chest/15_Tension_Pneumothorax",
      "diagnosis": "Right tension pneumothorax"
    },
    {
      "id": "case_03",
      "path": "/Volumes/Radiology/Library/Abdomen/08_Acute_Appendicitis",
      "diagnosis": "Acute appendicitis with perforation"
    },
    {
      "id": "case_04",
      "path": "/Volumes/Radiology/Library/MSK/22_Scaphoid_Fracture",
      "diagnosis": "Scaphoid waist fracture"
    },
    {
      "id": "case_05",
      "path": "/Volumes/Radiology/Library/Neuro/45_Subarachnoid_Haemorrhage",
      "diagnosis": "Subarachnoid haemorrhage - ruptured anterior communicating artery aneurysm"
    }
  ]
}
```

Save this as `exam-config.json` (or similar name).

### Configuration Fields

| Field | Required | Description |
|-------|----------|-------------|
| `exam_name` | Yes | Name of the exam (shown in results) |
| `results_path` | Yes | Full path to the shared results JSON file |
| `name` | Yes | Display name for the course |
| `course_type` | No | Usually "exam" |
| `modality` | No | "CT", "MR", "XR", "mixed", etc. |
| `cases` | Yes | Array of case references |

### Case Reference Fields

| Field | Required | Description |
|-------|----------|-------------|
| `id` | Yes | Unique identifier for the case (used in results) |
| `path` | Yes | Full path to the case folder in the DICOM library |
| `diagnosis` | No | Correct diagnosis (for marking - not shown to trainees if spoilers hidden) |

### Step 2: Distribute the Config File

Place the config file somewhere accessible to all trainees:
- Network share (recommended)
- Shared folder
- Email attachment

### Step 3: Create Results Directory

Ensure the directory for the results file exists and is writable by all trainees:

```bash
mkdir -p /Volumes/Radiology/Exams/ST1-OnCall-Jan2026/
chmod 777 /Volumes/Radiology/Exams/ST1-OnCall-Jan2026/
```

---

## Taking an Exam (Trainee)

### Step 1: Load the Exam Config

1. Open Muninn
2. Go to **Settings** (gear icon)
3. Scroll to **Exam Session**
4. Click **Load Exam Config**
5. Select the config file provided by your exam administrator

### Step 2: Enter Your Name

1. Enter your full name in the "Your Name" field
2. This name will be recorded with all your submissions

### Step 3: Save Settings

1. Click **Save**
2. The exam cases will load automatically
3. Settings panel will close and the first case will appear

### Step 4: Complete the Exam

1. Review each case and enter your findings, diagnosis, and differential
2. Click **Submit** when ready
3. Your answer is saved both locally and to the central results file
4. You'll see "Answer Submitted" confirmation - click **Next Case**
5. Repeat until all cases are complete

---

## What's Hidden in Exam Mode

To maintain exam integrity, the following features are hidden from trainees:

- **Self-rating**: No rating buttons shown after submission
- **Model answers**: Diagnosis and study notes are not displayed
- **Key findings**: DICOM viewer key findings overlay is hidden
- **AI feedback**: AI feedback is disabled during exam mode
- **Case diagnoses**: Navigation shows "Case 1, 2, 3..." instead of diagnoses
- **Learning mode features**: Study notes toggle is hidden

Trainees only see their submitted answer and a confirmation message.

---

## Results File Format

The results file is a JSON file that accumulates all submissions:

```json
{
  "exam_name": "ST1 On-Call Exam Jan 2026",
  "created_at": "2026-01-15T09:00:00Z",
  "results": [
    {
      "username": "John Smith",
      "exam_name": "ST1 On-Call Exam Jan 2026",
      "case_id": "case_01",
      "case_path": "/Volumes/Radiology/Library/Neuro/01_Acute_MCA_Infarct",
      "correct_diagnosis": "Acute left MCA territory infarct",
      "submitted_diagnosis": "Left MCA infarct",
      "submitted_findings": "Loss of grey-white differentiation in left MCA territory...",
      "submitted_differential": "1. Acute ischaemic stroke\n2. Hypoglycaemia",
      "submitted_management": null,
      "self_rating": 4,
      "time_taken_seconds": 180,
      "exam_type": "short",
      "submitted_at": "2026-01-15T09:05:32Z"
    },
    {
      "username": "Jane Doe",
      "exam_name": "ST1 On-Call Exam Jan 2026",
      "case_id": "case_01",
      "case_path": "/Volumes/Radiology/Library/Neuro/01_Acute_MCA_Infarct",
      "correct_diagnosis": "Acute left MCA territory infarct",
      "submitted_diagnosis": "Acute stroke - left hemisphere",
      "submitted_findings": "Sulcal effacement and loss of insular ribbon...",
      "submitted_differential": "Acute infarct vs tumour",
      "submitted_management": null,
      "self_rating": 3,
      "time_taken_seconds": 210,
      "exam_type": "short",
      "submitted_at": "2026-01-15T09:06:15Z"
    }
  ]
}
```

---

## Marking Results

The results JSON can be:
- Opened in a text editor
- Imported into a spreadsheet (parse JSON)
- Processed with a script for automated scoring

### Example: Export to CSV (Python)

```python
import json
import csv

with open('results.json', 'r') as f:
    data = json.load(f)

with open('results.csv', 'w', newline='') as f:
    writer = csv.writer(f)
    writer.writerow([
        'Username', 'Case ID', 'Correct Diagnosis',
        'Submitted Diagnosis', 'Self Rating', 'Time (s)'
    ])

    for r in data['results']:
        writer.writerow([
            r['username'],
            r['case_id'],
            r['correct_diagnosis'],
            r['submitted_diagnosis'],
            r['self_rating'],
            r['time_taken_seconds']
        ])
```

---

## Tips

### For Administrators

- **Use absolute paths** - Ensure all case paths work on all trainee machines
- **Test the config** - Load it yourself before distributing
- **Network share permissions** - Ensure trainees can write to the results directory
- **Backup results** - The results file is critical - back it up during the exam

### For Trainees

- **Don't close the app** - Your progress is saved, but closing mid-case may lose unsaved work
- **Check your name** - Verify your name is correct before starting
- **Network connection** - Ensure you can access the network share throughout the exam

---

## Troubleshooting

### "Failed to submit to central results"

- Check network connectivity
- Verify the results path is accessible
- Ensure you have write permissions to the directory

### Cases not loading

- Verify all case paths exist and are accessible
- Check that each case has a valid `case_data.json`
- Ensure DICOM files are present in the case folders

### Config file won't load

- Check JSON syntax is valid
- Ensure all required fields are present
- Verify `exam_name`, `results_path`, and `cases` array exist
