# Study Project Skills

A Claude Code Skill for deep analysis of open-source LLM projects — extracting prompts, tool definitions, and architecture, then generating translated documentation, technical reports, and product guides.

**[中文](README_CN.md)**

## Features

- **Prompt Extraction & Translation** — Systematically scans prompts scattered across projects (system prompts, user templates, few-shot examples, etc.) using 8 search methods, translates to Chinese while preserving English originals
- **Technical Analysis Report** — Auto-generates reports covering architecture diagrams, data flow, model parameter summaries, and more
- **Product Guide** — Plain-language product guide for non-technical users
- **Large Project Support** — Tiered parallel processing strategies from small (≤30 prompts) to extra-large (300+) projects
- **Checkpoint Resume** — Progress recovery via persisted files, allowing continuation after interruption

## Installation

Clone this repo into your Claude Code skill directory:

```bash
# Global installation
git clone https://github.com/autumnseasonism/study-project-skills.git ~/.claude/skills/study-project-skills

# Or project-level installation
git clone https://github.com/autumnseasonism/study-project-skills.git .claude/skills/study-project-skills
```

## Usage

In any directory containing an open-source LLM project, trigger via Claude Code:

```
> Analyze the prompts in this project
> Study this AI project
> Show me how this project calls GPT
> Where are the prompts in this agent framework
```

The skill automatically runs through five phases:

| Phase | Description |
|---|---|
| 1. Project Exploration | Identify project structure, LLM dependencies, and scale |
| 2. Prompt Identification | Systematically search all prompts and tool definitions using 8 methods |
| 3. Documentation | Translate each prompt and generate an index |
| 4. Analysis Report | Generate a technical analysis report |
| 5. Product Guide | Generate a user-facing product guide |

## Output Structure

```
ai_analysis/
├── translated_prompts/
│   ├── MANIFEST.md              ← Prompt manifest
│   ├── INDEX.md                 ← Translation index
│   └── [translated docs]       ← Individual translations
├── AI_MODEL_USAGE_ANALYSIS.md   ← Technical report
├── PRODUCT_GUIDE.md             ← Product guide
└── ERRORS.md                    ← Error log (if any)
```

## Project Structure

```
study-project-skills/
├── SKILL.md                 ← Main skill definition
├── reference/
│   └── fault_handling.md    ← Fault handling spec
└── templates/
    ├── doc_template.md      ← Translation template
    ├── guide_template.md    ← Product guide template
    ├── report_template.md   ← Report template
    └── search_patterns.md   ← Search patterns reference
```

## Use Cases

- Quickly understand the architecture and prompt design of an unfamiliar LLM/AI project
- Generate Chinese documentation of all AI calls in a project for your team
- Learn prompt engineering practices from top open-source projects
- Audit LLM usage across a project

## License

[MIT](LICENSE)
