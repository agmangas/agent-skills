# Agent Skills

Specialized skills for AI coding assistants.

## Skills

**Git Style-Aware Commit** (`skills/git-style-commit/`)
- Analyzes commit history and creates commits matching repository style

**NotebookLM Knowledge Base Organizer** (`skills/notebooklm-knowledge-base-organizer/`)
- Converts formats (PPTX→PDF, XLSX→CSV) and organizes files for NotebookLM

**Desloppify** (`skills/desloppify/`)
- Improves code quality by auditing maintainability issues and working through `desloppify` findings

> [!IMPORTANT]
> Attribution for the original *Codex Review* skill:
> 
> https://gist.github.com/LuD1161/84102959a9375961ad9252e4d16ed592

**Codex Review** (`skills/codex-review/`)
- Iteratively reviews implementation plans using OpenAI Codex and Claude—Claude revises the plan based on Codex feedback up to 5 rounds, ensuring correctness and risk reduction before approval.

Skills are automatically invoked when you describe matching tasks.
