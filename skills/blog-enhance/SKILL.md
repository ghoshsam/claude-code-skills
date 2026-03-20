# Blog Enhance: Visual Treatment Explorer

Analyze blog content for interactive enhancement opportunities. For each opportunity, research existing visual patterns across the website, swipe file, and slice catalog — then present 2-3 visual treatment options per section so the user can pick the most interesting approach before anything gets built.

This is a two-phase skill: **explore first, build second.**

---

## Phase 1: Explore & Present Options

### Step 1: Read the Content

Three input modes:

1. **File path** — `/blog-enhance Content/Signal/Drafts/memory-eli5-draft.md`
2. **Prismic UID** — `/blog-enhance uid:context-graphs-eli5`
3. **Inline content** — User pastes content directly

### Step 2: Identify Enhancement Opportunities

Break the content by H2/H3 headings. For each section, ask:

- Is there a concept here that would **land better visually** than as prose?
- Would the reader gain something from **interacting** with this content (toggling, expanding, self-assessing)?
- Is there enough substance to fill a visual treatment, or would it feel forced?

Not every section needs an enhancement. Aim for 3-5 per post. Skip sections that work fine as text.

### Step 3: Research Visual Patterns

Before proposing options, research three sources:

**a) Swipe file** — Read `Content/SwipeFile/` for collected patterns, screenshots, and inspiration. Look for visual approaches that match the rhetorical intent of the section. A comparison might be best served by a pattern you've never built, not the obvious grid.

**b) Existing website** — Scan the live site's pages and slices for how similar concepts have been presented before. Check `app/blog/preview-enhancements/page.tsx` for the 25 existing enhancements. Check other product pages (`app/align/`, `app/audit/`, etc.) for visual patterns that could apply.

**c) Slice catalog** — Review what's buildable today. The interactive essay slices are listed in the Slice Vocabulary below, but also consider non-essay slices that might work in a blog context (e.g., a product comparison slice repurposed for a conceptual comparison).

### Step 4: Present Options

For each enhancement opportunity, present **2-3 visual treatment options**. Each option should include:

```
### Section: [Heading from the content]

**What the content is doing:** [One sentence — the rhetorical intent]

**Option A: [Slice type or new concept]**
- How it works: [Brief description of the visual/interactive behavior]
- Why this fits: [What makes this treatment interesting for THIS specific content]
- Reference: [Where you saw this pattern — swipe file entry, existing page, or existing enhancement]
- Buildable today: Yes / Needs new slice

**Option B: [Different slice type or approach]**
- How it works: ...
- Why this fits: ...
- Reference: ...
- Buildable today: Yes / Needs new slice

**Option C: [Optional — a wilder take]**
- How it works: ...
- Why this fits: ...
- Reference: ...
- Buildable today: Yes / Needs new slice
```

### Principles for Generating Options

- **Don't default to the obvious mapping.** A comparison doesn't have to be a comparison table. It could be a toggle that tells both stories. It could be a pyramid that reveals the real hierarchy. It could be something from the swipe file you haven't built yet.
- **At least one option should be from outside the essay slice catalog.** Look at the swipe file. Look at the website's product pages. What visual idea would make this section *memorable*, not just *organized*?
- **Explain why each option is interesting for this specific content.** "This is a comparison so here's a comparison table" isn't a rationale. "This comparison has a hidden hierarchy — the reader thinks these are equal, but one dominates — so a pyramid reveals that" is.
- **Flag new slice opportunities honestly.** If the best visual treatment would require a new component, say so. Describe the interaction model. Don't shoehorn content into existing slices when a new one would serve it better.
- **Variety across the post matters.** If you're proposing three toggles in a row, reconsider. The post should feel like a curated experience, not a template.

### Wait for User Selection

After presenting all options, **stop and wait**. The user will pick one option per section (or suggest something different). Do not proceed to Phase 2 until selections are made.

---

## Phase 2: Build the Selected Treatments

Once the user has selected their preferred treatment for each section:

### Step 1: Generate Data Objects

Create the exact data objects each selected slice component expects. Use the templates in the Slice Data Shape Reference below. Every field must match — wrong shapes cause silent render failures.

For "Needs new slice" selections, discuss with the user:
- Build a simplified version using the closest existing slice?
- Flag for Gemini sandbox prototyping?
- Skip for now and revisit?

### Step 2: Write Preview Page

Write `app/blog/preview-enhance/page.tsx` (overwritten each run — single post focus). Use the Preview Page Template below.

### Step 3: Output Summary

```
## Built: [Post Title]

| # | Section | Treatment Selected | Slice Type |
|---|---------|-------------------|------------|
| a | [heading] | [Option letter + name] | essay_interactive_demo (toggle) |
| b | [heading] | [Option letter + name] | essay_expandable_table |
| c | [heading] | New slice needed | [flagged for sandbox] |

Preview: http://localhost:3000/blog/preview-enhance
```

---

## Slice Vocabulary

The interactive slice types available today. These are the building blocks, not the only answers — the interesting work is in choosing *which* one serves specific content best.

| Slice Type | What It Does | Best For |
|---|---|---|
| `essay_interactive_demo` (toggle) | Two-panel toggle, click to flip | Contrasting perspectives, before/after, "what you think vs what's true" |
| `essay_expandable_table` | Rows with surface values + expandable detail | Content where the surface comparison hides deeper nuance per row |
| `essay_comparison_table_v2` | Status grid with pass/fail/partial badges | Feature matrices, readiness checks, multi-dimensional scoring |
| `essay_assessment` | Checklist with tiered feedback | Self-diagnostics, "is your X real?", readiness evaluations |
| `essay_accordion` (default) | Expandable panels with icon + rich text | Anatomy breakdowns, concept catalogs, building blocks |
| `essay_accordion` (structured) | Panels with what/doesn't/gap/reality fields | Strategies, objectives, mitigations with structured analysis |
| `essay_system_diagram` | 3-column flow: input -> process -> output | Architecture flows, feedback loops, pipeline visualizations |
| `essay_investment_pyramid` | Layered hierarchy with investment levels | Priority stacks, resource allocation, revealing hidden hierarchies |
| `process_timeline` | Sequential phases with progression markers | Maturity models, evolution narratives, stage progressions |
| `essay_code_block` | Syntax-highlighted code snippet | Technical implementations, config examples |

---

## Slice Data Shape Reference

All shapes extracted from the 25 battle-tested examples in `app/blog/preview-enhancements/page.tsx`.

### essay_interactive_demo (toggle)

Two-panel toggle showing contrasting perspectives.

```typescript
{
  slice_type: "essay_interactive_demo",
  variation: "toggle",
  id: "unique-id",       // kebab-case, descriptive
  primary: {
    title: "The Documentation Gap",
    figure_number: "Fig 1",           // Sequential within the post
    before_label: "What's Written",   // Left/default panel label
    before_content: [                 // StructuredText array
      {
        type: "paragraph",
        text: "Full paragraph text here.",
        spans: [{ type: "em", start: 10, end: 121 }],  // Optional
      },
    ],
    after_label: "What's Actually Enforced",  // Right/toggled panel label
    after_content: [                          // StructuredText array
      {
        type: "paragraph",
        text: "Contrasting perspective here.",
        spans: [{ type: "strong", start: 0, end: 21 }],  // Optional
      },
    ],
  },
  items: [],  // Always empty for toggle
}
```

- Each panel needs 2-3 paragraphs for visual balance
- Labels should be short (2-4 words) and contrastive
- `spans` are optional — use `strong` for key phrases, `em` for quoted/cited text

### essay_expandable_table

Rows with surface-level comparison + expandable detail.

```typescript
{
  slice_type: "essay_expandable_table",
  variation: "default",
  id: "unique-id",
  primary: {
    title: "What Context Graphs Capture vs What's Needed",
    figure_number: "Fig 3",
    column_a_header: "Captures (Correlation)",
    column_b_header: "Needs (Causation)",
    footer_quote: "Pull quote that summarizes the table's insight.",
  },
  items: [
    {
      row_label: "Expert Behavior",
      column_a_value: "When condition X occurs, expert Y does Z",
      column_b_value: "Is Z correct because of policy Q — or despite violating it?",
      is_critical: true,           // boolean — highlights row
      expanded_details: "Detailed explanation shown when row is expanded. 2-3 sentences.",
    },
    // 3-4 rows typical
  ],
}
```

- `is_critical: true` for 1-2 most important rows
- `expanded_details` is a plain string, not StructuredText
- `footer_quote` wraps in quotation marks automatically

### essay_comparison_table_v2

Status grid with pass/fail/partial badges. Two variations: `default` (2-column) and `threeColumn`.

**Default variation:**

```typescript
{
  slice_type: "essay_comparison_table_v2",
  variation: "default",
  id: "unique-id",
  primary: {
    title: "Evals vs Audits",
    column_a_header: "Dimension",
    column_b_header: "Status",
    title_color: "default",     // "default" | "primary" | "engineering"
    body_color: "default",
  },
  items: [
    {
      column_a: "Evals: \"How good is this?\"",
      column_b_status: "easy",
      column_b_text: "Quality measurement — noise acceptable",
    },
  ],
}
```

**Three-column variation:**

```typescript
{
  slice_type: "essay_comparison_table_v2",
  variation: "threeColumn",
  id: "unique-id",
  primary: {
    title: "Architecture Fit by Dimension",
    row_header: "Dimension",
    column_a_header: "Simple LLM / RAG",
    column_b_header: "Graph RAG",
    column_c_header: "Ontology-Guided",
    title_color: "default",
    body_color: "default",
  },
  items: [
    {
      row_label: "Consistency",
      column_a_status: "fail",       // "pass" | "fail" | "partial" | "na" | "easy" | "hard" | "rebuild"
      column_b_status: "partial",
      column_c_status: "pass",
    },
  ],
}
```

- `title_color: "engineering"` gives red accent (warnings/critical)

### essay_assessment

Self-diagnostic checklist with tiered feedback.

```typescript
{
  slice_type: "essay_assessment",
  variation: "default",
  id: "unique-id",
  primary: {
    title: "Architecture Readiness Assessment",
    subtitle: "Score your workflow against the six dimensions.",
    title_color: "primary",
    body_color: "default",
    critical_message: "0-25% checked: message for low scores.",
    significant_message: "25-50% checked: message for medium-low scores.",
    partial_message: "50-75% checked: message for medium-high scores.",
    complete_message: "75-100% checked: message for high scores.",
  },
  items: [
    { question: "Same question yields the same answer every time" },
    // 5-8 questions typical
  ],
}
```

### essay_accordion (default variation)

Expandable panels for concepts needing unpacking.

```typescript
{
  slice_type: "essay_accordion",
  variation: "default",
  id: "unique-id",
  primary: {
    title: "Anatomy of an Ontology",
    subtitle: "Six building blocks",
    title_color: "primary",
    body_color: "default",
    allow_multiple: true,          // boolean
  },
  items: [
    {
      item_title: "Formal",
      item_icon: "lock",           // Lucide icon name
      item_content: [              // StructuredText array
        { type: "paragraph", text: "Explanation here.", spans: [] },
      ],
    },
    // 4-8 items typical
  ],
}
```

- Icons: `lock`, `cpu`, `layers`, `shield`, `target`, `zap`, `eye`, `search`, `database`, `git-branch`, `check-circle`, `alert-triangle`

### essay_accordion (structured variation)

Panels with four structured analysis fields.

```typescript
{
  slice_type: "essay_accordion",
  variation: "structured",
  id: "unique-id",
  primary: {
    title: "Three HITL Objectives",
    subtitle: "Most implementations conflate all three.",
    title_color: "default",
    body_color: "default",
    allow_multiple: false,
  },
  items: [
    {
      mitigation_name: "Accountability",
      item_icon: "shield",
      what_it_does: "Certify that the process was followed.",
      what_it_doesnt: "Verify substance or apply judgment.",
      the_gap: "Most systems ask the human to verify substance but frame it as accountability.",
      audit_reality: "The signature means 'I am now liable,' not 'I verified accuracy.'",
    },
    // 3-4 items typical
  ],
}
```

### essay_system_diagram

Three-column flow: input -> process -> output.

```typescript
{
  slice_type: "essay_system_diagram",
  variation: "default",
  id: "unique-id",
  primary: {
    figure_number: "Fig 2",
    title: "The Reconciliation Layer",
    column_a_label: "Context Graphs",
    column_b_label: "Reconciliation",
    column_c_label: "Formalized SOPs",
    column_a_items: "Tribal knowledge,Expert patterns,Operational workarounds",  // Comma-separated string
    column_b_options: "Align with policy,Surface violations,Formalize exceptions",
    column_c_options: "Versioned policies,Audit trails,Curated rules",
    warning_message: "Neither alone qualifies as the foundation",
    footer_note: "Summary sentence below the diagram.",
  },
  items: [],  // Always empty
}
```

- All list fields are **comma-separated strings**, not arrays

### essay_investment_pyramid

Layered hierarchy showing misallocated investment.

```typescript
{
  slice_type: "essay_investment_pyramid",
  variation: "default",
  id: "unique-id",
  primary: {
    title: "Where Teams Over- and Under-Invest",
    subtitle: "Most teams invest heavily at the bottom and skip the top",
    title_color: "default",
    body_color: "default",
    gap_label: "The Gap",
    insight: [
      {
        type: "paragraph",
        text: "Key takeaway rendered below the pyramid.",
        spans: [{ type: "strong", start: 0, end: 37 }],
      },
    ],
  },
  items: [
    {
      layer_name: "Ontology & Domain Modeling",
      investment_level: "low",        // "low" | "medium" | "high"
      governance_value: "high",       // "low" | "medium" | "high"
      is_underinvested: true,         // boolean
    },
    // 4-6 layers, ordered top to bottom
  ],
}
```

### process_timeline

Sequential phases with progression markers.

```typescript
{
  slice_type: "process_timeline",
  variation: "default",
  id: "unique-id",
  primary: {
    section_title: "The Path to Production",
    section_description: [
      { type: "paragraph", text: "Most teams go through three stages.", spans: [] },
    ],
    timeline_label: "Maturity",
  },
  items: [
    {
      phase_number: "01",            // Zero-padded string
      phase_timeline: "Stage 1",
      phase_title: "Observability",
      phase_description: '"What happened?" — Logs, metrics, monitoring.',
      phase_output: "Dashboards & Traces",
      is_current: false,             // Exactly one should be true
    },
    // 3-4 phases typical
  ],
}
```

- Renders full-width — set `fullWidth: true` in preview page

### essay_code_block

```typescript
{
  slice_type: "essay_code_block",
  variation: "default",
  id: "unique-id",
  primary: {
    title: "Example Configuration",
    language: "typescript",
    code: `const config = {\n  key: "value"\n};`,
    caption: "Optional caption below the code block",
  },
  items: [],
}
```

---

## StructuredText Format Reference

```typescript
[
  {
    type: "paragraph",    // "paragraph" | "heading2" | "heading3" | "list-item"
    text: "The full plain text of this block.",
    spans: [
      {
        type: "strong",   // "strong" | "em" | "hyperlink"
        start: 0,         // Character offset (0-indexed, inclusive)
        end: 21,          // Character offset (exclusive)
      },
    ],
  },
]
```

### Span Rules (Critical)

- Offsets are character-based. Count carefully.
- `start` inclusive, `end` exclusive.
- `end` must be `<= text.length`.
- Empty `spans: []` is valid and common.
- **Double-check every span offset.** Off-by-one errors cause garbled rendering.

---

## Preview Page Template

Write to `app/blog/preview-enhance/page.tsx`. Overwritten each run.

```typescript
"use client";

import { SliceZone } from "@prismicio/react";
import { components } from "@/slices";

/**
 * Preview: [Post Title] enhancements
 * Generated by /blog-enhance
 * Visit: http://localhost:3000/blog/preview-enhance
 */

const enhancement1 = { /* ... */ };
const enhancement2 = { /* ... */ };

const enhancements = [
  { slice: enhancement1, desc: "Type — Short description", fullWidth: false },
  { slice: enhancement2, desc: "Type — Short description", fullWidth: true },
];

export default function PreviewEnhancePage() {
  return (
    <section className="max-w-7xl mx-auto px-6 md:px-12 py-24 w-full border-x border-dashed border-ink/10">
      <div className="max-w-prose mx-auto mb-16">
        <p className="font-mono text-xs uppercase tracking-widest text-subtle mb-4">
          Preview — Blog Enhancement
        </p>
        <h1 className="font-serif text-4xl md:text-5xl leading-tight tracking-tight mb-4">
          [Post Title]
        </h1>
        <p className="font-serif text-lg text-subtle mb-8">
          {enhancements.length} interactive enhancements.
        </p>
      </div>

      {enhancements.map((entry, idx) => (
        <div key={idx} className="mb-16">
          <div className="max-w-prose mx-auto">
            <div className="border-l-2 border-primary pl-4 mb-6">
              <p className="font-mono text-xs uppercase tracking-widest text-ink">
                {String.fromCharCode(97 + idx)}. {entry.desc}
              </p>
            </div>
          </div>
          <div className={entry.fullWidth ? "" : "max-w-prose mx-auto"}>
            <SliceZone
              slices={[entry.slice] as any}
              components={components}
            />
          </div>
        </div>
      ))}
    </section>
  );
}
```

### Template Rules

- `"use client"` must be the first line
- `fullWidth: true` for `essay_system_diagram` and `process_timeline`
- Cast slices as `any` to avoid type mismatches

---

## Quality Checks (Phase 2 only)

Before finalizing the preview page:

1. **Span offsets** — Count characters. Verify `start`/`end` don't exceed `text.length`.
2. **Boolean types** — `is_critical`, `is_current`, `allow_multiple`, `is_underinvested` must be booleans, not strings.
3. **Comma-separated fields** — System diagram list fields are comma-separated strings, not arrays.
4. **`"use client"`** — First line of the file.
5. **Unique IDs** — Every slice needs a unique kebab-case `id`.
6. **StructuredText vs strings** — Rich text fields use `[{type, text, spans}]`. Plain string fields: `expanded_details`, `what_it_does`, `what_it_doesnt`, `the_gap`, `audit_reality`, `footer_quote`, `warning_message`, `footer_note`.
7. **Empty items** — Toggle and system diagram slices always have `items: []`.
8. **Figure numbers** — Sequential within the post.
9. **Variation** — Must match exactly: `"toggle"`, `"default"`, `"threeColumn"`, `"structured"`.

---

## Optional Follow-ups

- **`/copyreview`** — Review enhancement copy for AI writing patterns
- **Integration** — Add approved enhancements to cumulative `app/blog/preview-enhancements/page.tsx`
- **Sandbox** — Flag "needs new slice" items for Gemini prototyping
