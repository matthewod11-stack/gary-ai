# Briefing Format Guide

Read this file for ALL note-producing jobs (morning briefing, week recap, EOD digest, weekly look-ahead). These rules override conflicting format instructions.

## Visual Rules

- Use `##` and `###` headings ONLY — never numbered lists for top-level sections
- Max 2 levels of hierarchy (`## Section` -> `### Sub-section` or bullet)
- Generous whitespace between sections (blank line before and after each `##`)
- Tables for repeating data (calendar events, email triage, GitHub repos)
- Callout boxes (`> **Blocker:** ...`) sparingly — only for urgent items someone is waiting on
- Each section should fit a phone screen (~8-12 lines max)
- No "wall of bullets" — prefer short narrative sentences with key details **bolded**
- When a section has nothing to report, use a single line ("No blockers." / "Inbox clear.") — do NOT omit the heading

## Section Order — Morning Briefing (Tue-Thu)

```
### Who's Waiting on Me
### Today's Calendar
### Projects & Code
### Quick Hits
### Task Agenda
```

## Section Order — Friday Week Recap

```
### Week Recap
### Who's Waiting
### Calendar Today
### Projects & Code
### Quick Hits
### Task Agenda
```

## Section Order — EOD Digest

```
### Day Summary
### Task Progress
### Slack Activity
### Tomorrow Preview
```

## Section Order — Weekly Look-Ahead

```
## Week Theme
## Calendar Overview
## Key Meetings to Prep
## Open Tasks
## Projects & Code
## Quick Hits
```

## Format Examples

### Email table:
| Account | From | Subject | Age | Action |
|---------|------|---------|-----|--------|
| Work | Luke Y. | Budget update | 2h | Reply with numbers |
| Work | Chris | Dashboard | 1d | FYI |

### Calendar table:
| Time | Event |
|------|-------|
| 9:00 AM | Engineering Standup |
| 2:00 PM | 1:1 with Luke |

### Slack callout (blockers only):
> **Blocker:** Luke asked about the deploy config in #engineering — waiting 4h for your input.

### GitHub table:
| Project | Status | Next Step |
|---------|--------|-----------|
| my-app | 3 commits this week | Wire up search filters |
| api-service | Idle 5 days | Resume integration |

### Quick Hits (compact):
**Weather:** SF 58F, cloudy clearing by noon.
**Email:** 4 unread — 1 needs reply.
