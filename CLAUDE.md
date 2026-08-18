# CLAUDE.md

Guidance for working in this repository.

## What this repo is

A Claude Code **plugin marketplace** named `assistments`. It contains the catalog (`.claude-plugin/marketplace.json`) and the plugins it distributes (`plugins/*`). There is no build step, no test suite, and no runtime code — the deliverables are JSON manifests and markdown skills.

Current plugins: `math` (math assessment authoring skills).

## Layout rules

```
.claude-plugin/marketplace.json          # catalog — lists every plugin and its source
plugins/<plugin>/.claude-plugin/plugin.json
plugins/<plugin>/skills/<skill>/SKILL.md
```

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

## Content conventions for the `math` plugin

- Pedagogical claims in a skill should be actionable instructions, not cited research summaries. Say what to do and why in a clause, not in a paragraph.
- Prefer "check X" and "cut Y" over "consider whether X might be relevant" — these files are read by a model that will follow whatever hedge you write.
- Never let a skill assert a mathematical result it has not been told to verify; skills that produce math must instruct Claude to solve items independently.
- Keep a SKILL.md under roughly 500 words. Longer supporting material goes in a sibling file in the skill's directory (e.g. `references/misconceptions.md`) and is referenced from `SKILL.md` so it loads only when needed.

## Style

- Two-space JSON indentation, trailing newline on every file.
- Directory, plugin, and skill names: lowercase kebab-case.
- Markdown: sentence-case headings, no trailing whitespace.
