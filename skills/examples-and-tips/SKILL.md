---
name: examples-and-tips
description: >
  Use this skill when the admin asks "what can I say?", "show me examples",
  "how do I use this?", "what commands are there?", "help", "tips",
  "troubleshooting", "something went wrong", or needs to see what tools are
  installed. Provides example workflows, practical tips, quick reference,
  troubleshooting, and a list of installed tools.
---

# Examples, Tips & Reference

## Example Workflows

These show HOW to talk to Agency. Use natural language — no special syntax.

### Managing Your Manager's Calendar

View their schedule:
- "Show me Brian's calendar for Monday through Wednesday."
- "What does Brian have between 1pm and 5pm tomorrow?"

Find time for meetings:
- "Find a 1-hour slot that works for Brian and all his direct reports next week, preferably morning."
- "When is Brian free for a 30-minute call with Sarah Chen this week?"

Schedule meetings:
- "Schedule a meeting: Q4 Planning Review with Brian, Matt White, and the VP of Engineering. Next Tuesday 10am-11am, Building 33 Room 2001."
- "Set up a recurring weekly 1:1 between Brian and Sarah Chen, Mondays at 9am for 30 minutes."

Block focus time:
- "Block Brian's calendar from 2-4pm on Thursday. Title: Focus Time."
- "Put a tentative hold on Brian's calendar for Friday 1-3pm: Offsite Prep."

Reschedule:
- "Move Brian's 1:1 with Sarah from Wednesday to Thursday, same time."

### Email

- "Send an email to Brian's direct reports. Subject: Staff Meeting Moved."
- "Draft an email to the VP of Finance about the Q4 budget timeline. Let me review it before sending."
- "Forward the expense report to finance."
- "Find emails from David about the budget."

### Teams

- "Post in Admin Team general channel: Reminder, expense reports due Friday."
- "Message Sarah Chen in Teams: Brian's 3pm is moving to 4pm, heads up."
- "Create a private channel called Offsite Planning."

### People & Org Chart

- "Show me all of Brian's direct reports with names, emails, and titles."
- "Who are Brian's skips?"
- "What is Sarah Chen's job title and office location?"
- "Who is Mark's manager?"

### Tasks (Planner)

- "Create a task: book conference room for all-hands, due Friday."
- "What tasks are due this week?"
- "Mark the catering task as complete."

### Documents

- "Find the Q4 budget spreadsheet on SharePoint."
- "Search for the offsite planning doc that was shared last week."

### Daily Routines

- "Morning briefing" — manager's calendar today, conflicts, prep list
- "End of day" — summarize what you accomplished
- "Weekly wrap-up" — full week summary
- "Prep me for Brian's 2pm with the VP of Finance" — attendees, emails, action items
- "Monday prep" — manager's full week ahead, all-day events, external attendees

### Rich Teams Announcements

- "Post an announcement in the Admin Team channel that the offsite is confirmed for June 5-6. Include date, location, and RSVP link."

### Executive Workflows

- "Prep me for Brian's 2pm with the VP of Finance" — meeting brief with attendees, context, and open items
- "Daily digest for Brian" — calendar + urgent emails + overdue tasks in one view
- "Triage Brian's inbox" — prioritized list of what needs attention with suggested actions
- "Who is Sarah Chen?" — title, org, recent threads with Brian, upcoming meetings
- "Prep for tomorrow's staff meeting" — attendee updates, open items, flagged topics

## Tips

- Be specific with names — "Sarah Chen" not just "Sarah"
- "Draft" vs "send" — "draft an email" to review first, "send" to send now
- Chain requests — "Cancel Brian's Friday sync and post in Teams that it's off"
- Check availability — "Is Brian free at 3pm tomorrow?"
- Use possessives — "Brian's team" means his direct reports (Agency knows your managers from preferences)
- Start of day — just type "Morning briefing"
- End of day — "Summarize what I did today"
- Friday — "Weekly wrap-up" for a full week summary
- Meeting prep — "Prep me for Brian's 2pm" pulls attendees, emails, and context
- Find anything — "Find the Q4 budget on SharePoint" searches across SharePoint, OneDrive, Teams, and email
- Update preferences — ask Agency: "Add a new manager: Jane Doe, jdoe, VP of Marketing"
- Exit Agency — type /exit or close the Terminal window

## Quick Reference

| What you want | What to do |
|---------------|-----------|
| Start Agency | agency copilot --agent business-admin-companion:business-admin-companion |
| Resume last session | agency copilot --continue |
| Pick from recent sessions | agency copilot --resume |
| Quick one-off question | agency copilot --prompt "your question" |
| Ask about capabilities | "Can I send email as my manager?" |
| Exit | /exit or close the window |
| Update Agency | agency update |
| Check version | agency --version |
| Change preferences | Ask: "update my preferences" or edit the file directly |

## What's Installed

The onboarding sets up these tools automatically:

### M365 MCPs (direct Microsoft 365 integration)

| Tool | What it does |
|------|-------------|
| Calendar | View, create, update, cancel meetings; find free time; book rooms |
| Mail | Send, draft, reply, forward, search, flag, manage attachments |
| Teams | Post messages, create chats/channels, search conversations |
| Planner | Create/update tasks, manage plans and goals |
| m365-user | Look up people, org charts, managers, direct reports |
| m365-copilot | Search across all M365 content (docs, emails, chats, sites) |
| SharePoint | Browse and search SharePoint sites and documents |
| SharePoint Lists | Work with SharePoint lists (tracking, inventories) |
| Word | Create and edit Word documents |
| WorkIQ | Ask questions about your work data (read-only) |

### Plugins

| Plugin | What it does |
|--------|-------------|
| business-admin-companion | This plugin — delegation guide, session management, onboarding |
| business-admin-toolkit | Excel budgets, VP calendar patterns, email drafting, documents |
| ado-task-planner | Daily planner: pulls meetings + tasks into a prioritized day plan |
| agentic-journal | Generates work summaries from your session history |

## Troubleshooting

### "agency is not recognized"

Close Terminal and open a new one. If it still doesn't work, re-run the install:

Windows:
iex "& { $(irm aka.ms/InstallTool.ps1)} agency"

Then close and reopen Terminal.

### Authentication popup keeps appearing

Normal for the first use of each M365 tool (calendar, mail, Teams, etc.). After signing in once per tool, it won't ask again.

### Agency seems slow or confused

Long sessions accumulate context. Start fresh:

agency copilot --agent business-admin-companion:business-admin-companion

Your preferences load automatically — you only lose conversation history.

### Something went wrong

Close Terminal and start fresh. Each new session is independent. If you want to pick up where you left off: agency copilot --continue

### Need more help

- Agency docs: https://aka.ms/agency
- M365 tool issues: mcpplat@microsoft.com
- WorkIQ issues: https://aka.ms/workiq/support
- Copilot issues: https://aka.ms/copilotcli/support
