# Review Local Development

Run through a comprehensive local QA checklist before creating a PR or deploying.

## Input Required
Ask the user: **"What page are you reviewing? Provide the local URL (e.g., http://localhost:3000/workshop/criteria-guide)"**

---

## Phase 1: Automated Checks (Claude Can Verify)

### 1.1 Build Check (BLOCKER)
```bash
npm run build
```
- If build fails → Stop. Fix errors before proceeding.
- Check for TypeScript errors, missing imports, SSR issues.

### 1.2 Hydration Error Scan
Search modified files for patterns that cause hydration mismatches:
- `Math.random()` in render
- `Date.now()` without proper handling
- `typeof window !== 'undefined'` branches that render different content
- Dynamic values that differ between server/client

**Report format:**
```
### Hydration Risk Assessment
- [FILE:LINE] Math.random() used in render → Use deterministic values
- [FILE:LINE] Date formatting may differ → Use consistent locale
```

### 1.3 Design System Compliance
Scan modified TSX files for:

**Color violations:**
- Hardcoded hex colors (should use theme: `ink`, `paper`, `primary`, `accent`)
- `text-primary` or `text-ink` on `bg-ink` (contrast issue)
- `text-paper` on `bg-paper` (invisible text)

**Typography:**
- `font-serif` → headings only
- `font-mono` → labels, metrics, code
- `font-sans` → body text

### 1.4 SEO & Metadata Check
For the page being reviewed, verify:
- [ ] `metadata` export exists with `title` and `description`
- [ ] OpenGraph image configured
- [ ] Canonical URL set if needed
- [ ] Structured data (JSON-LD) present if applicable

### 1.5 Global Component Consistency
Check if page correctly shows/hides global components:
- Does the footer render? (Check for `display: none` CSS overrides)
- Does the navigation render?
- Are there z-index conflicts with fixed/absolute elements?

---

## Phase 2: Browser Checks

### 2.0 Automated via Chrome DevTools MCP (preferred)

If Chrome DevTools MCP is available, automate Phase 2 checks before asking the user:

1. **Navigate** to the localhost URL provided by the user
2. **Console check (BLOCKER):** Read console output — flag any errors, hydration warnings, or React warnings
3. **Network check (BLOCKER):** Inspect network for failed requests (404s, CORS errors, Prismic API failures)
4. **DOM inspection:** Verify key content sections render (not null/empty), slices are populated
5. **Performance trace:** Run `performance_start_trace`, measure LCP/CLS, identify slow resources

Report automated findings, then present the remaining manual checklist below.

### 2.1 Rendering (BLOCKER)
- [ ] Page loads without blank screen
- [ ] No console errors in browser dev tools
- [ ] No console warnings (especially hydration warnings)
- [ ] Network tab shows no 404s or failed requests

### 2.2 Responsive Design
Ask user to check at these breakpoints:
- [ ] **Mobile (375px)** - No horizontal scroll, text readable, tap targets adequate
- [ ] **Tablet (768px)** - Layout adjusts appropriately
- [ ] **Desktop (1280px+)** - Full layout renders correctly

### 2.3 Functionality
- [ ] All links work and point to correct destinations
- [ ] Interactive elements function (buttons, accordions, forms, toggles)
- [ ] Hover states work as expected
- [ ] Animations/transitions are smooth

### 2.4 Content & Images
- [ ] Prismic content connected and rendering
- [ ] Dynamic content handles empty/missing fields (no crashes, graceful fallbacks)
- [ ] Images load correctly (no broken images)
- [ ] Images are optimized (WebP format, appropriate sizes)
- [ ] Images have meaningful alt text

### 2.5 Prismic Slice Styling (for migrated pages)
If page was migrated from hardcoded to Prismic:
- [ ] **Typography matches original** (compare side-by-side)
- [ ] **Headings render as headings** (not body text)
- [ ] **Lists have bullets** (not plain text)
- [ ] **Spacing between sections correct**
- [ ] **No `prose` class issues** (if using prose, verify it renders)

**Quick check:** If headings look like body text or lists have no bullets, the `prose` classes failed. Use explicit Tailwind instead.

**Reference:** `.claude/docs/Gotchas.md` → "Prismic Migration Issues"

### 2.5 Cross-Browser (if critical page)
- [ ] Chrome
- [ ] Safari
- [ ] Firefox (optional)

---

## Phase 3: Live Preview Check

If Chrome DevTools MCP is available, use it instead of WebFetch for localhost verification — it handles client-side rendered components that WebFetch misses.

If MCP is not available, fall back to WebFetch:
- Page returns 200
- Key content sections are present
- Footer/header render correctly
- No obvious missing content

---

## Severity Levels

| Level | Examples | Action |
|-------|----------|--------|
| **BLOCKER** | Build fails, hydration errors, blank page, console errors | Must fix before PR |
| **SHOULD FIX** | Missing alt text, console warnings, minor contrast issues | Fix before deploy to production |
| **NICE TO HAVE** | Lighthouse optimizations, minor polish | Can address in follow-up |

---

## Output Format

```markdown
## Local QA Review: [Page Name]

### Automated Checks
| Check | Status | Notes |
|-------|--------|-------|
| Build | ✅ PASS | No errors |
| Hydration | ⚠️ WARNING | 1 potential issue in FlipLogic.tsx |
| Design System | ✅ PASS | Colors compliant |
| SEO | ✅ PASS | Metadata present |
| Global Components | ✅ PASS | Footer renders |

### Issues Found
| Severity | Issue | File/Location | Suggested Fix |
|----------|-------|---------------|---------------|
| BLOCKER | Math.random() causes hydration | FlipLogic.tsx:45 | Use deterministic array |

### Manual Checklist Status
- [ ] Rendering confirmed
- [ ] Responsive checked
- [ ] Functionality verified
- [ ] Content/images reviewed

### Recommendation
[READY FOR PR / NEEDS FIXES / BLOCKED]
```

---

## Important Rules

- ALWAYS run `npm run build` first - dev mode hides many issues
- NEVER skip hydration check - these errors break pages silently
- ALWAYS verify footer/navigation render correctly (we've had issues with CSS hiding them)
- If ANY blocker exists, do not proceed to PR
