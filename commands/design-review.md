# Design Review

Run a comprehensive design review on the codebase to catch contrast, readability, and design consistency issues.

## Scope
Review files based on argument provided:
- If a file path is given → review that specific file
- If a directory is given → review all TSX/JSX files in that directory
- If no argument → review all modified files (git diff) or scan `app/` directory

## Checks to Perform

### 1. Contrast & Readability (HIGH PRIORITY)

**Anti-patterns to flag:**

On dark backgrounds (`bg-ink`, `bg-primary`, `className="...bg-ink..."`, inline dark styles):
| Pattern | Issue | Fix |
|---------|-------|-----|
| `text-primary` | Blue (#0808BA) on dark (#272048) = ~1.3:1 contrast (WCAG requires 4.5:1) | `text-highlight`, `text-paper`, or `text-paper/80` |
| `text-ink` | Dark on dark = invisible | `text-paper` |
| `text-blue-100/200` | Too dark blue | `text-blue-300` or lighter |
| `border-primary` | Low visibility | `border-paper/30` or `border-highlight` |

**CRITICAL: text-primary on bg-ink is effectively invisible!**
- `text-primary` (#0808BA dark blue) on `bg-ink` (#272048 dark purple) = ~1.3:1 contrast ratio
- WCAG AA requires 4.5:1 for normal text, 3:1 for large text
- This pattern appears safe but fails badly — both colors are dark

**Correct patterns:**
- Dark bg: `text-paper`, `text-paper/80`, `text-paper/60`, `text-highlight`, `text-green-400`, `text-white`
- Light bg: `text-ink`, `text-ink/80`, `text-ink/60`, `text-primary`
- Accent on dark: `text-highlight` (#E0E7FF light blue) — preferred for maintaining brand feel

**Components to especially check:**
- Modals with dark backgrounds
- Tooltips
- Tour overlays
- Any component with `bg-ink`
- Footer sections (commonly use `bg-ink text-paper`)
- Sidebars
- Code blocks with dark backgrounds
- Call-to-action sections with dark backgrounds
- Interactive demos with dark overlays

**Quick grep to find SAME-LINE issues:**
```bash
grep -rn "bg-ink.*text-primary\|text-primary.*bg-ink" --include="*.tsx"
```

**⚠️ CRITICAL: Parent-Child Pattern Detection**

The above grep MISSES the most common bug: `bg-ink` on parent, `text-primary` on child (different lines).

**Step 1: Find all files with BOTH patterns:**
```bash
grep -l "bg-ink" slices/*/index.tsx | xargs -I {} sh -c 'grep -l "text-primary" {} 2>/dev/null'
```

**Step 2: For each flagged file, manually verify:**
1. Find sections with `bg-ink` or conditional dark backgrounds
2. Check ALL child elements for `text-primary`, `text-ink`, `border-primary`
3. Look for conditional patterns like: `isDark ? "text-primary" : "text-ink"` — the dark case is wrong!

**Step 3: Check for conditional dark backgrounds:**
```bash
grep -rn "is_.*\?.*bg-ink\|is_.*\?.*text-primary" --include="*.tsx"
```

**Real example of sneaky bug (StackPosition slice):**
```tsx
// Line 113 - Parent has conditional dark bg
className={layer.is_rails ? "bg-ink text-paper" : "bg-paper"}

// Line 121 - Child uses text-primary when parent is dark!
className={layer.is_rails ? "text-primary" : "text-ink/20"}  // ❌ INVISIBLE

// Fix:
className={layer.is_rails ? "text-highlight" : "text-ink/20"}  // ✅ Visible
```

### 2. Color Consistency

**Allowed theme colors:**
- `ink` (#272048) - dark text, backgrounds
- `paper` (#f4f4f0) - light backgrounds, light text on dark
- `primary` (#0808BA) - brand blue, CTAs on light backgrounds
- `engineering` (#DC2626) - alerts, errors

**Flag these:**
- Hardcoded hex colors (e.g., `#ffffff` instead of `paper`)
- Non-standard color names not in Tailwind
- Inconsistent opacity usage

### 3. Typography Hierarchy

**Check for consistency:**
- `font-serif` → h1, h2, h3, display text, card titles
- `font-mono` → labels, badges, metrics, code, technical text
- `font-sans` → body paragraphs, descriptions

**Flag:**
- `font-serif` used for small labels
- `font-mono` used for body paragraphs
- Missing font-family on text elements

### 3.1 Prose Class Detection (IMPORTANT)

**Flag usage of `prose` classes - they are unreliable:**
```bash
grep -rn "prose prose-lg\|prose-headings\|prose-p:" --include="*.tsx"
```

**Issue:** Tailwind typography `prose` classes sometimes fail to render headings/lists properly.

**Recommendation:** Use explicit Tailwind classes instead:
```tsx
// ❌ Unreliable
<div className="prose prose-lg">

// ✅ Reliable
<h2 className="font-serif text-2xl tracking-tight mb-4">
<ul className="list-disc list-inside space-y-1 ml-4">
```

**Reference:** `.claude/docs/Gotchas.md` → "Prose Classes Don't Render Properly"

### 4. Spacing & Alignment

**Check:**
- Consistent use of spacing scale (p-4, p-6, p-8, not p-7)
- Grid alignment (12-column grid usage)
- Margin/padding consistency within components

### 5. Interactive States

**Verify:**
- Hover states defined for buttons/links
- Focus states for accessibility
- Disabled states have proper styling

### 6. CTA Link Audit

**Check for dead CTAs (buttons without navigation):**

```bash
# Find buttons in slices that might be dead CTAs
grep -rn "<button" slices/ --include="*.tsx" | grep -v "onClick"
```

**Common issues:**
| Pattern | Issue | Fix |
|---------|-------|-----|
| `<button>` without href/onClick | Dead CTA - does nothing | Use `PrismicNextLink` |
| `href="demo"` (relative) | Creates wrong URL like `/align/demo` | Use absolute: `/align-demo` |
| `href="cogniswitch.ai/..."` | Missing protocol → 404 | Use `/path` or `https://...` |

**Verify Prismic link fields exist in model.json:**
```bash
# Check if slice has CTA text but no link field
grep -l "cta_text\|button_text" slices/*/model.json | xargs -I {} sh -c 'grep -L "cta_link\|button_link" {}'
```

**Reference:** See `/cta-audit` command for full audit, Gotchas.md for common issues

## Output Format

```markdown
## Design Review Report

**Scope:** [files reviewed]
**Date:** [timestamp]

### Critical Issues (Must Fix)
| File | Line | Issue | Suggested Fix |
|------|------|-------|---------------|
| ... | ... | ... | ... |

### Warnings (Should Fix)
| File | Line | Issue | Suggested Fix |
|------|------|-------|---------------|
| ... | ... | ... | ... |

### Passed Checks
- [x] Contrast on dark backgrounds
- [x] Color consistency
- [x] Typography hierarchy
- [x] Spacing alignment

### Summary
X critical issues, Y warnings found in Z files.
```

## Swipe File Comparison

After the automated checks above, read `.claude/docs/SwipeIndex.md`. If it has entries applicable to the page being reviewed (check "Applicable To" column), add a "Swipe Patterns" section to the report showing relevant collected patterns that could inform improvements. If SwipeIndex is empty or nothing matches, silently skip this section.

## After Review

If issues found:
1. Present the report
2. Ask: "Would you like me to fix these issues?"
3. If yes, make the corrections and show diff

If no issues:
1. Confirm all checks passed
2. Ready for deployment
