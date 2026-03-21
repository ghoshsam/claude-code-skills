# Sandbox Compare

Drop → Bootstrap → Compare → Tweak. One command for the AI Studio workflow.

## Usage

- `/sandbox-compare {folder}` — e.g., `/sandbox-compare 2026-02-08-audits`
- `/sandbox-compare` — Interactive mode, lists available concepts

---

## Phase A: Auto-Detect & Bootstrap

### A1. Resolve the concept folder

**If argument provided:**
Look for `sandbox/{argument}/`. Validate it exists.

**If no argument:**
```bash
ls -d sandbox/*/ 2>/dev/null
```
Show numbered list (excluding `archive/` and `archived/`), ask user to pick.

### A2. Auto-detect versions

List all subfolders inside the concept folder (skip files like `concept.json`, `.DS_Store`).

Sandbox concepts use different folder patterns:

| Pattern | Examples | Source |
|---------|----------|--------|
| `V{n}` / `v{n}` | `V1`, `v2`, `v3` | AI Studio (all Gemini) |
| `{source}-V{n}` | `claude-v1`, `gemini-V1`, `Human-v1` | Multi-source |
| Named folders | `Gartner Deck 2`, `CogniSwitch Animation Homepage` | Named iterations |

Report: "Found {n} versions: {list of folder names}"

### A3. Bootstrap AI Studio exports

**This is the critical step.** Google AI Studio exports are **incomplete** — they produce `App.tsx`, `components/`, `types.ts`, and a few config files, but they're missing everything needed to actually run:

For each version folder, check if it can run (`package.json` exists). If not, create the missing scaffolding:

#### Missing file 1: `package.json`

```json
{
  "name": "{kebab-case-version-name}",
  "private": true,
  "version": "0.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "@tailwindcss/vite": "^4.1.18",
    "lucide-react": "^0.563.0",
    "react": "^19.2.4",
    "react-dom": "^19.2.4",
    "tailwindcss": "^4.1.18"
  },
  "devDependencies": {
    "@types/node": "^22.14.0",
    "@vitejs/plugin-react": "^5.0.0",
    "typescript": "~5.8.2",
    "vite": "^6.2.0"
  }
}
```

**Check for Gemini API usage:** If a `services/geminiService.ts` (or any file importing `@google/genai`) exists, add `"@google/genai": "^1.30.0"` to dependencies.

#### Missing file 2: `vite.config.ts`

Assign **different ports** to each version to allow side-by-side localhost:

| Version | Port |
|---------|------|
| 1st version | 3000 |
| 2nd version | 3001 |
| 3rd version | 3002 |
| 4th version | 3003 |

```typescript
import path from 'path';
import { defineConfig, loadEnv } from 'vite';
import react from '@vitejs/plugin-react';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig(({ mode }) => {
    const env = loadEnv(mode, '.', '');
    return {
      server: {
        port: {PORT},
        host: '0.0.0.0',
      },
      plugins: [react(), tailwindcss()],
      define: {
        'process.env.API_KEY': JSON.stringify(env.GEMINI_API_KEY),
        'process.env.GEMINI_API_KEY': JSON.stringify(env.GEMINI_API_KEY)
      },
      resolve: {
        alias: {
          '@': path.resolve(__dirname, '.'),
        }
      }
    };
});
```

#### Missing file 3: `index.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>{Version Name}</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600&family=JetBrains+Mono:wght@400;500&family=Newsreader:ital,opsz,wght@0,6..72,400;0,6..72,500;1,6..72,400&display=swap" rel="stylesheet">
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/index.tsx"></script>
  </body>
</html>
```

**Google Fonts must be loaded here** — AI Studio components reference `font-serif` (Newsreader), `font-sans` (Inter), `font-mono` (JetBrains Mono) but never include the font link tags.

#### Missing file 4: `index.tsx`

```tsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';

const rootElement = document.getElementById('root');
if (!rootElement) {
  throw new Error("Could not find root element to mount to");
}

const root = ReactDOM.createRoot(rootElement);
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

#### Missing file 5: `index.css`

AI Studio uses **Tailwind v4** with `@theme` (not `tailwind.config.js`). Check the version's `types.ts` or `App.tsx` for color/font token names used, and define them:

```css
@import "tailwindcss";

@theme {
  --color-paper: #f4f4f0;
  --color-paper-muted: #e5e5e0;
  --color-paper-light: #fcfcf9;
  --color-ink: #272048;
  --color-primary: #0808BA;
  --color-electric: #0808BA;
  --color-engineering: #DC2626;
  --color-alert: #DC2626;
  --color-highlight: #E0E7FF;
  --color-subtle: #6B7280;
  --color-ink-dim: #6B7280;
  --color-ink-light: #9CA3AF;

  --font-serif: 'Newsreader', serif;
  --font-sans: 'Inter', sans-serif;
  --font-mono: 'JetBrains Mono', monospace;

  --shadow-hard: 8px 8px 0px 0px rgba(39, 32, 72, 1);
}
```

**Important:** AI Studio generates non-standard token names (`electric`, `alert`, `ink-dim`). Define them as aliases pointing to production values so the exported code works without renaming every class.

### A4. Install & verify

For each version:
```bash
cd sandbox/{concept}/{version} && npm install && npm run dev
```

Run each version on its assigned port. Confirm it renders. Kill the dev server after verifying.

Report the status:
```
Bootstrap complete:
  V1 → localhost:3000 ✓
  v2 → localhost:3001 ✓
  v3 → localhost:3002 ✓
```

### A5. Ensure concept.json exists

Read `sandbox/{concept}/concept.json`.

**If it exists:** Load it and proceed to Phase B.

**If it does NOT exist:** Ask 3 quick questions:

1. **What's this concept about?** (one sentence)
2. **Target page URL?** (e.g., `/products/audit`, `/`)
3. **Primary audience?** (e.g., "GRC leaders", "Healthcare CISOs")

Then generate `sandbox/{concept}/concept.json`:
```json
{
  "displayName": "{derived from folder name}",
  "description": "{answer to Q1}",
  "targetPage": "{answer to Q2}",
  "audience": "{answer to Q3}",
  "created": "{today}",
  "versions": ["{folder1}", "{folder2}", "{folder3}"],
  "ports": { "{folder1}": 3000, "{folder2}": 3001, "{folder3}": 3002 },
  "winner": null
}
```

---

## Phase B: Quick Compare

### B1. Read each version

For each version folder, read:
- `App.tsx` (or `src/App.tsx`) — the main component
- Key files in `components/` — focus on interactive ones
- `types.ts` — for the THEME object and type definitions

Skim — focus on structure and interactive elements, not every line.

**Watch for scale differences:** Some AI Studio versions are a single page, others can be full site prototypes (50+ components). If a version is a full site, identify which page/component is the relevant one for this concept.

### B2. Generate comparison table

Produce a concise Markdown table. Use actual folder names as column headers:

| Dimension | {folder1} | {folder2} | {folder3} |
|-----------|-----------|-----------|-----------|
| **Structure** | {How many sections, narrative arc, page flow} | | |
| **Interactivity** | {Interactive components: sliders, calculators, simulators, tabs, accordions} | | |
| **Production-readiness** | {Has CTA? Uses design tokens? Copy quality? Mobile-aware?} | | |
| **Standout element** | {The single best thing in this version} | | |

Keep each cell to 1-2 sentences max. Adapt column count to however many versions exist.

### B3. Recommend and pick

Recommend a winner with one-sentence reasoning. Ask user to confirm or pick a different version.

### B4. Record the winner

Update `sandbox/{concept}/concept.json`:
- Set `"winner": "{selected folder name}"`
- Set `"comparedOn": "{today}"`

---

## Phase C: Generate CONTEXT.md

Generate `sandbox/{concept}/CONTEXT.md` — the **context bridge** for any future Claude session tweaking the winner.

**This is the most important output.** A future session should be able to read ONLY this file and immediately start working — no re-learning the codebase, no re-discovering the design system, no re-figuring out how to run the app.

### CONTEXT.md Template

```markdown
# Context: {Concept Display Name}

> Read this file before making any changes to this sandbox concept.

## What This Is
{One paragraph: what the concept is, what it's for, who it targets, target page URL}

## Winner: {version folder name}
**Why it won:** {One sentence from the comparison}
**Preserve:** {Key strengths to keep — interactive components, structure, etc.}
**Pull from other versions:** {Specific components/patterns from losing versions worth incorporating}

## Running Locally

The winner runs at:
```bash
cd sandbox/{concept}/{winner} && npm run dev
# → localhost:{port}
```

All versions have been bootstrapped and can run side-by-side:
| Version | Port | Command |
|---------|------|---------|
| {folder1} | {port1} | `cd sandbox/{concept}/{folder1} && npm run dev` |
| {folder2} | {port2} | `cd sandbox/{concept}/{folder2} && npm run dev` |
| {folder3} | {port3} | `cd sandbox/{concept}/{folder3} && npm run dev` |

## AI Studio Export Notes

These apps were generated by Google AI Studio. Key things to know:

- **Tailwind v4** with `@theme` block in `index.css` (not `tailwind.config.js`)
- **Color token aliases:** AI Studio uses non-standard names. The `index.css` maps them:
  - `electric` → `primary` (#0808BA)
  - `alert` → `engineering` (#DC2626)
  - `ink-dim` → `subtle` (#6B7280)
  - `ink-light` → #9CA3AF
- **Icons:** lucide-react
- **THEME object** in `types.ts` — contains reusable style presets (eyebrow, headline, etc.)
- **Gemini API:** {If applicable: "v3 includes services/geminiService.ts — needs GEMINI_API_KEY in .env.local" | If not: "No API keys needed"}

## CogniSwitch Design System (Quick Reference)

### Colors
| Token | Value | Usage |
|-------|-------|-------|
| `ink` | `#272048` | Primary text, dark surfaces |
| `paper` | `#f4f4f0` | Background, light surfaces |
| `paper-muted` | `#e5e5e0` | Subtle backgrounds, info boxes |
| `paper-light` | `#fcfcf9` | Elevated surfaces, cards |
| `primary` | `#0808BA` | Accent, links, highlights |
| `engineering` | `#DC2626` | Warning, critical |
| `highlight` | `#E0E7FF` | Selection, emphasis bg |
| `subtle` | `#6B7280` | Secondary text, labels |

**CRITICAL:** NEVER use `text-primary` on `bg-ink` — invisible (dark blue on dark purple).

### Typography
| Font | Tailwind | Usage |
|------|----------|-------|
| Newsreader | `font-serif` | Headlines, editorial |
| Inter | `font-sans` | Body text, UI elements |
| JetBrains Mono | `font-mono` | Code, labels, tags |

### Layout & Spacing
- Section padding: `py-32 px-6 md:px-12`
- Max width: `max-w-7xl mx-auto`
- Shadow (brutalist): `shadow-[8px_8px_0px_0px_rgba(39,32,72,1)]`

## Mapping: Sandbox → Production

| Sandbox Token / Pattern | Production Equivalent |
|---|---|
| {detected pattern from winner's code} | {matching production class/token} |

_Populated by scanning the winner's code for non-standard classes and mapping to design system._

## Relevant Production Slices

{5-10 slices from SliceCatalog.md that match this concept's content type}

Source: `.claude/docs/SliceCatalog.md`

## When Tweaking
- **Keep interactive components** — they're the value-add over static pages
- **Align colors/typography** to the design system values above
- **Escape JSX entities:** `'` → `&apos;`, `"` → `&ldquo;`/`&rdquo;`, `&` → `&amp;`
- **Add "use client"** directive if using `useState`, `useEffect`, or `onClick`
- **For full design system:** read `.claude/docs/DesignSystem.md`
- **For all 99 slices:** read `.claude/docs/SliceCatalog.md`
- **For code patterns:** read `.claude/docs/Patterns.md`
```

### How to fill in the template

**Mapping table:** Scan the winner's `App.tsx` and components for inline styles, custom colors, font declarations, and class names. Map each to the nearest CogniSwitch design token.

**Relevant slices:** Read `.claude/docs/SliceCatalog.md` and pick slices whose purpose matches the concept (hero, features, comparison, CTA, etc.).

**AI Studio notes:** Check if any version uses `@google/genai` or has `services/` folder. Note which color aliases were defined in `index.css`.

---

## Output Summary

After all 3 phases, display:

```markdown
## Compare Complete

**Concept:** {Display Name}
**Versions:** {list with ports}
**Winner:** {folder name} → localhost:{port}

**Files generated/updated:**
- `sandbox/{concept}/concept.json` — winner + port assignments
- `sandbox/{concept}/CONTEXT.md` — context bridge for tweaking sessions
- Bootstrap files in each version (package.json, vite.config.ts, index.html, index.tsx, index.css)

**Next steps:**
1. Open new Claude session
2. Read `sandbox/{concept}/CONTEXT.md`
3. Tweak the winner at localhost:{port}
4. Run `/adapt` to extract for production
```

---

## Related Commands

- `/sandbox-add` — Add a single version to an existing concept
- `/adapt sandbox/{concept}/{winner}` — Extract winning version to production slices
- `/design-review sandbox/{concept}/{winner}` — Check design system compliance
