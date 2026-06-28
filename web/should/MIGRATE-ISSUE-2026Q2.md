SHOULD ISSUE LOG
prompt: review and update migration plans, version upgrades, breaking changes, deprecation paths, schema changes
path: should/MIGRATE-ISSUE-2026Q2.md
target: ts-toifood-web

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS THE SYSTEM EVOLVES.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ISSUE:MIGRATE {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->## ISSUE:MIGRATE 2026-06-29 06:24 ▸ wrangler.toml compatibility_date is ~20 months stale across both Cloudflare deployments

Both `frontend/wrangler.toml` (toifood-web) and `og-worker/wrangler.toml` (toifood-og) have `compatibility_date = "2024-09-23"`. Cloudflare Workers uses this date to gate runtime behaviour changes — any Workers platform fixes or new APIs released after September 2024 are excluded. This is ~20 months behind as of 2026-06. Should be bumped to a recent date (e.g. 2026-06-01) after reviewing the [Cloudflare compatibility flags changelog](https://developers.cloudflare.com/workers/configuration/compatibility-flags/) for breaking changes.

Vite 5.4.8 is in use (`frontend/package.json`). Vite 6 is now available with significant build performance improvements and a changed plugin API. The `vite.config.js` is minimal (only `@vitejs/plugin-react` plugin), so migration surface is small — but `@vitejs/plugin-react ^4.3.1` would need to be verified against Vite 6 compatibility before upgrading.

React 18.3.1 is current stable but React 19 is released. The codebase has no concurrent mode features, no `use()` hook, no Server Components — upgrade path is low-risk but untested. No test suite exists to validate the upgrade.

No test files detected in the repo (`.spec.ts`, `.test.ts` filtered). Any migration or version upgrade has no automated regression coverage.
