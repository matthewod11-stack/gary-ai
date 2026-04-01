# Error Handling & Degraded Mode

Every task must handle data source failures gracefully. Never let a single source failure prevent the briefing from being produced.

## Data Source Failure Rules

| Source | Detection | Degraded Behavior |
|--------|-----------|-------------------|
| **Email** | MCP tool returns error or no data | Write "Email unavailable — check connector in Settings" in briefing. Produce other sections. |
| **Calendar** | MCP tool returns error | Write "Calendar unavailable" in Today's Calendar section |
| **Slack digest** | `state/slack-digest.json` missing or >26 hours old | Use stale data with inline warning: "Slack: stale (data from [date])" |
| **GitHub** | `gh api user` timeout or error | Write "GitHub unavailable" in Projects section |
| **Weather** | `curl` timeout >5 seconds | Write "Weather: unavailable" |
| **Transcripts** | `vault/transcripts/` empty | Skip transcript references in meeting prep |

## Error State File

On any job failure, write to `state/last-error.json`:
```json
{
  "job": "morning-briefing",
  "timestamp": "2026-03-21T06:35:00-0800",
  "error": "Gmail connector returned error — may need re-authorization",
  "severity": "high"
}
```

## Catch-Up Mode Adjustments

When the morning briefing detects it's running after noon (catch-up mode), relax staleness thresholds:

| Source | Normal Max Staleness | Catch-Up Max Staleness |
|--------|---------------------|----------------------|
| Slack digest | 26 hours | 36 hours |
| Email lookback | 3 days | 4 days |

This prevents false "stale" warnings when the machine was simply asleep. The briefing header will already note that this is a catch-up run.

## Reliability Rules

- If a data source fails, note it in the briefing and continue with other sources
- Weather is best-effort — never block on it
- Slack digest is pre-fetched; if stale, use with warning
- Always produce a briefing, even if every source fails — a "degraded mode" briefing with clear status is better than nothing
