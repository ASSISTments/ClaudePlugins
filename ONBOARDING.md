# Onboarding: maintaining the ASSISTments Claude plugins

You do not need to know how to code to maintain this repo, and you will not need to learn git. You need a working setup (one afternoon, once) and four commands.

Everything in here is written for the person doing the content work. If something in it doesn't match what you see on screen, that's a bug in this document — say so, and it gets fixed.

## What you are actually maintaining

This repo holds **skills**: plain-English instruction files that change how Claude behaves. The skills in `plugins/math/skills/` e.g. `plugins/math/skills/math-standard-unpacker/SKILL.md` are the whole product — no code, no build. When a teacher says "unpack 7.RP.A.2," Claude reads that file and follows it.

Two consequences worth internalizing:

- **Your edits are the product.** Rewording a sentence in a SKILL.md changes what Claude does for every teacher using it. There is no code layer between your prose and the behavior.
- **The description field is not documentation.** The `description:` at the top of a SKILL.md is the only thing Claude sees when deciding whether to load the skill at all. Write it to name the phrases a teacher would actually type. A beautifully written skill with a vague description never fires.

**This repo is public.** Anything you commit is visible on the internet. No student names, no district names, no unreleased plans.

## One-time setup

### Before you start — ask Ryan for two things

1. **Write access** to `ASSISTments/ClaudePlugins`. Without it you can look but not submit. Most people in the org default to read-only.
2. Confirmation that **the email on your GitHub account is verified**. If your commits come from an unverified email, the repo flags your pull requests as "unattributed" and demands an extra approval. Annoying, and the cause is invisible.

### Step 1 — Install the Claude desktop app

Download it from [claude.ai/download](https://claude.ai/download) and sign in with your ASSISTments account. This is where all your work happens.

### Step 2 — Open Terminal once

This is the only time you need it. On a Mac, press `⌘ Space`, type "Terminal", press Return. Paste each block below and press Return.

Install the developer tools (git). If it says it's already installed, that's fine:

```bash
xcode-select --install
```

Install the GitHub command-line tool. Download the `.pkg` from [github.com/cli/cli/releases](https://github.com/cli/cli/releases/latest) — take the file ending in `macOS_arm64.pkg` (or `amd64.pkg` on an Intel Mac) and double-click it. No Terminal needed for this part.

Tell git who you are. **Use the exact email verified on your GitHub account** — this is the "unattributed" thing from above:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@assistments.org"
```

Sign in to GitHub, and choose "Login with a web browser" when it asks:

```bash
gh auth login
```

Download the repo to your computer:

```bash
cd ~/Documents && git clone https://github.com/ASSISTments/ClaudePlugins.git
```

You now have a folder at `~/Documents/ClaudePlugins`. You are done with Terminal.

### Step 3 — Open the repo in Claude

In the Claude desktop app, start a session and point it at `~/Documents/ClaudePlugins`. Type:

```
/repo-status
```

If it tells you the state of the repo, you're set up.

> **On Windows?** The steps are the same but the installers differ. Ask Claude: "I'm on Windows, walk me through installing git and the GitHub CLI." It'll adapt.

## The four commands

These are the whole workflow. You can also just describe what you want in plain English — Claude will reach for the right one on its own. Typing the command is the reliable way to be sure.

| Command | When | What it does for you |
| :-- | :-- | :-- |
| `/repo-status` | Any time you're unsure | Tells you what you're working on, what isn't saved yet, what's waiting on a reviewer, and the one thing to do next |
| `/start-change` | Before editing anything | Puts your work in its own separate space, so a half-finished edit can never reach real users |
| `/test-skill` | After editing | Catches the mistakes that make a skill silently fail to load, and gives you prompts to try it for real |
| `/submit-for-review` | When you're done | Sends the work to GitHub and opens a pull request for your teammate to approve |

A normal session looks like:

```
/start-change reword the quiz triggers so "quick check" fires reliably
   …you and Claude edit the skill together…
/test-skill
/submit-for-review
```

## The branch thing, explained once

This is the only concept worth understanding, because it's where confusion happens.

**`main` is what real users get.** Every install of the `math` plugin pulls from `main`. It must never contain a half-finished thought.

**A branch is a private copy of the repo where you can work.** You make one, you edit freely, and nobody sees it until you submit. `/start-change` creates one for you.

So the rule: **one branch per change, always started with `/start-change`.**

When you're not sure whether to keep working where you are or start fresh, ask Claude — that decision is built into `/start-change`, which will tell you to stay put if that's the right answer. If you'd rather decide yourself:

- **Keep working on the current branch** if your pull request is still open and this edit is part of that same change, or is you responding to a review comment.
- **Start a new branch** if the last pull request was merged, or if you're now doing something unrelated. "I'll just add this one other fix here" is the most common way a clean change becomes an unreviewable one.

### Two things that look broken and aren't

**"It says I have unsaved changes on main."** You started editing before making a branch. Nothing is lost, and you don't need to redo anything — the edits follow you onto a new branch. Run `/start-change` and it handles it.

**"My pull request just sits there."** It needs one approval from *someone else* — you can't approve your own, by design. Ping your teammate. That's the whole blocker.

## Reviewing each other's work

You two are each other's reviewers, so a real review is the only quality gate this repo has. When you get a request:

1. Open the pull request link. Read the change as prose. Does it say what it means?
2. Check the `description` line. Would it fire on the phrasings a teacher actually types?
3. Check the `version` in `plugin.json` went up. **If it didn't, the change reaches nobody** — installed users only get updates when that number changes. Ask for a bump.
4. Actually try it: install the branch and give the skill a real prompt. Ask Claude in your own session — "test the skill on branch `docs/whatever`" — and it'll set that up.
5. Approve, or leave a comment. Every comment thread has to be resolved before it can merge, so say clearly whether a comment is blocking or just a thought.

Approving a change you didn't understand is worse than asking. Ask.

## When something goes wrong

Say what happened in plain language and let Claude look: *"I was editing the unpacker and now it says something about a conflict"* is a perfectly good bug report. Claude can read the actual state and tell you where you are.

Two rules that will save you:

- **Never accept an offer to discard, reset, or throw away your changes** unless you understand exactly what's being thrown away. Ask "what would I lose?" first. Everything else in git is recoverable; this isn't.
- **Nothing you do locally can break what real users have.** Users get `main`, and `main` only changes when a pull request is approved and merged. Before that point, you can experiment freely.

Ask Ryan when: you need access, a pull request is blocked in a way `/repo-status` can't explain, or you're about to rename or delete a skill (that breaks existing installs and needs coordination).

## Conventions worth knowing

Claude enforces these, but they explain why it pushes back:

- Skills stay **single-purpose**. If a description needs "and" to cover unrelated triggers, it's two skills.
- Keep a SKILL.md **under about 500 words**. Long reference material goes in a separate file next to it, so it only loads when needed.
- Write **instructions, not research summaries**: "check X," "cut Y" — not "consider whether X might be relevant." A model follows whatever hedge you write.
- **Never let a skill assert math it hasn't verified.** Skills that produce problems must tell Claude to solve them independently first.

The full set lives in [CLAUDE.md](CLAUDE.md). You don't need to read it — Claude reads it every session — but it's where the reasoning is written down.
