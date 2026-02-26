---
name: git-style-commit
description: Analyze git history for commit style, stage changes logically, and commit without pushing. Use when the user wants to commit changes matching their repository's existing style.
---

# Git Style-Aware Commit

This skill instructs the agent to autonomously analyze repository commit conventions, logically group pending changes into atomic commits, and generate matching commit messages.

## 🎯 Trigger Conditions
- User asks to "commit changes", "save work", or "create a commit".
- User provides specific files to commit.
- User wants to ensure commit messages follow existing repository standards.

## 🛠️ Execution Steps

### 1. Analyze Commit Style
Run `git log -n 15 --pretty=format:"%s"` to detect the prevailing pattern:
- **Format:** Conventional Commits (`feat:`, `fix:`), bracket prefixes (`[API]`), or plain text.
- **Capitalization & Tense:** Sentence case, title case, lowercase; present vs. past tense.
- **Emoji Usage:** Detect if Gitmoji or other emoji conventions are used.

### 2. Autonomously Stage Changes (Atomic Commits)
Review all pending changes (`git diff` and `git diff --staged`).
- **Logical Grouping:** Do NOT blindly `git add -A`. Group changes into atomic, focused commits (e.g., separate bug fixes from new features).
- **Hunk-Level Staging:** Simulate `git add -p` by staging specific files (`git add <file>`) or applying patch files for specific hunks.
- **Sequential Commits:** Create multiple commits sequentially if changes encompass distinct logical units.

### 3. Generate Commit Message
Draft a concise message (subject < 72 chars) that perfectly matches the detected style.
- **Gitmoji Integration:** If Gitmoji is detected, run `curl -s https://gitmoji.dev/api/gitmojis`, parse the JSON, and prepend the most contextually appropriate emoji.
- **Conventional Commits:** Include appropriate scope if used in the repo (e.g., `feat(auth):`).

### 4. Execute Commit
Run the commit with the generated message:
```bash
git commit -m "YOUR_GENERATED_MESSAGE"
```
*(If multiple atomic commits were identified in Step 2, repeat Steps 3-4 for each logical group).*

## ⚠️ Constraints & Error Handling
- **NEVER PUSH:** Stop immediately after creating the commit(s).
- **No History:** If no commit history exists, default to Conventional Commits (`feat:`, `fix:`, etc.).
- **Staging/Commit Failures:** If `git status` or `git diff --staged` shows issues, report the specific error to the user and halt. Do not retry automatically.
- **Verification:** Print the final commit message(s) used with a success indicator (e.g., `✅ Committed: "..."`).
