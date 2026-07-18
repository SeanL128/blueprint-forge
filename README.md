<div align="center">

<img src="docs/banner.svg" alt="Blueprint Forge" width="440" />

**A Claude Code plugin that has your most powerful model interview you and write reviewable blueprints while you still have it, so that the cheaper models you'll still have later can build them cold.**

![version](https://img.shields.io/badge/version-1.3.0-blue?style=flat-square) ![Claude Code plugin](https://img.shields.io/badge/Claude_Code-plugin-d97757?style=flat-square) ![license](https://img.shields.io/badge/license-MIT-blue?style=flat-square)

[Learn more →](https://seanlindsay.xyz/blueprint-forge) · [Install](#quick-start) · [Roadmap](#roadmap)

</div>

## Why

I built this after the US Government blocked users from using Fable 5 for almost three weeks, which showed that you can't assume the high-tier model you have today will still be there a week from now when you want to actually build the thing you have planned. Blueprint Forge lets you batch-create blueprints once that are descriptive enough for cheaper models to follow months from now to create a result that is just about as good as if the high-tier model wrote it, and the blueprinting process accounts for the build order so that every blueprint is building off of what will exist when it is reached rather than simply what exists when it is created. One caveat is important to understand before using this: a pivot in the project's direction will likely invalidate any unused blueprints, so only blueprint up to the point that you are 100% certain of, and use `blueprint-oneshot` for the one task in front of you until the direction settles.

## Features

- **A hard interview gate** — every run opens with `QUESTIONS FIRST`, asks only the questions whose answers change what a builder produces, then stops and waits, because a wrong guess wastes a whole build.
- **Batch blueprinting with per-item approval** — `blueprint-backlog` turns a backlog or TODO into one blueprint per item, each of which you approve, revise, or reject in a single batch gate.
- **Run-order-aware blueprints** — a `RUN-ORDER.md` ledger orders the work so each blueprint assumes the state the previous ones leave behind, and its closed status vocabulary makes the plan resumable across sessions.
- **Role-based model routing** — blueprints name roles (blueprinter, builder-light through builder-heavy, reviewer), never model IDs, and `blueprints/MODELS.md` maps each role to a model, including per-role dispatch commands that can send builds to another harness entirely.
- **Review against the blueprint** — `blueprint-implement` re-runs every definition-of-done check itself after the builder reports, escalating to a heavier reviewer only when a risk trigger fires.
- **A oneshot path** — `blueprint-oneshot` takes a single task end to end with two stops (interview and approval), then builds and reviews autonomously.

## Quick start

```sh
/plugin marketplace add SeanL128/blueprint-forge
/plugin install blueprint-forge@blueprint-forge
```

Runs inside Claude Code; all three skills are user-invoked only, because they deliberately spend premium planning-model tokens.

## Configuration

| Option | What it does | Default |
|--------|--------------|---------|
| `blueprinter` | grounds, interviews, writes and approves blueprints; the judgment layer | `Claude Fable` |
| `implementer` | runs the implement session: dispatches builders, runs verify commands, keeps the ledger | `Claude Sonnet` |
| `builder-light` | purely mechanical items: new standalone files, config from exact given values | `Claude Haiku` |
| `builder-standard` | the default: touches existing code paths, has edge cases, reads before editing | `Claude Sonnet` |
| `builder-heavy` | genuinely hard or high-risk work: cross-module changes, auth, migrations, shared state | `Claude Opus` |
| `reviewer` | default review of built work against its blueprint | `Claude Sonnet` |
| `reviewer-heavy` | escalated review: high-risk items, builder-flagged uncertainty, verify failures | `Claude Opus` |

More in [docs/USAGE.md](docs/USAGE.md).

## Roadmap

- [x] Oneshot and split paths, benchmarked blind before release
- [x] Worked Claude + GPT split via per-role Codex dispatch commands
- [x] Plugin packaging, with the repo as its own marketplace

## License

[MIT](LICENSE)

---

<div align="center">

Built by Sean Lindsay · [seanlindsay.xyz](https://seanlindsay.xyz)

</div>
