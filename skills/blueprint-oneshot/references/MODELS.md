# MODELS.md — role → model map

This file resolves the role names used by the suite to concrete models for
the harness in play. Edit it to declare your own split (GPT/Codex, Ollama,
OpenRouter, …); blueprints and skills never name model IDs.

| Role | Model | Dispatch | Fits |
|------|-------|----------|------|
| blueprinter | Claude Fable (or your strongest planning model) | | grounds, interviews, writes and approves blueprints — the judgment layer |
| implementer | Claude Sonnet | | runs the implement session: dispatches builders, runs verify commands, keeps the ledger |
| reviewer | Claude Sonnet | | default review of built work against its blueprint |
| reviewer-heavy | Claude Opus | | escalated review — high-risk items, builder-flagged uncertainty, verify failures, second-attempt builds |
| builder-light | Claude Haiku | | purely mechanical items: new standalone files, copy drops, config from exact given values, no edits to existing logic |
| builder-standard | Claude Sonnet | | default: touches existing code paths, has edge cases, needs to read before editing |
| builder-heavy | Claude Opus | | genuinely hard or high-risk: complex/cross-module work, deep debugging, auth, billing, permissions, security, migrations, data loss, shared state, caching, concurrency, public APIs, user-visible workflows |

**Dispatch column (optional):** empty = the orchestrator spawns a native
subagent in its own harness (the default). To route a role to another
harness, put a shell command template there; the orchestrator runs that
command instead of spawning a subagent. Placeholders: `{model}` (the Model
column) and `{prompt}` (the kickoff prompt + blueprint contents,
shell-quoted). Example shape: `some-cli exec -m {model} "{prompt}"`. The
command must run non-interactively and do its work in the current repo. A
worked GPT/Codex map lives in the Blueprint Forge README.
