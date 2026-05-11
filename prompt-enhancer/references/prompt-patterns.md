# Advanced Prompt Patterns

Reference these patterns when a basic enhancement isn't enough for the task complexity.

---

## Chain-of-Thought (CoT)

**When to use**: Complex analysis, multi-step reasoning, decisions with trade-offs.

**How to apply**:
Add one of these phrases to the prompt:
- "Think through this step by step before giving your final answer."
- "Walk me through your reasoning before reaching a conclusion."
- "First outline the key considerations, then provide your analysis."

**Effect**: Forces the model to reason visibly, reducing hallucination and improving accuracy on complex tasks.

---

## Few-Shot Prompting

**When to use**: Specific output format required, tone needs to match a sample, novel task type.

**How to apply**:
```
Here are two examples of the kind of output I want:

Example 1:
[Input]: ...
[Output]: ...

Example 2:
[Input]: ...
[Output]: ...

Now do the same for:
[Input]: [actual task]
```

**Effect**: Anchors the model to a specific style, structure, and quality level.

---

## Meta-Prompting

**When to use**: User isn't sure how to frame the task, or the task is highly complex.

**How to apply**:
Ask the AI to help design the prompt first:
```
I want to [goal]. Before you do the task, help me write the best possible prompt to give you for this, then execute it.
```

**Effect**: The model uses its own knowledge of its capabilities to structure the best instruction.

---

## Reflection Prompting

**When to use**: Quality-sensitive outputs — reports, strategic memos, high-stakes writing.

**How to apply**:
Add at the end of the prompt:
```
After writing your response, review it against these criteria:
1. [Criterion 1 — e.g., "Is every claim supported by reasoning?"]
2. [Criterion 2 — e.g., "Is the tone appropriate for an executive audience?"]
3. [Criterion 3 — e.g., "Is it under [X] words?"]
If any criterion isn't met, revise before finalizing.
```

**Effect**: Builds a self-review loop into a single prompt, catching errors before they reach the user.

---

## Prompt Chaining

**When to use**: Long, complex tasks that benefit from being broken into stages.

**How to apply**:
Instead of one giant prompt, design a sequence:
1. Prompt 1: Research / gather raw material
2. Prompt 2: Analyze / synthesize
3. Prompt 3: Format / finalize

Pass the output of each step as context into the next.

**Effect**: Each step is cleaner, more focused, and easier to verify. Reduces hallucination in multi-step reasoning.

---

## Constraint Stacking

**When to use**: When past prompts keep producing outputs that are too long, too generic, or off-topic.

**How to apply**:
Layer explicit constraints:
```
- Do not exceed [X] words
- Avoid [specific content type]
- Do not start with a preamble or summary of what you're about to do
- Use specific examples, not general principles
- If you're unsure about a claim, say so explicitly
```

**Effect**: Tightens the output without restricting the model's reasoning.
