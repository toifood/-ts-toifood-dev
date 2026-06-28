MUST ASSET LOG
prompt: Usage tracking implementation, rate limiting coverage, quota enforcement, metering status
path: must/USAGE-ASSET-2026Q2.md
target: toifood/-ts-toifood-web

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS REQUIREMENTS EVOLVE.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ASSET:{NAME} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->## ASSET:USAGE 2026-06-28 13:12 ▸ Tier limits documented in FAQ; token-based recipe sharing provides access control
## ASSET:USAGE 2026-06-29 06:30 ▸ Tier limits documented in FAQ; token-based sharing; Cloudflare edge DDoS mitigation; state confirmed unchanged

FAQ.jsx (id: generation-limit) clearly communicates hourly limits for both tiers and explains the hourly reset. SharedRecipe.jsx uses opaque share tokens (/recipe/:token) for public recipe access with no authentication required for viewing — the intended design. Token-based URLs provide implicit access control: expired or removed recipes return a user-facing error ("This link may have expired or the recipe was removed"). Offline access for saved recipes is documented in FAQ (id: offline). Cloudflare Pages + Worker architecture (wrangler.toml) provides edge-layer DDoS mitigation upstream of the API.

FAQ.jsx (id: generation-limit) clearly communicates hourly limits for both tiers and explains the hourly reset. SharedRecipe.jsx uses opaque share tokens (`/recipe/:token`) for public recipe access — no authentication required for viewing, which is the intended design. Token-based URLs provide implicit access control: expired or removed recipes return a user-facing error ("This link may have expired or the recipe was removed"). Offline access for saved recipes is documented in FAQ (id: offline). The Cloudflare Pages + Worker architecture (wrangler.toml) provides edge-layer DDoS mitigation upstream of the API.
