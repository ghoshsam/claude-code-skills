# Deploy to Production

Run through the complete deployment checklist including design review before pushing to production.

> **Note:** A pre-push hook now automatically runs `npm run build` before any `git push` command. If the build fails, the push is blocked and you'll see the error immediately. This prevents broken builds from reaching Vercel.

## Pre-Deploy Checklist Output

Show this summary before pushing:

```
## Pre-Deploy Checklist
| Step | Status | Notes |
|------|--------|-------|
| Git status | ✅ | 3 files changed |
| Design review | ⚠️ | 1 warning (non-blocking) |
| New page launch | ✅/⏭️ | [checklist passed / no new pages] |
| Build | ✅ | 42s |
| SEO check | ✅ | Sitemap updated |
| Ready to push | ✅ | Awaiting commit message |
```

## Steps to Execute

### 1. Check git status
Show what files have changed and what's uncommitted.

### 2. Design Review
Run the comprehensive design review command on modified files:

```
Use the Skill tool to invoke: /design-review
```

This will scan all modified TSX/JSX files for:
- Contrast violations (`text-primary` on `bg-ink`, dark on dark patterns)
- Color consistency (hardcoded hex vs theme colors)
- Typography hierarchy (serif/sans/mono usage)
- Interactive states (hover, focus, disabled)
- Dead CTAs and broken links

**If issues found:**
- List all issues clearly with file paths and line numbers
- Ask: "Fix these issues before deploying, or proceed anyway?"
- If fix → make corrections, then continue
- If proceed → continue with warning

**For manual reference** (key anti-patterns):
- `text-primary` on `bg-ink` → INVISIBLE (use `text-paper` or `text-highlight`)
- `text-ink` on `bg-ink` → dark on dark
- Hardcoded hex colors → use theme tokens (`ink`, `paper`, `primary`, `engineering`)
- `prose` classes → often fail to render properly, use explicit classes

Full design system compliance checks are in `/design-review` command.

### 3. New Page Launch Check (Conditional)

**Only runs if `git diff --name-only` shows new `app/[slug]/page.tsx` files.**

If a new page is detected:

1. Run the checklist from `.claude/docs/PageLaunchChecklist.md`
2. Verify: sitemap entry, SEO metadata, 3+ inbound links, outbound links, PageArchitecture.md entry
3. Show the checklist table (see PageLaunchChecklist.md for format)
4. **If any checks fail:** Ask "Fix these before deploying, or proceed with orphan risk?"
5. If the user wants a full discoverability plan, run `/discover`

If no new pages detected, skip to Step 4.

### 4. Mobile Viewport Check (Conditional)

**Only runs if `git diff --name-only` shows modified `app/*/page.tsx` files.**

For each changed page:
1. Ensure dev server is running (`npm run dev`)
2. Run `npx ts-node scripts/mobile-check.ts /[page-slug] --no-server`
3. This captures full-page screenshots at 375px (mobile), 768px (tablet), 1280px (desktop)
4. Screenshots saved to `/tmp/mobile-check/`
5. **Read each screenshot** using the Read tool and visually inspect for:
   - Content clipped or hidden (buttons, text cut off)
   - Horizontal overflow / scroll
   - Text too small to read
   - Elements overlapping
   - Buttons/CTAs not visible or not tappable
6. **If issues found:** List them and fix before proceeding
7. **If clean:** Continue to build

If no page changes detected, skip to Step 5.

### 5. Run the build locally
Execute `npm run build` and check for errors.

### 6. If build succeeds:
- Show a summary of changes to be deployed
- Ask the user for a commit message (suggest one based on changes)
- Stage all changes, commit, and push to main
- Wait for confirmation that push succeeded

### 7. If build fails:
- Show the error clearly
- Suggest fixes based on common issues:
  - "uid received object" → Check for archived Prismic pages
  - TypeScript errors → Show file and line numbers
  - Missing dependencies → Suggest npm install
- Do NOT push if build fails

### 8. Post-deployment:
- Ask if Prismic content was changed
- If yes, trigger cache revalidation: `curl -X POST https://www.cogniswitch.ai/api/revalidate`
- Provide the live URL for testing

### 9. Verify Vercel Deployment

After push succeeds:

1. **Wait for deployment** (~1-2 minutes)
2. **Check deployment status:**
   ```bash
   # Quick check via git
   git log -1 --format="%H" | head -c 7
   # Then verify at: https://vercel.com/vivek-ks-projects-f50e46af/getcogniswitch/deployments
   ```

3. **Test live URL:**
   - If Chrome DevTools MCP is available: navigate to the page, check console for errors, inspect network for failed requests, run a quick performance trace for LCP/CLS
   - If MCP not available: Visit `https://cogniswitch.ai/[new-or-changed-page]`, check browser console for errors
   - Verify no 500/404 errors

4. **If deployment fails:**
   - Check Vercel logs for error details
   - Common issues:
     - Environment variables missing
     - Prismic API rate limits
     - Build timeout (increase in vercel.json)

---

### 10. Post-Deployment QA Suite (Automatic)

After successful Vercel deployment, automatically run three QA audits using Sonnet 4.5 subagents:

#### 10.1 CopyCheck Audit
Spawn subagent to check for:
- Grammar errors and typos
- British → American spelling conversions
- M-dashes (flag for removal/replacement)
- Broken or incorrect links

**Execution:**
```
Task tool:
- description: "CopyCheck QA audit"
- subagent_type: "general-purpose"
- model: "sonnet"
- prompt: [Use full prompt from copycheck.md]
```

#### 10.2 CTA Audit
Spawn subagent to check for:
- Broken links (404s)
- Dead CTAs (buttons without hrefs)
- Malformed URLs (missing protocol, relative path issues)
- Inefficient redirects

**Execution:**
```
Task tool:
- description: "CTA audit"
- subagent_type: "general-purpose"
- model: "sonnet"
- prompt: [Use full prompt from cta-audit.md]
```

#### 10.3 Post-Deploy Verification
Spawn subagent to verify:
- Core Web Vitals (LCP, CLS, INP) — **use Chrome DevTools MCP if available** for real browser metrics instead of Lighthouse estimates
- Console errors and network failures — MCP can check these directly
- SEO checklist (meta tags, sitemap, heading structure)
- Social sharing (OpenGraph tags)
- Functional smoke test — MCP can navigate, click CTAs, and verify rendering

**Execution:**
```
Task tool:
- description: "Post-deploy verification"
- subagent_type: "general-purpose"
- model: "sonnet"
- prompt: [Use full prompt from verify.md]
```

#### 10.4 Consolidated QA Report

After all three agents complete, present a consolidated summary:

```markdown
## Post-Deploy QA Report

### CopyCheck
- British spellings: X found
- M-dashes: X found
- Typos/grammar: X found
- Broken links: X found

### CTA Audit
- Critical issues (404s): X found
- Dead CTAs: X found
- Malformed URLs: X found

### Verification
- Core Web Vitals: [LCP/CLS/INP status]
- SEO Score: X/100
- Lighthouse: Performance X/100

### Action Required
[List of issues requiring immediate attention]
```

**If critical issues found:** Ask user if they want to fix before proceeding to indexing.

---

### 11. Request Google Indexing (for new pages)
If new pages were added:

1. **List new pages** that were added to sitemap
2. **Remind user to request indexing** via Google Search Console:
   - Go to [Google Search Console](https://search.google.com/search-console)
   - URL Inspection → Enter the new page URL
   - Click "Request Indexing"

**Note:** The old sitemap ping (`google.com/ping?sitemap=`) was deprecated in June 2023. Google now relies on:
- `lastmod` dates in sitemap (automatically updated)
- Manual URL Inspection requests
- Regular crawling

For bulk indexing, consider the [Search Console URL Inspection API](https://developers.google.com/webmaster-tools/v1/urlInspection.index/inspect) (requires OAuth setup).

### 12. Log Session (Optional)

For significant work, ask: **"Should I add this to SessionLog.md?"**

If yes, append entry with:
- Session name (kebab-case, e.g., `healthcare-page-launch`)
- Branch and commit hash
- What was built (bullet points)
- Files changed
- Key learnings or gotchas discovered

---

## Emergency Rollback

If production is broken after deploy:

```bash
# Option 1: Revert last commit
git revert HEAD --no-edit
git push origin main
# Vercel will auto-deploy the revert (~2 min)

# Option 2: Hard reset to previous commit (use with caution)
git reset --hard HEAD~1
git push --force origin main
```

**Before reverting, check:**
- Is the issue in code or Prismic content?
- If Prismic → fix in CMS, then hit revalidate endpoint
- If code → revert commit

---

## Important Rules
- NEVER push if the local build fails
- ALWAYS run design review on modified files
- ALWAYS show the user what's being committed before pushing
- ALWAYS ask for commit message confirmation
- ALWAYS verify deployment succeeded before marking complete
