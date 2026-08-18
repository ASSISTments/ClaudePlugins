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

Skills for writing math assessment content that holds up pedagogically. Invoke a skill directly as `/math:<skill>`, or just describe what you need and Claude will pick the relevant one.

| Skill | Use it to |
| :-- | :-- |
| `write-assessment-item` | Draft a single item for one learning objective — stem, answer type, accepted answers |
| `design-distractors` | Build multiple-choice options where each wrong answer diagnoses a specific misconception |
| `align-to-standards` | Align items to standards and a rigor level (DOK/Bloom's), and check blueprint coverage |
| `build-problem-set` | Sequence items into a set — difficulty ramp, interleaving, spaced review, time budget |
| `write-tutoring-support` | Write hint ladders, scaffolding sub-questions, worked examples, and answer-specific feedback |
| `review-assessment-quality` | Pre-publication review: math accuracy, clarity, bias and accessibility, alignment |

The skills compose. A typical flow is `write-assessment-item` → `design-distractors` → `write-tutoring-support` → `build-problem-set` → `review-assessment-quality`.

Example prompts:

- "Write three 7th-grade items on proportional relationships at DOK 2."
- "Give me distractors for this item and tell me what each one catches."
- "Turn these eight items into a homework set with 25% spiral review."
- "Review this quiz before I publish it."

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
