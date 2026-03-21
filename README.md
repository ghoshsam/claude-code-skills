# Claude Code Skills

A collection of SKILL.md files for [Claude Code](https://claude.com/claude-code) — Anthropic's CLI for Claude.

## Custom Project Skills

| Skill | Description |
|-------|-------------|
| [copyreview](skills/copyreview/) | AI writing pattern detector — 24 patterns based on Wikipedia's "Signs of AI writing" |
| [blog-enhance](skills/blog-enhance/) | Visual treatment explorer for blog content (two-phase: explore then build) |

## Canvas TUI Skills

| Skill | Description |
|-------|-------------|
| [canvas](skills/canvas/canvas/) | Primary skill for terminal TUI components (calendars, documents, flights) |
| [calendar](skills/canvas/calendar/) | Calendar display and meeting time picker |
| [document](skills/canvas/document/) | Markdown document display and text selection |
| [flight](skills/canvas/flight/) | Flight comparison and seat selection |

## Plugin Development Skills

| Skill | Description |
|-------|-------------|
| [plugin-structure](skills/plugin-dev/plugin-structure/) | Plugin directory layout, manifest, auto-discovery |
| [skill-development](skills/plugin-dev/skill-development/) | Creating skills for Claude Code plugins |
| [command-development](skills/plugin-dev/command-development/) | Slash command structure, frontmatter, dynamic arguments |
| [agent-development](skills/plugin-dev/agent-development/) | Subagent definitions, triggering, system prompts |
| [hook-development](skills/plugin-dev/hook-development/) | Event-driven hooks (PreToolUse, PostToolUse, Stop, etc.) |
| [mcp-integration](skills/plugin-dev/mcp-integration/) | Model Context Protocol server integration |
| [plugin-settings](skills/plugin-dev/plugin-settings/) | Plugin configuration via .local.md files |

## Productivity & Tooling Skills

| Skill | Description |
|-------|-------------|
| [skill-creator](skills/skill-creator/) | Create, test, benchmark, and optimize skills |
| [claude-automation-recommender](skills/claude-code-setup/claude-automation-recommender/) | Analyze codebase and recommend Claude Code automations |
| [claude-md-improver](skills/claude-md-management/claude-md-improver/) | Audit and improve CLAUDE.md files |
| [frontend-design](skills/frontend-design/) | Production-grade frontend interfaces with high design quality |
| [playground](skills/playground/) | Self-contained HTML playgrounds with interactive controls |
| [writing-rules](skills/hookify/writing-rules/) | Hookify rule syntax and patterns |
| [stripe-best-practices](skills/stripe-best-practices/) | Best practices for Stripe integrations |

## Channel Skills

| Skill | Description |
|-------|-------------|
| [discord:access](skills/discord/access/) | Discord channel access management |
| [discord:configure](skills/discord/configure/) | Discord channel setup |
| [telegram:access](skills/telegram/access/) | Telegram channel access management |
| [telegram:configure](skills/telegram/configure/) | Telegram channel setup |

## Example Skills

| Skill | Description |
|-------|-------------|
| [example-skill](skills/example-plugin/example-skill/) | Reference template for skill structure |
| [example-command](skills/example-plugin/example-command/) | Example user-invoked command in skills format |

## Installation

Copy individual skills into your project's `.claude/skills/` folder:

```bash
cp -r skills/copyreview/ your-project/.claude/skills/copyreview/
```

## License

MIT
