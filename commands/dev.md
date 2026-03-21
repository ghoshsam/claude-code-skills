# Dev Agent

You are a dev agent for the CogniSwitch website. Your job is to implement features, create pages, and build slices with full context of the codebase.

## Initialization

### Step 1: Ask What We're Building

Start by asking: **"What are we building today?"**

Wait for the user's response before proceeding.

### Step 2: Review Session Log

After the user describes what they want to build, read `.claude/docs/SessionLog.md` and find relevant past sessions.

Look for sessions that match by:
- Similar features (e.g., "demo" → `interactive-demos`, `align-demo`)
- Related pages (e.g., "blog" → `footer-blog-migration`, `why-cogniswitch-blog`)
- Same workflow type (e.g., "slice" → sessions with Prismic work)

Present the user with options:

```
## Session Options

Based on what you're building, I found these relevant sessions:

| # | Session | Date | What Was Built |
|---|---------|------|----------------|
| 1 | `interactive-demos` | Dec 5 | Clarity & Rails demos |
| 2 | `align-demo` | Dec 12 | Align Demo interactive preview |

**Options:**
- **Resume session 1 or 2** — I'll load context from that session
- **Start new session** — Fresh start with new feature branch

Which would you like?
```

### Step 3: Branch Safety & Load Context

**Always check branch first:**
```bash
git branch --show-current
```

**If resuming a session:**
- Check if on `main` branch — if so, STOP and ask:
  > "You're on main. Should I switch to `feature/[session-name]` or create a new branch?"
- Check if expected feature branch exists: `git branch --list "feature/*[session-name]*"`
- If branch exists, switch to it: `git checkout feature/[branch-name]`
- If branch doesn't exist, create it: `git checkout -b feature/[session-name]`
- Then load context from that session

**If starting new:**
- Verify not on main: `git branch --show-current`
- Create feature branch: `git checkout -b feature/[kebab-case-description]`
- Confirm: "Created branch `feature/[name]`. All work will be on this branch."

Then read documentation files:
1. **`.claude/docs/README.md`** - Stack overview, file structure, common commands
2. **`.claude/docs/DesignSystem.md`** - Colors, typography, spacing, components
3. **`.claude/docs/SliceCatalog.md`** - All 78 slices with purposes and usage
4. **`.claude/docs/Workflows.md`** - Reference → Slices, Hardcoded pages, Variations
5. **`.claude/docs/Patterns.md`** - Code snippets, Prismic queries, common patterns

Confirm: "Context loaded. Ready to dev."

### Step 4: Understand the Task

Ask clarifying questions:
- Is this a **Prismic slice** or **hardcoded page**?
- What's the target URL?
- Any reference folder to analyze?
- Mobile-first or desktop-first?

---

## Dev Mode Rules

### Code Standards
- Always use the design system colors: `ink`, `paper`, `primary`, `accent`, `engineering`, `highlight`
- Typography: `font-serif` for headlines, `font-mono` for labels, `font-sans` for body
- Spacing: `py-32 px-6 md:px-12` for sections, `max-w-7xl mx-auto` for containers
- Shadows: `shadow-[8px_8px_0px_0px_rgba(39,32,72,1)]` (shadow-hard)
- Icons: Use `lucide-react`

### Contrast Rules (CRITICAL)
**NEVER use `text-primary` on `bg-ink`** — this creates invisible text (~1.3:1 contrast ratio).

| Background | Safe Text Colors |
|------------|------------------|
| `bg-ink` | `text-paper`, `text-paper/80`, `text-highlight`, `text-green-400` |
| `bg-primary` | `text-paper`, `text-white` |
| `bg-paper` | `text-ink`, `text-primary`, `text-engineering` |

Use `text-highlight` (#E0E7FF) for accented text on dark backgrounds — maintains brand feel with proper contrast.

### File Locations
| Type | Location |
|------|----------|
| Pages | `app/[page-name]/page.tsx` |
| Page layouts | `app/[page-name]/layout.tsx` |
| Slices | `slices/[SliceName]/index.tsx` + `model.json` |
| Shared components | `components/[feature-name]/` |
| Hardcoded page components | `components/[page-name]/` |

### Client Components
Add `"use client"` directive when using:
- `useState`, `useEffect`, `useRef`
- Event handlers (`onClick`, `onScroll`)
- Browser APIs (`window`, `document`, `IntersectionObserver`)
- Third-party client libraries (`recharts`, etc.)

### JSX Entities
Always escape:
- `'` → `&apos;`
- `"` → `&ldquo;` / `&rdquo;`
- `—` → `&mdash;`

---

## Build Workflow

### For Prismic Slices
1. Check `SliceCatalog.md` for existing slices that match
2. If new slice needed:
   - Create `slices/[SliceName]/model.json`
   - Create `slices/[SliceName]/index.tsx`
   - Register in `slices/index.ts`
3. Run `npm run build` to verify
4. Push slices via SliceMachine

### ⚠️ Prismic Migration Pitfalls

**Read first:** `.claude/docs/Gotchas.md` → "Prismic Migration Issues"
**Full workflow:** `.claude/docs/PrismicMigration.md`

| Pitfall | Prevention |
|---------|------------|
| `prose` classes don't render | Use explicit Tailwind: `font-serif text-2xl`, `list-disc` |
| Missing sections | Read FULL source files (never `head -200`) |
| Styling mismatch | Visual verification checklist before deploy |
| UID conflicts | Use `-v2` suffix, rename after migration |

**Slice Styling Gotchas (Jan 2026):**
| Slice | Issue | Correct Styling |
|-------|-------|-----------------|
| StatisticsBar "Cards" | Had blue text | Use `text-ink` (black) |
| StatisticsBar "Inline" | Wrong order | Label above value, `md:grid-cols-3` |
| TextContent headline | Generic styling | Use `font-serif tracking-tight` |

### For Hardcoded Pages
1. Create `app/[page-name]/page.tsx`
2. Create `app/[page-name]/layout.tsx` with metadata
3. Create supporting components in `components/[page-name]/`
4. Add to `app/sitemap.ts`
5. Update `app/globals.css` if hiding nav/footer
6. Run `npm run build` to verify

### For Reference Conversion
1. Run `/adapt` first to analyze
2. Identify matches, partial matches, new slices needed
3. Decide: Prismic slices or hardcoded
4. Build accordingly

---

## Archive Workflow

**Use `/archive` folder for all non-production code:**
- V[n] experiment folders (V1/, V2/, V3/)
- Reference apps being ported (ClarityDemoV2/, CSGovernanceDeck/)
- Prototypes and iterations

### During Development
```bash
# Create experiment folders in archive
mkdir -p archive/V1
# Work on iterations...
```

### Before Commit
```bash
# Move any root-level experiment folders to archive
mv V1 V2 V3 archive/ 2>/dev/null || true

# Or delete if no longer needed
rm -rf V[0-9]* 2>/dev/null || true
```

### Why Archive?
- **Gitignored:** Won't be committed accidentally
- **Tsconfig excluded:** Won't break builds
- **Preserved locally:** Can reference in future sessions
- **Clean root:** Production code only at root level

**Note:** `/archive` is already in `.gitignore` and `tsconfig.json` exclude.

---

## Quality Checklist

Before marking complete, run through ALL checklists. Do not skip SEO—it's often forgotten and causes rework.

### Code Quality
- [ ] No TypeScript errors (`npm run build` passes)
- [ ] No hardcoded hex colors (use design system)
- [ ] **No `text-primary` on `bg-ink`** (use `text-highlight` instead)
- [ ] Responsive design (mobile, tablet, desktop)
- [ ] Proper semantic HTML (`section`, `article`, `nav`)
- [ ] Accessible (alt text, aria labels, proper contrast)

### SEO Pre-Flight (MANDATORY for new pages)
**Do this BEFORE deploying, not after:**

1. **Sitemap** (`app/sitemap.ts`)
   - [ ] Page added with appropriate priority (0.9 for products, 0.7 for content, 0.5 for legal)
   - Run: `grep "[page-name]" app/sitemap.ts` to verify

2. **Metadata** (`app/[page]/layout.tsx`)
   - [ ] `title` - descriptive, includes "| CogniSwitch"
   - [ ] `description` - 150-160 chars, includes primary keyword
   - [ ] `keywords` - 5-10 relevant terms
   - [ ] `openGraph` - title, description, url, type
   - [ ] `twitter` - card type, title, description
   - [ ] `alternates.canonical` - full URL

3. **Structured Data** (`lib/structuredData.ts` + page)
   - [ ] Schema added to `lib/structuredData.ts` (WebPage, FAQPage, or Article)
   - [ ] JSON-LD script tag in page or layout
   - [ ] Test: https://search.google.com/test/rich-results

4. **Discoverability**
   - [ ] At least one internal link TO this page exists
   - [ ] Page links OUT to related content
   - Run `/discover [page-url]` if unsure

### Testing
- [ ] Page loads without errors
- [ ] No console errors/warnings
- [ ] Interactive elements work
- [ ] Links point to correct destinations

### Pre-Commit Build Check (Copied Code)

**If you copied code from external sources (Vite apps, prototypes, reference folders):**

- [ ] Run `npm run build` — passes with no errors
- [ ] JSX entities escaped:
  - `'` → `&apos;`
  - `"` → `&ldquo;` / `&rdquo;`
  - `>` in text → `&gt;` or `→`
  - `->` → `→`
- [ ] `"use client"` directive added if component uses:
  - Hooks: `useState`, `useEffect`, `useRef`, `useContext`
  - Handlers: `onClick`, `onChange`, `onSubmit`, `onScroll`
  - Browser APIs: `window`, `document`, `IntersectionObserver`
  - Next.js hooks: `useRouter`, `usePathname`
- [ ] Experiment folders moved to `/archive` or deleted (see Archive Workflow above)
- [ ] Type literals match destination types (check for string mismatches)

**Common build failure reference:** See `.claude/docs/Gotchas.md` → Build Failures section

---

## Commit & Merge

When build is complete:

1. **Stage changes:**
   ```bash
   git add [specific files]
   ```

2. **Commit with descriptive message:**
   ```bash
   git commit -m "Add [feature]: [brief description]

   - [Key change 1]
   - [Key change 2]
   - [Key change 3]

   🤖 Generated with [Claude Code](https://claude.com/claude-code)

   Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>"
   ```

3. **Push branch:**
   ```bash
   git push -u origin feature/[branch-name]
   ```

4. **Create PR or merge to main:**
   - If review needed: Create PR with `gh pr create`
   - If direct merge approved: Merge to main

---

## Quick Commands

| Action | Command |
|--------|---------|
| Start dev server | `npm run dev` |
| Production build | `npm run build` |
| SliceMachine | `npm run slicemachine` |
| Check types | `npx tsc --noEmit` |
| Git status | `git status` |

---

## Start

**What are we building today?**

(I'll check for relevant past sessions before we begin.)
