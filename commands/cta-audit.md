# CTA Audit

> **Model:** Sonnet 4.5 (cost-effective for link checking)

Weekly broken link and dead CTA checker for the CogniSwitch website.

## Execution

When this command is invoked, immediately use the Task tool to spawn a subagent:

```
Task tool parameters:
- description: "CTA audit"
- subagent_type: "general-purpose"
- model: "sonnet"
- prompt: [full audit instructions below]
```

**Do not execute this command directly.** Delegate to the Sonnet subagent.

---

## Subagent Prompt

Copy everything below this line as the Task prompt:

---

**Model verification:** First, confirm which model you are by stating "Running as [model name]".

# CTA Audit Agent

Systematically crawl the CogniSwitch website and identify:
- Broken links (404s)
- Malformed URLs (missing protocol, relative path issues)
- Dead CTAs (buttons without href attributes)
- Inefficient redirects

## Phase 1: Gather Pages

Read `app/sitemap.ts` to get the list of all pages to audit.

Key pages to always check:
- Homepage (`/`)
- Product pages (`/clarity`, `/align`, `/rails`, `/audit`)
- Blog page (`/blog`) and individual blog posts
- Guide pages (`/neuro-symbolic`, `/governance-blind-spot`)
- Demo pages (`/clarity/demo`, `/rails-demo`, `/align-demo`)

## Phase 2: Check Production URLs

For each page, use WebFetch to:
1. Verify page loads (not 404)
2. Extract all CTA buttons and links
3. Identify the href/URL for each

Look for these patterns:
- Empty hrefs (`href=""` or no href at all)
- Malformed URLs (`cogniswitch.ai/path` without protocol)
- Relative paths that will break (`demo` instead of `/align-demo`)
- Placeholder hrefs (`#`, `javascript:void(0)`)

## Phase 3: Test Link Targets

For suspicious links, test the target URL with WebFetch to verify:
- Does it return 200 or 404?
- Does it redirect? To where?

## Phase 4: Code Audit

Search the codebase for potential issues:

```bash
# Find buttons without href (dead CTAs)
grep -r "<button" slices/ --include="*.tsx" | grep -v "onClick"

# Find hardcoded links that might be malformed
grep -rn 'href="[^/http#]' slices/ app/ components/ --include="*.tsx"
```

## Phase 5: Generate Report

Create a report in this format:

```markdown
## CTA Audit Report - [Date]

**Model:** [state your model]

### Critical Issues (404s)
| Page | CTA Text | Broken URL | Fix Location |
|------|----------|------------|--------------|

### Dead CTAs (No Link)
| Component | CTA Text | Issue | Fix |
|-----------|----------|-------|-----|

### Malformed URLs
| Page | CTA Text | Current URL | Should Be |
|------|----------|-------------|-----------|

### Warnings (Redirects)
| Location | Current | Redirects To | Recommendation |
|----------|---------|--------------|----------------|

### Clean Pages
- [List of pages with no issues]
```

## Common Issues Reference

### 1. Malformed Prismic URLs
**Symptom:** URL like `cogniswitch.ai/contact` without `https://` or leading `/`
**Cause:** Prismic link field entered incorrectly
**Fix:** Update in Prismic - use either:
- Full URL: `https://cogniswitch.ai/contact`
- Relative path: `/contact`

### 2. Dead Buttons in Slices
**Symptom:** `<button>` elements that don't navigate anywhere
**Cause:** Slice uses `<button>` instead of `PrismicNextLink`
**Fix:** Update slice TSX to use `PrismicNextLink` with `field` prop

### 3. Relative Paths Creating Wrong URLs
**Symptom:** Link like `demo` on `/align` page goes to `/align/demo` (404)
**Cause:** Relative path instead of absolute
**Fix:** Use absolute path: `/align-demo`

## Prismic Content Locations

If issues are in Prismic content, check these document types:
- `homepage` - TrustPipeline slice CTAs
- `blog_list` - FooterManifesto CTA, Featured Guides links
- `align_page` - AlignHero and AlignCta slice CTAs
- `page` - Various product page CTAs

## Quick Commands

```bash
# Check if a URL returns 404
curl -s -o /dev/null -w "%{http_code}" https://cogniswitch.ai/[path]

# Find all PrismicNextLink usages
grep -rn "PrismicNextLink" slices/ --include="*.tsx"

# Find all <button> elements in slices
grep -rn "<button" slices/ --include="*.tsx"
```

## Output

After completing the audit, provide:
1. The full report with all issues found
2. Categorize by severity (Critical/Warning/Clean)
3. Indicate fix location (Prismic CMS vs codebase)

Do NOT make fixes directly - just report findings.
