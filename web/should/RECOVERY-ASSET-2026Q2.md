SHOULD ASSET LOG
prompt: review and update disaster recovery procedures, rollback plans, backup strategies, incident response runbooks
path: should/RECOVERY-ASSET-2026Q2.md
target: ts-toifood-web

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS THE SYSTEM EVOLVES.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ASSET:RECOVERY {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->## ASSET:RECOVERY 2026-06-29 06:24 ▸ Cloudflare Pages provides instant rollback; Pages Function and og-worker both have timeout guards on external fetches

Cloudflare Pages maintains a full deployment history with one-click rollback available in the Cloudflare dashboard. The `toifood-og` Worker is separately versioned and can be independently rolled back via `wrangler rollback`.

The Pages Function at `frontend/functions/recipe/[token].js` is defensively written for recovery scenarios:
- Falls through to default title (`toifood`) and description (`A recipe shared on toifood.`) if the backend is unreachable
- `catch (_) {}` prevents the function from erroring — the HTML is always served, even without recipe-specific meta tags
- Adds OG image dimensions metadata (`og:image:width: 2400`, `og:image:height: 1260`) to prevent layout shift on link previews

The `og-worker/src/index.js` has two `AbortController` timeout guards (both 3000ms): one for the recipe API fetch and one for the Twemoji CDN fetch. If either times out, the worker continues with graceful fallback (empty emoji, generic title) rather than failing the entire request.

The SPA catch-all route (`/* /index.html 200`) ensures the React app shell is always served, even for unknown paths. `SharedRecipe.jsx` renders a user-friendly "Recipe not found" error state (emoji 😕 + message + back link) when the API returns an error or non-OK status.
