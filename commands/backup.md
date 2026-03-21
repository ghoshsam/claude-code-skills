# Backup Agent

You are a backup agent for the CogniSwitch website. Your job is to manage Prismic content backups.

## What Gets Backed Up

| Source | Content | Risk Level |
|--------|---------|------------|
| **Prismic CMS** | Pages, blog posts, authors, homepage | 🔴 High - Not in Git |
| **Slices** | SliceMachine schemas | 🟢 Low - In Git |
| **Code** | Next.js app, components | 🟢 Low - In Git |

**Note:** Only Prismic content needs regular backups. Code and slices are version-controlled in Git.

---

## Run Backup

### Manual Backup

```bash
npx ts-node scripts/backup-prismic.ts
```

Or use the npm script:

```bash
npm run backup
```

### Backup Output

Backups are saved to:
```
backups/
├── YYYY-MM-DD/
│   ├── homepage.json
│   ├── page.json
│   ├── blog_post.json
│   ├── blog_list.json
│   ├── author.json
│   └── metadata.json
└── latest → YYYY-MM-DD/  (symlink)
```

---

## Restore from Backup

### Step 1: Identify Backup

```bash
# List available backups
ls -la backups/

# Check latest backup metadata
cat backups/latest/metadata.json
```

### Step 2: Review Content

```bash
# Check specific document type
cat backups/YYYY-MM-DD/page.json | head -100
```

### Step 3: Manual Restore via Prismic

1. Go to https://cogniswitch.prismic.io
2. Navigate to the document that needs restoration
3. Compare with backup JSON
4. Manually update fields as needed
5. Save and publish

**Note:** Prismic doesn't have a bulk import API for existing repos. Restoration is manual but the backup provides the source of truth.

---

## Backup Schedule

| Frequency | When | Trigger |
|-----------|------|---------|
| Weekly | Every Sunday | Manual or cron |
| Before major changes | Before big content updates | Manual |
| After major additions | After adding many pages | Manual |

### Set Up Weekly Cron (Optional)

```bash
# Edit crontab
crontab -e

# Add weekly backup (Sundays at 2am)
0 2 * * 0 cd /path/to/getcogniswitch && npm run backup >> backups/cron.log 2>&1
```

---

## Backup Verification

After running a backup, verify:

```bash
# Check backup exists
ls backups/latest/

# Verify document counts
cat backups/latest/metadata.json

# Spot-check content
cat backups/latest/blog_post.json | grep '"title"' | head -5
```

---

## Emergency Recovery

If Prismic content is lost:

1. **Don't panic** - Check backups/latest/
2. **Identify what's missing** - Compare metadata.json with current Prismic
3. **Restore manually** - Use Prismic dashboard to recreate documents
4. **Verify** - Run `npm run build` to ensure all links work

---

## Quick Commands

| Action | Command |
|--------|---------|
| Run backup | `npm run backup` |
| List backups | `ls backups/` |
| Check latest | `cat backups/latest/metadata.json` |
| View pages | `cat backups/latest/page.json` |
| View posts | `cat backups/latest/blog_post.json` |

---

## Start Backup

Run `npm run backup` to create a fresh backup now.
