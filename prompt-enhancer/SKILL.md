---
name: prompt-enhancer
description: >
  Activate this skill whenever the user wants to improve, refine, enhance, or strengthen a prompt before sending it to an AI. Triggers include: "enhance this prompt", "improve my prompt", "make this prompt better", "help me prompt", "rewrite this for Claude/ChatGPT", "my prompt isn't working", "how should I phrase this", "turn this into a good prompt", or any time the user shares a rough idea and wants it turned into a polished, high-performing AI instruction. Use this skill proactively even if the user just pastes a rough draft and says "fix this" — if the deliverable is a better prompt, this skill applies. Do NOT use for general writing improvement unrelated to AI prompting.
---

# Prompt Enhancer Skill

This skill transforms rough, vague, or underspecified user prompts into high-performing AI instructions — optimized primarily for **Claude** and **ChatGPT/OpenAI** models, with a focus on strategy & business analysis, research & summarization, and writing & content tasks.

The approach is **interactive-first**: before enhancing, ask targeted clarifying questions to fill critical gaps. Then produce a single, polished enhanced prompt with a brief explanation of what was changed and why.

---

## The Anatomy of a Strong Prompt

Every high-performing prompt has these six elements. Diagnose which are missing before enhancing:

| Element | What it does | Common failure mode |
|---|---|---|
| **Role / Persona** | Frames the AI's perspective and expertise | Omitted entirely — AI defaults to generic mode |
| **Task** | The specific action to perform | Too vague ("write about X" vs "write a 3-section analysis of X") |
| **Context** | Background, situation, constraints | Missing — AI invents assumptions |
| **Format** | Output structure, length, tone | Unspecified — AI picks arbitrarily |
| **Constraints** | What to avoid, boundaries, scope limits | Absent — AI goes off-track |
| **Examples / Anchors** | Samples of desired output style | Rarely included — leaves too much to interpretation |

---

## Step 1: Triage the Raw Prompt

When the user shares their prompt or idea, quickly assess:

1. **Task clarity** — Is it obvious what the AI should *do* (analyze, write, summarize, compare, decide)?
2. **Context sufficiency** — Does the AI have enough background to avoid making up assumptions?
3. **Format specification** — Is the desired output shape defined (length, structure, tone)?
4. **Audience** — Who is this output for? (affects tone, depth, technicality)
5. **Model target** — Claude or ChatGPT? (minor structural differences apply — see References)

---

## Step 2: Ask Clarifying Questions (Interactive Mode)

Ask **2–4 focused questions** — never more. Prioritize the gaps that will most change the output. Use this decision logic:

- **Always ask** if the task is ambiguous or the context is thin
- **Always ask** about output format if unspecified (length? structure? tone?)
- **Ask** about audience if the prompt involves writing/content
- **Ask** about constraints if the topic is open-ended (scope, what to exclude)
- **Skip** questions where reasonable defaults are obvious

**Example clarifying questions by gap:**
- *Task vague*: "What's the main thing you want the AI to do — analyze, write, compare, or recommend?"
- *Context thin*: "Who is this for, and what decisions will this output inform?"
- *Format missing*: "How long should the output be, and what structure do you have in mind (bullets, sections, table)?"
- *Audience unclear*: "Is this for an internal audience who knows the context, or an outsider who needs more background?"
- *Scope open*: "Are there angles or subtopics you want the AI to avoid?"

---

## Step 3: Enhance the Prompt

Using answers from Step 2 and the six-element framework, produce the enhanced prompt.

**Enhancement principles (in priority order):**

1. **Assign a role** — "You are a [senior strategist / market researcher / executive editor]…"
2. **Sharpen the task verb** — Replace "write about" → "produce a structured 3-section analysis of"; "summarize" → "extract the 5 key takeaways from"
3. **Inject context** — Add what the AI needs to know that it can't infer
4. **Specify output format explicitly** — Length, structure, tone, what to include/exclude
5. **Add a constraint or two** — "Do not include X", "Avoid jargon", "Keep to under 400 words"
6. **Close with the desired outcome** — What will the user *do* with this output?

**Claude vs. ChatGPT tuning** → See `references/model-differences.md` for model-specific adjustments.

---

## Step 4: Deliver the Output

Present in this structure:

```
## ✦ Enhanced Prompt

[The full enhanced prompt, ready to copy]

---

## What changed & why

- **Role added**: [brief reason]
- **Task sharpened**: [what was changed]
- **Context injected**: [what was added]
- **Format specified**: [what was defined]
- [any other changes]
```

Keep the "What changed" section concise — 3–5 bullet points max. The goal is to teach the user *why* the changes matter, not just what they are.

---

## Quick Reference: Prompt Templates by Task Type

### Strategy & Business Analysis
```
You are a [senior strategy consultant / business analyst].
Analyze [topic/situation] from the perspective of [stakeholder or lens].
Context: [relevant background in 2–3 sentences].
Structure your response as: (1) Key findings, (2) Strategic implications, (3) Recommended actions.
Keep it to [X words / X bullet points]. Avoid generic advice — be specific to the context provided.
Goal: This will be used to [decision/presentation/internal review].
```

### Research & Summarization
```
You are an expert researcher in [domain].
Review the following [content/topic] and extract the [N] most important insights.
Context: [why this research matters / what decision it supports].
Format: [bullet points / structured sections / executive summary].
Prioritize [recency / practical applicability / strategic relevance].
Exclude [what's out of scope].
```

### Writing & Content
```
You are a [professional writer / editor / copywriter] specializing in [domain].
Write a [format: memo / article / email / report] about [topic].
Audience: [who will read this — their background, familiarity with the topic].
Tone: [formal / conversational / persuasive / neutral].
Length: approximately [X words / X paragraphs].
Include: [specific elements to cover].
Avoid: [clichés / jargon / specific topics].
```

---

## References

- `references/model-differences.md` — Claude vs. ChatGPT structural differences and prompt tuning tips
- `references/prompt-patterns.md` — Advanced patterns: chain-of-thought, few-shot, meta-prompting, reflection prompting
