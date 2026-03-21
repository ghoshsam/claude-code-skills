# CopyCheck - Website QA Audit

> **Model:** Sonnet 4.5 (cost-effective for text analysis)

## Execution

When this command is invoked, immediately use the Task tool to spawn a subagent:

```
Task tool parameters:
- description: "CopyCheck QA audit"
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

# CopyCheck QA Audit

Perform a comprehensive quality assurance audit for copy and links across the CogniSwitch website.

## What To Check

1. **Read sitemap** (`app/sitemap.ts`) to get all pages
2. **Fetch each page** from production (cogniswitch.ai) using WebFetch
3. **Check for:**
   - Grammar errors
   - Typos and spelling mistakes
   - **British vs American English** - We target American audiences, flag British spellings:
     - colour → color
     - behaviour → behavior
     - organisation → organization
     - analyse → analyze
     - recognise → recognize
     - favour → favor
     - honour → honor
     - labour → labor
     - centre → center
     - metre → meter
     - licence → license
     - defence → defense
     - programme → program
     - catalogue → catalog
     - dialogue → dialog
     - travelling → traveling
     - cancelled → canceled
     - focussed → focused
     - modelling → modeling
   - **M-dashes (—)** - Flag all em-dashes for removal/replacement with hyphens or rewording
   - Broken or incorrect links
4. **Report findings** in a table format
5. **Identify source** (Prismic CMS vs codebase)

## Process

### Step 1: Get All Pages
Read `app/sitemap.ts` to extract the full list of pages to audit.

### Step 2: Fetch & Analyze Each Page
For each page, use WebFetch to:
- Extract all text content (headings, paragraphs, buttons, links)
- List all href values
- Check for grammar/spelling issues

### Step 3: Compile Findings
Create a summary table:

| Page | Issue Type | Error | Correction |
|------|------------|-------|------------|
| `/rails` | Typo | "Complaince" | "Compliance" |

### Step 4: Identify Source
Determine if content is:
- **Prismic CMS** - User fixes in dashboard
- **Codebase** - Fix via code edit

## Output Format

```
## QA Audit Complete

**Model:** [state your model]

### Pages Reviewed: X pages + Y blog posts

### British English → American English (N total)

| Page | Location | British | American |
|------|----------|---------|----------|
| `/about` | Hero heading | colour | color |

### M-Dashes Found (N total)

| Page | Location | Text Context | Suggested Fix |
|------|----------|--------------|---------------|
| `/features` | Benefits section | "AI-powered—seamlessly" | "AI-powered, seamlessly" |

### Typos & Grammar Issues (N total)

| Page | Location | Error | Fix |
|------|----------|-------|-----|
| ... | ... | ... | ... |

### Broken Links: [None Found / List]

### Summary
- Total British spellings: X
- Total M-dashes: X
- Total typos/grammar: X
- Total broken links: X

### How to Fix
[Instructions for Prismic vs code fixes]
```

## Notes

- Blog posts are stored in Prismic (`blog_post` type)
- Most product pages use Prismic SliceZone
- Do NOT make fixes directly - just report findings
- CDN cache may delay visibility of fixes
