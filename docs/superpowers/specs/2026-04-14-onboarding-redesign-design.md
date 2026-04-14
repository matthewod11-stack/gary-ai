# Onboarding Redesign — Design Spec

**Date:** 2026-04-14  
**Status:** Approved  
**Problem:** Onboarding ends abruptly after personal questions. No MCP connection verification, no scheduled task walkthrough, no guidance on Cowork usage. New users get stuck when Claude improvises around unverified connectors.

---

## Summary

Extend the CLAUDE.md onboarding from 2 steps (machine detect + 6 questions) to 5 steps (machine detect + 4 questions + verify sources + scheduled tasks + first run). Approach A: all changes in CLAUDE.md directly, SETUP.md unchanged.

### Goals

1. Remove the "handoff cliff" — onboarding should leave users with everything running
2. Auto-detect MCP connections instead of asking about them
3. Walk through all 6 scheduled tasks during onboarding
4. Explain the Cowork pattern as the ongoing usage surface

### Non-Goals

- Changing SETUP.md (it serves a different audience — humans reading docs)
- Changing any prompt files
- Adding new features to Gary

---

## Design

### Step 0: Machine Detection (unchanged)

Silent auto-detect. Runs `system_profiler`, `pmset`, etc. Updates "About Your User" table with Machine and Always-on values. Gives machine-specific guidance about sleep settings.

No changes to this step.

### Step 1: User Questions (trimmed from 6 to 4)

**Removed questions:**
- "What email address did you connect?" → auto-detected in Step 2
- "What Slack workspace did you connect? And what are 3-5 channels?" → auto-detected in Step 2 (channels still asked there)

**Remaining questions (asked one at a time):**
1. "What's your name?"
2. "What's your role and company? (e.g., 'founder at Acme Corp', 'PM at BigCo')"
3. "What city are you in? (for weather in briefings)"
4. "Do you have a GitHub username you want me to track? (optional, press enter to skip)"

After collecting answers, update the "About Your User" table and confirm with the user.

### Step 2: Verify Data Sources (NEW)

**Automatic — do not ask about connectors. Test them.**

Silently call each MCP tool to verify the connection:

| Source | Verification call | What to extract |
|--------|------------------|-----------------|
| Gmail | `gmail_get_profile` | Email address |
| Google Calendar | `gcal_list_calendars` | Calendar list (confirms connected) |
| Slack | Attempt to list channels or check auth status | Workspace name |
| GitHub | `gh auth status` (bash) | Username, if configured |
| Exa | Trivial search call | Confirms connected |

**Display a status dashboard:**

```
📊 Data Sources
✅ Gmail (user@example.com)
✅ Google Calendar
✅ Slack (acme-corp workspace)
⬚ GitHub — not configured (optional)
⬚ Exa — not configured (optional, enables topic research)
```

**Status icons:**
- ✅ = connected and working
- ❌ = expected but not working (Gmail and Calendar are required)
- ⬚ = optional, not configured

**For ✅ sources:**
- Auto-populate the corresponding CLAUDE.md table (email account, Slack workspace, GitHub username)
- No questions asked — the data comes from the API response

**For ✅ Slack specifically:**
- After confirming connection, ask: "Which 3-5 Slack channels matter most to you? (e.g., #engineering, #general, #product)"
- Populate the Slack channels table in CLAUDE.md

**For ❌ required sources (Gmail, Calendar):**
- Give a specific fix instruction with the correct tab:
  - Gmail/Calendar: "Go to Claude Desktop → Settings → MCP Servers (Code tab) → search for [source] → authorize with your Google account."
  - Slack: "Go to Claude Desktop → Settings → MCP Servers (Cowork tab) → search for Slack → authorize with your workspace."
- After giving the instruction, say: "Let me know when you've connected it and I'll re-check."
- Re-run the verification for that source when the user says they're ready.

**For ⬚ optional sources:**
- One-liner explaining what they enable:
  - GitHub: "Enables PR and issue tracking in briefings. Install later with `brew install gh && gh auth login`."
  - Exa: "Enables on-demand topic research across the web. Add it in Settings → MCP Servers (Code tab) anytime."
- Do not pressure. Move on.

**Gate:** At least Gmail + Calendar must be ✅ before proceeding to Step 3. Slack is strongly recommended but not blocking.

### Step 3: Set Up Scheduled Tasks (NEW)

**Ask one question:** "What time do you usually start your day? (e.g., 7 AM, 9 AM)"

**Calculate all schedules from that anchor time.** Example for a 7 AM start:

| # | Task | Schedule | Prompt |
|---|------|----------|--------|
| 1 | Slack Prefetch | Daily at 6:30 AM | `Read prompts/slack-prefetch.md and run it.` |
| 2 | Morning Briefing | Weekdays at 7:00 AM | `Read prompts/morning-briefing.md and run it.` |
| 3 | Meeting Prep | Every 30 min, 7:00 AM – 5:00 PM weekdays | `Read prompts/meeting-prep.md and run it.` |
| 4 | EOD Digest | Weekdays at 5:00 PM | `Read prompts/eod-digest.md and run it.` |
| 5 | Weekly Retrospective | Friday at 5:00 PM | `Read prompts/weekly-retrospective.md and run it.` |
| 6 | Weekly Look-Ahead | Sunday at 7:00 PM | `Read prompts/weekly-lookahead.md and run it.` |

**Schedule calculation rules:**
- Slack Prefetch = start time - 30 minutes
- Morning Briefing = start time
- Meeting Prep = start time through start time + 10 hours, every 30 minutes, weekdays
- EOD Digest = start time + 10 hours, weekdays
- Weekly Retro = Friday at EOD time
- Weekly Look-Ahead = Sunday at 7 PM (fixed)

**Walkthrough:**

Present the full table with calculated times, then instruct:

> "To set these up, go to Claude Desktop → Settings → Scheduled Tasks. For each one, create a new task pointing at your gary-ai project folder with the schedule and prompt shown above."

Ask the user their preference:
> "Want me to walk through them one at a time, or would you rather see the full list and create them in a batch?"

- **One-by-one:** Present each task, wait for "done" / "created" / "next", then show the next.
- **Batch:** Show the full table (already shown), say "Let me know when you've created all 6."

After all tasks are created, confirm:
> "All 6 scheduled tasks set up. Your first automated briefing will run tomorrow at [briefing time]."

### Step 4: First Run + What's Next (NEW)

**Run the first briefing live:**
> "Let's run your first briefing now so you can see what Gary produces."

Execute `prompts/morning-briefing.md`. Let it complete.

**Show where the output landed:**
> "Your daily note is at `vault/YYYY-MM-DD.md`. Open it in any text editor to review."

If the briefing succeeded, mention Obsidian:
> "For a searchable knowledge base, point [Obsidian](https://obsidian.md/) at the `vault/` folder. Optional but recommended."

**Explain the Cowork pattern:**
> "From now on, open the gary-ai folder in Claude Desktop as a Cowork session. That's your surface for talking to Gary — status checks, research, draft replies, decision-ready docs. The scheduled tasks build context in the background; the Cowork chat is where you use it."

**Closing line:**
> "Tomorrow morning, check `vault/` for your daily note. If it's there, Gary is working. Welcome aboard."

---

## Changes Required

### Files Modified

| File | Change |
|------|--------|
| `CLAUDE.md` | Rewrite the Onboarding section (Steps 0-1 modified, Steps 2-4 added) |

### Files NOT Modified

| File | Reason |
|------|--------|
| `SETUP.md` | Serves a different audience (humans reading docs). Some duplication is fine. |
| `README.md` | No changes needed. Get Started section already points to SETUP.md. |
| `prompts/*` | No prompt changes. Onboarding just references existing prompts. |

---

## Acceptance Criteria

1. New user runs onboarding → gets asked 4 personal questions (not 6)
2. MCP connections are tested automatically, not asked about
3. Status dashboard shows which sources are connected with specific fix instructions for missing ones
4. CLAUDE.md tables are auto-populated from API responses (email, Slack workspace, channels)
5. All 6 scheduled tasks are walked through with calculated times based on user's start time
6. First briefing runs live during onboarding
7. User ends onboarding understanding the Cowork pattern
8. Onboarding works even if Slack or optional sources aren't connected (degrades gracefully)
