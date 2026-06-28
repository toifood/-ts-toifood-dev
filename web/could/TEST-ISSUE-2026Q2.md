ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} → {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->## ISSUE:test 2026-06-14 08:29 → Zero tests across all packages; no testing framework installed
## ISSUE:test 2026-06-29 06:25 → AnnouncementNote crashes on unknown type with no guard; FAQ accordion hard clip undetectable without visual tests

**Finding — `frontend/src/components/AnnouncementNote.jsx`**
`const ts = TYPE_STYLES[config.type ?? 'note']` returns `undefined` for any `type` value not in the `TYPE_STYLES` map (e.g. `'critical'`, `'alert'`, or a future `NoteType` addition). The component immediately dereferences `ts.iconBg`, `ts.icon`, `ts.iconColor`, and `ts.label` — all four throw `TypeError: Cannot read properties of undefined` if `ts` is `undefined`. `useAnnouncementNoteManager` currently only emits types from the `NoteType` enum plus the hardcoded string `'tip'`, so this path is unreachable in production today. However, `COLLAPSED_LINES = 2` is defined at the top of `AnnouncementNote.jsx` and never used, indicating prior refactoring; a developer adding a new note type to the hook without a matching `TYPE_STYLES` entry would silently crash every recipe page rendering that note. No test guards this type-map invariant.

**Finding — `frontend/src/pages/FAQ.jsx` (line 1039)**
`maxHeight: open ? 300 : 0` hard-codes the expanded accordion height at 300 px. Several answers — notably "How do I set or change my dietary preferences?" which lists five steps — may exceed 300 px on viewports narrower than 420 px. Content beyond 300 px is clipped with no scroll, ellipsis, or visual indicator that text was cut. No snapshot or visual regression test exists to catch when existing FAQ copy grows past the clip boundary, or when a newly added long answer is silently truncated.
## ISSUE:test 2026-06-28 06:36 → Zero test infrastructure; wrapTitle regression undetected for 6+ weeks due to no coverage

**Finding — `frontend/package.json` and `og-worker/package.json`**
Neither the frontend nor the og-worker has a test framework configured. `frontend/package.json` scripts are `dev`, `build`, and `preview` only — no `test` entry, no vitest, no jest, no testing-library. `og-worker/package.json` lists only `@resvg/resvg-wasm` as a dependency. `frontend/vite.config.js` has no test configuration block. There are no `*.spec.*` or `*.test.*` files anywhere in the repository. The codebase has zero automated test coverage.

**Finding — `og-worker/src/index.js` `wrapTitle` as a case study**
The `wrapTitle` word-drop bug has been logged in five separate BUG-ISSUE entries across this quarter (June 14, 20, 23, 24, 27) and remains unfixed. It is a pure function with deterministic input/output — a four-line unit test would have caught the regression on the first commit that introduced it and prevented five subsequent re-discoveries. The same applies to `resolveNote`, `resolveDietaryAllergyNote`, and `resolveDietaryInfoNote` in `announcementNote.js`: all pure functions with safety-critical output (allergen warnings) and no test coverage. The `onRequest` Pages function in `frontend/functions/recipe/[token].js` is also a pure HTML-string transform that could be unit-tested against a mock `env.ASSETS` without any Cloudflare runtime.
## ISSUE:test 2026-06-27 10:55 → Zero test coverage — og-worker SVG logic and dietary note resolution fully unverified

**Finding — No test infrastructure**
Neither `frontend/` nor `og-worker/` contain test files or a test runner. `frontend/package.json` has no test script and no testing dependencies (no Vitest, Jest, or Testing Library). `og-worker/package.json` is equally bare. There is no CI pipeline in the repository.

**Highest-risk untested units**

`og-worker/src/index.js` — `wrapTitle(title, maxChars)` has a confirmed silent-truncation bug (words discarded at overflow) that a unit test covering titles longer than ~52 characters would catch immediately. `emojiCodepoint(emoji)` strips variation selectors (`fe0f`) but has no coverage for multi-codepoint sequences, ZWJ sequences, or regional flag pairs. `escapeXml` is exercised only implicitly.

`frontend/src/utils/announcementNote.js` — `resolveDietaryAllergyNote` and `resolveDietaryInfoNote` branch on `criticalAllergen` vs `dietaryGuideline` fields in `dietaryInfo`. Adding a new dietary tag that lacks one of these fields silently produces no note — no error, no fallback, no observable failure during development.

`frontend/src/hooks/useReveal.js` — `IntersectionObserver` is used without a polyfill check. No test guards against environments where the API is unavailable (older Safari, SSR contexts).
## ISSUE:test 2026-06-24 19:10 → og-worker is not testable without Miniflare; `bufToBase64` chunked encoding and AnnouncementNote type-routing are unverified

Two specific gaps not yet addressed in prior entries:

**1. `og-worker` fetch handler requires Miniflare — no harness exists**
`og-worker/src/index.js` uses `cf: { cacheTtl: 300 }` inside both `fetch()` calls. This is a Cloudflare-specific API extension; calling it in a standard Node test runner does not throw but the field is silently ignored, which is fine for unit purposes. However the full handler also calls `initWasm(resvgWasm)` with a WASM binary and uses `new Resvg(svg)` — both of which fail outside a Worker or WASM-capable Node environment. `og-worker/package.json` has no Miniflare dependency and no `wrangler.toml` `[env.test]` block. The four pure helpers (`wrapTitle`, `emojiCodepoint`, `escapeXml`, `bufToBase64`) can be extracted and tested in Node, but the end-to-end render path is entirely unexercised.

**2. `bufToBase64` chunk-boundary correctness is untested**
`bufToBase64` accumulates binary using `String.fromCharCode(...bytes.subarray(i, i + chunk))` with `chunk = 8192`, then calls `btoa`. The spread of a TypedArray subarray is safe up to ~125 000 elements before V8's argument limit is reached. For 72×72 Twemoji PNGs (typically 3–8 KB) this is fine. But there is no test asserting that the base64 output round-trips correctly through `atob`, and no boundary test at exactly `chunk` and `chunk + 1` byte inputs.

**3. `AnnouncementNote` type-to-style routing is unverified**
`AnnouncementNote.jsx` renders six distinct visual styles keyed by `config.type`. `useAnnouncementNoteManager` emits `type: 'warning'` for allergen notes and `type: 'headsup'` for dietary guidelines — but there is no test confirming the correct type flows through to the correct icon colour. A one-character key mismatch (e.g. `criticalAllergen` vs `critical_allergen`) in `DIETARY_INFO` would silently swap a red allergy warning for a teal informational note.
## ISSUE:test 2026-06-24 09:32 → Zero test infrastructure; complex branching logic and worker utilities are entirely unverified

`frontend/package.json` lists no test framework (no Vitest, Jest, or Testing Library). There are no `.test.jsx`, `.spec.js`, or any other test files anywhere in the repository. As a result:

- `frontend/src/utils/announcementNote.js` contains three exported functions (`resolveNote`, `resolveDietaryAllergyNote`, `resolveDietaryInfoNote`) with branching allergen-filtering and guideline-joining logic. A one-character typo in the `criticalAllergen` property name would silently produce empty allergy warnings for users with serious dietary restrictions — with no tests to catch it.
- `og-worker/src/index.js` contains `wrapTitle` (multi-line text truncation), `emojiCodepoint` (Unicode codepoint extraction with variation-selector stripping), and `escapeXml` (attribute sanitisation). These are pure, stateless functions that run on every OG image request and have never been executed in a test harness.
- `frontend/src/pages/SharedRecipe.jsx` manages five distinct UI states (loading, error, recipe-only, recipe+author, recipe+full-profile) plus a countdown timer — all are exercised only via live network requests against the production API.
## ISSUE:test 2026-06-24 09:00 → Zero tests exist across frontend and og-worker — critical utility logic is unverified

Neither `frontend/package.json` nor `og-worker/package.json` lists a testing framework. No test files appear in the tree (`*.spec.ts`, `*.test.ts`, `*.test.jsx`, or similar). Every path through the application is entirely untested. The highest-priority gaps:

**1. `frontend/src/utils/announcementNote.js` — pure functions with zero tests**
`resolveNote`, `resolveDietaryAllergyNote`, and `resolveDietaryInfoNote` contain business logic that gates what safety warnings users see (allergen and dietary notices). These are pure functions with no dependencies — trivially unit-testable, but entirely uncovered. A silent regression here could suppress a critical allergen warning.

**2. `og-worker/src/index.js` — `wrapTitle` and `escapeXml` have no tests**
`wrapTitle` (26-char wrapping, 2-line max) and `escapeXml` (SVG injection guard) are also pure and testable in isolation. A boundary error in `wrapTitle` produces a malformed or visually broken OG image for any shared recipe link.

**3. No E2E or integration test for the SharedRecipe fetch pipeline**
The two-step fetch (recipe then author profile) and the resulting render of dietary/allergen notes, timer state, and author card is the core feature of this web app and has no integration coverage at all.
## ISSUE:test 2026-06-23 22:01 → Zero test infrastructure — no runner, no scripts, no test files anywhere in the repo

Neither `frontend/package.json` nor `og-worker/package.json` declares a `test` script or any testing dependency (no vitest, jest, testing-library, playwright, or miniflare). The git tree contains no `*.spec.*` or `*.test.*` files.

**Highest-risk untested paths:**

1. **`og-worker/src/index.js` — OG image rendering pipeline** is the only place `@resvg/resvg-wasm` is exercised. The emoji codepoint extraction, SVG template assembly, and WASM PNG render are entirely black-box. The silent emoji fallback bug described in BUG-ISSUE would have been caught by a single unit test for `emojiCodepoint('')`.

2. **`frontend/functions/recipe/[token].js` — HTML meta-tag injection** replaces six regex patterns against live `index.html` content. Any change to `index.html` meta structure can silently break OG/Twitter preview cards. There are no snapshot or fixture tests to catch this.

3. **`frontend/src/utils/announcementNote.js`** — dietary allergy and info notes are generated from tag combinations. Edge cases (empty tag array, unknown tag, multiple critical allergens) are only validated manually in production.

**Recommended next step:** add vitest to `frontend/` and a Miniflare-based test harness to `og-worker/`. Both packages already have no framework lock-in that would complicate adoption.
## ISSUE:test 2026-06-20 19:23 → Zero test coverage — three priority test targets identified

No test files exist anywhere in this repo. No Vitest, Jest, or any test runner is configured. `frontend/package.json` has no test script.

**Priority 1 — `wrapTitle` unit tests** `og-worker/src/index.js`
This function has a known bug (word dropped when second line fills exactly). Test cases:
- Single word shorter than `maxChars` → `['word']`
- Title fitting on one line → `['full title']`
- Title needing two lines normally → `['line one', 'line two']`
- Title where second line fills exactly 26 chars and next word triggers split → confirm that triggering word is NOT dropped (currently fails)
- Title with a single very long word exceeding `maxChars` → `['longword']`
- Empty string → `[]`

**Priority 2 — Pages Function regex pipeline** `frontend/functions/recipe/[token].js`
Create a fixture of the `index.html` content and run the 7 regex replacement operations against it. Assert:
- `<title>` is replaced correctly
- `og:title`, `og:description`, `og:image` properties are replaced
- `twitter:title`, `twitter:description`, `twitter:image` are replaced
- No duplicate meta tags appear after replacement
- HTML returned to crawler contains no residual default values when recipe data is present

**Priority 3 — `announcementNote.js` utility** `frontend/src/utils/announcementNote.js`
- `resolveNote(null)` → `null`
- `resolveNote('  ')` → `null` (whitespace trimmed)
- `resolveNote('text')` → `{ text: 'text', type: 'note' }`
- `resolveDietaryAllergyNote(['GlutenFree', 'NutFree'], DIETARY_INFO)` → warning with both allergens
- `resolveDietaryAllergyNote(['Vegan'], DIETARY_INFO)` → `null` (no criticalAllergen)
- `resolveDietaryInfoNote(['Keto', 'Halal'], DIETARY_INFO)` → headsup with both guidelines
## ISSUE:test 2026-06-15 20:02 → Zero test infrastructure — no unit tests, no E2E tests, no test runner configured

The repo has no test infrastructure:
- No test runner in frontend/package.json devDependencies (no Vitest, Jest, etc.)
- No test files anywhere in frontend/src/
- No vitest.config.js or jest.config.js
- No E2E test framework (Playwright, Cypress)
- No og-worker test suite

Critically untested paths:
1. announcementNote.js functions (currently empty/broken — a test would have caught this immediately)
2. SharedRecipe data-fetch and render with various recipe shapes (missing fields, long titles, >8 steps for longRecipeNote, no cookTime)
3. OG worker: wrapTitle character wrapping with long/short titles, emojiCodepoint variation-selector stripping, bufToBase64 chunking
4. Cloudflare Function meta tag injection and regex replacements for all 7 tags
5. useAnnouncementNoteManager hook edge cases (empty dietaryTags array, null recipe fields)
6. formatCookDuration and formatMemberSince date utilities with edge cases (0 months, exactly 12 months)

`frontend/package.json` has only `@vitejs/plugin-react` and `vite` as devDependencies — no Vitest, Jest, Testing Library, or Playwright. `og-worker/package.json` has only `@resvg/resvg-wasm` as a dependency and no test tooling. No test files exist anywhere in the codebase. Critical untested paths include: `wrapTitle` edge cases in the OG worker, `resolveNote` / `resolveDietaryAllergyNote` / `resolveDietaryInfoNote` in `announcementNote.js`, the Pages Function meta-tag regex replacements, and the `useAnnouncementNoteManager` hook logic.
