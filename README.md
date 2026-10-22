# ASSISTments Claude Plugins

A [Claude Code plugin marketplace](https://code.claude.com/docs/en/plugin-marketplaces) published by ASSISTments.

Marketplace name: **`assistments`**

## Install

Repository: <https://github.com/ASSISTments/ClaudePlugins>

In any Claude Code session:

```
/plugin marketplace add ASSISTments/ClaudePlugins
/plugin install math@assistments
```

`/plugin install` opens a details view where you pick an installation scope to confirm. If the install summary says `Run /reload-plugins to activate.`, run that command.

To pull down later changes:

```
/plugin marketplace update assistments
```

Updates reach you when the plugin's `version` in its manifest changes, so a marketplace update after a version bump is what refreshes the skill.

## Setup

The `math` plugin's skills work out of the box for analysis and drafting. Saving problems into ASSISTments additionally requires the ASSISTments connector:

- The skills call the ASSISTments connector's tools (for example `assistments_save_problem`) to write problems and return preview links. The connector is **not** bundled with this plugin — add **ASSISTments** from the Connectors Directory (Customize > Connectors in Claude), and run `/mcp` to confirm it shows as connected and authenticated. See [Connecting ASSISTments with Claude](https://www.assistments.org/user-resources/connecting-assistments-with-claude).
- Without it, `math-standard-unpacker` still unpacks standards and drafts problems for review; it just cannot save them.

## Data and privacy

This plugin is markdown skills and a manifest only. It runs no code, makes no network calls, and collects nothing.

- **What leaves your machine:** only what you paste into the conversation, which Claude processes under your own Claude account terms, and — if you connect the ASSISTments connector and approve a problem — that problem's content, which is saved to your ASSISTments account.
- **Nothing is saved without approval.** Problems are drafted for your review; the skill saves only the ones you approve.
- **Student data:** the skills never ask for student names or records. ASSISTments states that student personally identifiable information is never transmitted through the connector.
- Saved content is covered by the [ASSISTments privacy policy](https://www.assistments.org/privacy-policy-assistments).

Verify the install by invoking a skill directly:

```
/math:math-standard-unpacker
```

### Work from a local checkout

To test changes before pushing, point the marketplace at your clone instead of GitHub:

```
git clone https://github.com/ASSISTments/ClaudePlugins.git
```

```
/plugin marketplace add /absolute/path/to/ClaudePlugins
/plugin install math@assistments
/plugin marketplace update assistments    # after each edit
```

Skills are read at load time, so refresh the marketplace (or run `/reload-plugins`) after editing a `SKILL.md`. A given marketplace name can only be registered once per user — adding the local checkout replaces the GitHub-hosted `assistments` marketplace until you re-add it.

## Plugins

### `math` — Math Assessment Authoring

Skills for turning math standards into teachable, assessable content. Invoke a skill directly as `/math:<skill>`, or just describe what you need and Claude will pick the relevant one.

| Skill | Use it to |
| :-- | :-- |
| `math-standard-unpacker` | Break a math standard into teachable, assessable sub-skills with vertical alignment and an item map, then build ASSISTments problems from it |

#### `math-standard-unpacker`

Paste a standard (CCSS, state, or district — text or a code) and the skill produces:

1. **Standard Unpacked** — the cognitive-demand verb and the content it applies to, which sets the rigor floor.
2. **Skills** — the 3–6 independently teachable sub-skills the standard bundles, each as an "I can" statement, with every skill justified against specific words in the standard's text.
3. **Vertical alignment** — prior knowledge assumed, and what the standard builds toward.
4. **Item map** — per skill: target item count, the sub-cases problems should sample, and the suggested ASSISTments problem type (`CHOOSE_ONE`, `CHOOSE_N`, `FILL_IN`, `DRAG_DROP`, `SORT`, `OPEN_RESPONSE`).

It then offers two paths, and waits for you to choose:

- **Skill Practice** — 4–6 problems per skill you select, spread across that skill's sub-cases. For assigning fluency practice.
- **Standard Mastery Quiz** — exactly one item per skill, each at the skill's *most demanding* case, so results show which sub-skill a student is stuck on.

Problems are drafted for your review and only written to ASSISTments after you approve them, via the ASSISTments connector. Without it connected, the skill still produces the breakdown and the problem drafts — it just cannot save them.

The skill also intercepts requests that sound like plain problem generation — "give me a quiz on 7.RP.A.2", "mastery check for 6.NS.B.3", "quick check on this standard" — and runs the breakdown first so the problems trace back to specific sub-skills.

Example prompts:

- "Unpack 8.EE.C.7b."
- "Break down this standard: *Fluently add, subtract, multiply, and divide multi-digit decimals using the standard algorithm for each operation.*"
- "I need a mastery quiz for 7.RP.A.2."
- "Build a skill builder for the standard I just pasted."

## Repository layout

```
.claude-plugin/marketplace.json     # marketplace catalog
LICENSE                             # MIT
plugins/
└── math/
    ├── .claude-plugin/plugin.json  # plugin manifest
    ├── assistments_logo.png        # directory icon
    ├── LICENSE
    └── skills/
        └── <skill-name>/SKILL.md
```

## Contributing

See [CLAUDE.md](CLAUDE.md) for conventions, validation, and how to add a skill or a plugin.

## License

[MIT](LICENSE) © 2026 The ASSISTments Foundation
