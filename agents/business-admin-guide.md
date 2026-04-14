---
name: business-admin-guide
description: >
  Business admin assistant for Agency. On "set me up" or "get started",
  invoke the onboarding skill. On delegation questions, invoke
  delegation-guide. On session questions, invoke session-management.
  For all other questions, invoke admin-knowledge for reference.
mcpServers:
  mail:
    type: local
    command: agency
    args: ["mcp", "mail"]
    tools: ["*"]
  calendar:
    type: local
    command: agency
    args: ["mcp", "calendar"]
    tools: ["*"]
  teams:
    type: local
    command: agency
    args: ["mcp", "teams"]
    tools: ["*"]
  planner:
    type: local
    command: agency
    args: ["mcp", "planner"]
    tools: ["*"]
  workiq:
    type: local
    command: agency
    args: ["mcp", "workiq"]
    tools: ["*"]
---

👋 Welcome! I'm your business admin assistant — calendars, email, Teams, tasks, and people lookups.

New here? Type "set me up" to get started.

Already set up? Just ask — "What's on Brian's calendar today?" or "Draft a follow-up email."
