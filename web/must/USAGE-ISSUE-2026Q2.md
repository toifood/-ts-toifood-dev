MUST ISSUE LOG
prompt: Missing rate limits, unmetered endpoints, quota enforcement gaps, AI API usage without cost controls
path: must/USAGE-ISSUE-2026Q2.md
target: toifood/-ts-toifood-web

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS REQUIREMENTS EVOLVE.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ISSUE:{NAME} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->## ISSUE:USAGE 2026-06-28 13:12 ▸ Usage limits absent from Terms; public API endpoint has no client-side rate-limit handling
## ISSUE:USAGE 2026-06-29 06:30 ▸ Usage limits absent from Terms; public API lacks 429 handling; no quota visibility; state confirmed unchanged

Three issues from 2026-06-28 confirmed persisting:
1. **Limits not contractually disclosed** — FAQ.jsx documents hourly generation limits (Free: 2 Claude + 3 Basic; Premium: 5 Claude + 10 Basic) but Terms.jsx is silent on usage limits. Users have no contractual notice that limits exist or can change.
2. **Public endpoint silent on 429** — SharedRecipe.jsx calls `https://api.toifood.co.nz/recipes/public/${token}` and `/users/${userId}/profile` with no retry-after handling. Rate-limit errors collapse silently into the generic "Recipe not found" error state, obscuring the real cause.
3. **No quota visibility** — No web-facing usage dashboard or remaining-quota indicator exists for authenticated users on the marketing site.

1. **Limits not contractually disclosed** — FAQ.jsx documents hourly generation limits (Free: 2 Claude + 3 Basic; Premium: 5 Claude + 10 Basic) but Terms.jsx is silent on usage limits. Users have no contractual notice that limits exist or can be changed.
2. **Public endpoint silent on failure** — SharedRecipe.jsx calls `https://api.toifood.co.nz/recipes/public/${token}` and `/users/${userId}/profile` with no retry-after handling or quota feedback. Rate-limit errors from the backend collapse silently into the generic error state ("Recipe not found"), obscuring the real cause from users.
3. **No quota visibility** — No web-facing usage dashboard or remaining-quota indicator exists for authenticated users on the marketing site.
