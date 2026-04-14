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

### What Agency Cannot Do (But You Can in Outlook)

These actions are not supported in Agency's current tools, but if you have
delegate permissions in Exchange, you can do all of them directly in Outlook.

| Action | Why Agency can't | What to do instead |
|--------|-----------------|-------------------|
| Accept meeting for manager | `AcceptEvent` has no `userIdentifier` parameter | Do it in Outlook — you have delegate access |
| Decline meeting for manager | `DeclineEvent` has no `userIdentifier` parameter | Do it in Outlook |
| Tentatively accept for manager | `TentativelyAcceptEvent` has no `userIdentifier` | Do it in Outlook |
| Delete manager's event | `DeleteEventById` has no `userIdentifier` parameter | Do it in Outlook if you have delegate access |
| Cancel manager's event | `CancelEvent` operates on admin's calendar only | If admin organized it, Agency can cancel. Otherwise, do it in Outlook. |

### How to Explain Limitations

Don't be technical. Don't say "the API doesn't support userIdentifier."
But DO tell them who to ask and what to ask for if they want these
features added.

**Say:**

"I can see Brian's calendar and schedule meetings that show up on it, but
I can't accept or decline meetings on his behalf yet — that's a limitation
of my current tools, not something you're doing wrong. You can still do
that in Outlook with your delegate permissions."

"I can cancel meetings I created, but not meetings someone else organized
on Brian's calendar. You can handle those in Outlook since you have
delegate access there."

"If you'd like these features added to Agency, you can request them at
https://aka.ms/agency/feedback or email mcpplat@microsoft.com — ask for
delegate support in the calendar and mail tools. The more admins who ask,
the faster it gets prioritized."

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

Since Agency always sends email from the admin's account, use one of these approaches:

- Add "On behalf of [Manager Name]" to the email signature
- Make the context clear in the body: "Per Brian's request..." or
  "Brian asked me to follow up on..."
- If the admin needs true send-as, they can do it in Outlook directly —
  Exchange delegate permissions support send-as and send-on-behalf

### How to Explain It

"Email from Agency always comes from your account — I can't send as
[Manager] yet. Most admins handle this by adding 'on behalf of Brian' to
the message, which is standard practice. Or if you need it to come from
Brian's address, you can do that in Outlook with your delegate permissions.
Want me to draft it for you to send from here?"

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

These are the delegate features admins want most that Agency doesn't support yet:

1. Accept, decline, or tentatively accept meetings on a manager's calendar
2. Delete or cancel events on a manager's calendar (that the admin didn't create)
3. Send email as the manager (send-as or send-on-behalf)

All of these work in Outlook with delegate permissions — they just aren't
available in Agency's tools yet.

**Want these features?** The M365 MCP team builds the tools Agency uses:
- Submit feedback: https://aka.ms/agency/feedback
- Email directly: mcpplat@microsoft.com
- What to ask for: "delegate support for calendar RSVP and send-on-behalf in the M365 MCP tools"

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
  → Usually not in Agency. Accept/decline/delete on another person's
    calendar is not supported in Agency yet.
  → BUT: The admin can do these in Outlook with delegate permissions.
    Point them there.
```
