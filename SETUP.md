# Gary AI — Setup Guide

This guide gets you from zero to a working personal AI assistant in about 30 minutes. No coding required.

---

## Before You Start: System Requirements

Gary runs as a set of **scheduled tasks** on your computer via Claude Code Desktop. These tasks run in the background — checking your email, calendar, and Slack, then writing daily briefings to your vault. For this to work reliably, **your machine needs to stay awake.**

This isn't a server or a cloud service. Gary runs on your computer, using your accounts, with your data staying local. That's the point — but it means your machine matters.

### What machine are you on?

| Machine | Works? | What to do |
|---------|--------|------------|
| **Mac Mini / Mac Studio / Mac Pro / iMac** | Best setup | Turn off sleep in System Settings. You're done. |
| **MacBook (at a desk, plugged in)** | Works great | Enable clamshell mode + prevent sleep on power. See below. |
| **MacBook (mobile, unplugged)** | Partial | Tasks run when awake, catch up when you return. See below. |
| **Windows desktop** | Works | Claude Desktop runs on Windows. Prevent sleep in Power Settings. |
| **Windows laptop** | Partial | Same as MacBook mobile — tasks run when awake. |
| **Linux** | Varies | Claude Code CLI works. Scheduled tasks require Desktop app. |

### Mac Desktop Setup (Mac Mini, iMac, Mac Studio, Mac Pro)

Go to **System Settings > Energy** and set:
- **"Prevent automatic sleeping when the display is off"** → ON
- **Display sleep** → your preference (display can sleep, the machine stays awake)

Verify with this terminal command:
```bash
pmset -g | grep "^ sleep"
# Should show: sleep 0
```

### MacBook Setup (Plugged In at a Desk)

MacBooks can run Gary reliably if they stay plugged in. You can close the lid (clamshell mode) — the machine keeps running.

**Step 1:** Go to **System Settings > Energy > Options** and set:
- **"Prevent automatic sleeping when the display is off and the power adapter is connected"** → ON

**Step 2:** If you use an external monitor, just close the lid. If no external monitor, keep the lid open or slightly cracked.

**Step 3:** Verify:
```bash
pmset -g | grep "^ sleep"
# Should show: sleep 0 (when on power)
```

> **Important:** When you unplug and take your MacBook mobile, scheduled tasks will pause. When you return and plug back in, Claude Desktop will catch up — it runs ONE catch-up for the most recently missed scheduled time (within the last 7 days). So your morning briefing still happens, just delayed.

### MacBook / Laptop (Mobile Use)

If you primarily use a laptop without leaving it plugged in, Gary works in **catch-up mode:**

- Scheduled tasks only run while your machine is awake
- When you open your laptop, Claude Desktop detects missed tasks and runs the most recent one
- Your morning briefing might arrive at 10 AM instead of 7 AM — but it still arrives
- The briefing will note that it's a catch-up run so the context is clear

This is a totally valid way to use Gary. You won't get perfectly timed briefings, but you'll still get the synthesis and action items when you sit down to work.

### Windows Setup

Claude Desktop runs on Windows. For scheduled tasks:

1. Go to **Settings > System > Power & Sleep**
2. Set "When plugged in, turn off my screen after" → your preference
3. Set "When plugged in, put my device to sleep after" → **Never**

> **Note:** Windows laptops on battery will still sleep. Same catch-up behavior applies — tasks run when you return.

### What Happens When Tasks Miss Their Schedule

Claude Desktop has built-in catch-up: when the app starts (or the machine wakes), it checks the last 7 days of scheduled tasks and runs ONE catch-up for each task's most recently missed time. Gary's prompts are also aware of late runs — a briefing generated at 2 PM will adjust its greeting and note that it's a catch-up.

**Bottom line:** An always-on desktop is the best experience. A plugged-in laptop is nearly as good. A mobile laptop still works — you just get your briefings when you sit down instead of when you wake up.

---

## Tier 1: Connect & Run (20 minutes)

### Step 1: Install Claude Code

If you don't have Claude Code yet:

```bash
# Install Homebrew (if you don't have it)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Claude Code CLI
brew install claude

# Log in
claude login
```

Or download [Claude Code Desktop](https://claude.ai/download) — the desktop app includes everything.

### Step 2: Download Gary

Download this repo to your computer:

```bash
# Option A: Clone with git
git clone https://github.com/yourusername/gary-ai.git ~/Projects/gary-ai

# Option B: Download as ZIP from GitHub, unzip to ~/Projects/gary-ai
```

### Step 3: Connect Your Data Sources

Open **Claude Code Desktop** (or run `claude` in terminal). Go to **Settings > MCP Servers** and connect:

1. **Gmail** — Click "Add", search for Gmail, authorize with your Google account
2. **Google Calendar** — Click "Add", search for Google Calendar, authorize (same Google account works)
3. **Slack** — Click "Add", search for Slack, authorize with your workspace

> **Note:** These are in-app connectors. You click through OAuth prompts in the browser — no API keys or tokens to manage. Each connector takes about 30 seconds.

**Optional:**
- **GitHub** — If you use GitHub, install the `gh` CLI: `brew install gh && gh auth login`

### Step 4: Run the Onboarding

Open the `gary-ai` folder in Claude Code:

```bash
cd ~/Projects/gary-ai
claude
```

Then say:

> **"Read CLAUDE.md and run my onboarding. Ask me the setup questions."**

Gary will ask you ~6 questions:
1. What's your name?
2. What's your role? (e.g., "founder at Acme Corp", "PM at BigCo")
3. What email account should I check? (the one connected in Step 3)
4. What Slack workspace did you connect?
5. Which Slack channels matter most to you? (list 3-5 to start)
6. Do you have a GitHub account to track? (optional)

Gary writes your answers into `CLAUDE.md` — that becomes your assistant's brain.

### Step 5: Run Your First Briefing

Still in the Claude Code session, say:

> **"Read prompts/morning-briefing.md and run it."**

Gary will:
- Check your email for unread messages
- Pull today's calendar
- Read your Slack channels
- Write a daily note to `vault/YYYY-MM-DD.md`

**If something fails** (e.g., Slack not connected yet), Gary handles it gracefully — the briefing still generates with a note about which sources were unavailable.

Open the file in any text editor (or Obsidian — see Tier 3) to see your first briefing.

---

## Tier 2: Automate It (15 minutes)

Now that you've seen the briefing work, set it up to run automatically.

### Step 6: Set Up Scheduled Tasks

In **Claude Code Desktop**, go to **Settings > Scheduled Tasks** (or use the CLI: `claude task create`).

Create these three tasks, each pointing at the `gary-ai` project folder:

#### Task 1: Slack Prefetch
- **Schedule:** Daily, 30 minutes before your briefing time
- **Prompt:** `Read prompts/slack-prefetch.md and run it.`
- **Why first:** The morning briefing reads from the Slack digest. This needs to run before it.

#### Task 2: Morning Briefing
- **Schedule:** Weekdays at your preferred wake-up time (e.g., 7:00 AM)
- **Prompt:** `Read prompts/morning-briefing.md and run it.`

#### Task 3: EOD Digest
- **Schedule:** Weekdays, 30 minutes before you stop working (e.g., 6:30 PM)
- **Prompt:** `Read prompts/eod-digest.md and run it.`

#### Task 4: Meeting Prep
- **Schedule:** Every 30 minutes during work hours (e.g., 8:00 AM - 6:00 PM weekdays)
- **Prompt:** `Read prompts/meeting-prep.md and run it.`
- **What it does:** Checks your calendar, and if a meeting is coming up in the next 30-60 minutes, Gary researches the context — what was discussed last time, what's changed, what you should know walking in — and posts a brief to your Slack prep channel (or writes it to a file).

#### Task 5: Weekly Retrospective (optional)
- **Schedule:** Friday evening (e.g., 6:00 PM)
- **Prompt:** `Read prompts/weekly-retrospective.md and run it.`
- **What it does:** Reviews the work week — goals vs. actuals, task scorecard, patterns, carry-forward for next week.

#### Task 6: Weekly Look-Ahead (optional)
- **Schedule:** Sunday evening (e.g., 7:00 PM)
- **Prompt:** `Read prompts/weekly-lookahead.md and run it.`
- **What it does:** Plans the week ahead — calendar overview, meetings to prep, open tasks. If the retrospective ran Friday, its carry-forward feeds into this automatically.

> **Important:** Your computer needs to stay awake for scheduled tasks to run. See [System Requirements](#before-you-start-system-requirements) above for setup instructions specific to your machine. If you're on a laptop, tasks will catch up when you return.

### Step 7: Verify the Loop

The next morning, check `vault/` for your daily note. If it's there, Gary is working.

If not:
- Open Claude Code and run the briefing manually to see what failed
- Usually it's a connector that needs re-authorization (OAuth tokens expire)
- Re-authorize in Settings > MCP Servers

### Step 8: Open Your Gary Chat

This is the step that unlocks everything.

In **Claude Code Desktop**, open the `gary-ai` folder as a **Cowork session** (the chat interface). This is now your surface for talking to Gary — keep it open, pin it, treat it like a DM with your chief of staff.

This is where you ask things like:
- "What's the status on [project]?"
- "Summarize what's been happening in #engineering this week"
- "Research [topic] across my Slack, email, and transcripts"
- "Help me prep for tomorrow's board meeting"
- "Draft a response to [person]'s email about [topic]"

Gary has access to all your connected data sources (email, calendar, Slack) AND your vault (daily notes, transcripts, project docs). So when you ask about a topic, Gary can pull from meeting transcripts where it was discussed, Slack threads where the team debated it, emails with external context, and any project docs from previous research.

**This is why the scheduled tasks matter.** The daily briefings and Slack digests build up context in the vault over time. When you ask Gary a question two months from now, it can reference daily notes, transcripts, and project docs that accumulated in the background. The scheduled tasks are the engine; the Cowork chat is where you drive.

> **Example:** "I need a synthesis on the situation with [vendor] over the last few months." Gary pulls from Slack channels, meeting transcripts, daily note action items, and previous project docs — then writes a decision-ready document to your vault that you can share with your team.

---

## Tier 3: Level Up (ongoing)

### Add Obsidian (recommended)

[Obsidian](https://obsidian.md/) is a free markdown editor that turns your vault folder into a searchable knowledge base.

1. Download Obsidian
2. Open Vault > point it at `~/Projects/gary-ai/vault/`
3. Your daily notes, project docs, and transcripts are now browsable and searchable
4. Add [Obsidian Sync](https://obsidian.md/sync) ($4/mo) to access your vault from your phone

### Add Meeting Transcripts

If you use a meeting recording tool (Otter.ai, Fireflies, Grain, etc.):

1. Export transcripts as markdown or text files
2. Drop them in `vault/transcripts/`
3. Gary's briefing will pick up on key topics, decisions, and action items from recent transcripts

You can also just paste meeting notes manually — any `.md` file in `vault/transcripts/` gets read.

### Customize Your Slack Channels

Edit `CLAUDE.md` — in the Slack section, add or remove channels as your needs change. Start with 3-5 high-signal channels and expand from there.

### Add More Scheduled Tasks

Ideas for tasks you can add later:
- **Meeting prep** — run before important meetings to pull relevant context
- **Transcript sync** — nightly synthesis of meeting transcripts into a status doc
- **Monthly rollup** — first of the month summary and archival

### Customize the Prompts

The files in `prompts/` are just markdown instructions. Edit them to match how you work:
- Change section order in the briefing
- Add data sources (e.g., Linear, Jira, Notion)
- Adjust the triage rules for email priority
- Change the output format

---

## Troubleshooting

### "Gmail/Calendar/Slack isn't returning data"
- Go to Settings > MCP Servers and check the connector status
- Re-authorize if the token expired
- Try a manual query first: open Claude Code and ask "check my email for unread messages"

### "The briefing didn't run this morning"
- Was your computer on? Scheduled tasks require the machine to be awake
- Run it manually: `cd ~/Projects/gary-ai && claude "Read prompts/morning-briefing.md and run it."`
- Check `state/last-error.json` for details

### "The Slack digest is stale"
- The Slack prefetch task needs to run before the morning briefing
- Check that the prefetch task is scheduled ~30 minutes earlier
- If it failed, run it manually: ask Claude to "Read prompts/slack-prefetch.md and run it."

### "I want to add a second email account"
- Connect the second account in Settings > MCP Servers
- Update `CLAUDE.md` with the second account in the Email section
- The prompts will automatically check both accounts

---

## How Gary Gets Better Over Time

The more you use Gary, the more useful it becomes:

- **Transcript accumulation** — drop meeting notes in `vault/transcripts/` and Gary starts connecting dots across conversations
- **Project docs** — when Gary researches something for you (like a vendor evaluation), save it in `vault/projects/`. Future briefings can reference it.
- **Daily notes as memory** — your vault of daily notes becomes a searchable log of what happened, when, and what you decided. Ask Gary "what did we discuss about X last month?" and it can find it.
- **Prompt tuning** — after a week of briefings, you'll know what's useful and what's noise. Edit the prompts. That's the whole point — these are your instructions, not black-box code.
