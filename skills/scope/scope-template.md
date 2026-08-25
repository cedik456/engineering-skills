Reference shapes for writing the scope and the completion report. Rules live in `SKILL.md`.

## What keeps it readable (the format rules)

- **Two parts:** a slim **At a glance** table for a quick scan, then **the plan** as clean feature sections grouped into `Now`, `Next`, and `Later`. Build order inside `Now` is just the section order. There is no separate "build order" list to keep in sync.
- **Clean headings.** A heading is `### <N>. <Feature name>` plus a short status word and short tags **only when they carry real information** (`needs a decision`, a per feature approach override, a workflow tier override like `· GA`). Never a pipe delimited metadata row like `Title | P0 | inherit | …`.
- **Each fact appears once.** Intent, the definition of done, tasks, and pointers live in the section; the At a glance table is the quick index. Status is shown in the table and beside the heading, and nowhere else.
- **Only what is set.** No `n/a`, no `inherit`, no empty fields. A pointer line (`spec <n> · code in <path>`) appears **only once those exist**: the spec link added by `/architect` at capture, the code path by `/develop`.
- **A feature grows a defined shape.** It has a one or two line **intent**, a single **Done when:** line (the acceptance criteria seeds), and **checkbox steps**. A **not yet designed** feature has **one box** (its entry command: `/architect` when it `needs a decision`, else `/develop`, or `/audit` for standards & tooling). **When its spec is captured, `/architect` fills in the built ready shape:** `Design it` (ticked) → `Build it: /develop <feature>` with **2 to 5 milestone sub items rolled up from the spec** → `Verify it: /check verify <feature>` → `Test it: /test <feature>`. **The atomic build tasks stay in the spec's `## Build plan`, never here**. The scope carries only the milestone rollup. The next step is always the first unticked box.

## Single file scope

```markdown
# Scope: <Product name>

<One or two plain sentences: what the product is and who it serves.>

**Build approach:** <Tracer Bullet | Skateboard | Facade | Journey> (<one-line principle>).
**Workflow:** <Prototype | Alpha | Beta | GA> (<what runs after develop, e.g. Beta = check verify, then test>). The project default level of rigor. `/architect` is the recommended first stop for a feature with a real decision, but skippable when you already know the build. Any feature can carry its own tag (e.g. `· GA`) to do more or less.

_These are recommendations to keep your build orderly, not requirements. Skip anything that does not fit: if you already know how to build a feature, use `/develop` and skip `/architect`. You decide when a feature is `done`._

## At a glance

| # | Feature | Phase | Status |
|---|---------|-------|--------|
| 1 | Runnable product foundation | Now | in-progress |
| 2 | Core standup loop | Now | planned |
| 3 | Daily reminders | Next | planned |
| 4 | Billing & plans | Later | planned |
| … | … | … | … |

## Now

### 1. Runnable product foundation · in-progress
Choose and scaffold only the stack the first working loop needs.
**Done when:** the project boots locally and is ready for the core standup loop.
- [x] Decide the narrow stack (spec): `/architect runnable product foundation`
- [x] Scaffold it: `/develop runnable product foundation`
Spec 0001 · code in `./`

### 2. Core standup loop · in-progress
<!-- DESIGNED: /architect captured spec 0002 and filled in the shape below. The boxes under
     "Build it" are a ROLLUP of the spec's ## Build plan. Atomic details stay in the spec. -->
Sign in, create one team, submit today's update, and read today's feed. This is the real walking skeleton.
**Done when:** the first user can complete the whole standup loop and see the submitted update.
- [x] Design it (spec): `/architect core standup loop`
- [ ] Build it: `/develop core standup loop`
   - [ ] Minimal user, team, and update data (AC-1..3)
   - [ ] Submit and read today's update (AC-2..5)
- [ ] Verify it: `/check verify core standup loop`
Spec 0002 · code (filled by /develop)

## Next

### 3. Daily reminders
Nudge members who have not submitted before a team cutoff, so daily standup becomes a habit.
**Done when:** unsubmitted members get a timezone-aware reminder before cutoff, and submitters are not nagged.
- [ ] Build it: `/develop daily reminders`

## Later
Useful wider product work, kept visible but not allowed to shape the first build.
- **Email invites**: invite teammates by email · needs a decision
- **Billing & plans**: free and paid tiers · needs a decision · GA
- **Chat integrations**: post standups to team chat · needs a decision
- **Product analytics**: measure signups and habit · needs a decision

## Legend

**The decision box.** A feature marked `needs a decision` carries one sub task whose label ends with `(spec)`. Its wording varies, so skills locate it by that suffix, never by an exact label. A feature without a blocking decision starts with `/develop`. Every other box is an execution box and `/architect` never ticks one.

**Feature lifecycle**: the scope updates as a feature moves; each row is what it shows and who sets it:

| State | Set by | The feature shows |
|---|---|---|
| `planned` · needs a decision | `/scope` | one box: `Design it (spec): /architect <feature>` |
| `in-progress` (designed) | **`/architect` at spec capture** | `Design it` ticked; spec linked; `Build it: /develop <feature>` + **2 to 5 milestones**; the tier's closing boxes (`Verify it` Alpha+, `Test it` Beta+, `Review it` + `Document it` GA); any surfaced follow-up enrolled |
| `in-progress` (building) | `/develop` | milestone sub-boxes tick one by one; code pointer filled |
| `in-progress` (verified) | `/check verify` | `Build it` + milestones ticked; `Verify it` ticked |
| `done` | **you, when you decide it is** (any skill sets it when you say so); `/sync` reconciles | boxes you ran ticked, skipped ones marked skipped; the tier's last stage (`Prototype` → after `/develop`; `Alpha` → after `/check verify`; `Beta`/`GA` → after `/test`) is the suggested point to call it done; `/sync` captures conventions |

- **Next step** = the first unticked box (always a command or a tracked milestone).
- **needs a decision** = run `/architect` first; otherwise straight to `/develop` (or `/audit` for standards & tooling). The tag drops once the spec is captured.
- **Atomic build tasks live in the spec's `## Build plan`, not here**: the scope carries only the milestone rollup.
- **Status** `planned` → `in-progress` → `done`, plus `existing` (pre-workflow) and `dropped` (de-scoped, kept for history).
- **Approach tag** beside a heading (e.g. `· Facade`) overrides the project default for that feature; no tag = inherits it.
- **Workflow tier tag** beside a heading (e.g. `· GA`, `· Prototype`) sets that one feature's rigor above or below the project default; no tag inherits the default. It decides the feature's check boxes and each skill's next suggestion.
- **Workflow** (header line) is the project default, what runs after `/develop`: **Prototype** = nothing (trust develop's own build time self check); **Alpha** = `/check verify`; **Beta** = `/check verify` then `/test`; **GA** = adds a fresh model `/check review` then `/document`. A feature built on an unratified decision (an `Assumed` spec) stays flagged, but that never blocks `done`.
- **Pointer line** (`spec <n> · code in <path>`): the spec link added by `/architect`, the code path by `/develop`.
```

## Brownfield enrollment

Already built features are enrolled **for context**, above the planned ones, with status `existing` (complete, no task list) or `in-progress` (partial, finish via `/develop`), each with a code pointer. They also appear in the At a glance table.

```markdown
### A. Auth · existing
Pre-workflow auth: sign in, sessions, reset. code in `src/auth/`

### B. Product catalog · in-progress
Partial catalog; finish the remaining pieces via /develop. code in `src/catalog/`
```

`existing` is not `done`: it predates the workflow, so `/develop` and `/sync` leave it alone.

## Large product: epic split

When `scope.md` outgrows a comfortable scan (roughly a dozen plus features across clearly distinct areas), split by epic: **rename `scope.md` to `docs/scope/index.md`** (keep the At a glance table across all epics + a one line status rollup per epic, each linking its file), and **move each area's feature sections out into its own `docs/scope/<epic>.md`**. Promote **on demand**; don't split a small product early. File names are always **semantic** (`scope.md` / `index.md` / `<epic>.md`), never numbered. In a monorepo, each workspace gets its own `docs/scope/<workspace>/` the same way, with a top level `docs/scope/index.md` mapping the workspaces (one line + rollup each).

## Completion report block

Lead with what the pass produced and the first step; the approach, tier, and full list are in the scope file itself (per `docs/conventions.md`). Template:

```
## /scope <plan | replan | add> Â· <product, one line>

**<N> features captured, <A> in Now, <B> in Next, <C> in Later. Build approach <name>, workflow <tier>.**
Next: /clear, then <the first unticked box in Now; a new stack may need `/architect`, otherwise prefer `/develop`>
Heads up: <a feature bumped to a higher tier, a `needs a decision` foundation, or a genuine risk>   (omit if none)
Scope written to <docs/scope/scope.md>.
```

_Context hygiene: the scope, the specs, and `AGENTS.md` are the durable state, so the workflow hands off through files, not the chat. Advise `/clear` between units (after `/scope`, after each `/architect`, between features) and `/compact` mid unit if one run gets long. On Claude Code use `/clear` / `/compact`; use your agent's fresh session equivalent elsewhere._
