# End Session

The user is ending this dev session. Before closing:

## 1. Cleanup Experiments

Check for and remove abandoned experiment folders that break builds:

```bash
# Check for versioned experiment folders
ls -d V[0-9]* 2>/dev/null
ls -d app/*-v[0-9]* 2>/dev/null
```

If found, ask user:
> "I found experiment folders: [list]. Remove them to keep the repo clean? (y/n)"

If yes, remove them:
```bash
rm -rf V[0-9]* app/*-v[0-9]*
```

## 1.5 Sandbox Status Check

Check for sandbox concepts that may need attention:

```bash
# Check for concepts without comparison reports
for dir in sandbox/????-??-??-*/; do
  if [ -d "$dir" ] && [ ! -f "$dir/comparison-report.md" ]; then
    echo "Pending comparison: $dir"
  fi
done
```

If pending concepts found, ask user:
> "Found sandbox concepts without comparison reports:
> - [list]
>
> Options:
> 1. Run `/sandbox-compare` now
> 2. Leave for next session
> 3. Archive as incomplete"

This ensures design explorations don&apos;t get forgotten.

## 2. Write Session Summary

The session log is split by year:
- **SessionLog.md** — Index with one-liner summaries (quick reference)
- **SessionLog-Entries-2026.md** — 2026 sessions (current)
- **SessionLog-Entries-2025.md** — 2025 sessions (archive)

### Step 2a: Select Session Tags

Ask the user to categorize the session with tags:

**Primary tag** (select one):
- 🔧 **Dev** — Development, features, infrastructure, bug fixes
- 📝 **Content** — Research, writing, editorial, documentation
- 🎨 **Design** — Visual design, prototyping, sandbox work
- 📊 **Strategy** — Business planning, positioning, market research

**Secondary tags** (optional, 2-4 keywords):
Examples: `prismic`, `seo`, `interactive`, `signal`, `slices`, `demo`, `audit`, `migration`, `deployment`

**Hybrid sessions** can have multiple primary tags (e.g., 🔧📝 for dev + content).

### Step 2b: Add Full Entry to SessionLog-Entries-2026.md

Add a new entry at the **top** of `.claude/docs/SessionLog-Entries-2026.md` (after the header).

```markdown
## YYYY-MM-DD | Brief Title 🔧 `dev`, `tag2`, `tag3`

**Session:** `kebab-case-session-name`
**Branch:** `branch-name` @ `commit-hash`
**Context:** One sentence explaining WHY we did this work (business/strategic reason)

### What We Built
- Bullet points of features/changes

### Files Changed
- List of key files (group by category if many)

### Prismic Content (if any)
- Documents or slices created/modified

### Related Sessions (if continuing prior work)
- `previous-session-name` — what we continued from

---
```

### Step 2c: Add Summary Row to SessionLog.md

Add a new row to the **Session Index** table in `.claude/docs/SessionLog.md` under the current month.

```markdown
| MM-DD | [Session Title](SessionLog-Entries-2026.md#yyyy-mm-dd--session-title-kebab) | One-line summary |
```

**Anchor format:** `#yyyy-mm-dd--title-in-kebab-case` (double dash after date, spaces become single dashes)

### Session Naming Convention
- Use kebab-case (e.g., `content-audit-slack`, `footer-blog-migration`)
- Keep it short but descriptive (2-4 words)
- User can resume with: "Resume session `session-name`"

### Context Examples
Good context lines:
- "Healthcare vertical launch requires dedicated landing page with clinical examples"
- "SEO audit revealed orphan pages hurting discoverability"
- "User requested AI crawler support for better LLM visibility"

Bad context lines (too vague):
- "User asked for this"
- "Continuing previous work"
- "Bug fix"

## 3. Graduate Learnings

Review the session and ask the user if anything should be graduated to permanent docs:

> "Before we close, should any learnings from this session be saved permanently?"
>
> - **Gotchas.md** — Did something break? How did we fix it?
> - **Decisions.md** — Did we make a significant architecture choice?
> - **OpenQuestions.md** — Are there deferred questions or ideas to revisit?
> - **Patterns.md** — Did we discover a reusable code pattern?

If yes, add entries to the relevant docs with proper formatting (see each doc for template).

If the user declines or nothing applies, proceed to next step.

## 4. Verify Build Still Passes

Run a quick build check before committing:
```bash
npm run build 2>&1 | tail -5
```

If build fails, fix the issue before ending session.

## 5. Commit All Changes

```bash
# Stage session logs and any graduated docs
git add .claude/docs/SessionLog.md .claude/docs/SessionLog-Entries-2026.md .claude/docs/Gotchas.md .claude/docs/Decisions.md .claude/docs/OpenQuestions.md .claude/docs/Patterns.md

git commit -m "Add session log: [session-name]

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>"
git push origin main
```

## 6. Say Goodbye

End with a one-line summary:
> Session `session-name` logged. [One sentence of what shipped].

---

## Session Log Analytics (for reference)

The session logs can be analyzed for patterns:

```bash
# Count sessions by month (from index)
grep -E "^\| [0-9]" .claude/docs/SessionLog.md | wc -l

# Find SEO-related sessions (search full entries)
grep -B2 -i "sitemap\|structured data\|schema" .claude/docs/SessionLog-Entries-2026.md | grep "Session:"

# Find sessions related to a feature
grep -B5 "clarity" .claude/docs/SessionLog-Entries-2026.md | grep "Session:"

# Filter by tag (all dev sessions)
grep "🔧" .claude/docs/SessionLog-Entries-2026.md | grep "^##"

# Filter by keyword (all prismic sessions)
grep "prismic" .claude/docs/SessionLog-Entries-2026.md | grep "^##"

# Quick scan of recent sessions (from index)
head -50 .claude/docs/SessionLog.md | grep -E "^\|"
```
