SHOULD ASSET LOG
prompt: review and update migration plans, version upgrades, breaking changes, deprecation paths, schema changes
path: should/MIGRATE-ASSET-2026Q2.md
target: ts-toifood-web

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS THE SYSTEM EVOLVES.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ASSET:MIGRATE {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->## ASSET:MIGRATE 2026-06-29 06:24 ▸ Minimal, predictable dependency surface — stateless frontend with no schema migrations

The `frontend/package.json` dependency set is deliberately lean:
- `react ^18.3.1`, `react-dom ^18.3.1` — React 18 stable
- `react-router-dom ^6.26.2` — Router v6 (current)
- `@vitejs/plugin-react ^4.3.1` + `vite ^5.4.8` — Vite 5 (current major)

The `og-worker/package.json` has a single runtime dependency: `@resvg/resvg-wasm ^2.6.2`. No other runtime dependencies.

No Prisma schema, no database, no migration files — the web layer holds no persistent state. All data migrations occur in `ts-toifood-back`.

The `frontend/public/_redirects` file defines the API proxy surface. Adding or removing backend routes requires editing this file — it is the migration boundary for any backend route changes that affect the web layer. Current proxied paths: `/auth/*`, `/recipes/*`, `/pantry/*`, `/user/*`, `/users/*`, `/lists/*`, `/flows/*`, `/stats`, `/health`, `/app-config`.
