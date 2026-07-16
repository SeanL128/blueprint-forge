# BLUEPRINT [NN]: [short name]

- **BUILDER ROLE:** [builder-light | builder-standard | builder-heavy] — [one line on why this tier fits; heavy only for genuinely hard or high-risk work]
- **GROUNDED AGAINST:** [short commit SHA + date, or "no commits — repo state described in STARTING STATE"] <!-- repo state this plan assumes; if moved past it, re-ground or flag before building -->
- **DEPENDS ON:** [blueprint IDs that must be built AND reviewed first, or "none"] <!-- needs their output or edits files they also edit -->

## GOAL

[One or two plain sentences: what exists and works when this is done — a finished
result, not a task list.]

## STARTING STATE

[What the repo looks like when this blueprint begins: what DEPENDS-ON left
behind, what already exists that this plan builds on. Builder verifies before
step 1; a mismatch is a flag, not a guess.]

## CONTEXT THE BUILDER NEEDS

<!-- No memory of any planning conversation. -->
- **Files to read first:** [real paths to open before editing]
- **Real inputs, in full:** [actual copy, colors, schema, examples — pasted verbatim]
- **Data shapes / examples:** [sample input → expected output, real values]
- **Gotchas:** [naming quirks, shared utils, env vars, look-alikes that are not reusable]
- **Kickoff prompt:** "[paste-ready instruction that starts the build from this blueprint]"
- **Verbatim inputs:** [files to open/create by name; copy/values/snippets the builder would otherwise invent]

## CONSTRAINTS

- **Must stay inside:** [exact files/folders the builder may touch]
- **Must not change:** [files, public APIs, schemas, copy, behavior to leave alone]
- **Stack / tools:** [languages, frameworks, libs already in use; anything it must or must not use]
- **Non-negotiables:** [perf, style, naming, security, length, tone, product rules]
- **Simplicity:** the simplest implementation that passes the DoD — no unrequested abstractions, files, dependencies, or scope.

## PLAN

<!-- Every step one concrete action, no judgment call left in it. Numbers are
build order. When the DoD includes tests, order each test before the code
that passes it — the builder writes the failing check first. Multi-chunk:
group under "### Chunk A — [name] (role: builder-…)", each ending with
"**State after this chunk:** [one line]". -->

1. [exact file to create/edit] — [exact change: function/section/copy + signature or shape + what it does]
2. [next]

## DEFINITION OF DONE

<!-- Runnable, tickable, pass/fail. Builder ticks against its own output;
reviewer re-runs them. Any box fails → fix and recheck. -->

- [ ] [observable behavior 1 is true]
- [ ] [the exact command/test that must pass, e.g. `npm test` green]
- [ ] [edge case handled: ...]
- [ ] [nothing in CONSTRAINTS was violated]

## REVIEWER NOTES

<!-- Subjective quality bar, riskiest step to scrutinize, acceptable-vs-not
judgment calls the blueprinter can foresee. Smuggled scope or an unrequested
abstraction is a finding, same as any other quality gap. -->

[what to check beyond the DoD boxes]

## IF SOMETHING IS UNCLEAR (anti-stall)

If the builder hits a genuine gap this blueprint does not cover: make the
smallest safe assumption, write `ASSUMPTION: ...` at the top of its report, and
keep going. Never stall, never ask, never invent new scope. If STARTING STATE or
GROUNDED AGAINST no longer matches the repo, that is not a small gap — stop and
flag for re-blueprinting instead of building on a stale spec.
