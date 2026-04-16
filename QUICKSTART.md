# Getting Started with Agency for Business Admins

Agency is an AI assistant you run from a terminal window. You type what you
need in plain English — schedule meetings, send emails, look up org charts,
post in Teams — and it does it for you using your Microsoft 365 account.

This takes about 5 minutes. No technical experience required.

---

## Step 1 — Install Agency

Open a Terminal window (press the Windows key, type "Terminal", click it).

Copy and paste this line into Terminal, then press Enter:

    iex "& { $(irm aka.ms/InstallTool.ps1)} agency"

Wait for it to finish (about a minute). Then close Terminal and open a new one.

To verify it worked, type "agency" and press Enter. You should see a list
of commands.

Trouble? If you get a NuGet feed error, try this instead:

    iex "& { $(irm aka.ms/InstallTool.ps1)} agency -nugetorg msazure"

Then close and reopen Terminal.

---

## Step 2 — Log In

Type this and press Enter:

    agency copilot

When you see the prompt, type:

    /login

Select GitHub.com. A link and code will appear — open the link in your
browser and enter the code. Your GitHub account is usually your alias
with _microsoft at the end (like jdoe_microsoft). More info: https://aka.ms/copilot

Once logged in, type /exit to close this session. We'll start a proper
one in the next step.

---

## Step 3 — Install the Plugin

Open a new Terminal window and run these two commands:

    git clone https://github.com/jeffbearer/business-admin-companion.git "$HOME\.agency\business-admin-companion"

    agency plugin install "local:$HOME\.agency\business-admin-companion"

You should see "Installed plugin" when it's done. To verify, run:

    agency plugin list

You should see business-admin-companion in the list.

NOTE: The plugin is now installed globally — it loads automatically.
You do NOT need to add it to your agency.toml file.

---

## Step 4 — Set Up Your Preferences

Start Agency with the business admin assistant:

    agency copilot --agent business-admin-companion:business-admin-companion

You'll see a welcome message. Type:

    set me up

The assistant will ask you a few questions (your name, your managers, your
timezone, etc.) and then automatically configure everything for you. This
takes about 2 minutes.

When it's done, it'll tell you to close Terminal and open a new one.

---

## Step 5 — Start Working

After restarting Terminal, run:

    agency copilot --agent business-admin-companion:business-admin-companion

The first time you use calendar, email, or Teams, a browser window will pop
up to sign in with your Microsoft account. This is one-time per tool.

Try these to get started:

    Show me Brian's calendar for this week.

    What does Brian have tomorrow afternoon?

    Draft an email to the team about the offsite agenda.

    Morning briefing.

(Replace "Brian" with your manager's first name.)

---

## Everyday Commands

| What you want | What to type |
|---------------|-------------|
| Start a new session | agency copilot --agent business-admin-companion:business-admin-companion |
| Resume where you left off | agency copilot --continue |
| Pick from recent sessions | agency copilot --resume |
| Quick one-off question | agency copilot --prompt "What's on Brian's calendar today?" |
| Exit | /exit or close the window |

---

## What You Can Do

- View and manage your manager's calendar
- Schedule meetings, find free time, block focus time
- Send, draft, and search email
- Post in Teams channels and chats
- Look up anyone in the org chart
- Create and track tasks in Planner
- Search for documents on SharePoint
- Morning briefings, meeting prep, weekly wrap-ups

Ask the assistant "what can you do?" or "show me examples" anytime.

---

## Optional: Create a Shortcut

The full launch command is long. You can create a shortcut so you just
type "admin" to launch.

**Step 1:** Run this to create your PowerShell profile (safe to run even if one exists):

    New-Item -Path $PROFILE -ItemType File -Force

**Step 2:** Open it in Notepad:

    notepad $PROFILE

**Step 3:** Paste this line into the file, then save and close Notepad:

    function admin { agency copilot --agent business-admin-companion:business-admin-companion @args }

**Step 4:** If you get an error about "execution policy" when you reopen
Terminal, run this once:

    Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force

Close and reopen Terminal. Now just type "admin" to launch.

---

## Known Issues

**"adaptive-cards-mcp" crash:** If you get "TypeError: a.replace is not
a function", the adaptive-cards-mcp plugin has a metadata issue with
certain Agency versions. Remove it from your agency.toml plugins list
if it was added. The other plugins work fine.

**Profile path on corp machines:** On machines with OneDrive folder
redirection, the PowerShell profile lives under
"OneDrive - Microsoft/Documents/PowerShell/" not the local Documents
folder. The $PROFILE variable handles this automatically — always use
$PROFILE rather than typing the path manually.

---

## Need Help?

Just ask the assistant! It knows how to help with:
- "How do I resume a session?"
- "Can I send email as my manager?"
- "Show me examples"
- "Tips"

Or reach out:
- Agency docs: https://aka.ms/agency
- M365 tool issues: mcpplat@microsoft.com
- Feature requests: https://aka.ms/agency/feedback
