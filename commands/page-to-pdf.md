# Page to PDF

Convert a web page (especially slide decks from InteractiveApps) to PDF using Puppeteer.

## Input Required

Ask the user: **"Which page do you want to convert to PDF? Provide either:"**
- A local URL (e.g., `http://localhost:5173`)
- An InteractiveApps folder name (e.g., `CSGovernanceDeck`)

---

## Workflow

### Step 1: Identify the Source

**If user provides a URL:**
- Use the URL directly
- Confirm the dev server is running

**If user provides an InteractiveApps folder name:**
1. Verify the folder exists: `ls InteractiveApps/<folder-name>/`
2. Start the dev server:
   ```bash
   cd InteractiveApps/<folder-name> && npm install && npm run dev
   ```
3. Note the URL (usually `http://localhost:5173`)

### Step 2: Ask for Output Location

Ask: **"Where should I save the PDF?"**

Suggest: `Content/<FolderName>.pdf` or `~/Desktop/<FolderName>.pdf`

### Step 3: Generate PDF

Run the page-to-pdf script:

```bash
npx ts-node scripts/page-to-pdf.ts <url> <output.pdf> [options]
```

**Available Options:**
| Option | Description | Default |
|--------|-------------|---------|
| `--landscape` | Landscape orientation | Portrait |
| `--wait=<ms>` | Wait after page load | 2000ms |
| `--format=<size>` | A4, Letter, Legal, Tabloid | A4 |
| `--scale=<n>` | Scale factor (0.1-2) | 1 |
| `--full-page` | Full scrollable height | false |

**Example Commands:**
```bash
# Basic export
npx ts-node scripts/page-to-pdf.ts http://localhost:5173 Content/GovernanceDeck.pdf

# Landscape with longer wait
npx ts-node scripts/page-to-pdf.ts http://localhost:5173 deck.pdf --landscape --wait=5000

# Letter format, scaled down
npx ts-node scripts/page-to-pdf.ts http://localhost:5173 deck.pdf --format=Letter --scale=0.9
```

### Step 4: Verify Output

1. Confirm PDF was created
2. Report file size
3. Offer to open: `open <output.pdf>`

---

## Common Use Cases

### Export CSGovernanceDeck
```bash
cd InteractiveApps/CSGovernanceDeck && npm install && npm run dev
# In new terminal:
npx ts-node scripts/page-to-pdf.ts http://localhost:5173 Content/GovernanceDeck.pdf
```

### Export Pitch Deck
```bash
cd "InteractiveApps/Pitch Deck" && npm install && npm run dev
# In new terminal:
npx ts-node scripts/page-to-pdf.ts http://localhost:5173 Content/PitchDeck.pdf --landscape
```

### Export Any Local Page
```bash
npm run dev
# In new terminal:
npx ts-node scripts/page-to-pdf.ts http://localhost:3000/deck Content/WebsiteDeck.pdf
```

---

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Cannot find module puppeteer" | Run `npm install` in project root |
| PDF is blank | Increase `--wait` time for heavy pages |
| Content cut off | Try `--landscape` or `--scale=0.8` |
| Dev server not running | Start with `npm run dev` first |
| Fonts look wrong | Ensure web fonts are loaded (increase wait) |

---

## Output Format

```
📄 Page to PDF Converter

  URL:        http://localhost:5173
  Output:     Content/GovernanceDeck.pdf
  Format:     A4 (portrait)
  Wait:       2000ms
  Scale:      1

  Launching browser...
  Navigating to http://localhost:5173...
  Waiting 2000ms for animations/rendering...
  Triggering lazy-loaded content...
  Generating PDF...

  ✅ PDF created successfully!
  📁 /path/to/Content/GovernanceDeck.pdf (2.45 MB)
```

---

## Quick Reference

```bash
# One-liner for CSGovernanceDeck (if dev server already running)
npx ts-node scripts/page-to-pdf.ts http://localhost:5173 Content/CSGovernanceDeck.pdf
```
