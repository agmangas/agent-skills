# Agent Skills

Installable skills for AI coding assistants.

## Skills

| Skill | Path | Purpose |
| --- | --- | --- |
| Git Style-Aware Commit | `skills/git-style-commit/` | Analyzes commit history and creates commits matching repository style. |
| NotebookLM Knowledge Base Organizer | `skills/notebooklm-knowledge-base-organizer/` | Converts formats such as PPTX to PDF and XLSX to CSV, then organizes files for NotebookLM. |
| Desloppify | `skills/desloppify/` | Improves code quality by auditing maintainability issues and working through `desloppify` findings. |
| Plainspoken | `skills/plainspoken/` | Makes agent output clear, concise, and easy to understand without dumbing down software engineering content. |
| Local Delegation | `skills/local-delegation/` | Offloads bounded codebase browsing, inventories, extraction, log triage, and simple single-file reasoning to a bundled LM Studio helper while keeping final judgment in the main model. |
| Codex Review | `skills/codex-review/` | Iteratively reviews implementation plans or local code changes using OpenAI Codex and Claude, then revises based on feedback before approval. |

## Attribution

Local Delegation was inspired by [alisorcorp/ask-local](https://github.com/alisorcorp/ask-local). Its bundled helper scripts are adapted from that project; the original MIT license is included at `skills/local-delegation/LICENSE.ask-local.txt`.

Codex Review was inspired by the original [Codex Review skill gist](https://gist.github.com/LuD1161/84102959a9375961ad9252e4d16ed592).
