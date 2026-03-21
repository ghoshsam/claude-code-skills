# Claude Code Skills & Commands

A collection of skills (SKILL.md) and slash commands for [Claude Code](https://claude.com/claude-code) — Anthropic's CLI for Claude.

---

## Custom Project Skills

| Skill | Description |
|-------|-------------|
| [copyreview](skills/copyreview/) | AI writing pattern detector — 24 patterns based on Wikipedia's "Signs of AI writing" |
| [blog-enhance](skills/blog-enhance/) | Visual treatment explorer for blog content (two-phase: explore then build) |

---

## Custom Slash Commands

### Session & Project Management

| Command | Description |
|---------|-------------|
| [/end-session](commands/end-session.md) | Session closer — cleanup experiments, write session log, graduate learnings, verify build, commit |
| [/dev](commands/dev.md) | Dev agent — feature implementation with session history, branch safety, build workflow |
| [/deploy](commands/deploy.md) | Full deployment pipeline — design review, build, push, Vercel verification, post-deploy QA |
| [/sprint](commands/sprint.md) | Weekly sprint planner — task pool from backlog with Build/Fix/Optimize modes |
| [/today](commands/today.md) | Daily task picker — surfaces today's task based on sprint plan and work mode |
| [/retro](commands/retro.md) | Sprint retrospective — completion metrics, time accuracy, patterns, velocity trends |

### Content & Editorial

| Command | Description |
|---------|-------------|
| [/content](commands/content.md) | Content brainstorming agent — blog ideas, essay concepts, thought leadership |
| [/idea](commands/idea.md) | Fast idea capture — auto-routes to Signal inbox or Website ideas |
| [/prompt](commands/prompt.md) | Prompt generator with U-mode/S-mode, persona library, and prompt management |
| [/review-paper](commands/review-paper.md) | AI research paper reviewer — generates structured review for Measured briefing |

### Quality Assurance

| Command | Description |
|---------|-------------|
| [/copycheck](commands/copycheck.md) | Website copy audit — grammar, British/American spelling, em dashes, broken links |
| [/cta-audit](commands/cta-audit.md) | CTA and link checker — broken links, dead buttons, malformed URLs |
| [/verify](commands/verify.md) | Post-deploy verification — Core Web Vitals, SEO, social sharing, smoke test |
| [/design-review](commands/design-review.md) | Design system compliance — contrast, colors, typography, dead CTAs |
| [/review-local](commands/review-local.md) | Local QA checklist — build, hydration, design system, SEO, responsive |
| [/pr-review](commands/pr-review.md) | Pull request reviewer — code quality, design system, accessibility, security |

### Architecture & SEO

| Command | Description |
|---------|-------------|
| [/discover](commands/discover.md) | Discoverability architect — information architecture, internal linking, orphan prevention |
| [/extract-faqs](commands/extract-faqs.md) | FAQ extraction from pages for schema.org structured data |
| [/adapt](commands/adapt.md) | Reference analyzer — compare React components to existing Prismic slices |
| [/inventory](commands/inventory.md) | Asset inventory manager — track built assets awaiting placement |

### Sandbox & Design

| Command | Description |
|---------|-------------|
| [/sandbox](commands/sandbox.md) | AI Studio workflow manager — bootstrap, compare, and add sandbox versions |
| [/review-handoff](commands/review-handoff.md) | Review Gemini CLI creative work — spin up, design checks, structured feedback |
| [/swipe](commands/swipe.md) | Swipe file — capture screenshots, URLs, and text observations of interesting patterns |
| [/swipe-audit](commands/swipe-audit.md) | Pattern-match pages against collected swipe entries |

### Integrations & Utilities

| Command | Description |
|---------|-------------|
| [/push-to-slack](commands/push-to-slack.md) | Push files or messages to Slack channels |
| [/page-to-pdf](commands/page-to-pdf.md) | Convert web pages to PDF using Puppeteer |
| [/prismic-populate](commands/prismic-populate.md) | Generate copy-paste ready content for Prismic CMS |
| [/backup](commands/backup.md) | Prismic content backup manager |

### Archive

| Command | Description |
|---------|-------------|
| [/archive:extract](commands/archive/extract.md) | Extract papers and links from Slack channels to CSV |
| [/archive:sandbox-add](commands/archive/sandbox-add.md) | Add a single version to existing sandbox concept |
| [/archive:sandbox-compare](commands/archive/sandbox-compare.md) | Full sandbox bootstrap + compare + CONTEXT.md workflow |

---

## Plugin Skills (from Claude Code Marketplace)

### Canvas TUI Skills

| Skill | Description |
|-------|-------------|
| [canvas](skills/canvas/canvas/) | Primary skill for terminal TUI components |
| [calendar](skills/canvas/calendar/) | Calendar display and meeting time picker |
| [document](skills/canvas/document/) | Markdown document display and text selection |
| [flight](skills/canvas/flight/) | Flight comparison and seat selection |

### Plugin Development Skills

| Skill | Description |
|-------|-------------|
| [plugin-structure](skills/plugin-dev/plugin-structure/) | Plugin directory layout, manifest, auto-discovery |
| [skill-development](skills/plugin-dev/skill-development/) | Creating skills for Claude Code plugins |
| [command-development](skills/plugin-dev/command-development/) | Slash command structure, frontmatter, dynamic arguments |
| [agent-development](skills/plugin-dev/agent-development/) | Subagent definitions, triggering, system prompts |
| [hook-development](skills/plugin-dev/hook-development/) | Event-driven hooks (PreToolUse, PostToolUse, Stop, etc.) |
| [mcp-integration](skills/plugin-dev/mcp-integration/) | Model Context Protocol server integration |
| [plugin-settings](skills/plugin-dev/plugin-settings/) | Plugin configuration via .local.md files |

### Productivity & Tooling Skills

| Skill | Description |
|-------|-------------|
| [skill-creator](skills/skill-creator/) | Create, test, benchmark, and optimize skills |
| [claude-automation-recommender](skills/claude-code-setup/claude-automation-recommender/) | Analyze codebase and recommend Claude Code automations |
| [claude-md-improver](skills/claude-md-management/claude-md-improver/) | Audit and improve CLAUDE.md files |
| [frontend-design](skills/frontend-design/) | Production-grade frontend interfaces with high design quality |
| [playground](skills/playground/) | Self-contained HTML playgrounds with interactive controls |
| [writing-rules](skills/hookify/writing-rules/) | Hookify rule syntax and patterns |
| [stripe-best-practices](skills/stripe-best-practices/) | Best practices for Stripe integrations |

### Channel Skills

| Skill | Description |
|-------|-------------|
| [discord:access](skills/discord/access/) | Discord channel access management |
| [discord:configure](skills/discord/configure/) | Discord channel setup |
| [telegram:access](skills/telegram/access/) | Telegram channel access management |
| [telegram:configure](skills/telegram/configure/) | Telegram channel setup |

### Example Skills

| Skill | Description |
|-------|-------------|
| [example-skill](skills/example-plugin/example-skill/) | Reference template for skill structure |
| [example-command](skills/example-plugin/example-command/) | Example user-invoked command in skills format |

---

## Installation

Copy individual skills or commands into your project's `.claude/` folder:

```bash
# Skills
cp -r skills/copyreview/ your-project/.claude/skills/copyreview/

# Commands
cp commands/deploy.md your-project/.claude/commands/deploy.md
```

## License

MIT
