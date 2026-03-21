# Idea Capture

Fast capture for content ideas. Zero friction. Routes to the right inbox automatically.

## Usage

Just tell me the idea. I'll figure out where it goes.

**Examples:**
- "Newsletter: Why guardrails are security theater" → Signal inbox
- "Blog post about why RAG fails in healthcare" → Signal inbox
- "Series: Jargon-busting for enterprise AI" → Signal inbox
- "Landing page for clinical pathways use case" → Website ideas
- "Tool: policy change calculator" → Website ideas
- "Optimization: add structured data to product pages" → Website ideas

---

## Routing

**Signal Newsletter** (`Content/Signal/Ideas/inbox.md`):
- Newsletter topics, blog post ideas, series concepts
- Subscriber growth ideas, interview ideas, format experiments
- Keywords: newsletter, signal, blog, essay, series, ELI5, topic

**Website** (`Content/Ideas.md`):
- Landing pages, tools, optimizations, product pages
- Design changes, CTA experiments, page builds

If ambiguous, ask. If it could be both, add to Signal (content-first).

---

## Step 1: Capture

When user shares an idea, extract:
- **Title**: Short name for the idea
- **Type**: One of the following:
  - **Topic** - Single article/newsletter issue idea
  - **Series** - Multi-part series concept
  - **Subscriber** - Source for new subscribers
  - **Interview** - Guest or interview idea
  - **Format** - Newsletter format/structure experiment
  - **Cross-promo** - Partnership or cross-promotion opportunity
  - **Landing Page** - Website page idea
  - **Tool** - Interactive tool or calculator
  - **Optimization** - Website improvement
  - **Other** - Anything else
- **Notes**: Any additional context they shared
- **Urgency**: Hot (timely, do soon) / Warm / Backlog

---

## Step 2: Add to Inbox

### For Signal ideas:
Prepend to the `## Inbox` section in `Content/Signal/Ideas/inbox.md`:

```markdown
### [Title]
- Type: [Type]
- Urgency: [Hot/Warm/Backlog]
- Captured: [Today's date]
- Notes: [Any context]
```

If the idea relates to an existing research file (like `jargon-eli5-series.md`), also add a reference there.

### For Website ideas:
Prepend to `Content/Ideas.md`:

```markdown
### [Title]
- Type: [Type]
- Captured: [Today's date]
- Notes: [Any context]
```

---

## Step 3: Confirm

```markdown
Added to [Signal / Website] ideas:

**[Title]**
Type: [Type] | Urgency: [Urgency]

---

Quick actions:
- Add another idea
- Review inbox
- Done for now
```

---

## Batch Mode

If user has multiple ideas:

```markdown
Got it. Give me your ideas (one per line or just stream them).

I'll capture them all, then show you the list.
```

After capturing all:

```markdown
Added [X] ideas:

Signal:
1. [Idea 1] (Type)
2. [Idea 2] (Type)

Website:
3. [Idea 3] (Type)

Run `/idea review` to triage these.
```

---

## Review Mode

If user says `/idea review` or "review ideas":

1. Read `Content/Signal/Ideas/inbox.md`
2. Show all items in inbox
3. For each, ask: Move to research? Mark as next up? Delete?
4. Update files accordingly

---

## Start

**What's the idea?**

(Just type it - no format needed)
