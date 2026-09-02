---
name: repo-status
description: Reports where the user's in-progress work stands in this repo — unsaved edits, which branch they're on, open pull requests and what each is waiting on — and recommends one next action. Read-only; never changes anything.
when_to_use: Use whenever the user seems unsure of the state of their work or asks any variation of "where is my change", "did that go through", "is it live yet", "what happened to my edit", "did anyone approve it", "what should I do next", "is anything waiting on me", or asks about pull requests, branches, or whether something was saved. Also run this before any other repo action when the current state is unknown.
allowed-tools: Bash(git fetch:*), Bash(git status:*), Bash(git branch:*), Bash(git log:*), Bash(git diff --stat:*), Bash(gh pr list:*), Bash(gh pr view:*), Bash(gh pr checks:*)
---

# Repo status

Orient the user. They may not know what a branch is. Report state as plain facts about *their work*, never in git jargon.

## Gather (read-only)

1. `git fetch origin`
2. `git status --short --branch`
3. `git log --oneline origin/main..HEAD` — commits made but not yet on GitHub
4. `gh pr list --state open --json number,title,author,reviewDecision,mergeStateStatus,headRefName`
5. If the current branch has a PR: `gh pr view --json number,reviewDecision,mergeStateStatus,reviewThreads`

## Report

- **What you're working on** — the branch, described by what it changes rather than by its name.
- **Unsaved edits** — files changed but not committed, by name. Say "not on GitHub yet," not "in the working tree."
- **Open pull requests** — for each: number, what it does, and its actual blocker. Be specific: *waiting on a reviewer* / *has unresolved comments* / *approved and ready to merge* / *failing a check*. Never just "open."
- **What to do next** — exactly one recommended action, and offer to do it.

## Cases that actually confuse people — handle each explicitly

- **Uncommitted edits on `main`.** The most common bad state. The edit is fine, it's just in the wrong place. Offer to move it onto a new branch — [[start-change]] does this safely, since uncommitted edits follow a branch switch.
- **Their PR merged since they last looked.** Say so plainly, then offer to update `main` and delete the finished branch.
- **Local `main` behind `origin/main`.** Offer to update it. Skip the explanation of why.
- **Commits on a branch with no PR.** The work is invisible to everyone else and cannot be merged. Offer [[submit-for-review]].
- **A PR waiting on review.** Name who can approve it. The ruleset requires one approving review from someone *other than the author*, so the author cannot unblock themselves.
- **Nothing in flight.** Say the repo is clean and up to date, and stop. Do not invent work.

## Constraint

This skill only reads. Never commit, push, switch branches, discard changes, or merge as part of a status check — even if the fix looks obvious. Recommend and offer; let the user say yes.
