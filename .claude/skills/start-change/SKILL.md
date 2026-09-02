---
name: start-change
description: Puts new work on its own correctly-named branch, cut from an up-to-date main, before any editing begins — and decides whether a new branch is even the right move versus continuing on the current one.
when_to_use: Use before editing any file in this repo when the current branch is main, or when the user says they want to change, add, fix, reword, or improve something — including "let's edit the unpacker skill", "I want to add a new skill", "can you fix the wording in", "start a new change", or "work on something else now". Also use when the user is mid-session and pivots to an unrelated change.
argument-hint: [what you want to change]
allowed-tools: Bash(git fetch:*), Bash(git status:*), Bash(git switch:*), Bash(git branch:*), Bash(git log:*), Bash(gh pr list:*), Bash(gh pr view:*)
---

# Start a change

The user wants to work on: **$ARGUMENTS**

Set up a clean place for that work. Run the git yourself — never ask the user to type a command.

## First decide whether a new branch is right

Run `git fetch origin`, `git status --short --branch`, and `gh pr list --state open --json number,title,headRefName,author`.

- **Current branch has an open PR, and this change belongs to it** (same change, or answering review feedback) → stay put. Say so in one sentence, and skip the rest of this skill.
- **Current branch has commits not yet pushed, and no PR** → stop. Starting a new branch now strands that work somewhere the user won't find it. Say what's on it and offer [[submit-for-review]] first.
- **Otherwise** → new branch.

## Create the branch

1. If there are uncommitted edits, **keep them**. They carry over to the new branch automatically. Do not stash, reset, discard, or commit them to `main`. If some of those files look unrelated to `$ARGUMENTS`, name them and ask which belong on this branch before continuing.
2. Name it `<type>/<kebab-slug>` from `$ARGUMENTS`:
   - `feat/` — a new skill or a new capability in an existing one
   - `fix/` — corrected behavior or a wrong instruction
   - `docs/` — wording, README, CLAUDE.md
   - `chore/` — manifests, versions, repo plumbing
3. `git switch -c <name> origin/main` — one step, and it guarantees the branch starts from the freshly fetched remote state.

If the name already exists, do not append a number. Check what is on the existing branch, tell the user, and agree on a name.

## Confirm, then work

Two sentences maximum: the branch name, that in-progress edits came along (if any), and that nothing is on GitHub yet.

Then do the actual work on `$ARGUMENTS`, following the content conventions in CLAUDE.md — in particular, a `SKILL.md` `description` is written for dispatch rather than for humans, and anything changed under `plugins/` needs a `version` bump in `plugin.json` before it can be submitted.

When the edit is done, test it with [[test-skill]] before [[submit-for-review]].
