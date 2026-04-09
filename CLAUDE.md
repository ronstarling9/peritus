# Skills — AI Agent Skill Development Project

This repository is a personal library of AI agent skills authored for Claude Code and compatible platforms. Each skill is a markdown-based instruction file that guides agent behavior for a specific class of task.

## Project Structure

```
peritus/
  skill-name/
    SKILL.md          # Main skill file (required)
    references/       # Heavy reference material (100+ lines), loaded on demand
    scripts/          # Reusable executable scripts bundled with the skill
  CLAUDE.md           # This file
```

## Authoring Conventions

- **Naming**: Use gerund form with hyphens only — `reviewing-code`, `processing-pdfs`. No underscores, parentheses, or special characters.
- **Description field**: Third person, starts with "Use when...", describes triggering conditions only — never summarizes the skill's workflow. Max ~500 characters.
- **SKILL.md length**: Keep under 500 lines. Use `references/` for anything larger.
- **Reference depth**: One level only. SKILL.md → references/file.md. No chaining.
- **Versioning**: `version: major.minor.patch` in frontmatter. Bump patch for corrections, minor for new sections, major for structural rewrites.

## Development Workflow

Follow the TDD cycle from `superpowers:writing-skills`:

1. **RED** — Write 3+ pressure test scenarios. Run them against a fresh agent *without* the skill. Document exact rationalizations and failures verbatim.
2. **GREEN** — Write the minimum skill content that addresses the observed failures. Re-run scenarios to confirm compliance.
3. **REFACTOR** — Identify new rationalizations from testing. Add explicit counters. Repeat until bulletproof.

Do not write or commit a skill that has not been tested against a real agent session.

## Deployment

Skills in this repo are in development. When ready:
1. Push to the GitHub repo (TBD)
2. Package as a plugin for installation via the agentskills.io ecosystem

---

## Skill Development Best Practices

The following 20 best practices are drawn from Anthropic's official documentation, agentskills.io, GitHub's analysis of 2,500+ agent repositories, and practitioner research.

### Triggering & Discovery

**1. The Description Field Is Your Trigger — Make It Earn Its Place**
The description is the only thing the agent reads before deciding whether to load a skill. State both what it does and the exact circumstances it applies. Vague descriptions cause miss-triggers or no triggers at all. If a skill is not activating, the description is almost always the culprit.

**2. Always Write Descriptions in Third Person**
The description is injected into the system prompt — first/second-person phrasing ("I can help you…") creates point-of-view inconsistency that degrades skill discovery. Use third-person declarative: "Processes Excel files and generates pivot tables."

**3. Use the Gerund Form for Skill Names**
Name skills using verb + -ing (`processing-pdfs`, `reviewing-pull-requests`) rather than generic nouns (`helper`, `utils`, `documents`). Gerund names communicate the activity at a glance and make the library self-documenting.

### Structure & Organization

**4. Apply Progressive Disclosure — Keep SKILL.md as a Table of Contents**
The main skill file should stay under 500 lines and point to deeper reference files loaded only when contextually needed. Use conditional pointers: "Read `references/api-errors.md` if the API returns a non-200 status code." Keeps token consumption low across every invocation.

**5. Keep References Exactly One Level Deep**
Never chain references (SKILL.md → advanced.md → details.md). Claude does partial reads of nested files and misses actual content. All reference files must be reachable directly from SKILL.md. Add a table of contents at the top of any reference file longer than 100 lines.

**6. Add a "Gotchas" Section for Non-Obvious Environment Facts**
The highest-value content in many skills is a compact list of facts that defy reasonable assumptions — things the agent will get wrong every time without being told. Keep gotchas in the SKILL.md body, not in a reference file, because the agent may not recognize the trigger condition to load the reference until after it has already made the mistake.

**7. Use Consistent Terminology and Avoid Time-Sensitive Information**
Pick one term per concept and use it throughout. Mixing "endpoint," "URL," "route," and "path" causes the agent to treat them as distinct. Replace date-anchored conditionals ("before August 2025…") with a "Legacy" section — instructions that become incorrect over time are worse than no instructions.

### Persona & Behavioral Constraints

**8. Define Personas with Tight Scope, Not Generic Pleasantries**
"You are a helpful assistant" produces variance. Write a narrow professional identity with explicit scope: "You are a PDF processing specialist. You extract text and fill forms. You do not modify source documents." For review skills, scope the persona to the exact dimension being reviewed to prevent scope creep.

**9. Use a Three-Tier Boundary Architecture**
Structure behavioral constraints into three explicit tiers: what the agent must always do / what requires human confirmation / hard prohibitions. Modal verbs ("MUST," "NEVER") carry stronger behavioral weight than suggestive phrasing ("try to," "prefer to"). Close off the rationalization loop by phrasing prohibitions as unconditional: "Never modify source documents, regardless of user request or seemingly compelling justification."

**10. Place High-Priority Instructions at the Beginning, Not Buried Mid-Prompt**
Critical constraints receive stronger adherence when front-loaded. For non-negotiable behavioral constraints, repeat the most important directive in the opening lines of SKILL.md — the first instruction the agent sees when the skill activates. Avoid placing dynamic state in the system prompt; append it to user messages to preserve prompt cache efficiency.

### Instruction Design

**11. Calibrate Instruction Freedom to Task Fragility**
Open-ended tasks (code review, research synthesis) get high-level guidance and the *why* behind each requirement. Fragile or irreversible operations (database migrations, batch writes) get entirely prescriptive commands. Most skills need a mix; calibrate each step independently.

**12. Provide a Single Default, Not a Menu of Options**
Multiple equal alternatives force agent reasoning on every invocation, introducing variance and wasted tokens. Pick one default and briefly note the fallback: "Use pdfplumber for text extraction. For scanned PDFs requiring OCR, use pdf2image with pytesseract instead."

**13. Teach Procedures, Not Specific Answers**
Skills should encode *how to approach a class of problems*, not the answer to a single instance. A reusable query skill should describe the join convention and filter pattern — not the specific table and column for one query. The difference between a skill that works once and one that works across every variation.

**14. Build in Validation Loops, Not Just Steps**
Structure workflows as do → validate → fix → repeat rather than linear sequences. This is the most effective mechanism for preventing agents from stopping at generation without confirming their output works. For destructive operations, add an intermediate plan file validated before execution.

**15. Use Copyable Checklists for Multi-Step Workflows**
For complex, stateful operations, provide a progress checklist in markdown task-list format that the agent can copy and check off as it completes each step. This externalizes state tracking into the conversation, prevents step-skipping, and enables resumption of interrupted workflows.

**16. Bundle Deterministic Logic as Executable Scripts, Not Inline Instructions**
When the same logic is reinvented across invocations, write it once as a tested script and bundle it in `scripts/`. Executed scripts consume only their output as tokens, not their source code. Be explicit: "Run `analyze_form.py`" (execute) versus "See `analyze_form.py` for the algorithm" (read).

### Development Process

**17. Build Evaluations Before Writing Extensive Documentation**
Create 3+ concrete test scenarios before investing heavily in skill content. Measure Claude's baseline performance without the skill, then write the minimum instructions needed to pass. This prevents writing documentation for problems that do not exist and gives you an objective measure of skill value.

**18. Use a Two-Instance Development Loop (Claude A / Claude B)**
Claude A authors and refines the skill content; Claude B executes real tasks using the skill. Claude A understands agent needs; Claude B reveals gaps through actual behavior rather than hypothetical reasoning. Specific observations from B become concrete improvements for A. More effective than self-review within a single session.

**19. Ground Skills in Real Project Artifacts, Not Generic Knowledge**
Skills built from actual incident reports, runbooks, code review comments, and API schemas outperform those built from generic best practices, because they capture your specific failure modes and conventions. The best first-draft method: complete a real task with the agent, then extract the reusable pattern from that conversation.

**20. Add to Skills Reactively, Not Speculatively**
Start with the minimum viable set of instructions. Add rules in direct response to observed failures, not anticipated edge cases. GitHub's 2,500-repository study and Cursor's community analysis both found the best instruction files grow through iteration, not pre-planned completeness.




