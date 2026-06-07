ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->
## ISSUE:toifood 2026-06-07 → Claude skill tool execution requires local machine — GitHub Actions hosted runners incompatible

Claude skills split into two parts: LLM inference runs on Anthropic servers (covered by Claude Pro), tool execution (Bash, Read, Write, file I/O) runs on the local machine. GitHub Actions hosted runners (`ubuntu-latest`) have no connection to the local machine, so Claude's tool calls have nowhere to land.

**Options excluding local machine:**
- `anthropics/claude-code-action` — runs Claude Code in GitHub Actions but requires `ANTHROPIC_API_KEY` (API billing, not Claude Pro)
- Direct `api.anthropic.com` call — same billing
- Self-hosted runner — uses Claude Pro but requires local machine to be on

**Conclusion:** Excluding local machine = Anthropic API billing (~$1–3/month at Haiku rates). Decision pending.
## ISSUE:toifood 2026-06-07 → Claude skills are CLI-only — cannot be called from GitHub Actions

Confirmed: Claude skills run in the interactive Claude Code CLI session covered by Claude Pro. GitHub Actions cannot invoke them. Resolved by running the `/would-update` skill locally via wmux cron instead — no GitHub Actions LLM dependency, no `ANTHROPIC_API_KEY` needed.
## ISSUE:toifood 2026-06-07 → Claude skills (Claude Code) are CLI-only — not callable from GitHub Actions

Confirmed: skills run in the interactive CLI session, covered by Claude Pro subscription. Cannot be invoked from GitHub Actions workflows. Rules out using Claude Code/skills as the pipeline LLM backend — `api.anthropic.com` is used instead (`ANTHROPIC_API_KEY` org secret).
