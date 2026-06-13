Analyse the source codebase and write issue/asset entries to the target repo's could/ directory.

## Arguments
`$ARGUMENTS` is the target repo name, e.g. `ts-back`.

## Derived values
- Source repo: `toifood-dev/ts-toifood-{suffix}` where suffix = strip `ts-` from `$ARGUMENTS`
- Target: `$GITHUB_WORKSPACE/could/`
- Categories: `migrate`, `price`, `recovery`, `usage`, `instruction`, `bug`, `analysis`, `test`

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

Hold all codebase content in context for all analyses.

### 3. Generate analyses and write to could/

For each category in `migrate`, `price`, `recovery`, `usage`, `instruction`, `bug`, `analysis`, `test` — generate both ISSUE and ASSET entries.

For each category/type:

1. Read the corresponding `could/{CATEGORY}-{TYPE}-${QUARTER}.md` file from `$GITHUB_WORKSPACE/could/`. Extract the header section (everything above the `####### <!-- ANCHOR MARKER` line):
   - **CUSTOM PROMPT** — use as the analysis focus for this entry. If empty, infer from the category name.
   - **PATHS** — if present, prioritise reading those specific paths from the source repo before the general `src/` scan. If empty, use the full `src/` scan from step 2.

2. Generate a concise analysis grounded in the actual source code, shaped by the CUSTOM PROMPT.

3. Format the entry:
   ```
   ## ISSUE:{category} {TS} → {one-line summary}

   {analysis content}
   ```
   (use `ASSET:` prefix for asset type)

4. Write to the file:
   - Find the `####### <!-- ANCHOR MARKER` line
   - Insert the new entry directly below it
   - Never delete or edit entries below the marker

### 4. Clean up
```bash
rm -rf /tmp/toifood-source.zip /tmp/toifood-source
```
