# Review Sandbox Handoff

Review creative work from Gemini CLI. Reads `sandbox/HANDOFF.md`, spins up the project locally, collects visual feedback, and writes structured feedback back for Gemini.

## Phase 1: Read Handoffs

1. Read `sandbox/HANDOFF.md`
2. Parse entries under `## Active Handoffs`
3. Filter for entries with `Status: Ready for Review`

**If no entries:** Report "No pending handoffs from Gemini." and stop.

**If multiple entries:** List them and ask: "Which project do you want to review?"

**If one entry:** Proceed automatically.

Extract from the entry:
- **Project name**
- **Location** (sandbox path)
- **Description** (what was built)

---

## Phase 2: Spin Up Locally

1. Verify the sandbox project exists at the specified path
2. Check for `package.json` — if missing, report and stop
3. Run:
```bash
cd sandbox/{project-path} && npm install && npm run dev
```
4. Give the user the localhost URLs. Projects may have dual versions:
   - **Strict** (Compliance Auditor): `http://localhost:3000/strict`
   - **Wild** (Brutalist Architect): `http://localhost:3000/wild`
   - If only a single page exists: `http://localhost:3000`
5. Tell the user:

> **Project running at http://localhost:3000**
> - Strict version: http://localhost:3000/strict
> - Wild version: http://localhost:3000/wild
>
> Compare both side-by-side with the live site. Tell me which version works, what to pull from each, and what needs fixing.

**Wait for user feedback before proceeding.**

---

## Phase 3: Automated Design Checks

While the user reviews visually, scan the sandbox project's TSX/JSX files:

### 3.1 Border Radius (Zero Tolerance)
```bash
grep -rn "rounded-" sandbox/{project-path}/src --include="*.tsx" --include="*.jsx" | grep -v "rounded-none"
```
Flag ANY rounded corners that aren't `rounded-none`.

### 3.2 Color Palette
Allowed colors: `ink` (#272048), `paper` (#f4f4f0), `primary` (#0808BA), `engineering` (#DC2626).

Flag:
- Hardcoded hex colors not in the palette
- `text-primary` on `bg-ink` (invisible — contrast ~1.3:1)
- Stock Tailwind colors (`text-blue-500`, `bg-gray-200`, etc.) unless aliased

### 3.3 Shadows
```bash
grep -rn "shadow" sandbox/{project-path}/src --include="*.tsx" --include="*.jsx" | grep -v "shadow-\[.*0px_0px_rgba"
```
Flag blur shadows, soft shadows, or `shadow-sm/md/lg/xl` (stock Tailwind). Only hard shadows allowed: `shadow-[4px_4px_0px_0px_rgba(39,32,72,1)]` or `8px` variant.

### 3.4 Typography
- `font-serif` → headings, display text
- `font-sans` → body, descriptions
- `font-mono` → labels, badges, metrics, code

Flag misuse (e.g., `font-serif` on small labels, `font-mono` on body paragraphs).

### 3.5 Transitions
Flag spring, bounce, or long duration animations. Only `duration-150 ease-linear` or `ease-out` allowed.

### 3.6 JSX Build Safety (BLOCKER)
Before giving the user a localhost URL, run `npm run build` in the sandbox project. If it fails, fix the issue before proceeding.

Common failures:
- Unescaped `>` in JSX text → use `{'>'}`or `&gt;`
- Unescaped quotes in JSX text → use `&ldquo;`/`&rdquo;`
- Missing `"use client"` on components with hooks/event handlers

Reference: `.claude/docs/Gotchas.md` → "JSX Entity Escaping Errors"

---

## Phase 4: Combine Feedback

After receiving user feedback, compile:

### Report Format

```markdown
## Handoff Review: {Project Name}

**Location:** `sandbox/{path}`
**Reviewed:** {date}

### User Feedback
{What the user said — paraphrased into actionable items}

### Automated Checks
| Check | Status | Details |
|-------|--------|---------|
| Border Radius | PASS/FAIL | {specifics} |
| Color Palette | PASS/FAIL | {specifics} |
| Shadows | PASS/FAIL | {specifics} |
| Typography | PASS/FAIL | {specifics} |
| Transitions | PASS/FAIL | {specifics} |

### Action Items
- [ ] {Specific fix 1}
- [ ] {Specific fix 2}
```

Present the report to the user. Ask: **"Should I send this feedback to Gemini, or is it ready to integrate?"**

---

## Phase 5: Update HANDOFF.md

### If Needs Fixes
Update the entry in `sandbox/HANDOFF.md`:
- Change `Status: Ready for Review` → `Status: Needs Fixes`
- Add `### Feedback (from Claude)` section with the action items
- Add `**Updated**: {date}`

Tell the user: "Feedback written to HANDOFF.md. Next time Gemini starts, it'll pick this up."

### If Approved
Ask: **"Integrate now, or keep in sandbox for later?"**

- **Integrate now:** Begin extraction via `/adapt` workflow. Move entry to `## Completed Handoffs` with `Status: Integrated` and note where it shipped.
- **Keep in sandbox:** Update status to `Status: Approved`. Entry stays in Active Handoffs.

---

## Important Rules

- ALWAYS spin up the project before reviewing code — the user needs to see it visually
- NEVER skip user feedback — automated checks supplement, not replace, visual review
- ALWAYS stop the dev server (`Ctrl+C` or kill process) after review is complete
- If `npm install` or `npm run dev` fails, report the error and suggest fixes before continuing
- Reference `.claude/docs/BrandGuidelines.md` and `GEMINI.md` Page Design Protocol for design system rules
