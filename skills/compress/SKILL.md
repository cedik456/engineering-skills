---
name: compress
allowed-tools: Bash, Read, Grep, Glob, Write, Edit, AskUserQuestion
description: "Run /compress to shrink a product idea, existing scope, or oversized feature into the smallest useful end to end build. Keeps the wider product picture as Next and Later, edits the living scope in place, and hands off one immediate build step."
---

## Output style (plain words, no dashes, no hyphens)

<!-- OUTPUT-STYLE:START -->
Write everything this skill produces, files and messages alike, in plain simple language. Talk to the reader as `you`, warm and direct like a colleague, and present every step as a recommendation they may run or skip, never an order. Keep technical terms that carry real meaning; explain each in plain words. Never use a dash or a hyphen as punctuation: no em dash, no en dash, and no hyphenated compounds. Write `read only`, not `read-only`. Say it in simple words, or reword the sentence. Code, file paths, command flags, and values other skills match on keep their hyphens. Use short sentences, commas, or parentheses. Clear beats clever.
<!-- OUTPUT-STYLE:END -->

## What this skill does

Turns a broad idea or plan into the smallest coherent build that proves value. It preserves the bigger picture, but separates what must be built now from what can wait.

Use it when:

* an idea has grown into too many features
* a scope is responsible but not buildable soon
* one feature or spec is trying to solve future versions too
* the engineer asks for an MVP, a smaller slice, or something shippable quickly

This is an optional pressure release valve. `/scope` already applies the same compression rules when making a new plan. `/compress` is for applying them again when a plan or feature has expanded.

## Artifact ownership

`/compress` may create or edit the living scope under `docs/scope/`, or `.workflow/scope/` when that is the existing artifact base. It never creates a second MVP plan beside the scope.

It never edits a spec under `docs/specs/`. When the input is a spec or one oversized feature, it reads it, writes the compressed boundary into the matching scope feature, and tells the engineer to run `/architect <feature>` if the governing spec must be revised.

Never delete a feature, requirement, or already recorded decision. Move work to `Next` or `Later`, or mark it `dropped` only when the engineer explicitly says it is no longer wanted.

## The compression test

Define one target user, one painful job, one observable outcome, and one time budget. Then test every capability:

> If this disappeared, could the target user still complete the core loop, and could we still learn whether the product is useful?

If yes, move it out of `Now`.

Something belongs in `Now` only when at least one is true:

* the core user cannot complete the core loop without it
* the team cannot observe or validate the promised outcome without it
* it is required for safety, security, data integrity, or an actual legal obligation in this slice
* it is a direct technical dependency of another `Now` item

Convenience, polish, automation, scale preparation, configurability, secondary roles, extra platforms, and speculative integrations default to `Next` or `Later`.

Prefer one role, one platform, one path, one integration, and simple defaults. Prefer manual operations, seeded content, and direct implementation when they still test the product honestly. Never fake the core value.

## Risk keeps its strength

Compression removes breadth, not necessary rigor. Authentication, payments, regulated or sensitive data, destructive migrations, public contracts, authorization boundaries, and difficult to reverse architecture still receive a real design step and the appropriate workflow tier.

For reversible choices, make a clear recommendation and record the assumption. Do not turn a reversible setup detail into another interview.

## Execution

### 1. Resolve the input

Infer the input from the command and repository:

* product idea with no scope: create the living scope using the compact shape below
* existing scope: compress that scope in place
* named feature: compress that feature and its dependencies inside the scope
* spec path: read the spec, find its linked scope feature, and compress the scope boundary without editing the spec

If no idea, feature, scope, or spec can be inferred, ask one plain question: "What should I compress into a buildable MVP?"

Read only the relevant scope file and linked spec. Do not scan unrelated specs.

### 2. Establish the core loop

Infer what the prompt and repository already reveal. Ask only for missing answers that materially change the cut:

* Who is the first user?
* What single job must they complete?
* What observable result proves the product helped?
* What time budget should `Now` fit?
* Is there a real safety, compliance, money, or sensitive data constraint?

Ask these in one compact round, up to four questions. A second round is allowed only when a missing answer would change `Now`. If no time budget is given, recommend the smallest useful build that can reach a visible working loop in a few focused development sessions, and state the assumption.

### 3. Inventory, then cut

List the plausible capabilities and existing scope features. Classify each as:

* `Now`: the core loop and only its direct dependencies
* `Next`: the first improvement after the loop is proven
* `Later`: useful wider product work that should not shape today's build

Keep `Now` coherent, not artificially tiny. It must work end to end. Avoid a front end shell with no real value unless the stated goal is a visual prototype.

Foundations are not automatically `Now`. Include only the narrow stack, schema, design, tooling, or integration work the first loop directly needs. Fold small foundations into the first slice when that makes the handoff simpler.

If `Now` still does not fit the time budget, reduce variants before removing the core loop: one role, one happy path, fewer states, manual administration, one provider, one platform. State what is simplified and what would make the result misleading or unsafe.

### 4. Update the living scope

Use the existing scope format and edit surgically. Preserve numbers, statuses, checked work, spec links, and code pointers.

Group active work as `Now`, `Next`, and `Later`. In the At a glance table, use those values in the Phase column. Existing or completed features keep their status and may appear before `Now` for context.

For `Now` features:

* keep a one or two line intent
* make `Done when:` describe an observable end to end outcome
* keep the existing command or milestone checkbox shape
* mark `needs a decision` only when building this slice requires an unresolved, difficult to reverse, or high risk choice

For `Next` and `Later`, keep compact feature sections or the scope's existing deferred list. Do not expand them into build tasks or specs.

If no scope exists, create the smallest normal `docs/scope/scope.md` compatible with `/scope`: header, At a glance table, `Now`, `Next`, `Later`, and the legend needed by later workflow skills.

### 5. Hand off to building

End with:

* the core loop in one sentence
* the time budget or assumption
* what moved out of `Now`
* the first unticked command from the first `Now` feature
* one genuine risk or assumption, only when it matters

The next command should normally be `/develop <feature>`. Use `/architect <feature>` first only when the current slice has a blocking decision under the risk rules above. Do not recommend running the whole workflow before building.

