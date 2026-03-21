# Prismic Content Population

Generate copy-paste ready content for Prismic documents based on existing page structure and content plans.

## ⚠️ Before You Start

**Read first:** `.claude/docs/Gotchas.md` → "Prismic Migration Issues"

**Key warnings:**
- `prose` classes are unreliable → verify headings/lists render after populating
- Always read FULL source files when analyzing (never `head -200`)
- Visual verification required after content is live

## Usage

```
/prismic-populate [page-name]
```

Examples:
- `/prismic-populate ownership` - Generate content for /ownership page
- `/prismic-populate clarity/healthcare` - Generate content for nested page
- `/prismic-populate blog/new-post` - Generate content for new blog post

## Workflow

### Step 1: Identify the Page

Based on the page name provided:

1. **Check for existing content plan:**
   ```bash
   ls Content/*[PageName]*ContentPlan*.md
   ```
   If found → use as primary source

2. **Check for existing page route:**
   ```bash
   ls app/[page-name]/page.tsx
   ```
   Read to understand slice structure

3. **Check Prismic document type:**
   - `page` - General pages with SliceZone
   - `blog_post` - Blog articles
   - `clarity_page` - Clarity sub-pages
   - `align_page` - Align page
   - Custom types in `customtypes/*/index.json`

### Step 2: Analyze Slice Structure

For each slice in the page:

1. **Read the slice model:**
   ```bash
   cat slices/[SliceName]/model.json
   ```

2. **Identify fields:**
   - Primary fields (single values)
   - Repeatable items (arrays)
   - Field types (Text, RichText, Link, Image, Select, Boolean)

3. **Check existing component** for context on how fields are used:
   ```bash
   cat slices/[SliceName]/index.tsx
   ```

### Step 3: Generate Content Plan

Output a structured content plan in this format:

```markdown
# [Page Name] - Slice-by-Slice Content Plan

**URL:** `/[page-path]`
**Prismic Document:** `[document-type]` type, UID: `[uid]`
**Total Slices:** [N]

---

## Page Flow

```
1. SliceName1 → Purpose
2. SliceName2 → Purpose
...
```

---

## Slice 1: [SliceName]

**Purpose:** [What this slice accomplishes]

### Primary Fields

| Field | Value |
|-------|-------|
| `field_name` | Content here |
| `rich_text_field` | Use **bold** for emphasis, *italic* for terms |

### Repeatable Items

| # | `field1` | `field2` | `field3` |
|---|----------|----------|----------|
| 1 | Value | Value | Value |
| 2 | Value | Value | Value |

### Notes
- Any special formatting or gotchas
- Link destinations
- Required vs optional fields

---

[Repeat for each slice]
```

### Step 4: Prismic-Ready Output

After the content plan, provide **copy-paste ready values** for the Prismic editor:

```markdown
## Prismic Editor Values

### Slice 1: [SliceName]

**field_name:**
```
Exact value to paste
```

**rich_text_field:**
```
Paragraph with **bold** and *italic* formatting.

Second paragraph if needed.
```

**Repeatable Item 1:**
- field1: `value`
- field2: `value`
```

## Content Guidelines

### Text Formatting

| Format | Prismic RichText | Display |
|--------|------------------|---------|
| Bold | `**text**` | Strong emphasis |
| Italic | `*text*` | Terms, quotes |
| Em-dash | `&mdash;` | — |
| Apostrophe | `&apos;` | ' |
| Quote marks | `&ldquo;` `&rdquo;` | " " |

### Link Fields

| Type | Format |
|------|--------|
| Internal page | `/page-name` (root-relative) |
| External URL | `https://full-url.com` |
| Anchor scroll | `#section-id` |
| Email | `mailto:email@example.com` |
| HubSpot demo | `https://meetings.hubspot.com/cogniswitch/intro` |

### Image Fields

Provide for each image:
- **Alt text:** Descriptive, includes keywords
- **Dimensions:** Recommended size
- **Source:** Where to find/create the image

### SEO Fields (if document type has them)

| Field | Guidelines |
|-------|------------|
| `meta_title` | 50-60 chars, includes primary keyword |
| `meta_description` | 150-160 chars, compelling CTA |
| `meta_image` | 1200x630 for OG, use page hero or custom |

## Verification Checklist

After generating, verify:

- [ ] All required fields have values
- [ ] RichText uses proper formatting (not HTML)
- [ ] Links are properly formatted (protocol for external)
- [ ] Repeatable items match expected count
- [ ] No placeholder text remains
- [ ] Apostrophes and special chars are escaped

## Common Patterns

### Hero Slices
```
label: Short category label (2-3 words)
headline_line1: First line (often the setup)
headline_line2: Second line (often italic, the punch)
subtext: 1-2 sentences expanding on headline
cta_label: Action verb (See, Book, Learn, Start)
cta_link: Destination URL or anchor
```

### Feature Cards (Repeatable)
```
icon: Lucide icon name (FileCode, Shield, Zap, etc.)
title: 2-4 word headline
description: 1-2 sentences
link: Optional destination
```

### CTA Slices
```
headline: Question or imperative
subtext: Brief supporting copy
primary_cta_label: Main action
primary_cta_link: Usually HubSpot or demo
secondary_cta_label: Alternative action (optional)
secondary_cta_link: Usually internal page
```

## After Generation

1. **Save content plan** to `Content/[PageName]-ContentPlan.md`
2. **Open Prismic** and create/edit the document
3. **Populate fields** slice by slice
4. **Save and publish** in Prismic
5. **Trigger revalidation:**
   ```bash
   curl -X POST https://www.cogniswitch.ai/api/revalidate
   ```
6. **Verify** on live site

## Troubleshooting

### Content not appearing?
1. Check document is **published** (not just saved)
2. Check UID matches what page route expects
3. Trigger manual revalidation
4. Check `revalidate` export in page.tsx

### RichText not formatting?
- Prismic RichText editor has its own toolbar
- Don't paste HTML, use the editor's bold/italic buttons
- Or use markdown-style in plain text fields

### Links broken?
- Internal: Must start with `/`
- External: Must include `https://`
- Anchors: Must match `id` attribute in component
