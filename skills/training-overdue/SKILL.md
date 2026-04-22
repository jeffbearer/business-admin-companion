---
name: training-overdue
description: >
  Check Power BI Required Training Dashboard for overdue training and send
  notification emails. Use when the admin asks about overdue training,
  training compliance, training notifications, or training report.
  Supports filtering by any manager alias (e.g., MANSAH).
---

# Training Overdue Notification Workflow

## Overview

Queries the Required Training Dashboard in Power BI to find people with overdue
training under a specified manager's org, generates a summary report, previews
notification emails, and sends them only after explicit admin confirmation.

## Trigger Phrases

- "check overdue training"
- "training report for MANSAH"
- "who has overdue training"
- "send training notifications"
- "training compliance check"
- "past due training for [alias]"

## Report Details

- **Report ID**: `c2390d89-5de8-474a-aa2d-fb29b2998d65`
- **Tab**: Individual Consumption Status
- **Power BI URL**: https://msit.powerbi.com/groups/me/reports/c2390d89-5de8-474a-aa2d-fb29b2998d65/ReportSectiona49cbaf31e7fbdd23326?experience=power-bi

## Prerequisites

This skill requires Azure CLI for Power BI API access. Before first use,
check that it's installed by running `az --version`. If missing, install with:
```powershell
winget install Microsoft.AzureCLI
```
Then close and reopen Terminal, and run `az login` to sign in.

## Workflow Steps

### Step 1: Identify Scope

Ask the admin which manager's org to check if not specified:
> Which manager alias should I filter by? (e.g., MANSAH)

### Step 2: Extract Data from Power BI

Authenticate and query the dataset behind the report.

**Option A — Export underlying dataset (preferred)**

All commands use PowerShell:
```powershell
$token = (az account get-access-token --resource https://analysis.windows.net/powerbi/api --query accessToken -o tsv)
$headers = @{ Authorization = "Bearer $token" }

$report = Invoke-RestMethod -Uri "https://api.powerbi.com/v1.0/myorg/reports/c2390d89-5de8-474a-aa2d-fb29b2998d65" -Headers $headers
$datasetId = $report.datasetId
```

Then export the report data using the Export API:
```powershell
# Initiate export of the "Individual Consumption Status" page
$body = @{
  format = "CSV"
  powerBIReportConfiguration = @{
    pages = @(@{ pageName = "ReportSectiona49cbaf31e7fbdd23326" })
  }
} | ConvertTo-Json -Depth 4

$export = Invoke-RestMethod -Method Post `
  -Uri "https://api.powerbi.com/v1.0/myorg/reports/c2390d89-5de8-474a-aa2d-fb29b2998d65/ExportTo" `
  -Headers $headers -ContentType "application/json" -Body $body

# Poll for completion (exports can take 30-120 seconds)
do {
  Start-Sleep -Seconds 5
  $status = Invoke-RestMethod `
    -Uri "https://api.powerbi.com/v1.0/myorg/reports/c2390d89-5de8-474a-aa2d-fb29b2998d65/exports/$($export.id)" `
    -Headers $headers
} while ($status.status -notin @("Succeeded", "Failed"))

if ($status.status -eq "Failed") {
  Write-Error "Export failed — falling back to manual export (Option B)"
  # Proceed to Option B
}

# Download the CSV
$csvPath = Join-Path $env:TEMP "training_export.csv"
Invoke-RestMethod `
  -Uri "https://api.powerbi.com/v1.0/myorg/reports/c2390d89-5de8-474a-aa2d-fb29b2998d65/exports/$($export.id)/file" `
  -Headers $headers -OutFile $csvPath
```

Filter the exported data:
```powershell
$data = Import-Csv -Path $csvPath
# Inspect column names on first run:
# ($data | Select-Object -First 1).PSObject.Properties.Name

# Filter: manager matches alias AND completion status is "Overdue"
$overdue = $data | Where-Object {
  $_.'Manager Alias' -eq $managerAlias -and
  $_.'Course Completion Status' -eq 'Overdue'
}
```

**Note:** The column names above (`Manager Alias`, `Course Completion Status`)
are guesses — see Field Mapping below. On first run, inspect the actual headers
with `($data | Select-Object -First 1).PSObject.Properties.Name` and ask the
admin to confirm the mapping. Remember the mapping for future runs.

**Option B — Guided manual export (fallback)**

If the API export fails (permissions, personal workspace limitations, or the
report is shared to the admin and not owned by them):
1. Open the report for the admin:
   ```powershell
   Start-Process "https://msit.powerbi.com/groups/me/reports/c2390d89-5de8-474a-aa2d-fb29b2998d65/ReportSectiona49cbaf31e7fbdd23326?experience=power-bi"
   ```
2. Guide the admin to:
   - Navigate to the "Individual Consumption Status" tab
   - Filter by the manager alias
   - Filter completion status = Overdue
   - Click on the table visual → "..." → "Export data" → CSV
3. Ask the admin to provide the file path to the exported CSV
4. Load and continue:
   ```powershell
   $data = Import-Csv -Path "C:\Users\admin\Downloads\export.csv"
   ```
5. Continue from Step 3

### Step 3: Generate Report

Present findings in two parts:

**Summary:**
> **Overdue Training Report — {manager alias}'s Org**
> - **{N}** people with overdue training
> - **{M}** total overdue courses
> - Date range: {earliest due date} – {latest due date}

**Detail Table (per person):**

| Person | Alias | Manager | Course Title | Due Date |
|--------|-------|---------|-------------|----------|
| Jane Smith | janesm | Brian Lee | Security Fundamentals | 2026-03-15 |
| ... | ... | ... | ... | ... |

### Step 4: Preview Email

Show a rendered preview of what the email will look like for the first person:

> **To:** janesm@microsoft.com
> **CC:** brianl@microsoft.com, {admin email}
> **Subject:** Training Overdue - janesm - 2026-03-15 - Security Fundamentals
>
> Hello Jane,
>
> You are receiving this e-mail as you have not completed the below Overdue
> Training. Please prioritize this training as soon as possible so we can make
> 100% completion for {manager name}'s org.
>
> **Security Fundamentals** - 2026-03-15 - Overdue
>
> Please visit Viva Learning (https://aka.ms/learning/assigned) for Required
> Trainings and to see what specific courses you still need to complete.
>
> Resources:
> - For general questions, utilize the new AskLearning QuickHelp site.
> - For issues with course registration, status, or technical issues, use the
>   Live Chat (https://asklearningchat.powerappsportals.com/) or email
>   asklearning@microsoft.com.
> - Please visit the Microsoft Viva Learning (https://aka.ms/learning/assigned)
>   to see what specific courses you still need to complete.
>
> If you have recently completed the training listed above Thank you for being
> proactive it does take some time for PowerBI to update the status.

Then state:
> This is a preview for 1 of {N} people. **{N} emails** will be sent.
> Each person gets one email per overdue course.
>
> **Ready to send? (yes/no)**

### Step 5: Send Emails

⚠️ **CRITICAL: NEVER send emails without an explicit affirmative response.**
Wait for "yes", "send", "go", "do it" or similar. Any hesitation or "let me
think" = do NOT send. Ask again.

For each overdue person+course, send via the mail MCP tool:
- **To**: the person's email
- **CC**: the person's manager email AND the admin's own email
- **Subject**: `Training Overdue - {alias} - {due date} - {course title}`
- **Content Type**: `HTML`
- **Body**: Use the HTML template above

After sending, report:
> ✅ Sent {N} notification emails.
> - {list of recipients and courses}

## Email Body Template

Send as **HTML** (`contentType: "HTML"` in the mail MCP tool).

```html
<p>Hello {name},</p>

<p>You are receiving this e-mail as you have not completed the below Overdue Training. Please prioritize this training as soon as possible so we can make 100% completion for {manager name}'s org.</p>

<p><strong>{Course Title}</strong> - {due date} - {completion status}</p>

<p>Please visit <a href="https://aka.ms/learning/assigned">Viva Learning</a> for Required Trainings and to see what specific courses you still need to complete.</p>

<p><strong>Resources:</strong></p>
<ul>
  <li>For general questions, utilize the new <a href="https://asklearningchat.powerappsportals.com/">AskLearning QuickHelp</a> site.</li>
  <li>For issues with course registration, status, or technical issues, use the <a href="https://asklearningchat.powerappsportals.com/">Live Chat</a> or email <a href="mailto:asklearning@microsoft.com">asklearning@microsoft.com</a>.</li>
  <li>Please visit <a href="https://aka.ms/learning/assigned">Microsoft Viva Learning</a> to see what specific courses you still need to complete.</li>
</ul>

<p>If you have recently completed the training listed above — thank you for being proactive! It does take some time for PowerBI to update the status.</p>

<p>Thank you,<br>{admin name}</p>
```

## Field Mapping

The exact column names depend on the Power BI dataset. Common expected columns:

| Concept | Likely Column Name |
|---------|-------------------|
| Person name | Display Name, Employee Name, Full Name |
| Person alias/email | Alias, UPN, Email, User Principal Name |
| Manager | Manager, Manager Name, Reports To |
| Course title | Course Title, Training Name, Course Name |
| Due date | Due Date, Completion Due Date |
| Completion status | Course Completion Status, Status |
| Org filter | Manager Alias, Manager L1-L8, Org |

On first run, inspect the actual column names from the export and map them.
If column names don't match, ask the admin to clarify which columns to use
and remember the mapping for future runs.

## Notes

- One email per person per overdue course (not one consolidated email).
  This ensures each course and person is visible in the subject line
  for easy tracking and follow-up.
- The "{manager name}'s org" phrasing in the template is dynamic — resolve
  the manager's display name from the alias used in the scope filter.
- The Viva Learning link had a typo in the original template ("httpa://") —
  corrected to "https://" in this skill.
- If a person has multiple overdue courses, they get multiple emails —
  this is intentional so each course appears in its own subject line.
