# Exam Mode Guide

Muninn supports a centralized exam mode where multiple trainees can take the same exam and submit their results to a shared file for marking.

## Overview

In exam mode:
- All trainees load the same exam configuration file
- Cases are loaded from existing paths in the DICOM library (no duplication needed)
- Results are submitted to a central JSON file (e.g., on a network share)
- Trainees select their name from a registry or enter it manually
- Learning features (study notes, hints, AI feedback) are hidden

## Setting Up an Exam (Admin)

### Option 1: Using Muninn Admin's Exam Builder (Recommended)

The easiest way to create exam configurations is using Muninn Admin's Exam Builder:

1. **Open Muninn Admin** and go to the **Exams** tab
2. **Browse your case library** - Select the root folder containing your DICOM cases
3. **Preview and select cases** - Check the cases you want to include
4. **Reorder cases** - Use the up/down buttons to set the exam order
5. **Configure settings**:
   - Enter the **Exam Name**
   - Set the **Results Path** (network share recommended)
   - Optionally link a **Trainee Registry**
6. **Save** - Click "Save Exam Config" and choose a location

The Exam Builder automatically:
- Scans nested folder structures to find cases
- Loads diagnoses from existing `case_data.json` files
- Detects modalities from metadata or folder names
- Creates properly formatted JSON config files

### Option 2: Manual Creation

Create a JSON file manually with the exam details and case references:

```json
{
  "exam_name": "ST1 On-Call Exam Jan 2026",
  "results_path": "/Volumes/Radiology/Exams/ST1-OnCall-Jan2026/results.json",
  "trainee_registry_path": "/Volumes/Radiology/Department/trainee_registry.json",

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
    }
  ]
}
```

### Configuration Fields

| Field | Required | Description |
|-------|----------|-------------|
| `exam_name` | Yes | Name of the exam (shown in results) |
| `results_path` | Yes | Full path to the shared results JSON file |
| `trainee_registry_path` | No | Path to trainee registry for name dropdown |
| `name` | Yes | Display name for the course |
| `course_type` | No | Usually "exam" |
| `modality` | No | "CT", "MR", "XR", "mixed", etc. |
| `cases` | Yes | Array of case references |

### Case Reference Fields

| Field | Required | Description |
|-------|----------|-------------|
| `id` | Yes | Unique identifier for the case (used in results) |
| `path` | Yes | Full path to the case folder in the DICOM library |
| `diagnosis` | No | Correct diagnosis (for marking - not shown to trainees) |

### Step 2: Set Up Trainee Registry (Optional)

If you want trainees to select their name from a dropdown (recommended for tracking):

1. Open **Muninn Admin** and go to the **Department** tab
2. Create or open a **Trainee Registry**
3. Add trainees (or import from CSV)
4. Note the registry file path
5. Include this path in your exam config's `trainee_registry_path` field

### Step 3: Distribute the Config File

Place the config file somewhere accessible to all trainees:
- Network share (recommended)
- Shared folder
- Email attachment

### Step 4: Create Results Directory

Ensure the directory for the results file exists and is writable by all trainees:

```bash
mkdir -p /Volumes/Radiology/Exams/ST1-OnCall-Jan2026/
chmod 777 /Volumes/Radiology/Exams/ST1-OnCall-Jan2026/
```

---

## Taking an Exam (Trainee)

### Step 1: Start Exam

1. Open Muninn
2. Click **Start Exam** button in the Library Browser

### Step 2: Select Your Name

- If a trainee registry is loaded, select your name from the dropdown
- Otherwise, type your full name in the text field

### Step 3: Load Exam Config

1. Click **Browse** to select the exam config file
2. The exam cases will be validated

### Step 4: Begin Exam

1. Click **Begin Exam**
2. Cases load in the order specified by the administrator
3. Learning features are automatically hidden

### Step 5: Complete Cases

1. Review each case and enter your findings, diagnosis, and differential
2. Click **Submit** when ready
3. Your answer is saved both locally and to the central results file
4. Click **Next Case** to proceed
5. Repeat until all cases are complete

---

## What's Hidden in Exam Mode

To maintain exam integrity, the following features are hidden:

- **Self-rating**: No rating buttons shown after submission
- **Model answers**: Diagnosis and study notes are not displayed
- **Key findings**: DICOM viewer key findings overlay is hidden
- **AI feedback**: AI feedback is disabled during exam mode
- **Case diagnoses**: Navigation shows "Case 1, 2, 3..." instead of diagnoses
- **Learning mode features**: Study notes toggle is hidden
- **Hints**: No hints available

Trainees only see their submitted answer and a confirmation message.

---

## Results File Format

The results file accumulates all submissions as JSON:

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
      "time_taken_seconds": 180,
      "exam_type": "short",
      "submitted_at": "2026-01-15T09:05:32Z"
    }
  ]
}
```

---

## Marking Results with Muninn Admin

### Using the Exam Marking Feature

1. Open **Muninn Admin** and go to the **Marking** tab
2. Click **Load Exam** and select the exam config file
3. Select a **Candidate** from the dropdown
4. Review their submissions:
   - View their answers alongside the DICOM images
   - Navigate through cases using the case list
5. **Mark each answer**:
   - Score 0-10
   - Add written feedback
6. **Save marks** as JSON or **Export to CSV**

### Generating Reports

Use the **Department** tab to:
- Generate training reports for a date range
- View individual trainee exam histories
- Export aggregate statistics

---

## Tips

### For Administrators

- **Use absolute paths** - Ensure all case paths work on all trainee machines
- **Test the config** - Load it yourself before distributing
- **Network share permissions** - Ensure trainees can write to the results directory
- **Backup results** - The results file is critical - back it up during the exam
- **Use trainee registry** - Makes identification consistent and marking easier

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

### Trainee registry not showing

- Verify the `trainee_registry_path` in the config is correct
- Check the registry file is accessible
- Ensure the registry contains active trainees
