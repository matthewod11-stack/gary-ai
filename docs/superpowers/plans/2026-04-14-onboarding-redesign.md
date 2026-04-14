# Onboarding Redesign Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Extend CLAUDE.md onboarding so new users finish with verified MCP connections, all 6 scheduled tasks configured, and their first briefing run — instead of getting stuck after personal questions.

**Architecture:** Single-file edit to CLAUDE.md. Replace the existing Step 1 (6 user questions) with a trimmed Step 1 (4 questions), then add 3 new steps: Step 2 (auto-detect and verify MCP connections), Step 3 (guided scheduled task setup), Step 4 (first run + Cowork explanation).

**Tech Stack:** Markdown (CLAUDE.md is a prompt instruction file, not code)

**Spec:** `docs/superpowers/specs/2026-04-14-onboarding-redesign-design.md`

---

### Task 1: Replace Step 1 (User Questions)

**Files:**
- Modify: `CLAUDE.md:214-227`

- [ ] **Step 1: Replace the existing Step 1 section**

Replace lines 214-227 of `CLAUDE.md` (the current `### Step 1: User Questions` through end of file) with the following:

```markdown
### Step 1: User Questions

**Ask the user these questions, one at a time:**

1. "What's your name?"
2. "What's your role and company? (e.g., 'founder at Acme Corp', 'PM at BigCo')"
3. "What city are you in? (for weather in briefings)"
4. "Do you have a GitHub username you want me to track? (optional, press enter to skip)"

After collecting answers, update the "About Your User" table with their responses. Confirm the changes with the user.

Do NOT ask about email, calendar, or Slack — those are verified automatically in Step 2.
```

- [ ] **Step 2: Verify the edit**

Read `CLAUDE.md` starting at line 214. Confirm:
- Only 4 questions listed (name, role, city, GitHub)
- No mention of email or Slack connections in the questions
- Ends with the "Do NOT ask" instruction

- [ ] **Step 3: Commit**

```bash
git add CLAUDE.md
git commit -m "Trim onboarding Step 1 from 6 questions to 4

Remove email and Slack questions — these are now auto-detected
in the new Step 2 (Verify Data Sources)."
```

---

### Task 2: Add Step 2 (Verify Data Sources)

**Files:**
- Modify: `CLAUDE.md` (append after Step 1)

- [ ] **Step 1: Add the Step 2 section**

Append the following immediately after the Step 1 section added in Task 1:

```markdown

### Step 2: Verify Data Sources (automatic — do not ask)

Test each MCP connector silently. Do not ask the user whether they connected anything — just try calling each one.

**Verification calls:**

| Source | What to call | What to extract |
|--------|-------------|-----------------|
| Gmail | `gmail_get_profile` | Email address from the profile response |
| Google Calendar | `gcal_list_calendars` | Calendar list (confirms connection) |
| Slack | List channels or check auth status | Workspace name |
| GitHub | Run `gh auth status` via bash | GitHub username |
| Exa | Run a trivial Exa search | Confirms connection |

**After testing all sources, show a status dashboard:**

```
📊 Data Sources
✅ Gmail (user@example.com)
✅ Google Calendar
✅ Slack (workspace-name)
⬚ GitHub — not configured (optional)
⬚ Exa — not configured (optional, enables topic research)
```

**Status icons:**
- ✅ = connected and working
- ❌ = not working (required sources only — Gmail and Calendar)
- ⬚ = not configured (optional sources)

**For each ✅ source:** Auto-populate the matching table in CLAUDE.md:
- Gmail ✅ → fill in the Email table with the detected email address and set Status to "Connected"
- Google Calendar ✅ → fill in the Calendar table and set Status to "Connected"
- Slack ✅ → fill in the Slack table with the detected workspace name and set Status to "Connected"
- GitHub ✅ → fill in the GitHub table with the detected username and set Status to "Connected"

**For ✅ Slack specifically:** After confirming the connection, ask one question:
> "Slack is connected. Which 3-5 channels matter most to you? (e.g., #engineering, #general, #product)"

Populate the "Channels to monitor" table with their answer.

**For ❌ required sources (Gmail, Calendar):** Give a specific fix instruction:
- Gmail or Calendar: "To connect: open Claude Desktop → Settings → MCP Servers (Code tab) → search for [Gmail / Google Calendar] → authorize with your Google account."
- Slack: "To connect: open Claude Desktop → Settings → MCP Servers (Cowork tab) → search for Slack → authorize with your workspace."

After giving the instruction, say: "Let me know when you've connected it and I'll re-check."
When the user is ready, re-run the verification call for that source and update the dashboard.

**For ⬚ optional sources:** Mention what they enable, do not pressure:
- GitHub: "Enables PR and issue tracking in your briefings. You can add it anytime: `brew install gh && gh auth login`."
- Exa: "Enables on-demand topic research across the web and social media. Add it in Settings → MCP Servers (Code tab) anytime."

**Gate:** Do not proceed to Step 3 until at least Gmail AND Calendar are ✅. Slack is strongly recommended but not required.
```

- [ ] **Step 2: Verify the edit**

Read the newly added section. Confirm:
- Verification table has all 5 sources with specific calls
- Dashboard example uses ✅/❌/⬚ icons correctly
- Auto-populate instructions reference the correct CLAUDE.md tables
- Slack channel question is asked after verification, not before
- Fix instructions reference correct tabs (Code tab for Gmail/Calendar/Exa, Cowork tab for Slack)
- Gate requires Gmail + Calendar before proceeding

- [ ] **Step 3: Commit**

```bash
git add CLAUDE.md
git commit -m "Add onboarding Step 2: auto-detect MCP connections

Silently tests each connector, shows a status dashboard, and
auto-populates CLAUDE.md tables from API responses. Only asks
about Slack channels — everything else is detected."
```

---

### Task 3: Add Step 3 (Set Up Scheduled Tasks)

**Files:**
- Modify: `CLAUDE.md` (append after Step 2)

- [ ] **Step 1: Add the Step 3 section**

Append the following immediately after the Step 2 section added in Task 2:

```markdown

### Step 3: Set Up Scheduled Tasks

**Ask one question:**
> "What time do you usually start your day? (e.g., 7 AM, 9 AM)"

**Calculate all task schedules from the user's start time.** Use these rules:

| Task | Schedule Rule |
|------|--------------|
| Slack Prefetch | Daily, start time minus 30 minutes |
| Morning Briefing | Weekdays at start time |
| Meeting Prep | Every 30 minutes from start time to start time + 10 hours, weekdays |
| EOD Digest | Weekdays at start time + 10 hours |
| Weekly Retrospective | Friday at start time + 10 hours |
| Weekly Look-Ahead | Sunday at 7:00 PM (fixed) |

**Present the calculated schedule as a table.** Example for a 7:00 AM start:

| # | Task | Schedule | Prompt |
|---|------|----------|--------|
| 1 | Slack Prefetch | Daily at 6:30 AM | `Read prompts/slack-prefetch.md and run it.` |
| 2 | Morning Briefing | Weekdays at 7:00 AM | `Read prompts/morning-briefing.md and run it.` |
| 3 | Meeting Prep | Every 30 min, 7 AM – 5 PM weekdays | `Read prompts/meeting-prep.md and run it.` |
| 4 | EOD Digest | Weekdays at 5:00 PM | `Read prompts/eod-digest.md and run it.` |
| 5 | Weekly Retrospective | Friday at 5:00 PM | `Read prompts/weekly-retrospective.md and run it.` |
| 6 | Weekly Look-Ahead | Sunday at 7:00 PM | `Read prompts/weekly-lookahead.md and run it.` |

**Then instruct the user:**
> "To set these up: open Claude Desktop → Settings → Scheduled Tasks. For each task, create a new scheduled task pointing at your gary-ai project folder with the schedule and prompt shown above."

**Ask the user their preference:**
> "Want me to walk through them one at a time, or would you prefer the full list to create them in a batch?"

- **One-by-one mode:** Present Task 1 details. Wait for user to confirm it's created (e.g., "done", "next", "created"). Then present Task 2. Repeat through Task 6.
- **Batch mode:** The full table is already shown. Say "Let me know when you've created all 6."

**After all tasks are created, confirm:**
> "All 6 scheduled tasks are set up. Your first automated briefing will run tomorrow at [calculated briefing time]."
```

- [ ] **Step 2: Verify the edit**

Read the newly added section. Confirm:
- Asks only one question (start time)
- Schedule calculation rules are listed for all 6 tasks
- Example table shows correct times for 7 AM start
- All 6 prompts reference the correct files in `prompts/`
- Instructions reference "Claude Desktop → Settings → Scheduled Tasks"
- Offers both one-by-one and batch walkthrough modes
- Confirmation message references the calculated briefing time

- [ ] **Step 3: Commit**

```bash
git add CLAUDE.md
git commit -m "Add onboarding Step 3: guided scheduled task setup

Asks start time, calculates all 6 task schedules, walks user
through creating them in Claude Desktop. Supports one-by-one
or batch creation."
```

---

### Task 4: Add Step 4 (First Run + What's Next)

**Files:**
- Modify: `CLAUDE.md` (append after Step 3)

- [ ] **Step 1: Add the Step 4 section**

Append the following immediately after the Step 3 section added in Task 3:

```markdown

### Step 4: First Run + What's Next

**Run the first briefing live:**
> "Let's run your first briefing now so you can see what Gary produces."

Read and execute `prompts/morning-briefing.md`. Let it complete fully.

**After the briefing completes, show where the output landed:**
> "Your daily note is at `vault/YYYY-MM-DD.md` (with today's date). Open it in any text editor to see the full briefing."

**Mention Obsidian (optional):**
> "If you want a searchable knowledge base, download [Obsidian](https://obsidian.md/) and point it at the `vault/` folder. It's free and turns your daily notes, transcripts, and project docs into a browsable wiki. Optional but recommended."

**Explain the Cowork pattern:**
> "From now on, open the gary-ai folder in Claude Desktop as a Cowork session. That's your surface for talking to Gary — ask about project status, research topics, draft replies, prep for meetings. The scheduled tasks build context in the vault over time; the Cowork chat is where you use it."

**Closing line:**
> "Tomorrow morning, check `vault/` for your daily note. If it's there, Gary is working. Welcome aboard."
```

- [ ] **Step 2: Verify the edit**

Read the newly added section. Confirm:
- Runs the first briefing via `prompts/morning-briefing.md`
- Points to `vault/YYYY-MM-DD.md` for output
- Mentions Obsidian as optional
- Explains Cowork as the ongoing usage surface
- Ends with a clear "you're done" signal

- [ ] **Step 3: Verify full onboarding flow end-to-end**

Read the entire Onboarding section of `CLAUDE.md` (from `## Onboarding` to end of file). Verify:
- Step 0 (Machine Detection) is unchanged
- Step 1 has 4 questions (no email/Slack)
- Step 2 has the full verification flow with dashboard
- Step 3 has all 6 scheduled tasks with calculation rules
- Step 4 has first run + Cowork explanation
- No orphaned references to the old 6-question flow
- The "Do NOT ask about email" instruction in Step 1 is consistent with Step 2 auto-detecting

- [ ] **Step 4: Commit**

```bash
git add CLAUDE.md
git commit -m "Add onboarding Step 4: first run and Cowork guidance

Runs the first briefing live, shows output location, explains
the Cowork pattern as the ongoing usage surface. Completes
the full onboarding redesign."
```

---

### Task 5: Final Verification

**Files:**
- Read: `CLAUDE.md` (full onboarding section)
- Read: `docs/superpowers/specs/2026-04-14-onboarding-redesign-design.md`

- [ ] **Step 1: Check spec coverage**

Read the spec's Acceptance Criteria and verify each one against the implementation:

1. New user gets asked 4 personal questions (not 6) → Step 1
2. MCP connections are tested automatically → Step 2
3. Status dashboard with fix instructions → Step 2
4. CLAUDE.md tables auto-populated from API responses → Step 2
5. All 6 scheduled tasks walked through with calculated times → Step 3
6. First briefing runs live during onboarding → Step 4
7. User understands Cowork pattern → Step 4
8. Graceful degradation if Slack/optionals not connected → Step 2 (gate only requires Gmail + Calendar)

- [ ] **Step 2: Test the onboarding trigger**

Verify the onboarding trigger condition still works: the section starts with "If the sections above show `[not yet configured]`, run the onboarding" — confirm this text is still present and unmodified.

- [ ] **Step 3: Run a grammar/consistency pass**

Read the full onboarding section once more. Check for:
- Consistent voice (instructions to Claude, not to the user)
- No references to removed questions (email/Slack in Step 1)
- Markdown formatting (tables render, code blocks close, headers nest correctly)
