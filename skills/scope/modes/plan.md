# Scope Mode: plan

### Step 1: Locate the scope; greenfield / brownfield / monorepo

Detect (skip `node_modules/` and `.git/`): source files (any `.ts`, `.tsx`, `.js`, `.py`, `.go`, `.rs`; presence ⇒ brownfield, none ⇒ greenfield); root `AGENTS.md`; existing scope under `docs/scope/` (`scope.md`, or `index.md` + epic files; monorepo: `docs/scope/<workspace>/`), noting the shape.

Read exactly one route file before continuing to Step 2:

- `modes/plan-monorepo.md` when workspace markers or multiple app/package manifests show a monorepo.
- `modes/plan-brownfield.md` when source files or a manifest show an existing codebase or an existing scope is being extended.
- `modes/plan-greenfield.md` when there is no source code and no manifest yet.

Do not read the other plan route files unless the classification changes. After the selected route has established the scope/workspace context, continue with Step 2 below.

### Step 2: Find the smallest valuable loop

Infer everything the idea, repository, and `AGENTS.md` already reveal. Ask only for missing answers that change the first build:

- the first user
- the single job they must complete
- the observable result that proves the product helped
- the time budget for the first working version
- any real money, safety, compliance, or sensitive data constraint in that first version

Ask in one compact round, up to 4 questions. Use a second round only when an unanswered item would materially change the first slice. Do not ask about monetization, scale, internationalization, analytics, SEO, secondary roles, or future integrations unless one directly affects the core loop or a real risk now.

Then run one capability sweep yourself. List the capabilities this product plausibly needs and classify them in the plan as `Now`, `Next`, or `Later`. This captures the bigger picture without turning every future capability into a current requirement or another interview question.

The compression test for `Now` is: if this capability disappeared, could the first user still complete the core loop, and could the team still learn whether the product is useful? If yes, it is not `Now`. Safety, data integrity, and actual legal obligations remain `Now` when the first slice triggers them.

### Step 3: Recommend the build approach

Decide how the first build is sliced and sequenced. Reason about this product and record the best fit with a one line explanation. Do not stop for a choice panel unless the engineer asked to compare approaches or rejects the recommendation.

- **Tracer Bullet**: vertical slices; each feature built end to end through every layer, working.
- **Skateboard**: MVP first; ship the thinnest usable whole first, then grow it.
- **Facade**: UI first; a clickable shell on placeholder data, then wire the back. Prototype grade (fast to demo, not production complete).
- **Journey**: a complete user path end to end per phase.

For a new MVP, prefer Skateboard when one thin usable whole can validate value quickly. Use Tracer Bullet when the first slice must prove a production path through several layers, Journey when the experience or funnel is the product, and Facade only for an explicitly visual prototype. Never name a tool; the approach shapes how, not with what.

**Once the approach is chosen, read its persona file and adopt that engineer's role for decomposition** (`approaches/tracer-bullet.md`, `approaches/skateboard.md`, `approaches/facade.md`, or `approaches/journey.md`). Read only the chosen one. Each persona defines how that engineer slices, what the first slice or deliverable is, what is real vs deferred, and the sequencing, with a worked example. All slicing and sequencing in Step 4 and Step 5 follows that persona. A per feature override (Step 5) reads that feature's chosen persona and applies it to that feature only.

Record it (the propagation source) in the scope header: `Build approach: <name> (<one-line principle>)`. A project wide convention: `/audit` and `/sync` persist it into root `AGENTS.md`; `/architect`, `/develop`, `/check verify` read and honor it. It also sets each feature's Phase (its slice / journey), shown in the At a glance table and as section grouping.

Header value = project default; a single feature may override via the optional per feature Approach (Step 5), a tag beside its heading (e.g. `· Facade`). Precedence: own tag if set, else project default; tag only when it differs (no tag = inherit).

### Step 4: Only the foundations the first slice needs

Include foundation work only when the first valuable loop directly depends on it. Keep the runnable scaffold, schema, design decisions, and tooling as narrow as that loop permits. Fold a small foundation into the first vertical slice when a separate feature would only delay building.

Do not plan a complete data model, design system, CI setup, observability stack, or walking skeleton as separate prerequisites by default. Promote one to a foundation feature only when it is difficult to reverse, shared by several `Now` items, required for risk, or too substantial to keep inside the first slice.

Order active work as `Now`, then `Next`, then `Later`. Within `Now`, place a required foundation immediately before the feature that consumes it.

- **Greenfield (and greenfield monorepo)**: apply the compressed foundation rules in `modes/plan-greenfield.md`. That route file is your Step 4 detail.
- **Brownfield**: the foundations already exist; do not plan them again. Plan the next slice on top per `modes/plan-brownfield.md`, shaping it to the Step 3 approach; enroll already built features rather than laying foundations.

### Step 5: Decompose into coarse feature sections (you reason; don't ask)

From the answers, produce the feature list as `Now`, `Next`, and `Later`. `Now` is the smallest coherent end to end build that fits the stated time budget. `Next` is the first improvement after value is proven. `Later` preserves the wider product picture without shaping today's implementation. Per feature:

- Keep features small: one page or one cohesive unit each (a listing, a product page, and a cart are three features, not one "storefront"); split anything spanning unrelated screens.
- **Intent (1 to 2 lines)**: what it is and why it matters.
- **Done when line (acceptance criteria seeds)**: one compact `Done when:` line of observable outcomes (e.g. "user can filter the list and the URL reflects it; empty and error states render"). Seeds, not a spec; `/architect` grows them into the spec's full requirements and acceptance criteria. Load bearing outcomes only.
- **Workflow tier** (only when it differs from the project default set in Step 5b): `Prototype` / `Alpha` / `Beta` / `GA`, from this feature's risk, scope, and compliance sensitivity. Most features inherit the project default; tag a feature (e.g. `· GA`) only when it warrants more or less rigor than the rest. Higher tier → more likely `Needs spec: yes`.
- **Approach (optional per feature override)**: inherit the project default. Recommend an override only when the feature clearly needs a different delivery shape, and do not open another panel unless the engineer asks to compare approaches.
- **Needs spec?**: yes only when the current slice has an unresolved choice that is difficult to reverse, high risk, or would change the product contract. Authentication, payments, sensitive data, destructive migrations, public APIs, authorization boundaries, and foundational architecture usually qualify. Reversible library, setup, layout, naming, and local implementation choices receive a recommendation or a recorded assumption and go straight to `/develop`. A whole page does not automatically require a spec when its core job, content, and existing design direction are clear.
- One decision per spec: multiple distinct decisions in one feature → one `Needs spec: yes` item each, never one lumped "strategy" spec. Several sharing one broad decision that then splits → an umbrella that dependents reference; never mark a dependent `no` when it carries its own decision.

No build task breakdown here. A not yet designed feature gets exactly one checkbox, its entry command: `/architect <feature>` when it `needs a decision`, else `/develop <feature>` (the coding standards and tooling foundation's first box is `/audit`, never `/develop`). Never enumerate UI / data model / API / test sub tasks; `/architect` fills the built ready shape on spec capture (see What this skill does; atomic tasks stay in the spec). The next step is then always the first unticked box, always a command or tracked milestone (no separate `Next:` line). See the lifecycle table in `scope-template.md`.

Analysis/inventory is not a scope row: cataloguing duplication, listing call sites, auditing current state is decision support research living with the spec (`/architect` puts it in the spec's `rationale.md`). Never plan a row or step that writes a `.md` into `docs/scope/`.

### Step 5b: Recommend the workflow depth without blocking

Now that the features exist, set the project's default **workflow depth** from the risk of the `Now` slice. State the recommendation and why. Do not stop for a choice panel unless the engineer asks to compare levels or rejects the recommendation.

Depth governs only the stages **after** `/develop` (verify, test, review, document). It does **not** turn off the `/architect` gate: at every depth, a feature that needs a load bearing decision still runs `/architect` first (or records an `Assumed` spec). Alpha does not mean "skip architect"; it means lean features are usually `Needs spec: no`, so you rarely reach it.

Use `Prototype` for throwaway experiments, `Alpha` for a low risk MVP that should be proven in the real app, `Beta` for a normal production slice, and `GA` for payments, sensitive data, compliance, destructive changes, or team critical systems. A risky feature may override a lighter project default.

Each tier also sets what `done` means (see `scope-template.md`); a feature built on an `Assumed` spec can still be `done`; the `Assumed` spec stays flagged as owing ratification until `/architect` ratifies it.

Record the pick as the project default in the scope header `**Workflow:**` line (see `scope-template.md`). This default is what `/develop` reads (via the effective tier) to scale the next steps it recommends after a build.

### Step 6: Write the scope (single file or epic split)

List the scope location again immediately before writing (a teammate may have changed it), then write per `scope-template.md`:

- Small product → single file `docs/scope/scope.md` (monorepo: `docs/scope/<workspace>/scope.md`): At a glance table (including brownfield enrolled features) + phase grouped feature sections + legend.
- Large product → epic split per Artifact ownership (`docs/scope/index.md` + `docs/scope/<epic>.md`); promote only when `scope.md` has outgrown a comfortable scan, else stay single file.
- Run again (living update): edit in place, never a dated file: append new rows with the next free `#`, sharpen existing rows' intent/seeds, leave existing statuses untouched; set a now out of scope row to `dropped` (never delete). Brownfield: append enrolled `existing`/`in-progress` rows above the `planned` ones.

Do not add citations or a References section unless the engineer explicitly requested sources under Step 6b.

### Step 6b: References only when requested

Do not ask a references question during ordinary scope planning. Keep the scope clean and focused on building. If the engineer explicitly asks for sources or current research, add a compact References section and verify any web links before writing them.

### Step 7: Report and hand off

Print the completion report using the `## /scope complete` block in `scope-template.md`, filled with this run's specifics. `/scope` does not run `/architect` or `/develop` for you; it hands you the ordered, coarse, weighted list to walk feature by feature (architect the `Needs spec: yes` ones, then build).
