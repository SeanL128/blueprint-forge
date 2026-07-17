# Blueprint Forge

A Claude Code plugin that has your most powerful model interview you and write reviewable blueprints while you still have it, so that the cheaper models you'll still have later can build them cold.

![version](https://img.shields.io/badge/version-1.3.0-blue?style=flat-square)
![Claude Code plugin](https://img.shields.io/badge/Claude_Code-plugin-d97757?style=flat-square)
![license](https://img.shields.io/badge/license-MIT-blue?style=flat-square)

## Why

After the US Government blocked users from using Fable 5 for almost three weeks, it became clear that you can't assume the high-tier model you have today will still be there a week from now when you want to actually build the thing you have planned. Blueprint Forge helps alleviate that problem by allowing you to batch-create blueprints once that are descriptive enough for cheaper models to follow months from now to create a result that is just about as good as if the high-tier model wrote it. The blueprinting process even accounts for the build order so that every blueprint is building off of what will exist when it is reached rather than simply what exists when it is created.

There is, however, a caveat that is important to understand before using this: making any pivot in the project and its end state will likely invalidate any unused blueprints, so only blueprint a direction that you are 100% certain of, or blueprint up to the point that you are 100% certain of, else you risk wasting premium tokens. If that isn't true yet, use `blueprint-oneshot` for the one task in front of you and blueprint the rest after the direction settles.

**Learn more →** [seanlindsay.xyz/blueprint-forge](https://seanlindsay.xyz/blueprint-forge)

## Quick start

In Claude Code:

```
/plugin marketplace add SeanL128/blueprint-forge
/plugin install blueprint-forge@blueprint-forge
```

Or send Claude this repo and have it install the skills.

## The skills

Two ways in. All are user-invoked only, because they deliberately spend premium planning-model tokens, so Claude never triggers them on its own.

| Command | Path | What it does |
|---|---|---|
| `/blueprint-forge:blueprint-oneshot <task>` | Oneshot | One task end to end: interview, blueprint, your approval, then an autonomous build and review. The lowest-friction entry point. |
| `/blueprint-forge:blueprint-backlog [path]` | Split, step 1 | Turns a backlog or TODO into approved blueprints (approve, revise, or reject each) plus a resumable run-order ledger. |
| `/blueprint-forge:blueprint-implement [path]` | Split, step 2 | Builds one approved blueprint: dispatches its builder subagents, reviews the result against it, then routes ship / fix / re-dispatch / re-blueprint. |

Model-agnostic by design: everything is routed by role (blueprinter / implementer / builder-light / builder-standard / builder-heavy / reviewer / reviewer-heavy), never by model ID. The routing lives in your repo's `blueprints/MODELS.md`, created on first run with a default all-Claude split, and sending builds elsewhere is just a matter of editing that map.

## The split I actually run: Claude blueprints, GPT builds

The default map keeps everything on Claude, but the setup that has been best in my experience routes the builder roles to the GPT-5.6 family through the Codex CLI while keeping the judgment roles (blueprinter, reviewer) on Claude:

- Building burns the large majority of tokens, and with builds on a separate $20 ChatGPT subscription, my Claude 5-hour and weekly limits are barely dented while Claude still does everything judgment-shaped.
- Cross-vendor review is a free win, since a Claude reviewer doesn't share a GPT builder's systematic failure modes or biases.

The worked map: copy the builder rows over your `blueprints/MODELS.md` and adjust to taste (the skills themselves need no changes).

| Role | Model | Dispatch | Fits |
|------|-------|----------|------|
| blueprinter | Claude Fable | | writes the blueprints in the native harness, same as the suite's default map |
| builder-light | gpt-5.6-luna | `codex exec --skip-git-repo-check --sandbox workspace-write -m {model} -c model_reasoning_effort=high "{prompt}"` | purely mechanical items |
| builder-standard | gpt-5.6-terra | `codex exec --skip-git-repo-check --sandbox workspace-write -m {model} -c model_reasoning_effort=high "{prompt}"` | default: touches existing code, edge cases |
| builder-heavy | gpt-5.6-sol | `codex exec --skip-git-repo-check --sandbox workspace-write -m {model} -c model_reasoning_effort=medium "{prompt}"` | genuinely hard or high-risk work (escalate `model_reasoning_effort` to `high` after a failed re-dispatch) |
| reviewer | Claude Sonnet | | default review of built work against its blueprint (native harness) |
| reviewer-heavy | Claude Opus | | escalated review: high-risk items, builder-flagged uncertainty, verify failures, second-attempt builds (native harness) |

Notes:

- Prereqs: Codex CLI installed and authenticated (`npm install -g @openai/codex`, then `codex login`). The dispatch commands run Codex non-interactively in the current repo.
- Reasoning effort rides in the dispatch command (`-c model_reasoning_effort=…`), since the Dispatch column is a full command template, so any per-role knob lives there.
- Drop `--skip-git-repo-check` if you always run inside a git repo; keep it if builders may run before the first commit. `--sandbox workspace-write` is required so builders can edit the repo.
- **Caveat:** this routes only *builder* roles to GPT, and it was validated blind (Terra/Sol builders shipped 4/4 blueprints clean in my test runs). Don't generalize it to running the *orchestrating* skills on a GPT harness: in my benchmarks, GPT orchestrators asked zero interview questions in 3/3 runs where Claude orchestrators held the stop 6/6. Keep blueprinter/reviewer on Claude.

## What I learned / what broke

- Advisory process gates do not survive autonomy. Every suite I tested that merely recommends asking questions waived its own gate the moment it ran headless, five out of five, while the hard stop-and-wait caught every planted ambiguity in 8/8 blind runs.
- Blind graders penalize negotiated answers, so the resolution you actually chose in an interview can grade worse than the guess a judge would have made. The value shows up in variance rather than means, which changed how I position the suite: insurance, not a quality upgrade.
- The economics only work with routing. Pinned to one premium model, the pipeline costs 3 to 4 times a plain session, and arbitraging the build tokens to cheaper builders cut that premium to about 1.5 times.

---
Built by Sean Lindsay · [seanlindsay.xyz](https://seanlindsay.xyz)
