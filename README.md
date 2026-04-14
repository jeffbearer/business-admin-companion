# business-admin-guide

The missing manual for business administrators using Agency. Covers manager-calendar
delegation, session management, onboarding, and org-specific customization.

## What This Is

A companion plugin for business admins (executive assistants, team admins, admin staff)
who use Agency to manage Microsoft 365 on behalf of executives and managers.

This plugin provides:

- **An agent** with built-in knowledge of M365 delegate capabilities, workarounds, and
  admin-specific workflows
- **Onboarding skill** — interactive first-time setup that creates config files,
  preferences, and a personalized agent
- **Delegation guide** — what works, what doesn't, and workarounds for managing
  someone else's calendar, email, and tasks
- **Session management** — how to name, resume, and organize conversations
  (written for non-technical users)

## Relationship to business-admin-toolkit (Keystone)

This plugin **complements** the [business-admin-toolkit](https://github.com/agency-microsoft/playground)
by 1ES AI Native Engineering. Use both together:

| Capability | business-admin-guide (this) | business-admin-toolkit (Keystone) |
|------------|----------------------------|-----------------------------------|
| Calendar delegation & workarounds | ✅ | — |
| Email on-behalf-of patterns | ✅ | ✅ |
| Session management guidance | ✅ | — |
| Interactive onboarding | ✅ | ✅ (getting-started skill) |
| Org-specific customization | ✅ | — |
| FY calendar awareness | ✅ | — |
| Planner integration | ✅ | — |
| Excel budget editing | — | ✅ |
| Word document creation | — | ✅ |
| PowerPoint decks | — | ✅ |
| Non-technical persona (Keystone) | — | ✅ |

**Install both:**
```
/plugin install business-admin-guide
/plugin install business-admin-toolkit
```

## Installation

```
/plugin install business-admin-guide
```

Or add to your `~/.agency/agency.toml`:

```toml
[plugins]
default = [
  "market:business-admin-guide@agency-microsoft/playground",
  "market:business-admin-toolkit@agency-microsoft/playground",
]
```

## Getting Started

After installing, start Agency with the agent:

```bash
agency copilot --agent business-admin-guide:business-admin-guide
```

Then say: **"Set me up"** — the onboarding skill will walk you through
configuring everything interactively.

## What's Included

### Agent: business-admin-guide

The main agent with built-in knowledge of:
- Calendar, email, Teams, Planner, and people management
- Manager delegation capabilities and limitations
- Daily routines (morning briefing, meeting prep, end of day)
- Session management
- FY calendar
- Formatting rules for terminal output

### Skill: onboarding

Interactive first-time setup. Creates:
- `~/.agency/agency.toml` with M365 MCPs and recommended plugins
- `~/.copilot-global/.github/instructions/admin-preferences.instructions.md`
  with your managers, timezone, communication style, and routines
- Environment variable for global instructions

### Skill: delegation-guide

Complete reference for what Agency can and cannot do as a delegate:
- Calendar: view ✅, schedule ✅, accept/decline ❌
- Email: send (from you) ✅, send-as ❌
- Teams: post (as you) ✅
- Planner: create/assign ✅

Includes workarounds for every limitation and the decision tree for
"can I do X for my manager?"

### Skill: session-management

Plain-language guide to sessions:
- Naming conventions (first message = session name)
- When to start fresh vs resume
- Running parallel sessions for multiple managers
- Recovering from crashes

## M365 MCPs Used

This plugin configures five M365 MCPs:

| MCP | What it does |
|-----|-------------|
| Calendar | View, create, update, cancel meetings; find free time; book rooms |
| Mail | Send, draft, reply, forward, search, flag emails |
| Teams | Post messages, create chats/channels, search conversations |
| Planner | Create/update tasks, manage plans |
| WorkIQ | Ask questions about M365 data (read-only) |

## Requirements

- Agency CLI installed (`agency --version` to check)
- Microsoft 365 account (sign-in prompted on first use of each tool)
- Windows Terminal, iTerm2, or any modern terminal emulator

## Support

- Agency docs: https://aka.ms/agency
- M365 tool issues: mcpplat@microsoft.com
- WorkIQ issues: https://aka.ms/workiq/support
- Copilot issues: https://aka.ms/copilotcli/support

## Author

Jeff Bearer (@jeffbearer)
