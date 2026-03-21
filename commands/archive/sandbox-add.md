# Sandbox Add

> **For the standard AI Studio workflow** (3 versions in V1/V2/V3 folders), use `/sandbox-compare` directly — it handles bootstrap, intake, and comparison in one command.

Add a single version to an existing sandbox concept.

## When to Use This

- Adding a 4th+ version after initial comparison
- Adding a human-designed version alongside AI versions
- Non-standard folder structures

## Usage

- `/sandbox-add [folder-path]` — Register an existing folder
- `/sandbox-add` — Interactive mode

---

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

If no matching concept exists, suggest using `/sandbox-compare` instead.

### 3. Determine version number

Scan existing version folders in the concept. New version = next number (e.g., if V1, v2, v3 exist → v4).

### 4. Copy files

```bash
mkdir -p sandbox/{concept}/v{n}
cp -r {source-folder}/* sandbox/{concept}/v{n}/
```

### 5. Bootstrap if needed

If the version is a raw AI Studio export (no `package.json`), bootstrap it using the same process as `/sandbox-compare` Phase A3:
- Create `package.json` (check for `@google/genai` dependency)
- Create `vite.config.ts` (assign next available port)
- Create `index.html` (with Google Fonts link tags)
- Create `index.tsx` (React root mount)
- Create `index.css` (Tailwind v4 `@theme` with color/font aliases)
- Run `npm install` and verify

See `/sandbox-compare` for the full bootstrap templates.

### 6. Update concept.json

Add the new version to the `versions` array and `ports` map.

### 7. Confirm

```markdown
## Added to Sandbox

**Concept:** {Display Name}
**Version:** v{n} → localhost:{port}
**Location:** sandbox/{concept}/v{n}

**Next steps:**
1. Run `/sandbox-compare {concept}` to re-compare with the new version
```

---

## Related Commands

- `/sandbox-compare {concept}` — Compare all versions (bootstrap + compare + CONTEXT.md)
- `/adapt sandbox/{concept}/{winner}` — Extract winning version to production
