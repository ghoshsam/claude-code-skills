# 29 Commands. 29 Roles. One Terminal.

This is the command library behind CogniSwitch's website — a 50-page site built by one person with two AI agents, a headless CMS, and $20/month.

No designer. No agency. No Webflow. Just Claude Code, Gemini CLI, and a terminal.

The thesis is simple: **you're not managing a tool, you're managing a relationship.** These commands are how that relationship stays structured. Each one handles a role you'd otherwise hire for — strategist, designer, QA lead, DevOps. The pipeline is the same one every marketing team runs: brief, review, build, check, ship.

The full story behind this approach was presented at [AI Boomi (March 2026)](https://cogniswitch.ai/ai-boomi-workshop).

---

## Three Pillars

These commands are organized around three ideas:

**01 — Don't Write Prompts.** Describe intent. Let the AI generate the structured prompt. You refine. (`/prompt`, `/idea`, `/content`)

**02 — Orchestrate, Don't One-Shot.** Assembly line, not vending machine. Each command handles one stage. Chain them. (`/dev` -> `/review-local` -> `/deploy`)

**03 — Handoffs, Not Human-in-the-Loop.** Structured transitions between you and the AI. What was done, what to check, what's next. (`/end-session`, `/review-handoff`, `/sandbox`)

---

## The Pipeline

Same stages a marketing team runs. Faster.

| Stage | Marketing Team (2020) | Solo + AI (2026) |
|-------|----------------------|------------------|
| Concept | Concept note | `/content`, `/idea` |
| Design | Designer (2-3 versions) | `/sandbox` (strict / wild) |
| Review | PMM reviews | `/review-handoff` |
| Build | Engineer builds | `/dev` |
| QA | QA checks | `/review-local` |
| Ship | Deploy | `/deploy` |

---

## Commands by Role

### Strategist — Planning

| Command | What it does |
|---------|-------------|
| [/idea](commands/idea.md) | Fast idea capture — auto-routes to the right inbox |
| [/sprint](commands/sprint.md) | Weekly sprint planner with Build/Fix/Optimize modes |
| [/today](commands/today.md) | Daily task picker based on sprint plan |
| [/retro](commands/retro.md) | Sprint retrospective — velocity, patterns, adjustments |
| [/content](commands/content.md) | Content brainstorming — blog ideas, essay concepts |

### Designer — Exploration

| Command | What it does |
|---------|-------------|
| [/sandbox](commands/sandbox.md) | AI Studio workflow — bootstrap, compare, pick a winner |
| [/review-handoff](commands/review-handoff.md) | Review Gemini's creative work with structured feedback |
| [/swipe](commands/swipe.md) | Capture screenshots and patterns from the wild |
| [/swipe-audit](commands/swipe-audit.md) | Pattern-match your pages against collected swipes |
| [/adapt](commands/adapt.md) | Analyze a reference React app against existing slices |

### Engineer — Build

| Command | What it does |
|---------|-------------|
| [/dev](commands/dev.md) | Feature implementation with session history and branch safety |
| [/prismic-populate](commands/prismic-populate.md) | Generate copy-paste ready content for the CMS |
| [/pr-review](commands/pr-review.md) | Pull request reviewer — code quality, accessibility, security |
| [/discover](commands/discover.md) | Information architecture — internal linking, orphan prevention |
| [/extract-faqs](commands/extract-faqs.md) | Pull FAQ candidates from pages for structured data |

### QA Lead — Quality

| Command | What it does |
|---------|-------------|
| [/review-local](commands/review-local.md) | Local QA checklist — build, hydration, design system, responsive |
| [/design-review](commands/design-review.md) | Design system compliance — contrast, colors, typography |
| [/copycheck](commands/copycheck.md) | Website copy audit — spelling, grammar, em dashes, links |
| [/cta-audit](commands/cta-audit.md) | Broken links, dead buttons, malformed URLs |

### DevOps — Ship

| Command | What it does |
|---------|-------------|
| [/deploy](commands/deploy.md) | Full pipeline — design review, build, push, Vercel check, post-deploy QA |
| [/verify](commands/verify.md) | Post-deploy verification — Core Web Vitals, SEO, social sharing |
| [/page-to-pdf](commands/page-to-pdf.md) | Convert web pages and decks to PDF |
| [/push-to-slack](commands/push-to-slack.md) | Push files or messages to Slack |

### Ops — Housekeeping

| Command | What it does |
|---------|-------------|
| [/end-session](commands/end-session.md) | Session closer — cleanup, session log, graduate learnings, commit |
| [/backup](commands/backup.md) | Prismic content backup manager |
| [/inventory](commands/inventory.md) | Track built assets awaiting placement |
| [/prompt](commands/prompt.md) | Prompt generator with persona library and S-mode/U-mode |
| [/review-paper](commands/review-paper.md) | AI research paper reviewer for intelligence briefings |

### Archive

| Command | What it does |
|---------|-------------|
| [/archive:extract](commands/archive/extract.md) | Extract links from Slack channels to CSV |
| [/archive:sandbox-add](commands/archive/sandbox-add.md) | Add a version to an existing sandbox concept |
| [/archive:sandbox-compare](commands/archive/sandbox-compare.md) | Full sandbox bootstrap + compare workflow |

---

## Skills

Skills are different from commands. Commands are things you invoke (`/deploy`). Skills are knowledge that Claude loads automatically when relevant — writing guides, visual treatment patterns, design system rules.

### Custom Skills

| Skill | What it does |
|-------|-------------|
| [copyreview](skills/copyreview/) | AI writing pattern detector — 24 patterns from Wikipedia's "Signs of AI writing" |
| [blog-enhance](skills/blog-enhance/) | Visual treatment explorer for blog content |

### Plugin Skills (from Claude Code Marketplace)

<details>
<summary>Canvas TUI (4 skills)</summary>

| Skill | What it does |
|-------|-------------|
| [canvas](skills/canvas/canvas/) | Primary skill for terminal TUI components |
| [calendar](skills/canvas/calendar/) | Calendar display and meeting time picker |
| [document](skills/canvas/document/) | Markdown document display and text selection |
| [flight](skills/canvas/flight/) | Flight comparison and seat selection |
</details>

<details>
<summary>Plugin Development (7 skills)</summary>

| Skill | What it does |
|-------|-------------|
| [plugin-structure](skills/plugin-dev/plugin-structure/) | Plugin directory layout, manifest, auto-discovery |
| [skill-development](skills/plugin-dev/skill-development/) | Creating skills for Claude Code plugins |
| [command-development](skills/plugin-dev/command-development/) | Slash commands — structure, frontmatter, dynamic arguments |
| [agent-development](skills/plugin-dev/agent-development/) | Subagent definitions, triggering, system prompts |
| [hook-development](skills/plugin-dev/hook-development/) | Event-driven hooks (PreToolUse, PostToolUse, Stop) |
| [mcp-integration](skills/plugin-dev/mcp-integration/) | Model Context Protocol server integration |
| [plugin-settings](skills/plugin-dev/plugin-settings/) | Plugin configuration via .local.md files |
</details>

<details>
<summary>Productivity & Tooling (7 skills)</summary>

| Skill | What it does |
|-------|-------------|
| [skill-creator](skills/skill-creator/) | Create, test, benchmark, and optimize skills |
| [claude-automation-recommender](skills/claude-code-setup/claude-automation-recommender/) | Analyze a codebase and recommend automations |
| [claude-md-improver](skills/claude-md-management/claude-md-improver/) | Audit and improve CLAUDE.md files |
| [frontend-design](skills/frontend-design/) | Production-grade frontend with high design quality |
| [playground](skills/playground/) | Self-contained HTML playgrounds with interactive controls |
| [writing-rules](skills/hookify/writing-rules/) | Hookify rule syntax and patterns |
| [stripe-best-practices](skills/stripe-best-practices/) | Best practices for Stripe integrations |
</details>

<details>
<summary>Channel Skills (4 skills)</summary>

| Skill | What it does |
|-------|-------------|
| [discord:access](skills/discord/access/) | Discord channel access management |
| [discord:configure](skills/discord/configure/) | Discord channel setup |
| [telegram:access](skills/telegram/access/) | Telegram channel access management |
| [telegram:configure](skills/telegram/configure/) | Telegram channel setup |
</details>

<details>
<summary>Example Skills (2 skills)</summary>

| Skill | What it does |
|-------|-------------|
| [example-skill](skills/example-plugin/example-skill/) | Reference template for skill structure |
| [example-command](skills/example-plugin/example-command/) | Example user-invoked command in skills format |
</details>

---

## Using These

Copy individual commands or skills into your project's `.claude/` folder:

```bash
# A command
cp commands/deploy.md your-project/.claude/commands/deploy.md

# A skill
cp -r skills/copyreview/ your-project/.claude/skills/copyreview/
```

Most of these are CogniSwitch-specific (Prismic, our design system, our editorial pipeline). Yours will look different. What transfers is the approach: document your process, give each stage a name, and let the AI execute the parts you've already figured out.

---

**Built by** [Vivek Khandelwal](https://cogniswitch.ai/about) at CogniSwitch.

**License:** MIT
