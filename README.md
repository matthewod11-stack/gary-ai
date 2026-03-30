# Gary AI

**Your personal AI chief of staff, built on Claude Code.**

Gary is a personal AI operations assistant that reads your email, calendar, Slack, and meeting transcripts — then synthesizes them into daily briefings, action items, and on-demand research. Named after [Gary Walsh](https://veep.fandom.com/wiki/Gary_Walsh) from Veep — the aide who knows what you need before you ask.

## What Gary Does

**Every morning**, Gary writes you a daily briefing:
- Who's waiting on you (Slack mentions, unanswered emails)
- Today's calendar with prep notes for key meetings
- Active project status from GitHub
- Synthesized task list from all sources
- Weather

**Every evening**, Gary appends an EOD digest:
- What got done today
- Tasks carrying over
- Tomorrow's first meeting and what to prep

**Every Sunday**, Gary writes a weekly look-ahead:
- Calendar overview for the week
- Key meetings to prepare for
- Open tasks prioritized by urgency

**On demand**, you can ask Gary to:
- Research a topic across your Slack, email, and transcripts
- Synthesize meeting notes into decision docs
- Draft communications based on context it already has
- Prep for upcoming meetings with relevant background

## How It Works

Gary runs on [Claude Code](https://docs.anthropic.com/en/docs/claude-code) (Desktop or CLI). It connects to your data sources via built-in MCP connectors — no API keys to manage, no custom code to write.

```
You ──→ Claude Code Desktop
           ├── Gmail (MCP connector)
           ├── Google Calendar (MCP connector)
           ├── Slack (MCP connector)
           ├── GitHub CLI (optional)
           └── Vault folder (local markdown files)
                ├── Daily notes (briefings + EOD digests)
                ├── Transcripts (meeting notes you drop here)
                └── Project docs (research, status docs)
```

Scheduled tasks run the briefing/digest/weekly prompts automatically. The vault folder is just markdown — point Obsidian at it for mobile sync, or just read the files.

## Get Started

**Time to set up: ~30 minutes**

Follow the walkthrough in [SETUP.md](SETUP.md). It covers:

1. Installing Claude Code
2. Connecting your data sources (Gmail, Calendar, Slack)
3. Running the interactive onboarding (Gary asks you 5 questions, writes your config)
4. Running your first morning briefing manually
5. Setting up scheduled tasks so it runs itself

## Requirements

- macOS (Claude Code Desktop runs on Mac today)
- A Claude account with a Pro or Team plan
- Gmail / Google Calendar account
- Slack workspace (optional but recommended)
- GitHub account (optional)

## What Makes This Different

Most AI assistant setups are demos. Gary is a working pattern extracted from a production personal assistant that's been running daily since March 2026. The prompts, error handling, and data synthesis patterns have been iterated on through real use.

The key insight: **scheduled tasks build context, context makes on-demand work powerful.** Your morning briefing isn't just a summary — it's the foundation that lets you say "research the Bridge situation" and get a decision-ready doc in 10 minutes because Gary already knows what's been discussed in Slack, what meetings happened, and what action items are open.

## License

MIT
