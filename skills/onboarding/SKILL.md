---
name: onboarding
description: >
  Use this skill when an admin is setting up Agency for the first time, asks
  "how do I get started", "set me up", or needs their preferences configured.
  Walks them through setup interactively — creates agency.toml, instruction
  files, environment variable, and personal agent. No technical knowledge required.
---

# Onboarding Skill

**IMPORTANT: This is an interactive skill. You MUST ask questions one at a time
and wait for the user's response before proceeding to the next question. Do NOT
summarize capabilities. Do NOT skip ahead. Start with question 1 immediately.**

**IMPORTANT: The admin is NOT technical. Do NOT tell them to run PowerShell
commands. YOU run all commands yourself using bash/shell tools. The admin
should never have to copy/paste commands, edit config files, or create
directories. You do all of that for them.**

## First Response

When this skill is invoked, your FIRST message must be exactly this:

> Great, let's get you set up! This takes about 2 minutes — I'll ask a few
> questions and then configure everything for you.
>
> **First: What is your full name?**

Then STOP and wait for their answer. Do not ask multiple questions at once.

## Question Flow

Ask these questions **one at a time**, in order. Wait for each answer before
asking the next:

1. What is your full name?
2. What is your Microsoft alias? (the part before @microsoft.com)
3. Who are the managers you support? Just give me their aliases and I'll
   look up their full names and titles automatically. Example: "brbarbis, aarono"
   If I can't find someone, I'll ask for their details.
4. What timezone do you work in? (e.g., Pacific Standard Time)
5. What are your working hours? (e.g., 8:00 AM - 5:00 PM)
6. Let me find your Teams channels — I'll pull up a list and you can pick
   the ones you use most. You can also add any I missed.
7. Do you use Microsoft Planner? If so, what is your main plan called?

## What to Create

After collecting answers, create ALL of the following:

### A. ~/.agency/agency.toml

```toml
[mcps.builtins]
calendar = true
mail = true
teams = true
planner = true
m365-user = true
m365-copilot = true
sharepoint = true
sharepoint-lists = true
word = true
workiq = true

[plugins]
default = [
  "market:business-admin-toolkit@agency-microsoft/playground",
  "market:ado-task-planner@agency-microsoft/playground",
  "market:agentic-journal@agency-microsoft/playground",
]
```

NOTE: Do NOT add business-admin-companion to the plugins list here.
It is already installed via `agency plugin install` and loads automatically.
Adding it to the toml causes a "plugin already loaded" error.

### B. ~/.copilot-global/.github/instructions/admin-preferences.instructions.md

Create the directory tree if it doesn't exist. Use frontmatter:

```yaml
---
applyTo: conversation
globs: "*"
---
```

Include sections filled in with the admin's answers:

- **Who I Am** — name, role, alias
- **Managers I Support** — name, alias@microsoft.com, title for each
- **Calendar Preferences:**
  - Default 30-min meetings, always include Teams link
  - When asked about "[manager]'s calendar", use ListCalendarView with
    their email as userIdentifier
  - When scheduling FOR a manager, create event with admin as organizer
    and manager as attendee
  - When finding time, use FindMeetingTimes with manager's email
  - Check for conflicts and warn
  - Admin's timezone, prefer morning slots unless specified
  - "The team" = direct reports of primary manager
  - "Block time on [manager]'s calendar" = event with manager as attendee
- **Email Preferences:**
  - Professional but warm tone
  - Show draft before sending unless "send it"
  - "On behalf of [Manager]" in signature when emailing for them
  - Preserve thread history on replies
- **Teams Preferences** — primary channels, clear formatting
- **People** — "the team" / "directs" / "skips" definitions
- **Task Management** — plan name if applicable
- **Document Search** — always show URLs
- **Recurring Routines:**
  - "Morning briefing" = primary manager's calendar + conflicts + prep list
  - "End of day" = session summary
  - "Weekly wrap-up" = agentic-journal summary
  - "Meeting prep for [meeting]" = attendees + emails + action items
- **Communication Style:**
  - Concise, action-oriented
  - ALWAYS show full URLs (terminal can't make text clickable)
  - Confirm before: sending, canceling, deleting
  - Don't confirm obvious lookups
- **Working Hours** — admin's and managers'
- **FY Calendar:**
  - FY26 = Jul 2025 – Jun 2026
  - Q1=Jul-Sep, Q2=Oct-Dec, Q3=Jan-Mar, Q4=Apr-Jun

### C. Environment Variable

The agent should run this command directly (do not ask the admin to run it):

```powershell
[Environment]::SetEnvironmentVariable("COPILOT_CUSTOM_INSTRUCTIONS_DIRS", (Join-Path $env:USERPROFILE ".copilot-global"), "User")
```

### D. PowerShell Alias

Ask the admin: "Want me to set up a shortcut so you can just type 'admin'
instead of the full launch command?"

If yes, the agent should run ALL of these commands directly — do not ask
the admin to copy/paste anything:

```powershell
# Create profile if it doesn't exist
if (!(Test-Path $PROFILE)) { New-Item -Path $PROFILE -ItemType File -Force }

# Add the alias function
Add-Content -Path $PROFILE -Value "`nfunction admin { agency copilot --agent business-admin-companion:business-admin-companion @args }"

# Fix execution policy if restricted
if ((Get-ExecutionPolicy -Scope CurrentUser) -eq 'Restricted') { Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force }
```

After running, tell the admin: "Done! After you close and reopen Terminal,
you can just type 'admin' to launch."

Note: On corp machines PowerShell 5 and PowerShell 7 use different
profile paths. $PROFILE auto-resolves for whichever shell is running.

### E. agency.toml

The agent should write the agency.toml file directly — do not ask the
admin to edit it manually. Create or overwrite ~/.agency/agency.toml
with the template from section A above. If the file already exists,
ask before overwriting.

### F. Preferences File

The agent should create the directory tree and write the preferences
file directly — do not ask the admin to create directories or edit files.
Create ~/.copilot-global/.github/instructions/admin-preferences.instructions.md
with the content from section B above.

### G. Completion Message

Tell the admin:
- Everything is set up
- Close this Terminal and open a new one
- If the alias was set up, just type "admin" to launch
- Otherwise: agency copilot --agent business-admin-companion:business-admin-companion
- The first time each M365 tool is used, a browser sign-in will pop up (one-time)
- Show 3-4 example things to try

## Reconfiguration

If the admin wants to add a manager, change settings, or reconfigure:
- Read the existing files
- Ask only about what's changing
- Update in place — don't recreate everything

## Error Handling

- If a directory can't be created, try with elevated permissions
- If a file already exists, ask before overwriting
- If the environment variable is already set, confirm before changing
- Never show raw error messages — explain what happened in plain language
