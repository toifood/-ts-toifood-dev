SHOULD ASSET LOG
prompt: review and update architecture patterns, service boundaries, infrastructure decisions, dependency graph
path: should/ARCH-ASSET-2026Q2.md
target: ts-toifood-web

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS THE SYSTEM EVOLVES.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ASSET:ARCH {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->## ASSET:ARCH 2026-06-29 06:24 ▸ Clean two-service Cloudflare architecture: Pages SPA + dedicated OG Worker

The web layer is composed of two clearly scoped Cloudflare services:

1. **toifood-web** (Cloudflare Pages) — React 18 + Vite 5 SPA. Routes: `/`, `/privacy`, `/terms`, `/faq`, `/recipe/:token`. Cloudflare Pages Functions handle SSR-like OG meta tag injection at `frontend/functions/recipe/[token].js` — fetches recipe title/description from the backend and rewrites `<head>` tags before serving to crawlers, without requiring a full SSR framework.

2. **toifood-og** (Cloudflare Worker) — standalone OG image generator at `og-worker/src/index.js`. Uses `@resvg/resvg-wasm ^2.6.2` to render SVG templates to PNG at 1200×630px. Fetches recipe emoji + title from the public API with a 3-second AbortController timeout; gracefully falls back to a generic title if the API is unavailable.

The SPA has five pages managed by React Router v6: landing (Home.jsx with App Store + Google Play CTAs), SharedRecipe.jsx (full recipe viewer with ingredients, steps, author card, cooking timer, YouTube embed), Privacy, Terms, FAQ. SharedRecipe fetches independently from `https://api.toifood.co.nz` — no backend-for-frontend layer.

No database or Prisma schema in this repo — the web layer is fully stateless; all data lives in `ts-toifood-back`.
