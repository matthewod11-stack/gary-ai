# Gary AI

**Your personal AI operations assistant, built for the Claude Desktop app.**

Gary reads your email, calendar, Slack, and meeting transcripts — then synthesizes them into daily briefings, action items, and on-demand research. Named after [Gary Walsh](https://veep.fandom.com/wiki/Gary_Walsh) from Veep — the aide who knows what you need before you ask.

<p align="center">
  <img src="docs/screenshots/gary-daily-timeline.png" alt="A Day with Gary AI" width="720" />
</p>

---

## What Gary Does

| When | What | Details |
|------|------|---------|
| Every morning | **Daily Briefing** | Email triage, calendar with prep notes, Slack mentions, GitHub status, proactive alerts, quality score |
| Before meetings | **Meeting Prep** | Last discussion, what's changed, fires to watch, your action items |
| All day | **On-Demand Chat** | Open Cowork in the gary-ai folder and ask anything — status checks, research, draft replies, decision-ready docs |
| Every evening | **EOD Digest** | What got done, carry-forward tasks, tomorrow's first meeting |
| Every night | **Transcript Sync** | Compiles meeting transcripts into a team operational status doc with change detection |
| Every night | **Knowledge Compile** | Synthesizes vault data into a queryable knowledge base — entities, people, situations, concepts |
| Friday | **Weekly Retro** | Goals vs. actuals, task scorecard, patterns to adjust |
| Sunday | **Week Ahead** | Calendar overview, key meetings, prioritized tasks |

<p align="center">
  <img src="docs/screenshots/gary-features.png" alt="Gary AI Features" width="720" />
</p>

## How It Works

Gary runs in the [Claude Desktop app](https://claude.ai/download). Open a Cowork chat in the gary-ai folder and you're talking to Gary. It connects to your data sources via built-in MCP connectors — no API keys to manage, no custom code to write.

```
You ──→ Claude Desktop (Cowork chat in the gary-ai folder)
           ├── Gmail (MCP connector)
           ├── Google Calendar (MCP connector)
           ├── Slack (MCP connector)
           ├── GitHub CLI (optional)
           └── Vault folder (local markdown files)
                ├── Daily notes (briefings + EOD digests)
                ├── Transcripts (meeting notes you drop here)
                ├── Project docs (research, status docs)
                └── Knowledge base (compiled entities, people, situations)
```

Scheduled tasks run the briefing/digest/weekly prompts automatically. Your machine needs to stay awake for these to fire on time — a Mac Mini or desktop is the best setup, but laptops work too (Gary catches up when you open the lid). See [System Requirements](SETUP.md#before-you-start-system-requirements) for per-machine setup.

The vault is just markdown — point Obsidian at it for mobile sync, or just read the files.

## Get Started

**~30 minutes to set up. No coding required. No terminal.**

Follow the walkthrough in **[SETUP.md](SETUP.md)**:

1. Download the [Claude Desktop app](https://claude.ai/download) — that's the only install
2. Connect your data sources (Gmail, Calendar, Slack) in Settings
3. Open this project folder in the app and start a chat — Gary walks you through onboarding (5 questions, writes your config)
4. Run your first morning briefing
5. Set up scheduled tasks so it runs itself

## Requirements

- [Claude Desktop app](https://claude.ai/download) (macOS or Windows)
- Claude account with a Pro or Team plan
- Gmail / Google Calendar account
- Slack workspace (optional but recommended)
- GitHub account (optional)
- A machine that stays awake — desktop is ideal, laptop works with the right settings (see [System Requirements](SETUP.md#before-you-start-system-requirements))

## What Makes This Different

Most AI assistant setups are demos. Gary is a working pattern extracted from a production personal assistant that's been running daily since March 2026. The prompts, error handling, and data synthesis patterns have been iterated through real use.

The key insight: **scheduled tasks build context, and context makes on-demand work powerful.** Your morning briefing isn't just a summary — it's the foundation that lets you say "research the situation with [vendor]" and get a decision-ready doc in 10 minutes, because Gary already knows what's been discussed in Slack, what meetings happened, and what action items are open.

The knowledge base takes this further — nightly compilation synthesizes your transcripts, notes, and project docs into structured entries (entities, people, situations, concepts) that Gary queries directly. No RAG pipeline, no vector DB. Just Claude-maintained markdown with cross-references. Inspired by [Karpathy's approach](https://x.com/karpathy/status/1936199801097752726) to LLM-native knowledge management.

## Tech Stack

| Component | Role |
|-----------|------|
| [Claude Desktop](https://claude.ai/download) | Runtime — Cowork chat interface with MCP connectors |
| MCP Connectors | Gmail, Google Calendar, Slack integration |
| Markdown Vault | Local knowledge base (daily notes, transcripts, project docs) |
| Scheduled Tasks | Automated briefings, digests, and weekly reports |
| Prompt Engineering | Structured prompts with shared modules and error handling |
| Knowledge Base | LLM-compiled entities, people, situations, and concepts from vault data |

## Project Structure

```
gary-ai/
├── prompts/           # Scheduled task prompts (briefing, digest, retro, prep)
│   └── shared/        # Shared prompt modules (error handling, formatting)
├── config/            # Configuration files
├── docs/              # Design docs and screenshots
├── vault/             # Your knowledge base (gitignored — personal data)
│   ├── daily/         # Archived daily + weekly notes
│   ├── transcripts/   # Meeting transcripts
│   ├── projects/      # Project docs and research
│   └── knowledge/     # LLM-compiled knowledge base (entities, people, situations, concepts)
├── state/             # Runtime state (gitignored)
├── SETUP.md           # Step-by-step setup guide
├── SOUL.md            # Gary's personality (customize this)
└── IDENTITY.md        # Gary's name and vibe (customize this)
```

## License

[MIT](LICENSE)
