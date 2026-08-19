# ASSISTments Claude Plugins

A [Claude Code plugin marketplace](https://code.claude.com/docs/en/plugin-marketplaces) published by ASSISTments.

Marketplace name: **`assistments`**

## Install

```
/plugin marketplace add assistments-org/ClaudePlugins
/plugin install math@assistments
```

Replace `assistments-org/ClaudePlugins` with this repository's actual `owner/repo` once it is pushed. To work against a local checkout instead:

```
/plugin marketplace add /path/to/ClaudePlugins
/plugin install math@assistments
```

If the install summary says `Run /reload-plugins to activate.`, run that command.

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

Problems are drafted for your review and only written to ASSISTments after you approve them, via the `ASSISTments Auth` MCP tools. Without that MCP server connected, the skill still produces the breakdown and the problem drafts — it just cannot save them.

The skill also intercepts requests that sound like plain problem generation — "give me a quiz on 7.RP.A.2", "mastery check for 6.NS.B.3", "quick check on this standard" — and runs the breakdown first so the problems trace back to specific sub-skills.

Example prompts:

- "Unpack 8.EE.C.7b."
- "Break down this standard: *Fluently add, subtract, multiply, and divide multi-digit decimals using the standard algorithm for each operation.*"
- "I need a mastery quiz for 7.RP.A.2."
- "Build a skill builder for the standard I just pasted."

## Repository layout

```
.claude-plugin/marketplace.json     # marketplace catalog
plugins/
└── math/
    ├── .claude-plugin/plugin.json  # plugin manifest
    └── skills/
        └── <skill-name>/SKILL.md
```

## Contributing

See [CLAUDE.md](CLAUDE.md) for conventions, validation, and how to add a skill or a plugin.
