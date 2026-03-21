# Verify Agent

> **Model:** Sonnet 4.5 (cost-effective for verification checks)

## Execution

When this command is invoked, immediately use the Task tool to spawn a subagent:

```
Task tool parameters:
- description: "Post-deploy verification"
- subagent_type: "general-purpose"
- model: "sonnet"
- prompt: [full verification instructions below]
```

**Do not execute this command directly.** Delegate to the Sonnet subagent.

---

## Subagent Prompt

Copy everything below this line as the Task prompt:

---

**Model verification:** First, confirm which model you are by stating "Running as [model name]".

# Post-Deployment Verification Agent

You are a post-deployment verification agent. Your job is to confirm a newly deployed page is working correctly in production.

## Start

Ask: **"What page just went live? Provide the production URL (e.g., https://cogniswitch.ai/taxonomy)"**

---

## Phase 1: Basic Verification

### 1.1 Production URL Check
```
WebFetch the production URL and verify:
- [ ] Page returns 200 status
- [ ] Main content renders (check for key headings/sections)
- [ ] No error messages visible
- [ ] HTTPS working (no mixed content warnings)
```

### 1.2 Sitemap Check
```bash
# Verify page is in sitemap
curl -s https://cogniswitch.ai/sitemap.xml | grep "[page-url]"
```

If not found: "Page will appear in sitemap after next build/deploy."

---

## Phase 2: Core Web Vitals

### 2.1 Automated Check via Chrome DevTools MCP

If the Chrome DevTools MCP server is available, use it to get real browser metrics:

1. **Navigate** to the production URL using the MCP navigation tool
2. **Run a performance trace** using `performance_start_trace` → let the page fully load → stop trace
3. **Check console** for any errors or warnings (especially hydration errors, failed API calls)
4. **Inspect network** for failed requests (404s, CORS issues, slow Prismic API calls)

This gives real LCP/CLS/INP numbers from an actual Chrome browser session — more accurate than Lighthouse simulations.

### 2.2 Fallback: Manual Tools

If Chrome DevTools MCP is not available, direct user to:

| Tool | URL |
|------|-----|
| PageSpeed Insights | https://pagespeed.web.dev/ |
| Chrome DevTools | Lighthouse tab (Incognito mode) |
| web.dev measure | https://web.dev/measure/ |

### Thresholds (Google's "Good" targets)

| Metric | Target | What It Measures |
|--------|--------|------------------|
| **LCP** (Largest Contentful Paint) | < 2.5s | How fast main content loads |
| **CLS** (Cumulative Layout Shift) | < 0.1 | Visual stability (no jumping) |
| **INP** (Interaction to Next Paint) | < 200ms | Responsiveness to clicks |

### If Metrics Fail

**LCP > 2.5s (Slow loading)**
Common culprits:
- Unoptimized hero image → Use Next.js `<Image>` with priority
- Render-blocking resources → Defer non-critical CSS/JS
- Large JavaScript bundles → Code split, lazy load
- Slow server response → Check Vercel edge functions

**CLS > 0.1 (Layout shifting)**
Common culprits:
- Images without width/height → Add dimensions or use aspect-ratio
- Late-loading fonts → Use `font-display: swap`, preload fonts
- Dynamically injected content → Reserve space with min-height
- Ads or embeds loading late → Set explicit dimensions

**INP > 200ms (Slow interactions)**
Common culprits:
- Heavy JavaScript on main thread → Break up long tasks
- Unoptimized event handlers → Debounce, throttle
- Third-party scripts → Defer or lazy load
- Complex DOM operations → Virtualize long lists

---

## Phase 3: SEO Checklist

### 3.1 Essential Meta Tags
WebFetch the page and extract/verify these elements:

| Check | Target | How to Verify |
|-------|--------|---------------|
| `<title>` | Present, 50-60 chars, includes page topic | WebFetch |
| `<meta name="description">` | Present, 150-160 chars, compelling | WebFetch |
| `<link rel="canonical">` | Points to correct URL | WebFetch |
| `<meta name="robots">` | `index, follow` or not present (default) | WebFetch |

### 3.2 Sitemap Verification
```bash
# Check page is in sitemap
curl -s https://www.cogniswitch.ai/sitemap.xml | grep "[page-path]"
```

**If missing:** Add to `app/sitemap.ts` with appropriate priority:
- 0.9 = Products (clarity, rails, etc.)
- 0.7 = Content/thought leadership
- 0.5 = Utility pages (contact, legal)

### 3.3 Heading Structure
WebFetch and verify:
- [ ] Exactly ONE `<h1>` tag (main page title)
- [ ] Logical H2/H3 hierarchy (no skipping levels)
- [ ] Headings are descriptive, not generic

### 3.4 Internal Linking
Check in code or via WebFetch:
- [ ] Page has outbound links to related pages
- [ ] At least 1 persistent link (footer or nav)
- [ ] CTA links use descriptive text (not "click here")

### 3.5 Structured Data (JSON-LD)
Check if page has appropriate schema:

| Page Type | Recommended Schema |
|-----------|-------------------|
| Product | `SoftwareApplication` |
| Article/Essay | `Article` or `TechArticle` |
| Guide | `HowTo` or `Article` |
| Company info | `Organization` |
| FAQ section | `FAQPage` |

**How to check:** View page source, search for `application/ld+json`

### 3.6 Image SEO
- [ ] Images use Next.js `<Image>` component
- [ ] Alt text present on all images
- [ ] Images are appropriately sized (not oversized)

---

## Phase 4: Social Sharing

### 4.1 OpenGraph Tags
WebFetch and verify presence of:
- `og:title`
- `og:description`
- `og:image` (1200x630px recommended)
- `og:url`

### 4.2 Preview Tools
Direct user to test social sharing:

| Platform | Tool |
|----------|------|
| Facebook | https://developers.facebook.com/tools/debug/ |
| Twitter/X | https://cards-dev.twitter.com/validator |
| LinkedIn | https://www.linkedin.com/post-inspector/ |

### 4.3 Google Indexability
```
# Check if Google can see the page (after a few days)
site:cogniswitch.ai/[page-path]
```

Or use Google Search Console for immediate inspection.

---

## Phase 5: Functional Smoke Test

### 5.1 Automated via Chrome DevTools MCP (preferred)

If Chrome DevTools MCP is available, automate the smoke test:

1. **Navigate** to the production URL
2. **Check console** for errors — flag any JS errors, hydration mismatches, or failed network requests
3. **Inspect DOM** to verify:
   - All content sections render (not null/empty)
   - Prismic slices are populated (check for empty slice containers)
   - Interactive components are functional (click buttons, test accordions)
4. **Check network tab** for:
   - Failed requests (404s, 500s)
   - Slow API calls (Prismic CDN > 500ms)
   - Missing assets (fonts, images)
5. **Simulate mobile** by checking responsive behavior

### 5.2 Manual Fallback

If MCP is not available:

### Desktop (Chrome)
- [ ] Page loads completely
- [ ] Navigation works
- [ ] Interactive elements function (buttons, forms, accordions)
- [ ] Links point to correct destinations
- [ ] **All CTAs navigate somewhere** (no dead buttons)
- [ ] No console errors (DevTools → Console)

### Prismic/Slice Styling Check (for migrated pages)
If page was migrated from hardcoded to Prismic, verify:
- [ ] **Typography matches original** (font-family, size, weight)
- [ ] **Spacing matches** (section padding, margins, gaps)
- [ ] **Lists render properly** (bullets visible, proper indentation)
- [ ] **Borders/shadows present** where expected
- [ ] **Colors correct** (no blue text on dark backgrounds)

**Reference:** `.claude/docs/Gotchas.md` → "Prismic Migration Issues"

### CTA Verification
Test ALL call-to-action buttons on the page:
```
WebFetch the production URL and list all CTA buttons with their href URLs.
Flag any that are:
- Empty href ("")
- Relative paths that create wrong URLs (e.g., "demo" → /page/demo)
- Missing protocol (cogniswitch.ai/contact → 404)
```

**Quick check:** Click each CTA button and verify it navigates correctly.

### Mobile (Real Device or DevTools)
- [ ] Page loads on mobile
- [ ] No horizontal scroll
- [ ] Text is readable without zooming
- [ ] Tap targets are adequate size (48x48px minimum)
- [ ] Sticky elements don't block content

---

## Phase 6: Performance Snapshot

### 6.1 Via Chrome DevTools MCP (preferred)

If MCP is available, run a performance trace directly:

1. Use `performance_start_trace` on the production URL
2. Wait for full page load
3. Stop trace and extract:
   - **LCP** value and the element that triggered it
   - **CLS** value and which elements shifted
   - **Total blocking time** (proxy for INP)
   - **Network waterfall** — identify the slowest resources
4. Report findings in the Core Web Vitals table below

### 6.2 Fallback: Ask User for Lighthouse Scores

If MCP is not available, ask user to share Lighthouse scores:

```
## Lighthouse Scores: [Page Name]

| Category | Score | Notes |
|----------|-------|-------|
| Performance | __/100 | |
| Accessibility | __/100 | |
| Best Practices | __/100 | |
| SEO | __/100 | |
```

### Score Targets
| Category | Target | Action if Below |
|----------|--------|-----------------|
| Performance | > 80 | Optimize images, defer scripts |
| Accessibility | > 90 | Add alt text, ARIA labels, color contrast |
| Best Practices | > 90 | Fix console errors, HTTPS issues |
| SEO | > 90 | Add meta tags, structured data |

---

## Output Format

```markdown
## Post-Deploy Verification: [Page Name]

**Model:** [state your model]
**URL:** https://cogniswitch.ai/[path]
**Deployed:** [date/time]

### Basic Checks
| Check | Status | Notes |
|-------|--------|-------|
| Page loads | ✅ | 200 OK |
| HTTPS | ✅ | Secure |
| In sitemap | ✅ / ⏳ | Present / Next build |

### Core Web Vitals
| Metric | Value | Status |
|--------|-------|--------|
| LCP | X.Xs | ✅ / ⚠️ / ❌ |
| CLS | X.XX | ✅ / ⚠️ / ❌ |
| INP | Xms | ✅ / ⚠️ / ❌ |

### SEO Checklist
| Check | Status | Notes |
|-------|--------|-------|
| Meta title | ✅ / ❌ | [length, content] |
| Meta description | ✅ / ❌ | [length, content] |
| In sitemap | ✅ / ⏳ | [priority] |
| Single H1 | ✅ / ❌ | |
| Internal links | ✅ / ❌ | [count] |
| Structured data | ✅ / ❌ / N/A | [schema type] |
| OG tags | ✅ / ❌ | |

### Lighthouse Scores
| Performance | Accessibility | Best Practices | SEO |
|-------------|---------------|----------------|-----|
| __/100 | __/100 | __/100 | __/100 |

### Issues Found
| Issue | Suggested Fix |
|-------|---------------|
| ... | ... |

### Verdict
[✅ VERIFIED / ❌ ISSUES FOUND — FIX ALL BEFORE CLOSING]
```

**IMPORTANT: There are no "low priority" issues.** Every issue found must be fixed before the page is considered shipped. Do not use severity labels (HIGH/MEDIUM/LOW) — if it's worth reporting, it's worth fixing. OG images, canonical URLs, sitemap entries, meta descriptions — these are all part of shipping a page, not follow-up tasks.

---

## Phase 7: Copy Quality (Optional)

Run a quick scan for AI writing patterns that may have slipped through.

### 7.1 Scan Page Copy
WebFetch the page and scan visible text for these red flags:

| Pattern | Examples | Fix |
|---------|----------|-----|
| Inflated significance | "testament to", "pivotal moment", "evolving landscape" | Use specific facts |
| Promotional fluff | "vibrant", "stunning", "nestled in" | Be direct |
| AI vocabulary | "delve", "crucial", "underscore", "foster" | Use simpler words |
| Rule of three | "seamless, intuitive, and powerful" | Pick one or two |
| Vague attribution | "experts say", "industry leaders believe" | Cite specifically or remove |
| Generic conclusions | "exciting times ahead", "bright future" | End with concrete next step |

### 7.2 Quick Check
```
Scan the page for these high-frequency AI phrases:
- "serves as a testament"
- "in today's [landscape/world/era]"
- "It's not just X, it's Y"
- "key/crucial/pivotal role"
- Any sentence starting with "Moreover" or "Furthermore"
```

### 7.3 Verdict
- **Clean:** No obvious AI patterns
- **Minor:** 1-2 phrases to revise
- **Needs review:** Multiple patterns detected, run `/copyreview` on the content

---

## Quick Links

| Resource | URL |
|----------|-----|
| PageSpeed Insights | https://pagespeed.web.dev/ |
| Facebook Debugger | https://developers.facebook.com/tools/debug/ |
| Twitter Card Validator | https://cards-dev.twitter.com/validator |
| Google Search Console | https://search.google.com/search-console |
| web.dev measure | https://web.dev/measure/ |
| Vercel Analytics | https://vercel.com/[team]/[project]/analytics |

---

## Start Verification

**What page just went live?**
