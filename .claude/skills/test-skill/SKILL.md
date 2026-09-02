---
name: test-skill
description: Checks an edited skill or manifest before it is submitted — runs plugin validation, catches the structural mistakes that make a skill silently fail to load, installs the local checkout, and gives the user concrete prompts to try the skill for real.
when_to_use: Use after editing any SKILL.md, plugin.json, or marketplace.json, and whenever the user asks "does this work", "can we try it", "test this", "check my changes", "will Claude pick this up", or wonders whether a skill will actually trigger. Always use before submit-for-review.
allowed-tools: Bash(claude plugin validate:*), Bash(git status:*), Bash(git diff:*), Bash(wc:*), Bash(ls:*)
---

# Test a skill before submitting

Nothing here is a formality — a skill can be perfectly worded and still never load.

## 1. Validate the manifests

```bash
claude plugin validate .
```

Fix anything it reports before going further. If it passes, say so briefly; don't paste the whole output.

## 2. Check the failure modes validation does not catch

Read the changed files and verify each:

- **Skill in the right place.** `plugins/<plugin>/skills/<name>/SKILL.md`. A skill nested inside `.claude-plugin/` is never loaded — validation will not complain.
- **`name` matches its directory name**, exactly, in kebab-case.
- **`version` bumped in `plugin.json`** if anything under `plugins/` changed. If it wasn't, stop and bump it — patch for wording, minor for a new or reworked skill, major for a rename or removal. An unbumped version means installed users receive nothing and the change silently does nothing.
- **No path escapes the plugin directory.** Plugins are copied into a cache on install, so `../anything` will not exist at runtime.
- **`marketplace.json` and `README.md`** updated if a plugin or skill was added or renamed.
- **Length.** `wc -w` the SKILL.md; over roughly 500 words, move reference material into a sibling file per CLAUDE.md.

## 3. Judge the description, out loud

The `description` (plus `when_to_use`) is the *only* thing Claude sees when deciding whether to load the skill. Read it back to the user and give a straight assessment: on the phrasings a teacher would actually type, would this fire? Name any real trigger it misses, and any unrelated trigger it would wrongly catch. If the description needs "and" to cover unrelated cases, say that it is two skills.

## 4. Install the local copy

```
/plugin marketplace add /absolute/path/to/ClaudePlugins
/plugin install math@assistments
/plugin marketplace update assistments
```

**Warn the user first:** a marketplace name registers once per user, so pointing `assistments` at the local checkout *replaces* the GitHub-hosted one until they re-add it. Tell them how to restore it (`/plugin marketplace add ASSISTments/ClaudePlugins`) and confirm before switching.

Skills are read at load time, so after every edit run `/plugin marketplace update assistments` or `/reload-plugins`.

## 5. Hand off the real test

Structural checks cannot tell you whether the skill *behaves*. Give the user two or three prompts to paste into a **new** session — phrased the way a teacher would, not using the skill's own vocabulary — and say exactly what to look for:

- Does the skill fire without being named?
- Does it follow its own steps in order, including the stopping points where it should wait for approval?
- On a near-miss prompt that should *not* trigger it, does it stay quiet?

Report what passed, what you could not check yourself, and what you changed. Then offer [[submit-for-review]].
