# Blueprint Forge

A Claude Code plugin to plan work as reviewable *blueprints* with your best model, then hand the build to cheaper ones.

![license](https://img.shields.io/badge/license-MIT-blue?style=flat-square)
![plugin](https://img.shields.io/badge/claude_code-plugin-orange?style=flat-square)

## Why

You can't trust that the powerful model you have today will still be there next week when you actually want to build; watching Claude Fable get pulled from the market made that concrete. Blueprint Forge solves it by spending the big model *while you still have it* on the part only it can do well: interviewing you about every ambiguity, then writing self-contained blueprints. The blueprints and their run-order ledger sit in your repo, so any cheaper model you still have access to can execute them cold - next week, next month, whenever - and the result is basically the same quality, because every decision was already made and written down.

Two more things it buys:

- **An interview gate that actually fires.** Every autonomous run eventually hits an ambiguous spec and resolves it with a coin flip. In my blind benchmarks (20+ graded runs), the hard interview stop caught every planted ambiguity in 8/8 runs, while plain sessions asked zero questions and advisory gates self-waived every time they ran headless.
- **Usage-limit arbitrage.** Plan with your strongest model, build on whatever's cheap — including a different vendor's subscription entirely (see the Claude + GPT split below).

**The honest caveat:** blueprints are easily invalidated by a pivot. Use the split path only on a project where the direction is certain, the decisions are made, and all that's left is the building. If that's not true yet, use `blueprint-oneshot` for the one task in front of you, or run a separate session to settle the direction first and blueprint after.

**Learn more →** [seanlindsay.xyz/blueprint-forge](https://seanlindsay.xyz/blueprint-forge)

## Quick start

In Claude Code:

```
/plugin marketplace add SeanL128/blueprint-forge
/plugin install blueprint-forge@blueprint-forge
```

Or send Claude this repo and have it install the skills.

## The skills

Two ways in. All are user-invoked only — Claude never triggers them on its own, because they deliberately spend premium planning-model tokens.

| Command | Path | What it does |
|---|---|---|
| `/blueprint-forge:blueprint-oneshot <task>` | Oneshot | One task end to end: interview → blueprint → your approval → autonomous build + review. Lowest-friction entry point. |
| `/blueprint-forge:blueprint-backlog [path]` | Split, step 1 | Turn a backlog/TODO into approved blueprints (approve / revise / reject each) + a resumable run-order ledger. |
| `/blueprint-forge:blueprint-implement [path]` | Split, step 2 | Build one approved blueprint: dispatch its builder subagents, review against it, route ship / fix / re-dispatch / re-blueprint. |

Model-agnostic by design: roles (blueprinter / builder-light / builder-standard / builder-heavy / reviewer / reviewer-heavy), not model IDs. Routing lives in your repo's `blueprints/MODELS.md`, created on first run with a default all-Claude split. To send builds elsewhere, edit the map.

## The split I actually run: Claude blueprints, GPT builds

The default map keeps everything on Claude, but the setup that's been best in my experience routes builder roles to GPT-5.6 family through the Codex CLI, keeping the judgment roles (blueprinter, reviewer) on Claude:

- Building burns the large majority of tokens. With builds on a separate $20 ChatGPT subscription, my Claude 5-hour and weekly limits are barely dented, while Claude still does everything judgment-shaped.
- Cross-vendor review is a free win: a Claude reviewer doesn't share a GPT builder's systematic failure modes or biases.

The worked map — copy the builder rows over your `blueprints/MODELS.md` and adjust to taste (the skills themselves need no changes):

| Role | Model | Dispatch | Fits |
|------|-------|----------|------|
| blueprinter | Claude Fable | | writes the blueprints (stays in the native harness) — same as the suite's default map |
| builder-light | gpt-5.6-luna | `codex exec --skip-git-repo-check --sandbox workspace-write -m {model} -c model_reasoning_effort=high "{prompt}"` | purely mechanical items |
| builder-standard | gpt-5.6-terra | `codex exec --skip-git-repo-check --sandbox workspace-write -m {model} -c model_reasoning_effort=high "{prompt}"` | default: touches existing code, edge cases |
| builder-heavy | gpt-5.6-sol | `codex exec --skip-git-repo-check --sandbox workspace-write -m {model} -c model_reasoning_effort=medium "{prompt}"` | genuinely hard or high-risk work (escalate `model_reasoning_effort` to `high` after a failed re-dispatch) |
| reviewer | Claude Sonnet | | default review of built work against its blueprint (native harness) |
| reviewer-heavy | Claude Opus | | escalated review — high-risk items, builder-flagged uncertainty, verify failures, second-attempt builds (native harness) |

Notes:

- Prereqs: Codex CLI installed and authenticated (`npm install -g @openai/codex`, then `codex login`). The dispatch commands run Codex non-interactively in the current repo.
- Reasoning effort rides in the dispatch command (`-c model_reasoning_effort=…`) — the Dispatch column is a full command template, so any per-role knob lives there.
- Drop `--skip-git-repo-check` if you always run inside a git repo; keep it if builders may run before the first commit. `--sandbox workspace-write` is required so builders can edit the repo.
- **Caveat:** this routes only *builder* roles to GPT — validated blind (Terra/Sol builders shipped 4/4 blueprints clean in my test runs). Don't generalize it to running the *orchestrating* skills on a GPT harness: in my benchmarks, GPT orchestrators asked zero interview questions in 3/3 runs where Claude orchestrators held the stop 6/6. Keep blueprinter/reviewer on Claude.

---
Built by Sean Lindsay · [seanlindsay.xyz](https://seanlindsay.xyz)
