# Knowledge Base Design

A Karpathy-inspired LLM-maintained knowledge base for your personal AI assistant. No RAG, no vector DB -- just Claude-maintained markdown and index files.

---

## What It Is

Your vault accumulates daily notes, transcripts, and project docs, but that's append-only temporal data. The knowledge base synthesizes that raw material into compiled, queryable entries organized by type. When you ask "what do we know about Acme Corp?", Gary checks the knowledge base first instead of searching through every file.

## Entry Format

Every file in `vault/knowledge/` (except indexes) follows this structure:

```markdown
---
type: entity | person | situation | concept
domains: [work, consulting, personal]
status: active | dormant | archived
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources:
  - vault/transcripts/2026-04-03-intro-call.md
  - vault/projects/acme-evaluation.md
related:
  - entities/acme.md
  - people/jane-acme.md
---

# Acme Corp Evaluation

Evaluating Acme as a vendor for fraud monitoring. $50K/yr minimum confirmed on intro call. Demo scheduled for next week, decision by end of month.

## Key Facts
- $50K/yr minimum, confirmed by Jane on April 3 intro call
- 4-6 week implementation timeline
- One engineer part-time needed

## Current State
Intro call completed. NDA signed. Demo scheduled for April 8.

## Open Questions
- Detailed per-unit pricing (blocked on NDA)?
- Trial/POC available before going live?

## Timeline
| Date | Event | Source |
|------|-------|--------|
| 2026-04-01 | Initial outreach | 2026-04-01.md |
| 2026-04-03 | Intro call, $50K/yr confirmed | 2026-04-03-intro-call.md |
| 2026-04-08 | Demo scheduled | -- |
```

### Entry Types

| Type | What It Captures | Examples |
|------|------------------|----------|
| **entity** | Companies, products, services | Vendors, tools, protocols |
| **person** | Individuals and their context | Contacts, collaborators |
| **situation** | Active threads with timelines | Evaluations, negotiations, projects |
| **concept** | Durable knowledge that survives job changes | Domain expertise, patterns |

People entries add `aliases` (for name resolution) and `context` (one-line role) to frontmatter.

---

## Directory Structure

```
vault/knowledge/
├── index.md                    <- Master index (auto-generated)
├── entities/
│   ├── index.md
│   └── *.md
├── people/
│   ├── index.md
│   └── *.md
├── situations/
│   ├── index.md
│   └── *.md
├── concepts/
│   ├── index.md
│   └── *.md
└── archived/
    ├── index.md
    └── *.md
```

Index files are auto-generated on every compilation run. Each line pulls the entry's summary sentence with domain tags for quick scanning.

---

## How Compilation Works

### Session-Driven

At the end of a working session (or explicit request), Gary scans what was created or modified and updates knowledge entries. New facts get added to existing entries; new topics get new entries; stub entries are created for referenced items that don't exist yet.

### Nightly Sweep

The `prompts/knowledge-compile.md` task runs nightly:

1. **Load state** -- read the master index and last-run timestamp
2. **Scan sources** -- transcripts, today's daily note, modified project docs, Slack digest
3. **Extract and compile** -- facts, status changes, new entities, relationships
4. **Regenerate indexes** -- folder-level and master index
5. **Lint** -- check for stale entries, orphaned entries, empty sections, dead source links, domain drift
6. **Write state** -- `state/knowledge-compile-state.json`

If session-driven compilation already updated an entry, the nightly sweep checks the `updated` timestamp and skips unless new source material arrived afterward.

---

## How to Query

All queries follow a tiered structure:

**Tier 1 -- Index scan:** Read `vault/knowledge/index.md` (~2-3K tokens). Match by entry name, domain, or keyword. Often sufficient for "do we know about X?"

**Tier 2 -- Entry read:** Open matched entries. Read Key Facts, Current State, Open Questions. Follow `related` links if the question spans topics.

**Tier 3 -- Source trace:** Follow `sources` links to raw transcripts/docs. Search vault for additional mentions.

Meeting prep and morning briefings automatically check the index for relevant context.

---

## Account Portability

When you change jobs, the knowledge base supports a clean transition:

| Status | Meaning |
|--------|---------|
| **active** | Current employer/project. Queried by default. |
| **dormant** | Role ended. Stays in place, included only when explicitly relevant. |
| **archived** | Fully cold. Moved to `archived/`. Excluded from default index. |

**Transition playbook:**
1. Tag employer-specific entries as `dormant`
2. Separate cross-domain concepts -- universal knowledge stays `active`, domain removed
3. Judge people entries -- ongoing relationships stay active
4. Move fully-cold entries to `archived/`
5. Regenerate all indexes

Concepts learned at one job but useful everywhere keep `status: active` with the old domain removed.

---

## Seeding Initial Entries

The knowledge base grows organically through compilation, but you can seed it manually:

1. Pick your 3-5 highest-value topics (active evaluations, key projects, important contacts)
2. Create entries following the format above
3. The nightly sweep will build on these as new information arrives

Don't batch-compile historical data. Start from "now" and grow forward -- historical knowledge is already distilled into project docs and monthly summaries.

---

## Lint Checks (Nightly)

| Check | Condition |
|-------|-----------|
| Stale | `updated` >30 days on active entry |
| Orphaned | No inbound `related` links |
| Empty sections | Key Facts or Current State blank |
| Dead sources | `sources` points to missing files |
| Domain drift | Active entry tagged with dormant domain |
| Duplicate suspected | Two entries covering same topic |

Lint results are written to `state/knowledge-compile-state.json` and surfaced in the morning briefing.
