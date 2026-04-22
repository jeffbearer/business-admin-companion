---
name: powerbi
description: >
  Power BI workspace and report management via REST API. Use when the admin
  asks about Power BI dashboards, reports, datasets, refreshes, or workspaces.
  Covers listing workspaces, viewing reports/dashboards, triggering dataset
  refreshes, and checking refresh history.
---

# Power BI Integration

Provides direct access to the Power BI REST API for workspace and report management.

## Prerequisites

Before first use, check that Azure CLI is installed:
```powershell
az --version
```

If not installed, guide the admin to install it:
```powershell
winget install Microsoft.AzureCLI
```

Then close and reopen Terminal. After install, the admin must sign in once:
```powershell
az login
```

A browser window will open — sign in with their Microsoft account.

## Authentication

Get a token using Azure CLI (the user must have `az login` completed):

```powershell
$token = (az account get-access-token --resource https://analysis.windows.net/powerbi/api --query accessToken -o tsv)
$headers = @{ Authorization = "Bearer $token" }
```

All API calls use:
```
Authorization: Bearer $token
Base URL: https://api.powerbi.com/v1.0/myorg
```

If any call returns 401/403, run `az login` and let the user complete the browser flow.

## API Reference

### List Workspaces (Groups)

```powershell
$workspaces = Invoke-RestMethod -Uri "https://api.powerbi.com/v1.0/myorg/groups" -Headers $headers
$workspaces.value | Select-Object id, name, type, state | Format-Table
```

Returns workspace id, name, type, state. Use workspace `id` for all subsequent calls.

### List Reports in a Workspace

```powershell
$reports = Invoke-RestMethod -Uri "https://api.powerbi.com/v1.0/myorg/groups/$workspaceId/reports" -Headers $headers
$reports.value | Select-Object id, name, webUrl, datasetId | Format-Table
```

Key fields: `id`, `name`, `webUrl`, `datasetId`

### Open a Report in Browser

Use `webUrl` from the report listing:
```powershell
Start-Process $report.webUrl
```

### List Dashboards in a Workspace

```powershell
$dashboards = Invoke-RestMethod -Uri "https://api.powerbi.com/v1.0/myorg/groups/$workspaceId/dashboards" -Headers $headers
$dashboards.value | Select-Object id, displayName, webUrl | Format-Table
```

Key fields: `id`, `displayName`, `webUrl`

### List Datasets in a Workspace

```powershell
$datasets = Invoke-RestMethod -Uri "https://api.powerbi.com/v1.0/myorg/groups/$workspaceId/datasets" -Headers $headers
$datasets.value | Select-Object id, name, isRefreshable, configuredBy | Format-Table
```

Key fields: `id`, `name`, `isRefreshable`, `configuredBy`

### Trigger Dataset Refresh

```powershell
Invoke-RestMethod -Method Post `
  -Uri "https://api.powerbi.com/v1.0/myorg/groups/$workspaceId/datasets/$datasetId/refreshes" `
  -Headers $headers -ContentType "application/json"
```

Returns 202 Accepted on success. The refresh runs asynchronously.

### Check Refresh History

```powershell
$refreshes = Invoke-RestMethod `
  -Uri "https://api.powerbi.com/v1.0/myorg/groups/$workspaceId/datasets/$datasetId/refreshes?`$top=5" `
  -Headers $headers
$refreshes.value | Select-Object status, startTime, endTime | Format-Table
```

Key fields: `status` (Completed/Failed/Unknown/Disabled), `startTime`, `endTime`, `serviceExceptionJson`

### List Dataset Data Sources

```powershell
$sources = Invoke-RestMethod `
  -Uri "https://api.powerbi.com/v1.0/myorg/groups/$workspaceId/datasets/$datasetId/datasources" `
  -Headers $headers
$sources.value | Format-List
```

### Get Workspace Users (Access List)

```powershell
$users = Invoke-RestMethod -Uri "https://api.powerbi.com/v1.0/myorg/groups/$workspaceId/users" -Headers $headers
$users.value | Select-Object emailAddress, groupUserAccessRight | Format-Table
```

Key fields: `emailAddress`, `groupUserAccessRight` (Admin/Member/Contributor/Viewer)

## Common Workflows

### "What reports do we have?"
1. List workspaces → show names and IDs
2. Ask which workspace (or search by name)
3. List reports in that workspace → show names and webUrls

### "Refresh the sales data"
1. List workspaces → find the right one
2. List datasets → find by name
3. Trigger refresh
4. Wait ~30 seconds, then check refresh history for status

### "Is the dashboard up to date?"
1. Find the dataset behind the dashboard
2. Check refresh history → show last successful refresh time
3. If stale, offer to trigger a refresh

### "Who has access to [workspace]?"
1. Get workspace users → show email, role

## Formatting

- Always show report/dashboard URLs so the user can open them
- Show refresh times in US Eastern (ET)
- Present workspace/report listings as clean tables
- When a refresh fails, show the `serviceExceptionJson` error message

## Limitations

- Cannot execute DAX queries (requires XMLA endpoint, not REST)
- Cannot edit report visuals (design-time only in Power BI Desktop/Service)
- Cannot download .pbix files via this API without admin scope
- Refresh triggers are rate-limited by Power BI (typically 8/day for Pro, 48 for Premium)
