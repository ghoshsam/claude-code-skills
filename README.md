# Claude Code Skills

Custom skills for [Claude Code](https://claude.com/claude-code) — Anthropic's CLI for Claude.

## Skills

### Copy Review (`/copyreview`)

A writing editor that identifies and removes signs of AI-generated text. Based on [Wikipedia's "Signs of AI writing"](https://en.wikipedia.org/wiki/Wikipedia:Signs_of_AI_writing) page, maintained by WikiProject AI Cleanup.

Covers 24 patterns across content, language, style, communication, and hedging categories — plus guidance on adding voice and personality so "clean" text doesn't read as soulless.

**Usage:** `/copyreview` then paste your text.

### Blog Enhance (`/blog-enhance`)

A two-phase visual treatment explorer for blog content. Phase 1 analyzes content sections and presents 2-3 visual treatment options per section (toggles, expandable tables, assessment checklists, system diagrams, etc.). Phase 2 builds the selected treatments as interactive slice components.

**Usage:** `/blog-enhance Content/Signal/Drafts/your-draft.md`

## Installation

Copy the `skills/` directory into your project's `.claude/skills/` folder:

```bash
cp -r skills/ your-project/.claude/skills/
```

## License

MIT
