# Economics — where the tokens are allowed to go

The suite's cost promise depends on one discipline: expensive models spend
tokens only where judgment compounds; everything checklist-shaped routes to
a cheaper tier — and the judgment role itself runs at the cheapest tier
that holds up under the blueprinter discipline in the skill, not
automatically at the frontier. This file is the shared machinery every
skill in the suite cites. MODELS.md maps the role names to concrete models.

## The routing test

Before any piece of work runs in the blueprinter session, ask: can it be
written as a checklist with an output someone else can verify? Yes → route
it down a tier (repo sweeps, file inventories, draft assembly, running
verify commands, mechanical edits). No — it needs taste, tradeoffs, or an
open-ended call → it stays with the judging role (interview questions,
approval decisions, vet reads, ship/fix routing). Every role default in
MODELS.md justifies itself against this test.

## Handoff packets

A delegated prompt is written for a worker with zero chat context. It
carries: the objective; the exact files/surfaces in scope and anything out
of scope; the evidence format to return (paths, line refs, commands run,
diffs, failures); the verification commands and what success looks like;
the likeliest way the task goes wrong and the countermove; stop conditions
(mismatch with the prompt, a command that keeps failing, out-of-scope files
needed → stop and report, don't improvise); and a mandatory "unverified"
list — anything asserted but not checked gets flagged, not folded in.

## Vet before trust

Packets are leads, not facts. Before a blueprint anchors to a cited
`file:line`, before a review verdict ships, before the user is told work is
done — the judging role reopens the specific cited file or re-runs the
specific check and confirms it. Vet the citation, don't re-do the sweep: a
full re-read of delegated work is a double-read (you paid twice for the
same lines).

## Spend map

| Phase | Tokens go to | Anti-pattern this prevents |
|---|---|---|
| Grounding | cheap recon sweep + a small personal vet-read (README, deps, 2–3 core files) by the blueprinter | top model reads the whole repo |
| Interview | blueprinter role — the questions are the product | delegating the one judgment step that catches wrong builds |
| Drafting | blueprinter role — writes each blueprint against the lean template | padding blueprints with restated context the builder doesn't need |
| Build | builder tiers per blueprint role | one big model building everything |
| Review | reviewer tier; escalate per the skill's trigger list | flat top-tier review of every diff, risky or not |
| Fix/retry | cheapest competent fixer; two failures at one tier → escalate one tier | burning retries at a tier that already failed |

## When not to use this suite

Below roughly a single-file, low-ambiguity change, a plain session is
cheaper than this pipeline — the fixed planning cost cannot amortize.
That's a measured result, not a guess. Use the pipeline when the task is
multi-item or repo-scale, when ambiguity is dense, or when a wrong silent
guess is expensive. Point small tasks at a plain session and say so.
