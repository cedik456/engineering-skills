# Architect Main Flow: design conversation

### Scope validation (before Framing)

Run these two checks in order; Check B before Check A.

**Check B: "Already built" detection (runs first)**

Scan the topic for phrases signalling an existing decision: "I built", "we built", "we're using", "we use", "I use", "we chose", "I chose", "already using", "already built", "just document", "document the decision we made", "decided to use", "we went with", "we're on".

If found, before anything else present a decision panel (plain text options where the agent has no picker): "This sounds like an existing decision you want to document rather than explore from scratch.", options: **Document it (write the spec from what you tell me) (recommended)** · **Reconsider it at the appropriate Slice or Full depth**.

If they pick Document it:
1. Capture the rationale behind the already made decision, as plain text (free text, not MCQ) questions generated for this specific decision. Cover at least these three angles, worded for what they built, and add any that this decision clearly raises: the **alternatives** they considered before choosing this (even a brief "we looked at X and Y but went with Z"), the **main reason** they chose it over those alternatives, and the **tradeoffs** the team is accepting (what it makes harder). Ask more if the decision has other load bearing rationale worth recording; the goal is a faithful account, not a fixed three.
2. Wait for their answers.
3. Take the documentation path: skip the staged conversation. Keep their answers as `DOCUMENTATION_CONTEXT` alongside the design topic, and treat the staged answers slot as `"skipped, documenting an already-made decision"`. Still infer the framing (MODE, platform, stack) from the topic + `AGENTS.md`.
4. Write the spec yourself as a documentation task: `DOCUMENTATION_CONTEXT` is the engineer's account. Read existing code if `SOURCE_FILE_COUNT` > 0 to verify and supplement (via a `scout` for a large repo). Document what was built, not another evaluation of options. Because this is **already shipped**, set `**Status**:` to **`Accepted`** at creation (not `Proposed`); same whenever the linked scope feature is already `existing` (shipped before this workflow existed).

If they pick the full process: proceed to Check A, then Framing and the staged conversation normally.

**Check A: Product vision vs. specific decision (runs second)**

Product scoped topic: describes what the product *is* rather than what to *decide* ("a B2B SaaS that manages teams", "a marketplace for freelancers"), names no specific technical component, feature, or technology, would need 5+ separate specs, and uses business/product language. Decision scoped: names a specific component, feature, or technical concern ("auth approach", "team invitations feature", "the data store choice, relational vs document").

If product scoped, do not start the staged conversation yet:

1. Tell the engineer: "This describes a full product. /architect works one decision at a time. Let me help you pick the first foundational decision."
2. Generate 4 foundational first decision options tailored to the product type and present via your agent's interactive picker (`AskUserQuestion` on Claude Code), or the same options as plain text (question: "Which foundational decision should we design first?", header: "First decision"). For most products: tech stack/architecture, auth/identity, core domain data model, and the most important product specific concern, worded for what they described.
3. After selection: update the design topic to that decision and proceed to Framing.

---

### Framing: infer, don't interrogate (no fixed question round)

Infer the framing from the topic + `AGENTS.md` + codebase, don't ask it. State it back in a line or two so a wrong read is cheap to correct, then spend all questions on the feature:

- **Mode**: `FEATURE` (new feature) · `ARCHITECTURE` (foundational stack) · `ENHANCEMENT` (changing something that exists) · `CROSS-CUTTING` (a project-wide standard). Infer from the topic and whether the thing exists in code; confirm only if genuinely ambiguous.
- **Platform**: web · mobile · API/backend · a mix. Infer from the stack in `AGENTS.md`, never assume web; it changes the questions (mobile auth, offline, push differ from web).
- **Workspace (monorepo)**: if a monorepo (workspaces config, or `apps/*`/`packages/*` manifests), identify which workspace this feature belongs to (topic, path, or the scope row's `Code area`; ask if unclear). Read that workspace's nested `AGENTS.md` for *its* stack (apps often differ; don't assume the root stack). Note the workspace in the spec's Context, and whether the decision is app-specific or repo-wide.
- **Stack & conventions**: language, framework, DB, community skills, from `AGENTS.md` (the target workspace's in a monorepo). Inferred, never asked.
- **Constraints**: team size, scale, compliance, from `AGENTS.md` / the product. Ask a per-feature compliance question only when *this* feature touches regulated data (payments, PII, health), never a generic deadline/team menu.

State it: *"Reading this as a new **FEATURE** on your existing stack (from `AGENTS.md`), web (correct me if not)."* Then begin the staged design conversation.

---

### Staged design conversation: buildability first (main model)

Design the current slice, not the mature product around it. At Slice depth, settle the smallest set of requirements and decisions `/develop` needs to build the linked `Now` feature safely. At Full depth, cover the wider decision surface justified by the risk, but still keep speculative future product features out of the current build plan.

Assemble the spec from what can be inferred, what the engineer must decide, and your recommendations. The engineer confirms the assembled result in the final spec review. Use a separate data model confirmation only when a wrong model would be costly to reverse. What you build becomes the spec's `## Requirements` and `## Build plan`, or `## Proposed stack` for a decision only spec.

Mechanics:
- **Offer the real options; exactly one is marked `(recommended)`** with a one line why (you make the call, they override). List every choice that genuinely applies, not a token two. Where the picker caps the count (Claude Code's `AskUserQuestion` allows four), present the strongest and let the custom slot carry the rest, or split across rounds; never drop real options to fit.
- **The last choice is always a free text custom input.** Claude Code's picker appends that "Other" slot automatically (don't add your own); in a plain text fallback, add it explicitly as the final option. This holds for design questions and confirm panels alike (the data model, accept the spec, References consent, an overlapping spec): one recommended option plus the custom slot.
- **Ask only blocking questions.** Ask when the engineer alone knows the answer, or when a choice is high risk, difficult to reverse, or changes the current product contract. Infer repository facts. Recommend reversible tool, setup, layout, naming, and local implementation choices, and record any material assumption in the spec.
- Capability first, per the picker/custom slot mechanics above. Batch related questions up to 4 per call. At Slice depth, aim for one compact round and continue only while a missing answer blocks a safe build. Full depth may use as many rounds as its risk requires.

Still infer the framing, then sort each current slice dimension as INFER, ASK, RECOMMEND, or DEFER. INFER from the prompt, codebase, and `AGENTS.md`. ASK only under the blocking question rule. RECOMMEND reversible expert choices. DEFER anything that does not affect the current acceptance criteria.

**Your mandate (senior+ role):** you are accountable for a safe, buildable slice and for resisting speculative architecture. A gap that blocks the current build is a failure. So is designing future scale, flexibility, or automation that the slice does not need.

**Before the stages, enumerate the dimensions touched by the current acceptance criteria,** sorting each INFER, ASK, RECOMMEND, or DEFER. Use the checklist as a risk scan, not a requirement to design every item:

- **Functional scope & boundaries**: what's in, what's explicitly out, key user flows with happy/unhappy paths
- **Data model & persistence**: entities, fields, types, nullability, relationships, indexes, uniqueness, retention/deletion
- **Lifecycle & state machine**: states, valid transitions, who/what triggers each
- **API / interface surface**: endpoints or actions, inputs, outputs, status codes, versioning
- **Authentication & authorization**: who may do what; ownership, roles, scoping across tenants
- **Validation & business rules**: limits, quotas, invariants that must always hold
- **External integrations**: providers, webhooks, idempotency, reconciliation
- **Library / provider & build vs buy**: central for any feature with a real implementation choice (auth, payments, search, storage, email, realtime); owned by Stage (c), whose rules apply (fresh current options, suggested pick, engineer chooses). For auth the mechanisms are: the project's existing platform/BaaS auth · a hosted auth provider · an auth library you host yourself · roll your own; pick the specific current products at runtime, never a frozen list.
- **Failure & edge cases**: concurrency, retries, timeouts, partial failure, empty/error/loading states
- **Performance & scale**: only the current expected volume and any limit that changes today's design; defer speculative scale systems
- **Security & compliance**: PII, encryption, audit logging, rate limiting, regulatory scope
- **Observability**: only what is needed to verify or safely operate this slice
- **Configuration & secrets**: new env vars, feature flags, credentials
- **UX surface (if UI in scope)**: capture the requirements (what each screen must show/do, states, accessibility); leave pixel/layout detail to `/develop`
- **Discoverability & SEO (public facing features)**: for any publicly indexed page: metadata, structured data (JSON-LD), OG/social cards, canonical URLs, sitemap/robots, SSR/SSG vs client render. Skip for internal/auth walled surfaces.
- **UI design (when the topic IS a page/screen, e.g. "home page UI", "shop page UI")**: a real design decision; the spec is the page's build spec. Settle:
  - **Design source: ASK, never assume.** Never auto pick the source (not even "a design MCP is connected, so use it"). Ask *"How should I get the design for this?"* as a panel with no recommendation picked in advance: **From a design tool** (a connected design MCP, e.g. Figma, to pull the real tokens, spacing, components, and frames; use it or offer to connect it) · **From a screenshot or images I'll give you** · **From the existing `design.md` / current UI** · **No design yet, suggest a direction** (only if picked do you propose a style). The picker adds its own Other. Record the chosen source in the spec (for a design tool, which file and frames) so `/develop` uses the same one. If they pick a design tool but no MCP is connected, point them to connect it (see *Tool skills & MCP*), then proceed.
  - **Design system**: a `design.md` or design tool is the source of truth if present. If not, recommend a narrow visual direction for the current screen. Create a separate design system decision only when several `Now` screens share it or visual consistency is itself load bearing.
  - **Page composition**: what sections/blocks the page contains and in what order (e.g. home: hero → featured categories → product grid → social proof → footer); the "what goes on the page" the engineer alone knows.
  - **Component inventory**: the reusable components the page needs (cards, nav, filters, carousel), existing vs net new.
  - **Asset strategy**: when no screenshot/design was given and the repo has no images, decide the fallback (real assets the engineer will add, or an online placeholder source, e.g. a stock photo or avatar placeholder service), so `/develop` doesn't stall or invent broken paths.

Then cover only the applicable stages. At Slice depth, several stages may be inferred, recommended, or not applicable without questions. State important inferences and recommendations in one compact summary before writing so the engineer can correct them cheaply.

**Stage (a): Requirements.** Seed the acceptance criteria from the scope row's intent and `Done when:` line. Ask only about a missing core job, outcome, rule, or failure that changes the slice. Derive a small set of acceptance criteria and let the engineer review them in the final spec.

**Stage (b): Data model.** For a data backed slice, model only the entities, fields, relationships, and constraints its acceptance criteria use. Infer conventional fields and recommend reversible details. Ask about business meaning, ownership, retention, uniqueness, or relationships only when the answer changes the model materially. Show and confirm the assembled model when it introduces a difficult to reverse schema or Full depth applies. Do not model entities reserved for `Next` or `Later`.

**Stage (c): Stack and tools.** Reuse the existing stack. For a greenfield slice, choose only the layers required to make the first loop runnable. Ask the engineer about a provider or architectural commitment when it is difficult to reverse, costly, high risk, or preference driven. Recommend ordinary reversible libraries and setup choices. Do not select future email, search, analytics, hosting scale, background jobs, storage, or observability systems before a current acceptance criterion needs them. Generate any compared options fresh at runtime.

  **Current research and references.** Default `REFERENCES_LEVEL` to `none` and do not add another question. If a blocking choice needs current facts, or the engineer explicitly asks for sources, run one narrow landscape check over only that choice and set `REFERENCES_LEVEL` to `sources+links`. Use official sources and verify links. Do not research tools reserved for `Next` or `Later`.

  **Agent Skills and MCP servers.** Offer discovery only when a new tool chosen for `Now` would materially benefit from a dedicated skill or connection. Do not make this a mandatory question for every stack decision.

**Stage (d): API and interface surface.** Define only the actions the current acceptance criteria call. Name each action's inputs, outputs, authorization, and important caller visible failures. Close the value sourcing loop for values the slice must produce or display. Ask when the source is a business decision. Recommend conventional sources when reversible. Do not design endpoints for `Next` or `Later`.

**Stage (e): Security and authorization.** Settle who may perform the current actions, ownership boundaries, and any compliance triggered now. Authorization, money, regulated data, and sensitive data automatically use Full depth.

**Stage (f): Edge cases and failure modes.** Cover failures that would make the current loop unsafe, corrupt data, mislead the user, or prevent recovery. Defer exotic operational cases until expected usage makes them real.

**UI page features:** settle the page's core job, essential content, states, and design source. Reuse an existing design direction. When none exists, recommend a narrow direction that can be implemented now. A single screen does not automatically require a complete design system or a question about every component.

**ARCHITECTURE stack decisions:** use Full depth because the choices are foundational, but design the smallest platform that supports `Now`. Walk the required layers in dependency order and stop when the first slice is runnable. Record likely future layers as follow up without choosing their providers now. Run Tool skills and MCP discovery only for tools actually chosen for `Now`.

**Quality bar per stage:** every option maps to a real, feature specific decision (never a placeholder like "how complex is the data model?"), each with a one line tradeoff; allow several answers where they are not exclusive. One question per dimension, suggested pick marked with a one line why, and never add your own Other. No `(basis: …)` tag or source citation in option labels; the source and reasoning behind a recommendation belong in the written spec (its Rationale, which always stays, and its References section when opted in), not the live panel.

**Collect the RECOMMEND items** you will settle yourself when you write the spec (calls better made with full design context) as a list; decide each there, stating the pick + one line why + the runner up, and never echo one back as an open question.

**Buildability gate before writing.** For every current acceptance criterion, confirm that `/develop` knows the behavior, the source of required values, the affected interface, and any safety or authorization rule. Anything still blocking becomes a question. Everything else is inferred, recommended, not applicable, or explicitly deferred. A short interview is healthy when the repository and scope already settle the build.

**After all stages are signed off** (buildable feature spec): the confirmed acceptance criteria seed the spec's `## Requirements`; the confirmed data model, API surface, and stack derive `## Build plan` (each task tagged with the AC it satisfies; the data model is the target, its migration sized to the feature as in Stage (b)). For a decision only spec (an ARCHITECTURE stack decision or a CROSS-CUTTING standard) there is no `## Requirements`/`## Build plan`: the spec is the decision itself (`## Proposed stack` / `## Standard definition`), and the feature that executes it (e.g. the scaffold sub task) derives its steps at `/develop` time. Order and slice the plan through your Staff/Principal lens on the feature's build approach (read in pre-flight: row override, else project default), reasoning about what it implies rather than a fixed recipe. With no approach on record, default to end to end slices and note the assumption. Then write the spec (below).

**Examples of calibrated depth:**

* `/architect auth` uses Full depth because identity and authorization are high risk. It settles only sign in methods, session behavior, identity data, roles used now, recovery, and relevant failure handling. SSO and passkeys stay in follow up unless they are in `Now`.
* `/architect home page UI` at Slice depth asks for the core action and content only when unclear, reuses the current design direction, recommends a simple composition, and leaves testimonials, pricing variants, advanced animation, and a complete component library for later.

**Too vague to generate from?** (rare; the topic should have been narrowed first) There are no canned questions to fall back to. Narrow instead: run Scope validation again or ask one clarifying question, then generate the stage questions from the dimension checklist above. Feature specific questions always, never a generic MCQ list.

**Skip the staged conversation** on the "documenting a made decision" path (Check B); proceed directly to writing the spec with the documentation context.

**Enhancement mode guard**: if the inferred mode is `ENHANCEMENT` AND `SOURCE_FILE_COUNT = 0`, stop before the staged conversation and tell the engineer:

"Enhancement mode reads existing code to understand what's being changed, but no source files were found. What's the situation?
- A) The code exists in a different directory. Tell me the path and I'll check again.
- B) There is no existing implementation. Then this is really a new **FEATURE** (or **ARCHITECTURE**)."

Wait for their answer. If (A): run the source file count for that path again. If (B): switch the inferred mode and continue.

---
