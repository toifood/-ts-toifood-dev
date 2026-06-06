Analyse the source codebase and update all 10 category docs in the target repo.

## Arguments
`$ARGUMENTS` is the target repo name, e.g. `ts-back`, `ts-front`, `ts-web`.

## Derived values
- Source repo: `jayreck996/ts-toifood-{suffix}` where suffix = strip `ts-` from `$ARGUMENTS` (e.g. `ts-back` → `back` → `ts-toifood-back`)
- Target path: `C:\Users\tnako\Documents\GitHub\$ARGUMENTS\`
- Categories: `migrate`, `price`, `recovery`, `usage`, `instruction`

## Steps

### 1. Download and extract source repo

Run in PowerShell:
```powershell
$zipPath = "$env:TEMP\toifood-source.zip"
$extractPath = "$env:TEMP\toifood-source"
$suffix = "$ARGUMENTS" -replace "^ts-", ""
Remove-Item $extractPath -Recurse -Force -ErrorAction SilentlyContinue
gh api "repos/jayreck996/ts-toifood-$suffix/zipball/latest" -o $zipPath
Expand-Archive -Path $zipPath -DestinationPath $extractPath -Force
$root = (Get-ChildItem $extractPath | Select-Object -First 1).FullName
Write-Output $root
```

Note the `$root` path printed — all source file reads use this as the base.

### 2. Read codebase context

Read these files from `$root`:
- `README.md`
- `package.json`
- `prisma/schema.prisma` (skip if not present)
- List contents of `src/` directory tree (Glob `src/**/*` from `$root`)

Hold this codebase context in mind for all 10 analyses.

### 3. For each of the 5 categories × issue/asset (10 total)

For each category in `migrate`, `price`, `recovery`, `usage`, `instruction`:

#### 3a. ISSUE analysis
1. Read `$root/-MUST/{category}-ISSUE.md` — this is your analysis instruction/prompt
2. Using that instruction and the codebase context from step 2, generate a concise analysis
3. Format the entry:
   ```
   ## ISSUE:{category} {YYYY-MM-DD HH:MM} → {one-line summary}
   
   {analysis content}
   ```
4. Prepend the entry into `C:\Users\tnako\Documents\GitHub\$ARGUMENTS\{category}-ISSUE.md` directly below the `####### <!-- ANCHOR MARKER` line — never edit existing entries below it

#### 3b. ASSET analysis
1. Read `$root/-MUST/{category}-ASSET.md` — this is your analysis instruction/prompt
2. Using that instruction and the codebase context from step 2, generate a concise analysis
3. Format the entry:
   ```
   ## ASSET:{category} {YYYY-MM-DD HH:MM} → {one-line summary}
   
   {analysis content}
   ```
4. Prepend the entry into `C:\Users\tnako\Documents\GitHub\$ARGUMENTS\{category}-ASSET.md` directly below the `####### <!-- ANCHOR MARKER` line — never edit existing entries below it

### 4. Commit and push

Run in PowerShell from `C:\Users\tnako\Documents\GitHub\$ARGUMENTS`:
```powershell
git add migrate-ISSUE.md migrate-ASSET.md price-ISSUE.md price-ASSET.md recovery-ISSUE.md recovery-ASSET.md usage-ISSUE.md usage-ASSET.md instruction-ISSUE.md instruction-ASSET.md
git commit -m "would-update: $((Get-Date -Format 'yyyy-MM-dd HH:mm')) codebase analysis"
git push
```

### 5. Clean up
```powershell
Remove-Item "$env:TEMP\toifood-source.zip", "$env:TEMP\toifood-source" -Recurse -Force -ErrorAction SilentlyContinue
```
