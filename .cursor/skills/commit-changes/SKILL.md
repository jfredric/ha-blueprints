---
name: commit-changes
description: >-
  Commit local git changes with a user-reviewed message. Checks the current
  branch, warns and requires confirmation when on main, drafts the commit
  message from the diff, presents it for accept or modify, then commits only
  after approval. Never pushes to remote. Use when the user asks to commit,
  save work to git, or create a commit.
---

# Commit Changes

## Hard Rules

- **Never push.** Do not run `git push` or any command that publishes to a remote. If the user wants to push, tell them to do it themselves.
- **Never commit without approval.** Always show the proposed message and wait for the user to accept or modify before running `git commit`.
- **Only commit when asked.** Do not commit proactively.
- **Never update git config** or run destructive git commands (`push --force`, `reset --hard`, etc.) unless the user explicitly requests them.
- **Never skip hooks** (`--no-verify`, `--no-gpg-sign`) unless the user explicitly requests it.
- **Do not commit secrets** (`.env`, credentials, tokens). Warn the user if they ask to commit those files.
- **Confirm main commits every time.** If the current branch is `main` or `master`, warn the user and require explicit confirmation that they want to commit directly to that branch — on every commit attempt, even if they confirmed earlier in the same conversation.

## Workflow

### 1. Inspect changes

Run these in parallel:

```bash
git branch --show-current
git status
git diff
git diff --staged
git log --oneline -5
```

Review all staged and unstaged changes. Do not commit files that look like secrets.

### 2. Check branch — stop if on main

If the current branch is `main` or `master`:

1. **Warn prominently** that commits will land directly on the default branch.
2. **Ask for explicit confirmation** that committing to `main`/`master` is intentional.
3. Do **not** proceed to draft the message or commit until the user confirms.

This confirmation is required **every single time** the user asks to commit while on `main`/`master` — never skip it based on a prior confirmation in the conversation.

Use the AskQuestion tool when available:

- Yes — commit directly to main
- No — cancel (suggest creating or switching to a feature branch)

If the user declines, stop. Do not commit.

If on any other branch, note the branch name in the review summary and continue.

### 3. Draft the commit message

- Summarize the **why**, not just the what (1–2 sentences).
- Match the repo's recent commit message style from `git log`.
- Use accurate verbs: `add` (new feature), `update` (enhancement), `fix` (bug fix), etc.

### 4. Present for review — stop here

Show the user:

1. **Current branch** (call out if `main`/`master`)
2. **Files to be committed** (list paths)
3. **Proposed commit message** (exact text that will be used)

Then ask the user to **accept**, **modify**, or **cancel**.

Use the AskQuestion tool when available:

- Accept — proceed with this message
- Modify — user provides edits; revise and present again
- Cancel — do not commit

Do **not** run `git add` or `git commit` until the user accepts.

### 5. Commit after approval

Once accepted:

```bash
git add <relevant-files>
git commit -m "$(cat <<'EOF'
<approved message>

EOF
)"
git status
```

If pre-commit hooks modify files and the commit succeeds, mention what changed. If the commit **fails** or is **rejected** by a hook, fix the issue and create a **new** commit — do not amend unless all amend conditions are met (see below).

### Amend rules

Only use `git commit --amend` when **all** of these are true:

1. User explicitly requested amend, **or** commit succeeded but hooks auto-modified files
2. HEAD commit was created by you in this conversation
3. Commit has **not** been pushed to remote

If the commit failed, never amend — fix and create a new commit.

## Example prompts

### On main — branch confirmation (required first)

```
⚠️ You are on branch `main`. These changes will be committed directly to main.

Do you want to commit directly to main? (Yes / No — create or switch to a feature branch)
```

Do not show the commit message or run any commit commands until the user confirms.

### Message review (after branch checks pass)

```
Branch: feature/cursor-config

Files to commit:
- .cursor/rules/entity-first-references.mdc
- .cursor/skills/commit-changes/SKILL.md

Proposed commit message:
Add Cursor rules and commit workflow skill.

Introduce entity-first blueprint guidance and a commit skill that
requires message review before committing and never pushes to remote.

Accept this message, or tell me how to modify it?
```
