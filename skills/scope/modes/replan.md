# Scope Mode: replan

## Replan (the living rhythm, run after a feature or phase ships)

The default cadence, not rare: run each time a feature or phase lands, keeping the scope matching reality and queueing the next slice. Reconciles in place, never spawns a new file; coarse and surgical (reconcile cells, append rows, don't rewrite the file).

1. Read the whole scope again (single file, or `index.md` + epics; the workspace's in a monorepo) and the code/specs for what just shipped.
2. Reconcile what shipped: mark completed features `done` (verify from code/spec, don't stamp); tick nothing unconfirmed; leave rows `/develop`/`/sync` already advanced. **A feature whose governing spec is `Assumed`** keeps its `assumed decision (spec NNNN)` note and is surfaced (below) as decision debt; the note does not block `done`, so reconcile its status from what actually shipped.
2a. Surface assumed decisions in the readout and report: for each feature with an `Assumed` spec, list it as "built, awaiting ratification (spec NNNN)" and point to `/architect <feature>`. The flag does not block `done`; it stays surfaced until ratified.
3. Enroll needs surfaced during the build: read shipped features' spec `## Consequences` and `## Follow-up` sections; a follow-up (e.g. "add rate limiting") not yet a scope row becomes a new `planned` row with intent, tier, `Needs spec?`; the scope grows from real build feedback.
4. Reprioritize active work into `Now`, `Next`, and `Later` using the compression test. Only foundations directly required by `Now` precede its core loop. Work dropped from scope → `dropped`. If risk clearly shifted, update the workflow recommendation and state why without another panel.
5. Queue the next `Now` feature. Route to `/architect` only for a blocking difficult to reverse, high risk, or product contract decision. Otherwise route straight to `/develop`.
6. Report via the completion block (mode: replan): marked done, enrolled from spec follow-up items, reordered/dropped, next step.
