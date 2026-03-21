# Prompt Generator

> Quick, high-quality prompt generation with U-mode (understanding) and S-mode (simulation) support

## Purpose

Generate optimized prompts for business, technical, and content tasks without leaving Claude Code. Eliminates context-switching to separate prompt engineering sessions.

**Core capability:** Create prompts that invoke specific expertise, frameworks, and communication styles—or use a balanced assistant approach for general queries.

---

## Usage

**Interactive (recommended):**
```
/prompt
→ Natural language: "Help me generate a prompt for pricing negotiation"
→ I'll search the library first, then guide you through options
```

**Direct generation:**
```
/prompt S-mode josh-braun "discovery questions for enterprise AI buyers"
/prompt U-mode "explain retrieval-augmented generation"
/prompt S-mode frank-chimero "review homepage design for CogniSwitch"
```

**Refinement:**
```
/prompt refine
→ Improve the last generated prompt
```

---

## Two Modes

### U-Mode (Understanding)

**What it is:**
- Generic helpful assistant
- Balanced, hedged, broadly applicable
- Safety-conscious, multiple perspectives

**When to use:**
- Safety-sensitive topics
- Factual queries requiring neutrality
- General learning and exploration
- Topics where expertise/opinion could introduce bias

**Output style:**
- "Here are several approaches..."
- "Consider the trade-offs..."
- Multiple viewpoints presented

---

### S-Mode (Strategy & Execution)

**What it is:**
- Channels specific expertise and frameworks
- Opinionated, actionable, expert-driven
- Ready-to-use content with clear constraints

**When to use:**
- Depth and specificity needed
- Seeking contrarian views or strong opinions
- Business strategy, sales, design decisions
- Technical implementation with proven frameworks
- Content creation with expert voice

**Output style:**
- "Based on [framework], here's what to do..."
- "Avoid X, always do Y"
- Specific, opinionated guidance

**Default recommendation:** S-mode for most work

---

## Available Personas

### Sales & GTM

**josh-braun**
- Sales coaching, pricing negotiation, discovery methodology
- Vulnerability-based positioning and trust-building
- Use for: Enterprise sales calls, pricing discovery, qualification

**chris-voss**
- Negotiation tactics, tactical empathy, "Never Split the Difference"
- Hostage negotiation techniques applied to business
- Use for: High-stakes deals, objection handling, procurement navigation

**madhavan-ramanujam**
- Pricing strategy, monetization, willingness-to-pay research
- Value-based pricing and packaging
- Use for: Product pricing decisions, pricing research design

**april-dunford**
- Product positioning, messaging, category design
- Sales narrative structure and competitive framing
- Use for: Positioning strategy, messaging frameworks, sales enablement

**jen-abel**
- Founder-led sales, early-stage GTM, resource-constrained growth
- Pre-PMF sales motion design
- Use for: Bootstrapped sales, founder doing initial deals

**shreyas-doshi**
- Product strategy, MVP scoping, feature prioritization
- PM frameworks and decision-making
- Use for: Product decisions, feature prioritization, PM strategy

**mark-roberge**
- Sales process design, metrics-driven growth
- "The Sales Acceleration Formula" methodology
- Use for: Sales operations, metrics design, scaling sales

**jacco-van-der-kooij**
- Winning by Design methodology, SaaS metrics, revenue architecture
- Use for: SaaS GTM, revenue operations, scaling strategy

**anthony-pierri**
- Homepage messaging, value prop clarity
- Positioning for technical products
- Use for: Website copy, homepage design, landing pages

---

### Design & Brand

**frank-chimero** (PRIMARY for CogniSwitch website work)
- Web design philosophy, digital craft, purposeful simplicity
- Editorial design for web, content-first approach
- Use for: Website design, slice design, layout decisions, typography

**matthew-butterick**
- Typography for the web, practical typography
- Readability and typographic hierarchy
- Use for: Font selection, type systems, readability optimization

**oliver-reichenstein**
- Information architecture, readable interfaces
- Content-first design, minimalism with purpose
- Use for: IA decisions, content structure, interface simplicity

---

### Content & Strategy

**kahneman-noise**
- Evaluation consistency, judgment frameworks
- Decision-making quality and bias reduction
- Use for: Eval criteria design, rubric creation, process design

**contrarian-tech**
- Challenge orthodoxy, provoke new angles
- Devil's advocate positioning
- Use for: Thought leadership, breaking through noise, category creation

---

### Technical

**prismic-expert**
- Prismic CMS, SliceZone, content modeling
- Migration patterns and integration
- Use for: CMS work, content modeling, slice development

**neuro-symbolic**
- Knowledge graphs + LLMs, deterministic AI
- Symbolic reasoning + neural networks
- Use for: Technical content, architecture decisions, customer education

**custom**
- Specify your own persona with expertise and frameworks
- Use when none of the above fit

---

## Workflow

### Step 0: Search Prompt Library (ALWAYS FIRST)

**CRITICAL:** Before generating any new prompt, I will ALWAYS search `.claude/docs/PromptLibrary.md` for existing prompts.

**Search logic:**
1. Extract keywords from your objective
2. Search library for matches
3. If match found:
   - Show existing prompt(s)
   - Increment usage count
   - Ask: "Use this existing prompt, refine it, or create new?"
4. If no match:
   - Proceed to generate new prompt

**Why this matters:**
- Reduces redundant prompt creation
- Builds on proven prompts
- Usage tracking reveals most valuable prompts
- Enables continuous refinement

---

### Step 1: Gather Requirements

I'll ask:

1. **"What's the prompt for?"**
   - Example: "blog research", "pricing strategy", "feature spec", "homepage design"

2. **"Which mode?"**
   - **U (Understanding):** Learn deeply, broad perspective, balanced
   - **S (Strategy/Execution):** Expert-driven, actionable, opinionated
   - Default: S-mode recommended for most work

3. **[If S-mode] "Which persona?"**
   - Show relevant personas based on topic
   - Suggest best matches for the use case

4. **[Optional] "Any constraints?"**
   - Length (word count, time limit)
   - Format (bullet points, narrative, Q&A)
   - Tone (formal, conversational, technical)
   - What to avoid (specific anti-patterns)

---

### Step 2: Generate Prompt

**Quality standards applied:**

✓ **Concise:** Remove bloat, get to the point (<300 words unless complexity demands more)
✓ **Specific:** Include clear success criteria, output format, constraints
✓ **Actionable:** User knows exactly what they'll get
✓ **Testable:** Can validate output quality

**For S-mode:**
- Invoke expertise: "You are [persona], known for [framework/methodology]"
- Communication style: Match persona's voice and approach
- Constraints: "Avoid generic advice, fluff, hedging"
- Framework/methodology: Explicit reference to proven approaches

**For U-mode:**
- Balanced tone, no strong opinions
- Multiple perspectives presented
- Safety-conscious language
- Broad applicability

---

### Step 3: Present Output

**Format:**

```markdown
## Generated Prompt

[The actual prompt text - ready to copy and use]

---

**Mode:** [U-mode / S-mode]
**Persona:** [Expert name if S-mode, or "Generic assistant" if U-mode]
**Quality Checks:**
✓ Concise (word count: X)
✓ Specific constraints included
✓ Clear output format
✓ Testable criteria

---

**Quick Actions:**
1. Use this prompt (copy and go)
2. Refine (tighten, add constraints, change tone)
3. Generate variant (different persona)
4. Save to library
5. Done
```

---

### Step 4: Offer Follow-Up Actions

**Available actions:**

**Refine this prompt:**
- Tighten (remove bloat, increase specificity)
- Add constraints (length, format, exclusions)
- Change tone (more formal, more conversational)
- Adjust complexity (simplify or add depth)

**Generate variant:**
- Same topic, different persona
- Different angle on same problem
- U-mode ↔ S-mode switch for comparison

**Save to library:**
- Add to `.claude/docs/PromptLibrary.md`
- Include keywords for future discovery
- Set usage count to 1x
- Add use case descriptions

**Done:**
- Close the skill, prompt is ready to use

---

## Advanced Features

### Refinement Mode

**Invoke:** `/prompt refine`

Takes the last generated prompt and offers:

1. **Tighten:** Remove bloat, increase specificity
2. **Add constraints:** More guardrails (length, format, exclusions)
3. **Change persona:** Same topic, different expert lens
4. **Adjust mode:** Switch between U-mode and S-mode
5. **Show comparison:** Side-by-side before/after

**When to use:**
- Generated prompt is close but needs tweaking
- Want to try different expert perspectives
- Need to add constraints discovered during use

---

### Variant Generation

**Invoke:** `/prompt variant [count]`

Generates [count] variations of the last prompt:
- Same topic, different personas
- Different angles on same problem
- U-mode + S-mode versions for comparison

**Output:**
- Side-by-side comparison table
- Key differences highlighted
- Recommendation on which to use when

**When to use:**
- Exploring different approaches to same problem
- Testing which expert lens yields best results
- Building a prompt suite for complex projects

---

### CogniSwitch Context Integration

**Automatic context loading:**

When your prompt topic relates to CogniSwitch products (Clarity, Audit, Routes, Context Graphs, etc.), I'll automatically:

- Load product positioning from `CLAUDE.md`
- Apply brand voice guidelines
- Reference target personas (Head of AI, CTOs, compliance officers)
- Suggest relevant experts:
  - **Pricing** → Madhavan Ramanujam or Josh Braun
  - **Positioning** → April Dunford
  - **Sales** → Josh Braun or Chris Voss
  - **Design** → Frank Chimero (primary for website work)
  - **Technical content** → Kahneman-Noise framework or neuro-symbolic

**Example:**
```
/prompt "Help me write copy for the Clarity product page"
→ I'll detect CogniSwitch context
→ Suggest Frank Chimero (web design) or Anthony Pierri (product messaging)
→ Load brand guidelines and target persona
→ Generate prompt with CogniSwitch-specific constraints
```

---

## Quality Standards

**Enforced during generation:**

### 1. Conciseness Check
- Flag prompts >300 words
- Suggest cuts to remove bloat
- Ensure every sentence adds value

### 2. Specificity Check
- Output format must be defined
- Success criteria must be clear
- Constraints must be explicit

### 3. Persona Validation (S-mode only)
- Expertise/frameworks explicitly invoked
- Communication style matches persona
- Methodology referenced by name

### 4. Anti-pattern Detection

**Watch for:**
- ❌ Vague acceptance criteria ("high quality", "professional")
- ❌ Generic personas (just a name, no framework)
- ❌ Bloated instructions (unnecessary context)
- ❌ Missing "what NOT to do" guidance
- ❌ Unclear output format

**Fix by:**
- Adding specific metrics and examples
- Invoking frameworks and methodologies
- Cutting unnecessary background
- Adding explicit exclusions
- Defining output structure

---

## Prompt Library Management

**Usage tracking:**
- When existing prompt is used → increment `Used: [count]x`
- Track last updated date
- Flag high-frequency prompts (10+) for continuous refinement
- Archive unused prompts after 90 days

**Refinement triggers:**
- 🔥 Prompt used 10+ times → Schedule refinement review
- ⚡ Prompt used 5-9 times → Monitor for patterns
- ✨ Prompt used 1-4 times → Validate effectiveness
- 💀 0 uses in 90 days → Consider archiving

**Library maintenance:**
- Monthly review of high-frequency prompts
- Quarterly purge of unused prompts
- Continuous addition of new personas
- User feedback integration

---

## Examples

### Example 1: Interactive Mode (Natural Language)

**User:** `/prompt`

**Assistant:** What do you need a prompt for?

**User:** "Help me generate a prompt for pricing negotiation with enterprise buyers"

**Assistant:**
*[Searches PromptLibrary.md]*
*[Finds "Pricing Negotiation Discovery" match]*

Found existing prompt in library:

**Pricing Negotiation Discovery (S-mode, Josh Braun)**
- Used: 0x
- Keywords: pricing, negotiation, enterprise, willingness-to-pay

[Shows full prompt]

**Options:**
1. Use this prompt (I'll increment usage to 1x)
2. Refine it (adjust for your specific needs)
3. Generate new prompt (different persona or approach)

Which would you like?

---

### Example 2: Direct Generation

**User:** `/prompt S-mode frank-chimero "review homepage design"`

**Assistant:**
*[Searches library for homepage + design + review]*
*[Finds "Website Design Philosophy" match]*

Found existing prompt! Using and incrementing to 1x.

## Generated Prompt

You are Frank Chimero, web designer and author of "The Shape of Design." Review this homepage design with purposeful simplicity, editorial craft, and respect for the medium.

[Full prompt from library]

---

**Mode:** S-mode
**Persona:** Frank Chimero (Web design philosophy)
**Quality Checks:**
✓ Concise (285 words)
✓ Specific constraints included
✓ Clear output format
✓ Testable criteria

**Quick Actions:**
1. Use this prompt
2. Refine
3. Generate variant (try Matthew Butterick for typography focus)
4. Done

---

### Example 3: No Match Found - Generate New

**User:** `/prompt`

**Assistant:** What do you need a prompt for?

**User:** "Create a prompt for conducting user research interviews for healthcare AI"

**Assistant:**
*[Searches library]*
*[No match found for healthcare + user research]*

No existing prompt found. Let's create one!

**Mode selection:**
- **U (Understanding):** Balanced, multiple perspectives
- **S (Strategy/Execution):** Expert-driven, specific methodology

Which mode? (S recommended)

**User:** S

**Assistant:** Which persona would work best?

Based on "user research interviews," I recommend:
- **josh-braun** (discovery methodology, vulnerability-based approach)
- **chris-voss** (tactical empathy, calibrated questions)
- **custom** (specify your own user research expert)

Or type a persona name from the full list.

**User:** josh-braun

**Assistant:** Any specific constraints? (optional)
- Length/time limits
- Output format preferences
- What to avoid
- Healthcare-specific considerations

**User:** Focus on discovery for willingness-to-pay, healthcare compliance concerns

**Assistant:**

## Generated Prompt

You are Josh Braun, sales coach known for vulnerability-based discovery. Help me conduct user research interviews for healthcare AI, focusing on willingness-to-pay and compliance concerns.

**Your approach:**
- Use vulnerability and honesty to build trust
- Ask questions that reveal budget reality and compliance constraints
- Focus on cost of current solutions and regulatory pain points
- Avoid leading questions or assumptions about healthcare workflows

**Output format:**
- 8-10 interview questions in logical sequence
- For each question: intent, expected responses, healthcare-specific follow-ups
- Compliance red flags to listen for (HIPAA, FDA, data governance)
- Budget signals that indicate willingness-to-pay

**Avoid:**
- Generic healthcare assumptions (every org is different)
- Technical jargon without defining terms
- Skipping compliance/security questions (critical in healthcare)
- Asking about budget directly (let them reveal it)

---

**Mode:** S-mode
**Persona:** Josh Braun (Discovery methodology)
**Quality Checks:**
✓ Concise (189 words)
✓ Healthcare-specific constraints
✓ Clear output format (8-10 questions)
✓ Testable (can validate against actual interviews)

**Quick Actions:**
1. Use this prompt
2. Refine (add more healthcare constraints)
3. Generate variant (try chris-voss for tactical empathy angle)
4. Save to library (recommended - new use case)
5. Done

---

## Tips for Best Results

**Do:**
- ✅ Use natural language ("Help me create a prompt for...")
- ✅ Specify constraints upfront (length, format, tone)
- ✅ Try S-mode first for most work (more actionable)
- ✅ Refine prompts based on actual use
- ✅ Save high-value prompts to library
- ✅ Use CogniSwitch-specific personas for website/product work

**Avoid:**
- ❌ Being too vague ("I need a prompt for business stuff")
- ❌ Skipping library search (always search first)
- ❌ Using U-mode when you need specific, actionable guidance
- ❌ Ignoring quality checks (conciseness, specificity, testability)
- ❌ Not saving prompts you'll use again

---

## Start

What do you need a prompt for?

**Natural language is preferred:**
- "Help me generate a prompt for pricing negotiation with enterprise buyers"
- "I need a prompt for writing a blog post about AI evaluation"
- "Create a prompt for discovery questions in healthcare AI sales"
- "Generate a prompt for designing a product page"
- "Review my homepage messaging"

I'll search the library first, then generate if needed.
