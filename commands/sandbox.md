# Sandbox - AI Studio Workflow Manager

Unified command for managing AI Studio sandbox workflows: bootstrap, compare, and add versions.

## Usage

**Compare mode** (primary workflow):
- `/sandbox compare {concept}` — Compare all versions in a concept folder
- `/sandbox compare` — Interactive mode, lists available concepts
- `/sandbox-compare {concept}` — Legacy alias (still works)

**Add mode** (4th+ versions):
- `/sandbox add {folder-path}` — Add a single version to existing concept
- `/sandbox add` — Interactive mode
- `/sandbox-add {folder-path}` — Legacy alias (still works)

**Interactive mode:**
- `/sandbox` — Shows menu: "Compare concept or Add version?"

---

## Mode Detection

**If invoked with `compare` or folder path matching `sandbox/*/`:**
→ Run **Compare Mode** (Phase A-C below)

**If invoked with `add` or unrecognized path:**
→ Run **Add Mode** (Steps 1-7 below)

**If invoked with no arguments:**
→ Ask: "What would you like to do?"
  1. **Compare versions** (bootstrap + compare + CONTEXT.md)
  2. **Add a version** (to existing concept)

---

# COMPARE MODE

Drop → Bootstrap → Compare → Tweak. The primary AI Studio workflow.

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
| 5th+ versions | 3004+ |

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

Use the template from `sandbox-compare.md` Phase C (lines 272-371) — includes:
- Concept description and winner rationale
- Running instructions with port assignments
- AI Studio export notes (Tailwind v4, color aliases, Gemini API)
- CogniSwitch design system quick reference
- Mapping table: Sandbox → Production tokens
- Relevant production slices (from SliceCatalog.md)
- Tweaking guidelines

---

## Output Summary (Compare Mode)

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

# ADD MODE

Add a single version to an existing sandbox concept.

## When to Use This

- Adding a 4th+ version after initial comparison
- Adding a human-designed version alongside AI versions
- Non-standard folder structures

## Steps

### 1. Identify the version

**If folder path provided:**
Read folder contents. Check if `package.json` exists (already bootstrapped) or if it's a raw AI Studio export.

**If interactive:**
Ask: Where is the app folder?

### 2. Associate with a concept

```bash
ls -d sandbox/*/ 2>/dev/null
```

Show existing concepts. Ask which one this version belongs to.

If no matching concept exists, suggest using `/sandbox compare` instead.

### 3. Determine version number

Scan existing version folders in the concept. New version = next number (e.g., if V1, v2, v3 exist → v4).

### 4. Copy files

```bash
mkdir -p sandbox/{concept}/v{n}
cp -r {source-folder}/* sandbox/{concept}/v{n}/
```

### 5. Bootstrap if needed

If the version is a raw AI Studio export (no `package.json`), bootstrap it using the same process as Compare Mode Phase A3:
- Create `package.json` (check for `@google/genai` dependency)
- Create `vite.config.ts` (assign next available port: 3000, 3001, 3002, 3003, 3004+)
- Create `index.html` (with Google Fonts link tags)
- Create `index.tsx` (React root mount)
- Create `index.css` (Tailwind v4 `@theme` with color/font aliases)
- Run `npm install` and verify

### 6. Update concept.json

Add the new version to the `versions` array and `ports` map.

### 7. Confirm

```markdown
## Added to Sandbox

**Concept:** {Display Name}
**Version:** v{n} → localhost:{port}
**Location:** sandbox/{concept}/v{n}

**Next steps:**
1. Run `/sandbox compare {concept}` to re-compare with the new version
```

---

## Related Commands

- `/adapt sandbox/{concept}/{winner}` — Extract winning version to production slices
- `/design-review sandbox/{concept}/{winner}` — Check design system compliance
