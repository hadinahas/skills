# Claude vs. ChatGPT — Prompt Tuning Differences

## Claude (Anthropic)

**Strengths**: Long-form reasoning, nuanced analysis, following complex multi-part instructions, structured outputs, intellectual depth.

**Prompt style tips**:
- Claude responds well to **explicit reasoning requests** ("Think through this step by step before responding")
- Use **XML tags** for structure in complex prompts: `<context>`, `<task>`, `<format>` — Claude handles these natively
- Claude prefers **positive instructions** ("Write in plain English") over negative ones ("Don't use jargon") — though both work
- For long outputs, specify sections explicitly: "Your response should have exactly 3 sections: X, Y, Z"
- Claude handles **nuance and caveats** well — you can ask it to flag uncertainties
- You can ask Claude to **ask you clarifying questions** before starting a task — it handles this gracefully

**Example Claude-optimized structure**:
```
<context>
[Background here]
</context>

<task>
You are a [role]. [Task description with clear action verb].
</task>

<format>
- Length: [X words]
- Structure: [sections or bullets]
- Tone: [adjective]
</format>

Think through the key considerations before writing your response.
```

---

## ChatGPT (OpenAI — GPT-4o / o-series)

**Strengths**: Broad general knowledge, instruction-following, conversational fluency, tool use (code interpreter, browsing).

**Prompt style tips**:
- ChatGPT responds well to **direct, imperative instructions** — less preamble, more command
- Persona framing works strongly: "Act as a [role]" is highly effective
- Use **numbered lists** for multi-part instructions — ChatGPT follows ordered steps reliably
- For GPT-4o: specify "think step by step" explicitly when you need reasoning shown
- For o1/o3 (reasoning models): keep prompts **shorter and cleaner** — these models do their own internal reasoning, so heavy scaffolding can interfere
- ChatGPT tends to over-explain — add "Be concise" or "No preamble" to suppress unnecessary framing

**Example ChatGPT-optimized structure**:
```
Act as a [role].

Task: [Clear imperative — "Analyze", "Write", "Compare", "Summarize"]

Context:
- [Bullet 1]
- [Bullet 2]

Instructions:
1. [Step or requirement]
2. [Step or requirement]
3. [Step or requirement]

Format: [length, structure, tone]
Do not include [what to exclude].
```

---

## When targeting both models

Use a model-agnostic structure that works well across both:
- Lead with role, then task, then context
- Specify format explicitly
- Use plain language constraints rather than XML or markdown-heavy formatting
- Avoid model-specific idioms (like XML tags for ChatGPT, or heavy numbered lists for Claude)
