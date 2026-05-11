# Skills

Personal skill library for Claude — bilingual (EN/AR) frameworks, brand systems, and analysis tools built for Startup ventures.

## Skills

| Skill | Purpose |
|---|---|
| `arabic-english-docx` | Rules for generating Arabic and mixed AR/EN Word documents — RTL handling, typography, no-italic/no-tashkeel conventions |
| `interview-analysis` | Behavioral interview analysis methodology for startup hiring — Arabic-output, signal-over-score |
| `prompt-enhancer` | Refines rough prompts into high-performing AI instructions |
| `smart-validation` | Silent pre-execution validator — surfaces flawed assumptions, contradictions, and missing prerequisites |

## Structure

Each skill follows the standard format:

```
skill-name/
├── SKILL.md          # Trigger description + core instructions
└── references/       # Optional supporting docs, case studies, patterns
```

## Usage

Drop the relevant folder into your Claude skills directory (typically `/mnt/skills/user/` or your local equivalent). Claude will auto-trigger based on the `description` field in each `SKILL.md`.
