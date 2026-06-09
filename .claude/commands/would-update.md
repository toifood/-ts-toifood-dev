Analyse the source codebase and write 14 issue/asset entries to the target repo's could/ directory.

## Arguments
`$ARGUMENTS` is the target repo name, e.g. `ts-back`.

## Derived values
- Source repo: `toifood-dev/ts-toifood-{suffix}` where suffix = strip `ts-` from `$ARGUMENTS`
- Target: `$GITHUB_WORKSPACE/could/`
- Categories: `migrate`, `price`, `recovery`, `usage`, `instruction`, `bug`, `analysis`

## Steps

### 0. Compute quarter and timestamp

Run in bash:
```bash
QUARTER=$(node -e "
  const o = process.env.QUARTER_OVERRIDE;
  if (o) { console.log(o); process.exit(0); }
  const m = new Date().getMonth() + 1;
  console.log(new Date().getFullYear() + 'Q' + Math.ceil(m / 3));
")
TS=$(TZ=Pacific/Auckland date '+%Y-%m-%d %H:%M')
echo "Quarter: $QUARTER | Timestamp: $TS"
```

### 1. Download and extract source repo

Run in bash:
```bash
suffix="${ARGUMENTS#ts-}"
zipPath="/tmp/toifood-source.zip"
extractPath="/tmp/toifood-source"
rm -rf "$extractPath"

latestBranch=""
latestDate=""
for branch in $(gh api "repos/toifood-dev/ts-toifood-${suffix}/branches" --jq '.[].name'); do
  [[ "$branch" == "main" ]] && continue
  created=$(gh api "repos/toifood-dev/ts-toifood-${suffix}/compare/main...${branch}" \
    --jq '.commits[-1].commit.committer.date' 2>/dev/null)
  if [[ "$created" > "$latestDate" ]]; then
    latestDate="$created"
    latestBranch="$branch"
  fi
done
echo "Branch: $latestBranch"

gh api "repos/toifood-dev/ts-toifood-${suffix}/zipball/${latestBranch}" > "$zipPath"
unzip -q "$zipPath" -d "$extractPath"
root=$(find "$extractPath" -mindepth 1 -maxdepth 1 -type d | head -1)
echo "Root: $root"
```

### 2. Read codebase

Read from `$root`:
- `README.md`
- `package.json`
- `prisma/schema.prisma` (skip if absent)
- Full content of all files under `src/` — routes, middleware, services, entry points

Also read `-MUST/{category}-ISSUE.md` and `-MUST/{category}-ASSET.md` for: `migrate`, `price`, `recovery`, `usage`, `instruction`.

Hold all codebase content and -MUST/ instructions in context for all 14 analyses.

### 3. Generate analyses and write to could/

For each category in `migrate`, `price`, `recovery`, `usage`, `instruction`, `bug`, `analysis` — generate both ISSUE and ASSET entries.

**Embedded prompts for `bug` and `analysis`:**
- **bug ISSUE**: Undiscovered bugs — edge cases, race conditions, null dereferences, async pitfalls, production risks.
- **bug ASSET**: Existing bug-prevention assets — error handling, validation, test coverage, logging, gaps.
- **analysis ISSUE**: Code quality — technical debt, architectural concerns, missing patterns, scalability risks.
- **analysis ASSET**: Codebase health — what is well-built, the tech stack, production-ready vs in progress.

For `migrate`, `price`, `recovery`, `usage`, `instruction`: use the matching `-MUST/` instruction read in step 2.

For each category/type:
1. Generate a concise analysis grounded in the actual source code
2. Format the entry:
   ```
   ## ISSUE:{category} {TS} → {one-line summary}

   {analysis content}
   ```
   (use `ASSET:` prefix for asset type)
3. Write to `$GITHUB_WORKSPACE/could/{CATEGORY}-{TYPE}-${QUARTER}.md`:
   - File does not exist → create with the header below, then insert entry after anchor
   - File exists → find `####### <!-- ANCHOR MARKER` line, insert entry directly below it

**ISSUE header:**
```
ISSUE LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ISSUE ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES.

REQUIRED FORMAT FOR EACH ISSUE ENTRY:

## ISSUE:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} -> {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ISSUE ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ISSUE ENTRIES-->
```

**ASSET header:**
```
ASSET LOG
INSTRUCTION FOR AI MODEL:

ALWAYS ADD NEW ASSET ENTRIES AT THE TOP, DIRECTLY BELOW THIS HEADER.

NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES.

REQUIRED FORMAT FOR EACH ASSET ENTRY:

## ASSET:{NAME OF ENVIRONMENT} {YYYY-MM-DD HH:MM} -> {CONTENT}

####### <!-- ANCHOR MARKER - ADD ALL NEW ASSET ENTRIES DIRECTLY BELOW THIS LINE, NEVER DELETE OR EDIT PREVIOUS ASSET ENTRIES-->
```

### 4. Clean up
```bash
rm -rf /tmp/toifood-source.zip /tmp/toifood-source
```
