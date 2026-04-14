# Progress — Gary AI

---

## Session: 2026-04-14 afternoon

### Completed
- Redesigned CLAUDE.md onboarding from 2 steps to 5 steps based on real user feedback (friend got stuck on MCP connector setup)
- Step 1: Trimmed from 6 questions to 4 (removed email/Slack — now auto-detected)
- Step 2 (NEW): Auto-detect MCP connections silently, show status dashboard, give targeted fix instructions per tab (Code tab vs Cowork tab for Slack)
- Step 3 (NEW): Guided scheduled task setup — asks start time, calculates all 6 task schedules, walks through creation in Claude Desktop
- Step 4 (NEW): Runs first briefing live, explains Cowork as ongoing surface, closing guidance
- Updated README Get Started section (3 steps, ~20 min estimate)
- Updated SETUP.md Step 4 to describe new onboarding flow, reframed Tier 2 as reference since onboarding now handles scheduled task setup
- Wrote design spec (`docs/superpowers/specs/2026-04-14-onboarding-redesign-design.md`)
- Wrote implementation plan (`docs/superpowers/plans/2026-04-14-onboarding-redesign.md`)
- All changes pushed to remote

### In Progress
- Nothing — all changes committed and pushed

### Issues Encountered
- Key discovery: Slack MCP connector lives in Cowork tab, not Code tab. Gmail/Calendar/Exa are in Code tab. Fix instructions in onboarding need to reference the correct tab for each.
- Confirmed that MCP connectors set up in Claude Desktop are available to Claude Code sessions (account-level, not app-level)

### Next Session Should
- Have friend re-test: delete existing gary-ai folder, clone fresh, run new onboarding end-to-end
- Update `docs/screenshots/gary-features.png` (still stale from last session)
- Review SOUL.md and IDENTITY.md for consistency with desktop-app positioning (carried from last session)
- Consider whether SETUP.md Step 3 needs Slack tab distinction (currently says "Go to Settings > MCP Servers and connect" without specifying Code vs Cowork tab)

---

## Session: 2026-04-03 12:00

### Completed
- Updated README with knowledge base and transcript sync features (added to features table, tech stack, architecture diagram, project structure)
- Clarified Cowork as the primary user interface — no terminal needed
- Simplified Get Started steps to: download app, connect sources, chat
- Reframed entire README from "built on Claude Code" to "built for the Claude Desktop app" — positions Gary for non-developers
- Updated GitHub description and added `knowledge-base` + `markdown-vault` topics (10 total)

### In Progress
- Nothing — all changes committed and pushed

### Issues Encountered
- None

### Next Session Should
- Update `docs/screenshots/gary-features.png` to reflect knowledge base and transcript sync (infographic is now stale)
- Consider whether SETUP.md also needs the "Claude Desktop" reframing (currently still references Claude Code in places)
- Review SOUL.md and IDENTITY.md for consistency with the new desktop-app positioning
