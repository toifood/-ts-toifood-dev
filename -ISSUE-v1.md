ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->
## ISSUE:toifood 2026-06-07 → Claude skills are CLI-only — cannot be called from GitHub Actions

Confirmed: Claude skills run in the interactive Claude Code CLI session covered by Claude Pro. GitHub Actions cannot invoke them. Resolved by running the `/would-update` skill locally via wmux cron instead — no GitHub Actions LLM dependency, no `ANTHROPIC_API_KEY` needed.
## ISSUE:toifood 2026-06-07 → Claude skills (Claude Code) are CLI-only — not callable from GitHub Actions

Confirmed: skills run in the interactive CLI session, covered by Claude Pro subscription. Cannot be invoked from GitHub Actions workflows. Rules out using Claude Code/skills as the pipeline LLM backend — `api.anthropic.com` is used instead (`ANTHROPIC_API_KEY` org secret).
