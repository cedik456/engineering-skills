# Scope Plan Route: greenfield

Greenfield: plan the smallest useful end to end product from scratch. Apply these rules at Step 4 of `plan.md`, after the build approach is recommended.

## Compressed foundations

The first slice needs enough ground to run, not a complete company platform. Treat foundations as implementation dependencies, not automatic features.

Always identify these concerns, but create a separate scope feature only when the first slice truly needs a substantial or difficult to reverse decision:

1. **Runnable scaffold**: choose the narrow stack needed for the first slice and boot the project. When the user has not chosen a stack, keep Stack and architecture as one compact decision plus scaffold feature. The decision covers what is needed now, not every eventual integration or operational system.
2. **Project conventions**: use framework defaults first. Run `/audit` after the scaffold when the repository needs durable context. Do not make formatting taste, hooks, full CI, or broad tooling a prerequisite for the first visible feature unless the project risk requires them.
3. **Data shape**: model only the entities, fields, relationships, and constraints used by `Now`. Keep it inside the first feature when the schema is small. Split a data model foundation only when several `Now` features share it, the migration is difficult to reverse, or data integrity risk warrants a dedicated decision.
4. **Visual direction**: reuse framework or existing component defaults for a simple MVP. Split a design system foundation only when several `Now` screens depend on shared visual rules or the product's value is strongly visual. A single page does not need a complete design system before it can be built.
5. **Walking skeleton**: the first real feature should normally be the walking skeleton. Do not create a separate trivial skeleton and then rebuild the same layers for the core feature.

The default greenfield `Now` shape is therefore:

* one narrow scaffold or stack decision when needed
* one core end to end feature that proves the product's value
* at most the direct safety, validation, or operational support that core loop requires

Anything else becomes `Next` or `Later`, even when it will probably be necessary for a mature product.

## Sequencing

Within `Now`, place a required foundation immediately before the feature that consumes it. Prefer merging small foundations into the core feature so the first handoff reaches `/develop` quickly.

Shape the first slice exactly as the chosen approach's persona directs (`approaches/<name>.md`, already read in Step 3 of `plan.md`). The approach changes what gets built first, not just its label.

Apply the `Needs spec` test narrowly. A current high risk, difficult to reverse, or product contract decision receives `/architect`. Reversible setup and implementation choices receive a recommendation or recorded assumption during `/develop`.
