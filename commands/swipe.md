# Swipe File — Capture, Review, Search

Screenshot-first capture of interesting patterns, designs, copy, and ideas from browsing. The swipe file collects *other people's* work worth learning from.

## Usage

`/swipe [screenshot path | URL | text observation]`
`/swipe process` — scan assets/ for unprocessed screenshots
`/swipe review` — graduate inbox entries to category files
`/swipe search [query]` — find entries by title, tag, or category

---

## Storage Locations

| What | Where |
|------|-------|
| Screenshots | `Content/SwipeFile/assets/` |
| Inbox (raw entries) | `Content/SwipeFile/inbox.md` |
| Category files | `Content/SwipeFile/{category}.md` |
| Lookup index | `.claude/docs/SwipeIndex.md` |

---

## Mode Detection

Based on the argument provided:

1. **Screenshot path** (file ends in .png, .jpg, .jpeg, .gif, .webp, or is a path to assets/) → Screenshot mode
2. **Multiple paths or glob** → Batch mode
3. **`process`** → Process new screenshots in assets/
4. **`review`** → Review/graduate mode
5. **`search [query]`** → Search mode
6. **URL** (starts with http) → URL mode
7. **Plain text** → Text observation mode
8. **No argument** → Show status (entry count, unprocessed screenshots)

---

## Screenshot Mode (Primary Flow)

This is the main workflow. User drops a screenshot, Claude reads and analyzes it.

### Step 1: Read the Screenshot

The screenshot can be anywhere — Desktop, Downloads, wherever. Use the Read tool on the provided path. You are multimodal — you can see the image.

### Step 2: Move to Assets

**Always** move the screenshot into `Content/SwipeFile/assets/` using Bash `mv`. The user should never have to manage file locations.

- Generate a searchable filename: `YYYY-MM-DD-descriptive-slug.ext` (e.g., `2026-02-16-linear-changelog-animation.png`)
- The descriptive slug comes from what you see in the screenshot (Step 3)
- Use lowercase, hyphens, no spaces

```bash
mv "/Users/vivekkhandelwal/Desktop/Screenshot 2026-02-16.png" "Content/SwipeFile/assets/2026-02-16-linear-changelog-animation.png"
```

### Step 3: Analyze

Extract from the screenshot:
- **What's shown** — the pattern, layout, copy, interaction, UI element
- **Source** — if a URL bar, logo, or site name is visible, capture it. Otherwise "unknown"
- **Category** — auto-assign one of the 7 categories (never ask the user)
- **Title** — generate a descriptive title from what you see

### Step 3: Incorporate User Notes

If the user said anything alongside the screenshot path ("this is from Linear's changelog, I like how they handle release notes"), incorporate it into "Why it's interesting" and use it to refine the source/title.

### Step 4: Write Entry

Prepend to the `## Inbox` section in `Content/SwipeFile/inbox.md`:

```markdown
### [Descriptive Title]
- **Screenshot:** `assets/[filename]`
- **Source:** [URL | site name | "unknown"]
- **Category:** [one of 7]
- **Tags:** [2-4 free-form, comma-separated]
- **Captured:** [today's date YYYY-MM-DD]
- **What I see:** [Factual description of the screenshot — pattern, layout, copy, interaction]
- **Why it's interesting:** [Why this is worth saving, what makes it notable, user notes if provided]
- **Applicable to:** [CogniSwitch page/asset if obvious, or "General"]
```

### Step 5: Update Index

Read `.claude/docs/SwipeIndex.md`. Add a row to the index table:

```
| [next #] | [Title] | [Category] | [tags] | [source] | [applicable to] | [date] | inbox |
```

The `File` column is `inbox` for new entries, updated to the category filename after `/swipe review`.

### Step 6: Confirm

```
Captured: **[Title]**
Category: [Category] | Tags: [tags]
Source: [source]

Quick actions:
- Add another screenshot
- `/swipe process` to scan for more in assets/
- `/swipe review` to organize inbox
```

---

## Batch Mode

If the user provides multiple screenshot paths, or says "process these":

1. Read each screenshot
2. Analyze and create entries for all
3. Show a summary table:

```
| # | Title | Category | Source |
|---|-------|----------|--------|
| 1 | ... | ... | ... |
| 2 | ... | ... | ... |
```

4. Update index with all entries

---

## Process Mode (`/swipe process`)

Scan `Content/SwipeFile/assets/` for image files not yet referenced in `inbox.md`.

### Steps:
1. List all files in `Content/SwipeFile/assets/` (exclude `.gitkeep`)
2. Read `Content/SwipeFile/inbox.md`
3. Find screenshots in assets/ not mentioned in inbox.md
4. If none found: "All screenshots in assets/ are already processed."
5. If found: show list, then process each one (read image, analyze, create entry)
6. Show summary table when done

---

## URL Mode

When user provides a URL:

1. Use WebFetch to get the page content
2. Create entry based on page content — title from page title, source from URL
3. Screenshot field: "N/A — captured from URL"
4. Otherwise same entry format and flow

---

## Text Mode

When user provides a plain text observation ("Stripe's pricing page uses a comparison toggle"):

1. Create entry from the text
2. Screenshot field: "N/A — text observation"
3. Source: extract from text if mentioned, else "unknown"
4. Otherwise same entry format and flow

---

## Review Mode (`/swipe review`)

Graduate inbox entries to their category files.

### Steps:
1. Read `Content/SwipeFile/inbox.md`
2. If inbox is empty: "Inbox is empty. Nothing to review."
3. Show all inbox entries as a numbered list with title + category
4. For each entry, ask: **Graduate** (move to category file), **Keep** (stay in inbox), or **Delete**
5. For graduated entries:
   - Append the full entry to the appropriate category file (`Content/SwipeFile/{category}.md`)
   - Remove from inbox
   - Update the `File` column in SwipeIndex.md from `inbox` to the category filename
6. Show summary: "Graduated X entries, kept Y, deleted Z"

### Category → File mapping:
| Category | File |
|----------|------|
| Design Patterns | `design-patterns.md` |
| UI/UX | `ui-ux.md` |
| Copy/Voice | `copy-voice.md` |
| Content Strategy | `content-strategy.md` |
| Technical | `technical.md` |
| Competitive Intel | `competitive-intel.md` |
| Inspiration | `inspiration.md` |

---

## Search Mode (`/swipe search [query]`)

1. Read `.claude/docs/SwipeIndex.md`
2. Search the index table for matches in Title, Tags, Category, or Source columns
3. Show matching rows
4. If user wants full details, read the entry from inbox.md or the category file

---

## Categories (7)

Auto-assign — never ask the user which category:

1. **Design Patterns** — layouts, component design, visual systems, IA
2. **UI/UX** — interactions, animations, micro-interactions, navigation
3. **Copy/Voice** — headlines, CTAs, tone, messaging, word choice
4. **Content Strategy** — publishing cadence, content types, SEO, distribution
5. **Technical** — stack choices, performance, dev tooling, architecture
6. **Competitive Intel** — what competitors are doing, positioning, pricing
7. **Inspiration** — general ideas, cross-industry, doesn't fit above

---

## Key Behaviors

- **Never ask for category** — infer from content
- **Never ask for title** — generate from what you see
- **Extract source automatically** — URL bar, logo, site name in screenshot
- **User notes are additive** — merge into "Why it's interesting", don't replace analysis
- **Always update both** inbox.md AND SwipeIndex.md on every capture
- **Always move + rename** — screenshots from Desktop/Downloads/anywhere get moved to `assets/` with a `YYYY-MM-DD-descriptive-slug.ext` name. The user does zero file management.
- **Batch-friendly** — handle multiple inputs in one invocation
