# Usage

## The skills

Two ways in. All are user-invoked only, because they deliberately spend premium planning-model tokens, so Claude never triggers them on its own.

| Command | Path | What it does |
|---|---|---|
| `/blueprint-forge:blueprint-oneshot <task>` | Oneshot | One task end to end: interview, blueprint, your approval, then an autonomous build and review. The lowest-friction entry point. |
| `/blueprint-forge:blueprint-backlog [path]` | Split, step 1 | Turns a backlog or TODO into approved blueprints (approve, revise, or reject each) plus a resumable run-order ledger. |
| `/blueprint-forge:blueprint-implement [path]` | Split, step 2 | Builds one approved blueprint: dispatches its builder subagents, reviews the result against it, then routes ship / fix / re-dispatch / re-blueprint. |

The first run bootstraps a `blueprints/` folder in your repo with the `MODELS.md` routing map, the `RUN-ORDER.md` ledger, the blueprint template, and an economics reference. Everything is routed by role, never by model ID, and sending builds elsewhere is just a matter of editing `blueprints/MODELS.md`.

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
