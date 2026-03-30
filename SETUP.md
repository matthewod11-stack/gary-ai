# Gary AI — Setup Guide

This guide gets you from zero to a working personal AI assistant in about 30 minutes. No coding required.

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

#### Task 5: Weekly Review (optional)
- **Schedule:** Sunday evening (e.g., 7:00 PM)
- **Prompt:** `Read prompts/weekly-review.md and run it.`

> **Important:** Your computer needs to be on (not sleeping) for scheduled tasks to run. If you have a desktop Mac or leave your laptop open, this works automatically. Otherwise, you can run them manually when you sit down.

### Step 7: Verify the Loop

The next morning, check `vault/` for your daily note. If it's there, Gary is working.

If not:
- Open Claude Code and run the briefing manually to see what failed
- Usually it's a connector that needs re-authorization (OAuth tokens expire)
- Re-authorize in Settings > MCP Servers

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
