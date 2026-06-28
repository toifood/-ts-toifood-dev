MUST ISSUE LOG
prompt: TODOs, stubs, incomplete flows, API surface implying features not yet implemented, breaking gaps
path: must/ROADMAP-ISSUE-2026Q2.md
target: toifood/-ts-toifood-web

INSTRUCTION FOR AI MODEL:

YOU MAY READ AND UPDATE EXISTING ENTRIES AS REQUIREMENTS EVOLVE.
ADD NEW ENTRIES AT THE TOP FOR NEW TOPICS; UPDATE IN PLACE FOR EXISTING ONES.

FORMAT: ## ISSUE:{NAME} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD OR UPDATE ENTRIES DIRECTLY BELOW THIS LINE -->## ISSUE:ROADMAP 2026-06-28 13:12 ▸ Commented-out social features and dietary enum mismatch signal incomplete scope alignment
## ISSUE:ROADMAP 2026-06-29 06:30 ▸ Commented-out social features, dietary enum mismatch, og-worker unverifiable; state confirmed unchanged

Three issues from 2026-06-28 confirmed persisting:
1. **Pulled-back social layer** — SharedRecipe.jsx contains two commented-out blocks: a mobile sticky cooking timer and a full "Author Cooking Profile" section (aggregating dietaryTags, continents, mealTypes, cooking styles per user). Both were built and removed, suggesting scope reduction or a privacy rethink on the community layer.
2. **Dietary enum mismatch** — DIETARY_INFO in SharedRecipe.jsx includes EggFree, SoyFree, Pescatarian, Kosher, Paleo, LowCarb — six options absent from FAQ.jsx (which lists only: Vegan, Vegetarian, GlutenFree, DairyFree, Halal, Keto, NutFree) and from Privacy.jsx's data enumeration. These are either backend-supported options not yet surfaced in the app UI, or legacy stubs. FAQ and Privacy must reflect the actual supported dietary set.
3. **og-worker deployment unverifiable** — `og-worker/` Cloudflare Worker handles OG image generation but is not referenced in any frontend page; active deployment cannot be confirmed from this repo.

1. **Pulled-back social layer** — SharedRecipe.jsx contains two commented-out blocks: a mobile sticky cooking timer (lines ~993-1015) and a full "Author Cooking Profile" section (lines ~1273-1309) aggregating dietaryTags, continents, mealTypes, and cooking styles per user. Both were built and removed, suggesting scope reduction or a privacy rethink on the community layer.
2. **Dietary enum mismatch** — DIETARY_INFO in SharedRecipe.jsx includes EggFree, SoyFree, Pescatarian, Kosher, Paleo, LowCarb — six options absent from FAQ.jsx (which lists only 7: Vegan, Vegetarian, GlutenFree, DairyFree, Halal, Keto, NutFree) and from Privacy.jsx's data collection enumeration. These are either backend-supported options not yet surfaced in the app UI, or legacy stubs. FAQ and Privacy must be updated to reflect the actual supported dietary set.
3. **og-worker deployment status unclear** — A Cloudflare Worker in `og-worker/` handles OG image generation but is not referenced in any frontend page; deployment and active use is unverifiable from this repo alone.
