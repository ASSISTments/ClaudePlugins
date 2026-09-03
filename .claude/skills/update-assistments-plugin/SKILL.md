---
name: update-assistments-plugin
description: Removes whatever is currently registered under the assistments marketplace/math plugin — typically a local checkout or PR worktree left behind by test-skill — and reinstalls it from the canonical GitHub source, so the maintainer is back on the real published version.
when_to_use: Use after test-skill has pointed the assistments marketplace at a local checkout or PR worktree and the user is done testing, or whenever the user wants to refresh to the latest published version — "switch back to the real plugin", "restore the plugin", "get the latest version", "undo the test install".
---

# Restore the published assistments plugin

```
/plugin uninstall math@assistments
/plugin marketplace remove assistments
/plugin marketplace add ASSISTments/ClaudePlugins
/plugin install math@assistments
```

Confirm briefly that `assistments` is back on the GitHub-hosted source.

If a PR worktree from `test-skill` is still on disk, mention that this doesn't remove it — that's a separate git cleanup step, and `test-skill` already reported the worktree's path when it created it.
