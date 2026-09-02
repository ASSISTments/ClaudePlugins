---
name: submit-for-review
description: Takes finished work from the user's machine to a pull request — validates, checks the version bump, commits, pushes the branch, opens the PR, and reports who needs to approve it. Handles the case where a PR is already open by updating it instead.
when_to_use: Use when the user signals the work is finished and should go out — "I'm done", "save this", "push it up", "ship it", "submit this", "open a PR", "send it for review", "make this live", "commit this" — or when they have commits or edits that are not yet on GitHub and want them shared.
argument-hint: [optional: what this change does]
allowed-tools: Bash(git fetch:*), Bash(git status:*), Bash(git diff:*), Bash(git log:*), Bash(git add:*), Bash(git commit:*), Bash(git push:*), Bash(git branch:*), Bash(git config user.email:*), Bash(claude plugin validate:*), Bash(gh pr list:*), Bash(gh pr view:*), Bash(gh pr create:*)
---

# Submit for review

Run every step yourself. Report in plain language.

## 1. Do not submit from `main`

If the current branch is `main`, the push will be rejected by the ruleset. Move the work to a branch first via [[start-change]], then continue here. Never work around this by pushing to `main`, even with admin bypass.

## 2. Gate before committing

- `claude plugin validate .` must pass.
- If anything under `plugins/` changed, `plugin.json`'s `version` must have been bumped. If not, bump it now — this is the difference between the change reaching users and doing nothing.
- If [[test-skill]] hasn't run on this change, run it.
- `git config user.email` must be an email verified on the user's GitHub account. The ruleset demands an extra approval for commits it can't attribute, so a mismatch here turns into a mysterious blocked PR later. If it looks wrong, say so before committing.

## 3. Show before sending

This repo is **public**. Show the user `git diff --stat` plus the proposed commit message and PR title, and flag anything that shouldn't be published — student names, district names, unreleased plans. Get a yes before the push. Pushing is the point where the work becomes visible to everyone.

## 4. Commit and push

- Stage only files that belong to this change. If unrelated edits are present, name them and ask.
- Commit message: `<type>(<scope>): <imperative summary>`, matching the existing history (`feat(/math-standard-unpacker): init skill`, `docs: init README and CLAUDE.md`). Body only if the *why* isn't obvious from the summary. End with:

  ```
  Co-Authored-By: Claude Opus 5 (1M context) <noreply@anthropic.com>
  ```

- `git push -u origin <branch>`

## 5. Open or update the PR

Check `gh pr list --head <branch>` first.

- **PR already open** → the push already updated it. Say so, and if the change answers review comments, say which threads still need resolving — the ruleset requires all threads resolved before merge.
- **No PR** → `gh pr create`. Title matches the commit summary. Body: what changed, why, and how a reviewer can check it (the trigger prompts from [[test-skill]] belong here). End with:

  ```
  🤖 Generated with [Claude Code](https://claude.com/claude-code)
  ```

## 6. Report what happens next

Give the user the PR number and URL, then the honest state of it:

- It needs **one approving review from someone other than them** — they cannot approve their own. Name who can.
- Merge is **squash only**; the branch is deleted automatically afterward.
- All review comment threads must be resolved first.

Do **not** merge the PR. If the user asks you to merge their own PR and they hold admin bypass, tell them plainly that it skips the review the ruleset requires, and get explicit confirmation before doing it.

After it merges, [[repo-status]] will pick up the cleanup: update `main`, delete the finished local branch.
