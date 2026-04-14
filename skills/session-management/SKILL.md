---
name: session-management
description: >
  Use this skill when the admin asks about sessions — how to name them, resume
  previous work, find a past session by topic or date, run multiple sessions,
  manage context, or when they seem confused about why Agency "forgot" something
  from a previous conversation.
---

# Session Management Skill

Business admins are not developers. They don't have a mental model of
"sessions" and "context windows." They just know that sometimes Agency
remembers what they said and sometimes it doesn't. This skill helps them
understand and use sessions effectively — in plain language.

## When to Use

- Admin asks "how do I get back to what I was working on?"
- Admin asks "why did you forget about the email we drafted?"
- Admin asks about naming, resuming, or organizing conversations
- Admin wants to find a past session by topic or date
- Admin seems confused about context loss between sessions
- Admin asks "how do sessions work?"

## Core Concepts (Explain Simply)

### What Is a Session?

A session is one conversation. Everything you discuss, every email drafted,
every meeting scheduled — Agency remembers it all within that conversation.

When you close Terminal or start a new `agency copilot` command, that's a
new conversation. It won't remember the old one unless you resume it.

**Analogy:** Think of sessions like phone calls. During the call, you both
remember everything discussed. After you hang up, a new call starts fresh.
But you can "call back" and pick up where you left off.

### Why Sessions Matter for Admins

- **Your preferences are always there.** Manager names, calendar settings,
  tone preferences — these are in config files, so every session has them.
- **Conversation context is per-session.** "That email we drafted" only
  exists in the session where you drafted it. Start fresh and it's gone
  (unless you resume).
- **Long sessions can slow down.** If a conversation gets very long
  (hundreds of messages), Agency may slow down. Start fresh.

## Session Commands

Present these as a simple reference card:

| What you want | Command |
|---------------|---------|
| Start fresh | `agency copilot --agent business-admin-companion:business-admin-companion` |
| Resume most recent conversation | `agency copilot --continue` |
| Pick from recent conversations | `agency copilot --resume` |
| Resume a specific conversation | `agency copilot --resume=SESSION_ID` |
| Quick one-off question | `agency copilot --prompt "your question"` |

## Naming Sessions

The first thing the admin types becomes the session name. This is what
shows up in the `--resume` list. Teach them to think of it like an email
subject line.

**Good first messages:**
- "Morning briefing for Brian — Monday April 14"
- "Offsite logistics planning for June 5-6"
- "Draft quarterly budget email for Brian's team"
- "Prep Brian's 1:1 with VP of Finance on Wednesday"

**Bad first messages:**
- "hi"
- "help me with something"
- "calendar stuff"

Future them will thank present them when scrolling through the resume list.

## When to Start Fresh vs Resume

### Start Fresh When:
- Starting a new day (morning routine)
- Switching to a completely different task
- The current session feels slow or sluggish
- You're done with the previous topic

### Resume When:
- Terminal crashed or was accidentally closed
- You were interrupted mid-task
- You need to continue a complex multi-step workflow
- You want to reference something from earlier

### How to Explain Context Loss

When an admin says "you forgot about the email we drafted":

"That was in a previous session — I don't have that context here. If you
want to pick up where we left off, close this and run:
`agency copilot --continue`
That'll reopen our last conversation with everything intact."

Don't say "context window" or "session state." Just say "that was in a
different conversation."

## Running Multiple Sessions

Admins who support multiple managers often want parallel sessions:

- **Window 1:** "Morning briefing for Brian"
- **Window 2:** "Offsite logistics with Lisa's team"
- **Window 3:** Quick one-off lookups

Each Terminal window is independent. They don't interfere.

**Tip for multi-manager admins:** Use one session per manager for the day.
This keeps context clean — Brian's scheduling in one window, Lisa's email
in another.

## Session Lifespan

- Sessions stay active as long as Terminal is open
- Idle sessions don't expire — just type to continue
- Closing Terminal ends the active session but saves history
- `--continue` and `--resume` work even after rebooting
- Session history is stored locally on the admin's machine

## Common Scenarios

### "I was drafting an email and Terminal crashed"

```
agency copilot --continue
```

Then: "We were working on an email — can you show me the draft?"

Agency will have the full context from before the crash.

### "I need to find a session from last week"

**Option 1: Browse the list**
```
agency copilot --resume
```
Scroll through the list. This is where good session names pay off.

**Option 2: Ask Agency to find it**

If you're already in a session, just describe what you were working on:

- "Find the session where we drafted the budget email for Brian"
- "Which session did we work on the June offsite logistics?"
- "Show me sessions from last Tuesday"

Agency can search through past sessions by topic, date, or what you worked
on. It will show you a list of matches — just tell it which one you want
and it can pull up the details.

**Example:**

Admin: "I need to find the session where we worked on the VP meeting invite"

Agency responds with something like:
1. "Prep VP Finance 1:1 — April 10" (3 turns, calendar work)
2. "Morning briefing Monday — April 7" (12 turns, included VP discussion)

Then the admin picks one: "Number 1 — can you show me what we did?"

### "I want to check Brian's calendar real quick without losing my place"

Open a **new Terminal window** and run:

```
agency copilot --prompt "What's on Brian Barbisch's calendar tomorrow?"
```

This runs the query and exits without touching the other session.

### "Agency is getting slow"

Long sessions accumulate context. Start fresh:

```
agency copilot --agent business-admin-companion:business-admin-companion
```

Your preferences and manager info are in config files — they load
automatically. You only lose the conversation history.
