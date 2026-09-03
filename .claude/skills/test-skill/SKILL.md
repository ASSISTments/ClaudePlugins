---
name: test-skill
description: Checks an edited skill or manifest before it is submitted — runs plugin validation, catches the structural mistakes that make a skill silently fail to load, installs the code under test, and gives the user concrete prompts to try the skill for real. Can test a pull request that isn't checked out locally.
when_to_use: >-
  Use after editing any SKILL.md, plugin.json, or marketplace.json, and whenever
  the user asks "does this work", "can we try it", "test this", "check my
  changes", "will Claude pick this up", or wonders whether a skill will actually
  trigger. Always use before submit-for-review. Also use to review a teammate's
  pull request — "test the skill from PR #12", or a pasted PR link.
argument-hint: <skill-name> [PR link or number]
allowed-tools: Bash(claude plugin validate:*), Bash(git status:*), Bash(git diff:*), Bash(git fetch:*), Bash(git worktree:*), Bash(git branch -D:*), Bash(gh pr view:*), Bash(wc:*), Bash(ls:*)
---

# Test a skill before submitting

Nothing here is a formality — a skill can be perfectly worded and still never load.

## 1. What are we testing

The skill name is required — if the user didn't name one, ask which skill.

A PR link or number is optional. It means: test the exact code in that PR, not what's currently checked out — the reviewer flow in CLAUDE.md's "Reviewing each other's work," for a teammate who does not have that branch on their machine.

## 2. Locate the code

- **No PR given** → test the current working directory, as-is.
- **PR given** → the reviewer's own branch and any uncommitted edits must not be disturbed:
  1. `gh pr view <PR> --json number,headRefName,url` to resolve it — accepts a bare number or a full URL, no manual parsing needed.
  2. `git fetch origin pull/<number>/head:pr-<number>` — works even if the PR is from a fork, unlike fetching by branch name.
  3. `git worktree add ../ClaudePlugins-pr-<number> pr-<number>` — a sibling directory, so the reviewer's current branch is untouched. If this worktree already exists from testing this PR before, reuse it: just re-fetch to update, don't recreate it.
  4. Every step below runs scoped to that worktree path, not the reviewer's own checkout.

## 3. Validate the manifests

```bash
claude plugin validate .
```

Run it against the located directory (`-C <path>` when a worktree is in play). Fix anything it reports before going further. If it passes, say so briefly; don't paste the whole output.

## 4. Check the failure modes validation does not catch

Read the changed files, from the located directory, and verify each:

- **Skill in the right place.** `plugins/<plugin>/skills/<name>/SKILL.md`. A skill nested inside `.claude-plugin/` is never loaded — validation will not complain.
- **`name` matches its directory name**, exactly, in kebab-case.
- **`version` bumped in `plugin.json`** if anything under `plugins/` changed. If it wasn't, stop and bump it — patch for wording, minor for a new or reworked skill, major for a rename or removal. An unbumped version means installed users receive nothing and the change silently does nothing.
- **No path escapes the plugin directory.** Plugins are copied into a cache on install, so `../anything` will not exist at runtime.
- **`marketplace.json` and `README.md`** updated if a plugin or skill was added or renamed.
- **Length.** `wc -w` the SKILL.md; over roughly 500 words, move reference material into a sibling file per CLAUDE.md.

## 5. Judge the description, out loud

The `description` (plus `when_to_use`) is the *only* thing Claude sees when deciding whether to load the skill. Read it back to the user and give a straight assessment: on the phrasings a teacher would actually type, would this fire? Name any real trigger it misses, and any unrelated trigger it would wrongly catch. If the description needs "and" to cover unrelated cases, say that it is two skills.

## 6. Install the code under test

Always swap explicitly — never leave it ambiguous whether the marketplace is pointed at the old or new code:

```
/plugin uninstall math@assistments
/plugin marketplace remove assistments
/plugin marketplace add <path being tested>
/plugin install math@assistments
/reload-plugins
```

`<path being tested>` is the current directory for a self-test, or the worktree path from step 2 for a PR. Tell the user plainly: this points `assistments` at the code under test until they run [[update-assistments-plugin]] to switch back to the published version.

The trailing `/reload-plugins` is a safety step, not the mechanism doing the work — the fresh install is. It exists in case the current session doesn't otherwise pick up a fresh install of a plugin whose name was already loaded earlier in this same conversation.

Plugins are copied into a cache at install time, and this is a local dev install with no pinned version — so neither `/plugin marketplace update assistments` (only refreshes the catalog, not an installed plugin's files) nor `/reload-plugins` alone, without reinstalling (only activates what's already cached), is guaranteed to pick up a further edit. After every further edit, redo the full swap above, including the trailing `/reload-plugins`.

If a worktree was created in step 2, once the user is done testing (not necessarily right away — they may still be iterating), clean it up: `git worktree remove <path>` and `git branch -D pr-<number>`. Safe to force-delete — it's a disposable local-only ref. This is separate from restoring the plugin install; doing one doesn't do the other.

## 7. Hand off the real test

Structural checks cannot tell you whether the skill *behaves*. Give the user two or three prompts to paste into a **new** session — phrased the way a teacher would, not using the skill's own vocabulary — and say exactly what to look for:

- Does the skill fire without being named?
- Does it follow its own steps in order, including the stopping points where it should wait for approval?
- On a near-miss prompt that should *not* trigger it, does it stay quiet?

Report what passed, what you could not check yourself, and what you changed. Then: if this was a self-test, offer [[submit-for-review]]. If this was a PR review, offer to help leave a review comment or approval on the PR instead — [[submit-for-review]] doesn't apply to a reviewer.
