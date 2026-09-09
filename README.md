# Agent Skills

Installable skills for AI coding assistants.

## Skills

| Skill | Path | Purpose |
| --- | --- | --- |
| Git Style-Aware Commit | `skills/git-style-commit/` | Analyzes commit history and creates commits matching repository style. |
| EU Proposal Organisation Assessment | `skills/eu-proposal-organisation-assessment/` | Explains a named organisation's role, partner boundaries, required expertise, and timeline in a Horizon Europe or LIFE proposal. |
| NotebookLM Knowledge Base Organizer | `skills/notebooklm-knowledge-base-organizer/` | Converts formats such as PPTX to PDF and XLSX to CSV, then organizes files for NotebookLM. |
| Explore | `skills/explore/` | Explores library and API options with verified examples and source references to inform implementation decisions. |
| Plainspoken | `skills/plainspoken/` | Makes agent output clear, concise, and easy to understand without dumbing down software engineering content. |
| Local Delegation | `skills/local-delegation/` | Offloads bounded codebase browsing, inventories, extraction, log triage, and simple single-file reasoning to a bundled LM Studio helper while keeping final judgment in the main model. |

## Attribution

Local Delegation was inspired by [alisorcorp/ask-local](https://github.com/alisorcorp/ask-local). Its bundled helper scripts are adapted from that project; the original MIT license is included at `skills/local-delegation/LICENSE.ask-local.txt`.
