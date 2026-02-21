# Exam Distribution Mail Merge Guide

This guide explains how to use the CSV export from Muninn Admin to send personalized exam notifications to trainees using Microsoft Outlook's mail merge feature.

## Prerequisites

- Microsoft Outlook (desktop version)
- Microsoft Word (for mail merge)
- Exam distribution CSV exported from Muninn Admin

## Step 1: Export Distribution CSV

1. Open **Muninn Admin** and go to **Exams**
2. Load your exam configuration
3. Click **Distribute** button
4. Select trainees (or use pre-selected eligible trainees)
5. Configure PIN options:
   - Check "Generate PINs for trainees without one" (recommended)
   - Only check "Regenerate ALL PINs" if you need to reset existing PINs
6. Click **Generate Distribution**
7. Review the preview, then click **Export CSV**
8. Save the file (e.g., `st1-oncall-exam-distribution.csv`)

## Step 2: Prepare Your Email Template in Word

1. Open Microsoft Word
2. Create your email body text:

```
Dear [Name],

You have been scheduled to take the following exam:

Exam: [Exam Name]
Exam File Location: [Exam Path]
Your PIN: [PIN]

Instructions:
1. Open Muninn
2. Click "Start Exam"
3. Open the exam file
4. Select your name from the drop down menu
5. Enter your PIN when prompted

If you have any questions, please contact your training coordinator.

Best regards,
[Your Name]
```

3. Go to **Mailings** tab > **Start Mail Merge** > **E-mail Messages**

## Step 3: Connect to Your CSV

1. **Mailings** tab > **Select Recipients** > **Use an Existing List**
2. Browse to your exported CSV file
3. Click **Open**

## Step 4: Insert Merge Fields

Replace the placeholders with merge fields:

1. Click where you want to insert a field
2. **Mailings** tab > **Insert Merge Field**
3. Select the appropriate field:
   - `[Name]` > Insert `name` field
   - `[Exam Name]` > Insert `exam_name` field
   - `[Exam Path]` > Insert `exam_path` field
   - `[PIN]` > Insert `pin` field

## Step 5: Preview and Send

1. **Mailings** tab > **Preview Results** to check your merged emails
2. Use arrow buttons to cycle through recipients
3. When satisfied, click **Finish & Merge** > **Send Email Messages**
4. In the dialog:
   - **To:** Select `email` field
   - **Subject line:** Enter your subject (e.g., "ST1 On-Call Exam - Action Required")
   - **Mail format:** HTML (recommended)
5. Click **OK** to send

## CSV Columns Reference

| Column | Description |
|--------|-------------|
| `name` | Trainee's full name |
| `email` | Trainee's email address |
| `level` | Training level (ST1, ST2, etc.) |
| `exam_name` | Name of the exam |
| `exam_path` | Network path to the exam config file |
| `pin` | Trainee's PIN (new or "(existing)" if not regenerated) |

## Tips

- **Test first**: Send a test email to yourself by creating a CSV with just your email
- **Check spam**: Ask trainees to check spam/junk folders
- **Save your template**: Keep your Word template for future exams
- **PIN security**: The CSV contains plain-text PINs - delete it after sending

## Troubleshooting

### PIN shows "(existing)"

This means the trainee already had a PIN that wasn't regenerated. If you need to include their PIN, check "Regenerate ALL PINs" when exporting.

### Emails not arriving

- Check hospital email policies for bulk sending limits
- Try sending in smaller batches
- Verify email addresses in the CSV are correct

### Merge fields showing as codes

- Press Alt+F9 to toggle field code display
- Or right-click > Toggle Field Codes

## Alternative: Manual Distribution

If mail merge is not available, you can:

1. Export the CSV
2. Open in Excel
3. Manually send emails to each trainee using the information in the spreadsheet

## Security Considerations

- The exported CSV contains plain-text PINs
- Delete the CSV file after sending emails
- Do not share the CSV file or store it in shared locations
- Consider regenerating PINs periodically for security
