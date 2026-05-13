---
name: skill-builder
description: Create, refine, and package Claude skills that comply with the Agent Skills open standard. Invoke whenever a user asks to "create a skill", "build a skill", "make a skill", "write a skill", "turn this into a skill", "save this as a skill", "package this workflow as a skill", or anything implying they want to codify a reusable Claude capability. Also trigger when someone says "I want Claude to always do X" or "how do I make Claude remember how to do Y" — those are skill requests in disguise. This skill runs a structured interview before writing anything, ensuring the final output is a complete, well-structured, standard-compliant skill package — not just a SKILL.md file, but a properly considered folder with the right bundled resources.
---

# Skill Builder

You are helping a user create a **Claude skill** — a reusable, installable capability package that complies with the **Agent Skills open standard**. You know this standard well. Your job is to produce a complete, well-structured skill, not just write a SKILL.md file and call it done.

Your process: **interview first, write second.** Never skip straight to writing.

---

## What you already know: The Agent Skills Open Standard

Skills are folders, not single files. You understand the full structure and should actively consider every layer during the interview:

```
skill-name/
├── SKILL.md              ← required; YAML frontmatter + markdown instructions
├── scripts/              ← optional; executable code for deterministic/repetitive tasks
├── references/           ← optional; docs loaded into context as needed
└── assets/               ← optional; templates, fonts, icons used in output
```

### Progressive disclosure — the core architecture

The standard uses a three-level loading system. Keep this in mind when deciding what goes where:

| Level | What | When loaded | Size guidance |
|-------|------|-------------|---------------|
| 1 | Frontmatter (`name` + `description`) | Always in context | ~100 words |
| 2 | SKILL.md body | When skill triggers | < 500 lines ideally |
| 3 | Bundled resources | On demand, as needed | Unlimited |

This means: keep the SKILL.md body focused on *decisions and instructions*. Heavy reference material, large templates, reusable scripts — those belong in the subfolders, with clear pointers from SKILL.md about when to load them.

### What each folder is for

**`scripts/`** — Code that would otherwise be re-invented on every invocation. If Claude would likely write the same helper script every time (e.g., a docx builder, a chart generator, a file formatter), bundle it here. The skill instructs Claude to use it rather than recreate it.

**`references/`** — Documentation, style guides, schemas, API references, or domain knowledge too large to live in SKILL.md. Loaded into context when needed. If a skill supports multiple variants (e.g., AWS vs GCP vs Azure), each gets its own reference file — Claude reads only the relevant one.

**`assets/`** — Static files used in output: Word templates, HTML templates, fonts, icon sets, default configs. The skill instructs Claude to copy/use these rather than generate from scratch.

### The description field is the trigger mechanism

Claude decides whether to invoke a skill based solely on the `description` frontmatter field — it never sees the body until after it decides to use the skill. A vague or narrow description = an undertriggered skill. Descriptions should be slightly "pushy" — err toward inclusion.

### Packaging

Once complete, the skill folder can be packaged into a `.skill` file for installation. On Claude.ai, the user downloads and installs it manually. On Claude Code, `python -m scripts.package_skill <path>` produces the `.skill` file.

---

## Phase 1: The Interview

Before writing anything, conduct a structured interview. The goal: understand the skill well enough to make good decisions about *every layer* of the standard — not just SKILL.md, but whether scripts, references, or assets belong in the package too.

### How to conduct the interview

Ask 2–3 of the most important questions first. Listen carefully. Follow up on vague answers — "it should just work well" is not an answer. Push for concrete examples. Continue until you have specific, actionable answers across all categories.

As you learn about the skill, actively think: *Would a reusable script help here? Is there reference material that should be bundled? Are there templates or assets that belong in the package?* Surface these possibilities — users often don't know the standard supports them.


### Interview question bank

Work through these categories. You don't have to ask every question — use judgment. Prioritize the ones most likely to reveal gaps or bundling opportunities.

#### 1. Purpose & Scope
- What does this skill do, in one sentence?
- What problem does it solve? What's the pain without it?
- What should it *not* do?
- Is this a one-shot task or a multi-step workflow?

#### 2. Trigger Conditions
- What would a user type to invoke this? Give 3–5 example phrasings.
- Are there near-miss cases where it looks applicable but isn't?
- Does this overlap with any other skill or built-in Claude behavior?

#### 3. Inputs
- What does the user provide? (Files? URLs? Descriptions? Structured data?)
- What file types or formats are expected?
- What if the input is missing, malformed, or ambiguous?
- What's required vs. optional?

#### 4. Output
- What should the output look like? Can you show an example?
- What format — a file, inline prose, code, a table?
- What makes it *good* vs. just acceptable?

#### 5. Step-by-Step Behavior
- Walk me through how Claude should handle this, step by step.
- Are there decision branches based on context?
- Are there steps that are easy to get wrong or commonly skipped?
- What tools does Claude need? (bash, file creation, web search, etc.)

#### 6. Bundling Opportunities ← think carefully here
- Is there a script Claude would write from scratch every single time? → `scripts/`
- Is there reference material (docs, schemas, style guides) too large for SKILL.md? → `references/`
- Are there templates, fonts, or static files used in output? → `assets/`
- Does the skill have multiple variants or domains needing separate reference files?

#### 7. Quality Bar & Edge Cases
- What's the most common failure mode to prevent?
- What edge cases would trip this up?
- Is there a style, tone, or convention the output must follow?
- Are there things Claude tends to do you'd explicitly want to prohibit?

#### 8. Examples
- Can you share an ideal input → output pair?
- Is there an existing file or workflow you want this to replicate?

#### 9. Context & Users
- Who will use this? Technical or non-technical?
- Which environment: Claude.ai, Claude Code, Cowork, or API?
- Any environment constraints? (no internet, specific tools available/unavailable)

---

### When to stop interviewing

Stop only when you can honestly answer yes to all of these:

- [ ] I know exactly what this skill does and doesn't do
- [ ] I have at least 3 concrete trigger phrases
- [ ] I know what a good output looks like (ideally with an example)
- [ ] I know what inputs to expect and how to handle bad ones
- [ ] I understand the step-by-step behavior, including decision branches
- [ ] I know the top 2–3 failure modes to guard against
- [ ] I've assessed the bundling question — scripts, references, assets
- [ ] I know the target environment and any constraints

Still missing answers after 2–3 rounds? Say so: "I want to make sure the skill is complete before I write it — can we cover a few more things?"

---

## Phase 2: Design the Package Structure

Before writing, decide and state what the package will contain and why. Tell the user your plan:

- **SKILL.md only** — simple skills with no reusable code or heavy reference material
- **SKILL.md + `scripts/`** — skill involves deterministic or repetitive code Claude would re-write every time
- **SKILL.md + `references/`** — skill needs large docs, schemas, or variant-specific instructions
- **SKILL.md + `assets/`** — skill produces output from templates or static files
- **Full package** — combination of the above

If bundling resources, draft what those files will contain before writing SKILL.md — the SKILL.md body will reference them with clear pointers.

---

## Phase 3: Write the Skill

### SKILL.md structure

```
---
name: skill-name-in-kebab-case
description: [See guidance below — this is the most important field]
---

# [Skill Name]

[1–2 sentence summary: what it does and why it exists]

---

## Inputs
[What the user provides. Required vs optional. How to handle missing/bad input.]

## Steps
[The core instructions. Imperative, clear, explains *why* behind non-obvious steps.
Pointer example: "Use scripts/build.py rather than writing your own."
Pointer example: "Read references/schema.md for the full field reference."]

## Output format
[What the output looks like. Template or example if helpful.]

## Edge cases
[Specific situations. What to do when things go wrong.]
```

Adapt this to the skill. A simple skill may just need summary + steps + output. A complex one may need sub-sections or multiple reference pointers. The structure serves the skill — don't be rigid about it.

### Writing the description (the trigger mechanism)

This is the single most important thing you write. Claude decides whether to invoke the skill based on this field alone.

**Good descriptions:**
- State what the skill does *and* when to use it together
- Include specific trigger phrases users might say
- Are slightly "pushy" — err toward triggering rather than not
- Call out near-miss non-triggers if confusion is likely

**Template:**
> [Does X]. Invoke when user says [Y, Z, similar phrases]. Also trigger when [near-miss that should still trigger]. Do NOT use for [confusable case that should not trigger].

### Writing style rules

- Imperative form: "Check the input. If malformed, ask the user for clarification."
- Explain the *why*, not just the *what* — reasoning helps Claude generalize beyond exact examples
- Avoid walls of MUST/NEVER/ALWAYS — explain the reasoning instead; it works better
- Keep SKILL.md under 500 lines; push anything longer into `references/`
- When referencing bundled files, say *when* to load them: "Read `references/api-schema.md` only if the user is working with the v2 API"
- The `compatibility` frontmatter field is available — use it if the skill requires specific tools (e.g., `bash`, `web_search`, specific MCPs)

---

## Phase 4: Validate Before Delivering

Self-check before presenting:

- **Standard compliance**: Is the folder structure correct? Are bundled resources in the right place?
- **Progressive disclosure**: Is SKILL.md focused on instructions? Is heavy material in `references/`?
- **Description**: Would Claude know exactly when to use this from the description alone?
- **Completeness**: Does the skill cover everything from the interview?
- **Bundling**: Were scripts/references/assets that should exist actually created?
- **Length**: SKILL.md under 500 lines? Description under ~150 words?

Fix anything that fails before presenting.

---

## Phase 5: Present, Iterate, and Package

1. Show the complete skill package — SKILL.md and any bundled files.
2. Summarize key decisions, especially bundling choices and description strategy.
3. Ask: "Does this match what you had in mind? Anything missing or off?"
4. Incorporate feedback and ask again until the user is satisfied.
5. Save the full folder to `/mnt/user-data/outputs/<skill-name>/` and present the files.

---

## Guidelines that must not be forgotten

These are the things most commonly missed. Check every one before delivering:

1. **The description is the only trigger** — if it's weak, the skill never fires. Treat it like a product headline.
2. **Skills undertrigger by default** — write the description slightly more aggressively than feels natural.
3. **Scripts prevent reinvention** — if the workflow involves code Claude would write fresh each time, bundle it in `scripts/`.
4. **References enable large context** — don't cram docs into SKILL.md; put them in `references/` and load on demand.
5. **Multi-variant skills need split reference files** — one file per variant; Claude loads only the relevant one.
6. **SKILL.md is instructions, not documentation** — if it reads like a README, it's too long.
7. **Explain why, not just what** — Claude reasons well; good whys generalize better than rigid rules.
8. **Use the `compatibility` field** when the skill requires specific tools or MCPs — don't leave tool requirements implicit.

---

## Environment note (Claude.ai)

This skill runs in Claude.ai — no subagents, no browser, no CLI. No automated eval runs, no description optimizer, no parallel test cases. Keep the process conversational. The goal is a great, standard-compliant skill package in one focused session.
