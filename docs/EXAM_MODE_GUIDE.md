# Exam Mode Guide

Muninn supports a centralized exam mode where multiple trainees can take the same exam and submit their results to a shared database for marking.

## Overview

In exam mode:
- All trainees load the same exam configuration file
- Cases are automatically shuffled into a random order (each trainee gets a unique order)
- Multi-component cases unlock sequentially (e.g., view CXR before CTPA becomes accessible)
- Cases are loaded from existing paths in the DICOM library (no duplication needed)
- Results are submitted to the central tracking database (`muninn_tracking.db`)
- Trainees select their name from a registry or enter it manually
- Learning features (study notes, hints, AI feedback) are hidden
- Trainees can flag cases for review and edit answers before finishing

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
  "results_path": "legacy",

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
| `results_path` | Yes | Legacy field (required but not used - submissions go to tracking database) |
| `name` | Yes | Display name for the course |
| `course_type` | No | Usually "exam" |
| `modality` | No | "CT", "MR", "XR", "mixed", etc. |
| `cases` | Yes | Array of case references |

> **Note**: Exam submissions are stored in the department's tracking database (`muninn_tracking.db`), not the `results_path`. Trainees must have their department folder configured for submissions to work.

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

### Step 4: Verify Department Setup

Ensure trainees have:
- Department folder configured in Muninn settings
- Write access to the tracking database (`{department_root}/tracking/muninn_tracking.db`)
- Trainee profiles set up (for name selection)

The tracking database is automatically created by Muninn Admin when setting up the department.

---

## Taking an Exam (Trainee)

### Step 1: Start Exam

1. Open Muninn
2. Click **Start Exam** button in the Library Browser

### Step 2: Select Your Name

- If a trainee registry is loaded, select your name from the dropdown
- Otherwise, type your full name in the text field

### Step 3: Enter PIN (If Required)

If your department has set up PIN authentication for exams:

1. After selecting your name, a PIN entry dialog appears
2. Enter your 4-6 digit PIN
3. Click **Continue**
4. If the PIN is incorrect, you'll see an error message - try again or contact your supervisor

> **Note**: PINs are only required if configured by your department administrator. Practice mode does not require a PIN.

### Step 4: Load Exam Config

1. Click **Browse** to select the exam config file
2. The exam cases will be validated

### Step 5: Begin Exam

1. Click **Begin Exam**
2. Cases are automatically shuffled into a random order
3. Learning features are automatically hidden

### Step 6: Complete Cases

1. Review each case and enter your findings, diagnosis, and differential
2. Click **Submit** when ready
3. Your answer is saved both locally and to the central tracking database
4. Review your submitted answer on the confirmation screen
5. **Edit** your answer if needed
6. **Flag** the case if you want to review it later
7. Click **Next Case** to proceed
8. Repeat until all cases are complete

### Step 7: Review and Finish

1. After the last case, click **Show Summary** to review all cases
2. The summary shows:
   - All cases with their status (Completed, Skipped, Pending)
   - Flagged cases highlighted for easy identification
3. Click any case to go back and edit your answer
4. Click **Go Back** to return to the current case
5. Click **Finish Exam** when ready to submit

### Multi-Component Cases

Some cases contain multiple imaging studies (e.g., initial CXR followed by CTPA):

- Component tabs appear at the top of the DICOM viewer
- Components unlock sequentially - you must view Component 1 before Component 2 becomes accessible
- Locked components show a lock icon
- This prevents "spoilers" from later imaging that might give away the diagnosis
- Once a component is unlocked, it remains accessible for that case

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
- **Later components**: Multi-study cases unlock sequentially to prevent spoilers

After submission, trainees see their answer with options to edit or flag before moving on.

---

## Results Storage

Exam submissions are stored in the central department tracking database (`muninn_tracking.db`). Each submission includes:

| Field | Description |
|-------|-------------|
| `trainee_id` | Trainee identifier |
| `trainee_name` | Trainee's display name |
| `exam_name` | Name of the exam |
| `case_id` | Case identifier |
| `case_path` | Path to the case folder |
| `correct_diagnosis` | Expected diagnosis (for marking) |
| `submitted_diagnosis` | Trainee's submitted diagnosis |
| `submitted_findings` | Trainee's findings |
| `submitted_differential` | Trainee's differential diagnoses |
| `submitted_management` | Trainee's management plan (if long form) |
| `time_taken_seconds` | Time spent on the case |
| `exam_type` | "short" or "long" |
| `submitted_at` | Timestamp of submission |

The database is located at `{department_root}/tracking/muninn_tracking.db` and is shared across all trainees on the network.

### Audit Logging

All exam activity is logged to the audit trail:

| Action | When Logged |
|--------|-------------|
| `exam_start` | When trainee clicks "Begin Exam" |
| `exam_submit` | When trainee submits a case answer |
| `pin_failed` | When PIN verification fails (if using PINs) |

This audit trail can be used for:
- ARCP evidence (proving when trainees took exams)
- Security review (detecting unusual activity)
- Troubleshooting (tracking submission issues)

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
- **Use flags wisely** - Flag uncertain cases to review before finishing
- **Review before finishing** - Use the summary screen to check all your answers
- **Multi-component cases** - View all components before answering (Component 1 unlocks Component 2)

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

### Component tabs showing lock icons

- This is expected behavior for multi-component cases
- View and scroll through Component 1 to unlock Component 2
- Once unlocked, components remain accessible for that case

### Cases appear in different order than expected

- Cases are automatically shuffled when the exam starts
- Each trainee receives a unique random order
- This is intentional for fair assessment
- The original order is preserved in the exam config for marking

### PIN entry failing

- Ensure you're entering the correct 4-6 digit PIN
- PINs are numeric only (no letters or symbols)
- If you've forgotten your PIN, contact your supervisor or training coordinator
- Administrators can view and reset PINs in Muninn Admin > Department > Registry
- Failed PIN attempts are logged for security

### PIN entry not appearing

- PIN authentication is optional - your department may not require it
- If you expect a PIN but don't see the dialog, check with your administrator
