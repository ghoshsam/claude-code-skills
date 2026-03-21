# Swipe Audit — Pattern-Match Pages Against Collected Swipes

Compare a CogniSwitch page against collected swipe file patterns to find improvement opportunities.

## Usage

`/swipe-audit [page path or URL path]`

**Examples:**
- `/swipe-audit /clarity`
- `/swipe-audit homepage`
- `/swipe-audit app/blog`

---

## Flow

### Step 1: Load Relevant Swipes

Read `.claude/docs/SwipeIndex.md`.

Find entries that are:
- Tagged with `Applicable to:` matching the target page
- Broadly applicable (`Applicable to: General`)
- In categories relevant to the page (e.g., Copy/Voice for text-heavy pages, Design Patterns for layout-focused pages)

If SwipeIndex is empty or no entries match: "No swipe entries found for this page. Capture some patterns with `/swipe` first." — then stop.

### Step 2: Read Page Source

Determine the page source files:
- If argument is a URL path (e.g., `/clarity`), find the corresponding `app/` directory
- If argument is a page name (e.g., `homepage`), map to `app/page.tsx` or the appropriate file
- Read the page source (TSX files, slice components referenced)

Identify the page sections: hero, features, CTAs, testimonials, footer, etc.

### Step 3: Fetch Live Page (Optional)

If the site is deployed, use WebFetch on `https://cogniswitch.ai[path]` to see the rendered result.

If fetch fails (page not deployed yet), note this and continue with source-only analysis.

### Step 4: Cross-Reference

For each page section, check if any collected swipe entries suggest an improvement.

**Rule: Only suggest changes backed by a swipe entry.** This is pattern-matching, not free design advice. Every suggestion must reference a specific swipe.

### Step 5: Generate Report

```markdown
## Swipe Audit: [Page Name]

**Date:** [today]
**Swipes checked:** [X entries]
**Page sections identified:** [list]

### Opportunities

| Section | Current Pattern | Swipe Reference | Suggested Change | Impact |
|---------|----------------|-----------------|------------------|--------|
| Hero | Static headline | #12 "Linear's animated changelog" | Add subtle motion to key metric | Medium |
| CTA | Single button | #7 "Stripe's dual CTA" | Add secondary action link | High |
| ... | ... | ... | ... | ... |

### No Match (Sections Without Relevant Swipes)

These sections had no matching swipe patterns:
- [Section name] — consider collecting swipes in [category] to benchmark

### Summary

[X] opportunities found across [Y] sections, referencing [Z] swipe entries.

**Top priority:** [highest impact suggestion]
```

---

## Key Behaviors

- **Swipe-backed only** — never suggest changes that don't reference a collected swipe entry
- **Impact rating** — High (affects conversion/engagement), Medium (improves experience), Low (polish)
- **Graceful empty state** — if no swipes exist, say so and stop (don't generate generic advice)
- **Section-aware** — break the page into logical sections before cross-referencing
- **Source + live** — check both code and rendered page when possible
