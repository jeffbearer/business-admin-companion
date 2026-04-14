---
name: delegation-guide
description: >
  Use this skill when the admin asks about managing a manager's calendar, sending
  email on behalf of someone, delegate access, or when they hit a limitation like
  "can you accept this meeting for Brian?" Provides the full capability table and
  workarounds for every M365 MCP delegate operation.
---

# Delegation Guide Skill

Business admins spend most of their time working on behalf of someone else.
This skill documents exactly what Agency can and cannot do as a delegate,
with workarounds for every limitation.

## When to Use

- Admin asks "can I [action] for my manager?"
- Admin hits a delegate limitation ("accept this meeting for Brian")
- Admin asks about send-on-behalf, send-as, or delegate permissions
- Admin wants to understand what's possible with manager calendars

## Calendar Delegation

### What Works

| Action | MCP Tool | How |
|--------|----------|-----|
| View manager's calendar | `ListCalendarView` | Set `userIdentifier` to manager's email |
| Find free time | `FindMeetingTimes` | Set `userIdentifier` to manager's email |
| Get timezone/hours | `GetUserDateAndTimeZoneSettings` | Set `userIdentifier` to manager's email |
| Schedule for manager | `CreateEvent` | Admin = organizer, manager = required attendee |
| Block manager's time | `CreateEvent` | Event with manager as sole required attendee |
| Reschedule | `UpdateEvent` | Only for events admin organized |

### What Does NOT Work

| Action | Why | Workaround |
|--------|-----|------------|
| Accept meeting for manager | `AcceptEvent` has no `userIdentifier` parameter | Tell admin: "That one needs [Manager] to accept directly" |
| Decline meeting for manager | `DeclineEvent` has no `userIdentifier` parameter | Same — manager must decline |
| Tentatively accept for manager | `TentativelyAcceptEvent` has no `userIdentifier` | Same |
| Delete manager's event | `DeleteEventById` has no `userIdentifier` parameter | Admin can only cancel events they organized |
| Cancel manager's event | `CancelEvent` operates on admin's calendar only | If admin organized it, they can cancel. Otherwise, manager must do it. |

### How to Explain Limitations

Don't be technical. Don't say "the API doesn't support userIdentifier."

**Say:**

"I can see Brian's calendar and schedule meetings that show up on it, but
I can't accept or decline meetings on his behalf — he needs to do that
himself. That's a limitation of the current tools, not something you're
doing wrong."

"I can cancel meetings I created, but not meetings someone else organized
on Brian's calendar. For those, Brian would need to decline them directly."

### Scheduling Pattern (the 90% case)

Most admin scheduling follows this pattern:

1. Admin says "schedule a meeting with Brian, Sarah, and Mike"
2. Use `FindMeetingTimes` with Brian's email as `userIdentifier` to find
   slots that work for everyone
3. Create the event with the admin as organizer, all attendees as required
4. The meeting appears on everyone's calendar

This covers the vast majority of what admins need to do. The limitations
only matter for RSVP management and event deletion — both of which the
manager handles themselves.

## Email Delegation

### Current State: No Delegate Support

The mail MCP has **zero delegate capabilities**. There is no `from`,
`sender`, `onBehalfOf`, or `userIdentifier` parameter on any send or
draft operation. All email is sent from the authenticated user (the admin).

### What Works

| Action | How |
|--------|-----|
| Send email from admin's account | `SendEmailWithAttachments` — always sends as admin |
| Draft for review | `CreateDraftMessage` — admin's drafts folder |
| Search anyone's sent emails | `SearchEmails` — searches across accessible mailbox |
| Flag emails | `FlagEmail` has `mailboxAddress` for delegate flagging |

### Workaround for "Send as Manager"

Since email always comes from the admin, use the "on behalf of" pattern:

- Add "On behalf of [Manager Name]" to the email signature
- Make the context clear in the body: "Per Brian's request..." or
  "Brian asked me to follow up on..."
- If the admin needs true send-as, they must use Outlook directly with
  Exchange delegate permissions

### How to Explain It

"Email always comes from your account — I can't send as [Manager]. But
most admins handle this by adding 'on behalf of Brian' to the message,
which is standard practice. Want me to draft it that way?"

## Teams Delegation

Teams MCP operates as the authenticated user. There is no delegate
posting capability.

| Action | Works? |
|--------|--------|
| Post as admin in channels | ✅ Yes |
| Post as manager | ❌ No |
| Search messages | ✅ Yes |
| Create channels | ✅ Yes |

## Planner Delegation

Planner tasks are created by the authenticated user. Tasks can be
assigned to anyone.

| Action | Works? |
|--------|--------|
| Create task assigned to manager | ✅ Yes |
| Create task assigned to admin | ✅ Yes |
| View plan tasks | ✅ Yes |
| Complete tasks for manager | ⚠️ Only if admin has plan access |

## Feature Gap Summary

These are the most-requested delegate features not yet in the M365 MCPs:

1. **Calendar: Accept/Decline for another user** — needs `userIdentifier`
   on `AcceptEvent`, `DeclineEvent`, `TentativelyAcceptEvent`
2. **Calendar: Delete events from another user's calendar** — needs
   `userIdentifier` on `DeleteEventById`
3. **Mail: Send-as / send-on-behalf** — needs `from` or `onBehalfOf`
   parameter on `SendEmailWithAttachments` and `CreateDraftMessage`

To request these features: mcpplat@microsoft.com or
https://aka.ms/agency/feedback

## Quick Decision Tree

When an admin asks "can I [X] for [Manager]?":

```
Is it viewing/searching?
  → YES: Almost certainly works. Check if the tool has userIdentifier.

Is it creating something new?
  → Calendar: Yes — admin as organizer, manager as attendee
  → Email: Yes — but from admin's account, not manager's
  → Teams: Yes — but from admin's account
  → Planner: Yes — can assign to anyone

Is it modifying someone else's existing item?
  → Usually NO. Accept/decline/delete on another person's calendar
    doesn't work. The owner must do it.
```
