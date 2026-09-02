# CLAUDE.md

Guidance for working in this repository.

## What this repo is

A Claude Code **plugin marketplace** named `assistments`. It contains the catalog (`.claude-plugin/marketplace.json`) and the plugins it distributes (`plugins/*`). There is no build step, no test suite, and no runtime code — the deliverables are JSON manifests and markdown skills.

Current plugins: `math` (math assessment authoring skills).

## Who works in this repo

Most maintainers are product and pedagogy staff, not engineers. They own the *content* judgment — what a skill should say — and rely on you for everything mechanical.

- **Run git and `gh` yourself.** Never hand the user a git command to type or tell them to "open a terminal." Do it, then report what changed in plain language ("I put your edit on a branch called `docs/clearer-triggers` and opened PR #12 — Uche needs to approve it").
- Don't explain git internals unless asked. "Your change is saved on its own branch" beats "I created a feature branch off `origin/main`."
- **Never** run a destructive git operation (`reset --hard`, `checkout -- <file>`, `push --force`, branch deletion) on work you did not just create, without describing what would be lost and getting explicit confirmation.
- Any procedure the user will repeat belongs in a skill in `.claude/skills/`, not in a chat explanation. Existing ones: `/repo-status`, `/start-change`, `/test-skill`, `/submit-for-review`. They are model-invoked too — expect them to fire on plain phrasings like "did my change go through?", so don't re-derive their procedures by hand.
- The repo is **public**. Flag it before committing anything with student names, district names, or unreleased roadmap detail.

## Layout rules

```
.claude-plugin/marketplace.json          # catalog — lists every plugin and its source
plugins/<plugin>/.claude-plugin/plugin.json
plugins/<plugin>/skills/<skill>/SKILL.md # shipped to users who install the plugin
.claude/skills/<skill>/SKILL.md          # maintainer workflow — never shipped
```

Two kinds of skill live here and must not be confused. Anything under `plugins/` is a **product**: it is distributed to everyone who installs the plugin, so it needs a version bump and a review. Anything under `.claude/skills/` is **tooling for working in this repo** and reaches no one outside it, so it needs neither.

- **Only `plugin.json` goes inside a plugin's `.claude-plugin/` directory.** `skills/`, `commands/`, `agents/`, and `hooks/` live at the plugin root. Components nested inside `.claude-plugin/` are not loaded.
- Plugin `source` paths in `marketplace.json` are relative to the marketplace root and must start with `./`.
- Plugins are copied into a cache on install, so a plugin must not reference files outside its own directory (`../shared/...` will not exist at runtime). Duplicate or symlink instead.

## Adding a skill

1. Create `plugins/<plugin>/skills/<kebab-case-name>/SKILL.md`.
2. Frontmatter: always set `name` (must match the directory name) and `description`. Everything else is optional.
3. Write the `description` for dispatch, not for humans: it is the only thing Claude sees when deciding whether to load the skill, so name the concrete triggers ("Use when the user asks for hints, scaffolds, feedback on wrong answers…").
4. Body is instructions to Claude, in the imperative. State the procedure and the judgment calls; do not restate general knowledge.
5. Cross-reference sibling skills with `[[skill-name]]`.
6. Bump the plugin's `version` in `plugin.json` (see below).

Keep skills single-purpose. If a skill's description needs "and" to cover unrelated triggers, it is two skills.

## Adding a plugin

1. `mkdir -p plugins/<name>/.claude-plugin plugins/<name>/skills`
2. Write `plugin.json` with at minimum `name` (kebab-case, matching the directory). Add `description`, `version`, `author`, `keywords`.
3. Add an entry to the `plugins` array in `.claude-plugin/marketplace.json` with `name` and `source: "./plugins/<name>"`.
4. Update the plugin table in `README.md`.

## Versioning

- The plugin's `version` lives in **`plugin.json` only** — deliberately not duplicated in the marketplace entry, so there is one place to bump.
- Users only receive updates when that string changes. Bump it in the same commit as any change to a plugin's contents, following semver: patch for wording fixes, minor for new or reworked skills, major for renamed or removed skills.
- Renaming or removing a plugin requires a `renames` entry in `marketplace.json` so installed users migrate cleanly.

## Validation

Run before committing any manifest or skill change:

```bash
claude plugin validate .                      # marketplace + all plugins
claude plugin validate ./plugins/math         # one plugin
```

Then verify behavior against a local checkout rather than trusting the manifests:

```
/plugin marketplace add /absolute/path/to/ClaudePlugins
/plugin install math@assistments
/plugin marketplace update assistments        # after each edit
```

Skills are read at load time — after editing a `SKILL.md`, refresh the marketplace (or `/reload-plugins`) before testing.

## Branching, commits, and PRs

`main` is protected by a ruleset. Direct pushes are rejected. Every change must go through a pull request that has **one approving review from someone other than its author**, all review threads resolved, and is merged with **squash** (the only method the ruleset allows). Merged branches are deleted automatically on the remote.

### Starting a change

1. `git fetch origin` first, always. Never branch off a stale local `main`, and never branch off another feature branch.
2. `git switch -c <type>/<slug> origin/main` — this creates the branch from the freshly fetched remote state in one step.
3. Branch names: `feat/`, `fix/`, `docs/`, or `chore/` plus a kebab-case slug describing the change (`docs/clarify-quiz-triggers`).

If the working tree is already dirty when a new change starts, do **not** commit it to `main` and do **not** discard it. Uncommitted edits follow you across `git switch`, so create the branch first and the changes land there. If the dirty files are unrelated to the change being started, say so and ask which belong on this branch.

### One branch, one change

A branch should be one thing a reviewer can approve or reject as a unit. When the user starts describing something unrelated mid-session, that is a new branch — say so rather than piling it on.

**Reuse the current branch** when its PR is still open *and* the new edit is part of the same change or responds to review feedback. Push to it; the open PR updates itself.

**Start a new branch** when the current branch's PR is merged or closed, or when the edit is unrelated to what that PR claims to do. Never start a new branch while unpushed commits sit on the current one — push and open the PR first, or the work is orphaned where the user can't find it.

### After a merge

`git switch main && git pull && git branch -d <branch>`. Do this before starting the next change, not later. A local `main` that lags the remote is the single most common cause of confusing conflicts here.

### Commit identity

The ruleset requires an extra approval for commits it considers unattributed. Every maintainer's `git config user.email` must be an email verified on their GitHub account. If a push produces a PR flagged for unattributed changes, check the commit email against the account before doing anything else.

### Before every push

Run `claude plugin validate .` and confirm the plugin `version` in `plugin.json` was bumped if anything under `plugins/` changed. A version that didn't move means installed users never receive the change — the change silently does nothing. Do not open a PR without checking both.

### Never

- Rewrite history on a pushed branch (`rebase`, `commit --amend`, `push --force`) once anyone has reviewed it — it dismisses reviews and loses comment context.
- Merge a PR you authored. If the user asks you to and they hold admin bypass, tell them it bypasses the review requirement and confirm before proceeding.

## Content conventions for the `math` plugin

- Pedagogical claims in a skill should be actionable instructions, not cited research summaries. Say what to do and why in a clause, not in a paragraph.
- Prefer "check X" and "cut Y" over "consider whether X might be relevant" — these files are read by a model that will follow whatever hedge you write.
- Never let a skill assert a mathematical result it has not been told to verify; skills that produce math must instruct Claude to solve items independently.
- Keep a SKILL.md under roughly 500 words. Longer supporting material goes in a sibling file in the skill's directory (e.g. `references/misconceptions.md`) and is referenced from `SKILL.md` so it loads only when needed.

## Style

- Two-space JSON indentation, trailing newline on every file.
- Directory, plugin, and skill names: lowercase kebab-case.
- Markdown: sentence-case headings, no trailing whitespace.
