# Engineering Workflow Skills

A set of [Agent Skills](https://agentskills.io) that take a change from a vague idea to shipped, verified, documented code, for any AI coding agent. One skill per phase. Run only the ones a change needs, in any order.

The state lives in files (a scope, specs, AGENTS.md, tests), not in a chat session. So work survives across sessions, picks up where it left off, and works for a whole team.

```
idea → /scope → /develop → /check verify → /test → /check review → /document → /sync
              ↘ /architect only when a blocking decision needs it
```

Run `/debug` anytime something breaks. Run a bare `/scope` anytime to see where things stand.

> 📖 **Want the full picture?** Read the **[Workflow Guide](docs/workflow-guide.md)** — a plain-language walkthrough of every skill, the files that carry the work, who owns what, and one idea followed all the way from scope to shipped.

## The skills

| Skill | What it does |
|---|---|
| `scope` | Captures the wider product, then plans the smallest useful build as Now, Next, and Later. |
| `compress` | Shrinks an idea, scope, or feature when it has grown beyond a buildable MVP. |
| `audit` | Writes the AGENTS.md context files every other skill reads. |
| `architect` | Designs decisions blocking the current slice, with full depth preserved for risky work. |
| `develop` | Builds from the scope and any governing spec. Gates only on a blocking decision. |
| `check` | Confirms a change before merge. `/check verify` runs the real app; `/check review` reads the code on a second model. |
| `test` | Writes a test suite for the code you just changed. |
| `document` | Writes the PR text, changelog, release note, or postmortem from the real diff. |
| `sync` | Keeps AGENTS.md, the scope, and spec statuses current after a change. |
| `debug` | Finds and fixes the root cause of a bug, then hands a regression test to `/test`. |

Hardening (systems level failure mode analysis) is temporarily removed and will return as a system design specialization.

## Install

Uses [npx skills](https://github.com/vercel-labs/skills). Pick your agent:

```bash
# Claude Code (installs into .claude/skills, then restart Claude Code)
npx skills@latest add cedik456/engineering-skills -a claude-code

# Generic .agents/skills, read by Codex and other agents
npx skills@latest add cedik456/engineering-skills
```

Works on any Agent Skills client (Claude Code, Cursor, Codex, Gemini CLI, and [more](https://agentskills.io/clients)). Commit the installed skills folder to share the workflow with your team.

Each skill's instructions live in its `SKILL.md`, which is what every client reads. The `agents/openai.yaml` beside it is interface metadata only (the name, blurb, and opening prompt Codex shows in its agent picker); it carries no logic of its own.

## Where to start

**New product (greenfield):** `/scope` the idea. It captures the bigger picture but puts only the smallest useful loop in `Now`. Run the first command it gives you. A new stack may need one narrow `/architect` decision and scaffold; an existing stack normally goes straight to `/develop`.

**Existing codebase (brownfield):** `/audit` first so every skill understands your project, then `/scope` the next slice on top of what exists, then the feature loop.

**Any single change:** run only what it needs. A bug goes straight to `/debug`. A small change can be `/develop` then `/check verify`.

**Monorepo:** everything scopes to the target workspace, which has its own AGENTS.md, scope, stack, and commands.

### The feature loop

```
/develop → /check verify → /test → /check review → /document → /sync
    ↑
/architect only when the slice has a blocking decision
```

`/scope` also recommends a **workflow depth** for the project: `Prototype` (just `/develop`), `Alpha` (adds `/check verify`), `Beta` (adds `/test`), or `GA` (adds `/check review` and `/document`). It states the recommendation without making another planning gate. A risky feature can still use a heavier path.

`/scope` records the wider product but compresses active work into `Now`, `Next`, and `Later`. `/compress` applies the same cut again when a plan grows. `/architect` uses Slice depth by default and asks only about decisions blocking `Now`; foundational architecture, payments, authentication and authorization, sensitive data, destructive migrations, and public contracts retain Full depth. Reversible implementation choices can go straight to `/develop` with a recommendation or recorded assumption.

The gate is layered, not magic: `/architect` names the source of every value a feature must produce (so gaps surface at design time), `/develop` checks that coverage again before building, and at Beta+ `/architect` recommends running an independent cross-model critic over the spec for decisions it never settled (you decide, and you decide on any gaps it finds). It's a strong, defense-in-depth gate that catches the vast majority — not an absolute guarantee, no prompt can be. Behavioral correctness is caught by the `/check verify` and `/test` layers.

## What gets written, and where

| Artifact | Path | Owner |
|---|---|---|
| Scope | `docs/scope/` | scope and compress |
| Specs | `docs/specs/` | architect |
| Context files | AGENTS.md (plus a thin CLAUDE.md pointer) | audit, kept current by sync |
| Design system | `design.md` (art direction; token values live in CSS) | develop |
| Review findings | `docs/reviews/` | check |
| Tests | your test dirs | test |
| App code | your source tree | develop |
| Human docs | PR body, CHANGELOG.md, `docs/releases/`, `docs/postmortems/` | document |

If `docs/` is a published docs site, these move to `.workflow/` so they do not ship with your site. Because state lives in files, each skill suggests `/clear` at handoffs, so a fresh session reads from disk again and long chats do not pile up cost.

## Skill reference

For each skill: what it does, and when to run it.

**scope**: Captures the wider product, then compresses it into `Now`, `Next`, and `Later`.
When: to start a product or plan the next useful slice.

**compress**: Reapplies the MVP cut to an existing idea, scope, feature, or spec without deleting deferred work.
When: when the plan is responsible but no longer feels buildable soon.

**audit**: Writes the AGENTS.md context files that give every skill your project's stack, commands, and conventions.
When: brownfield, run it first. Greenfield, run it after the stack is chosen and the project is scaffolded. Monorepo: gives each workspace its own nested AGENTS.md.

**architect**: Designs the decisions blocking the current slice and writes a build spec. Slice depth is the default; `/architect full <topic>` and high risk work use comprehensive depth.
When: a difficult to reverse, high risk, foundational, or product contract choice is still open, or `/develop` identifies one.

**develop**: Builds a feature, UI or backend from the scope and any governing spec, runs migrations, and advances the scope.
When: when the next `Now` feature is clear enough to build. It gates to `/architect` only for a blocking decision. Monorepo: builds inside the target workspace using its commands.

**check**: Confirms a change before merge, in two modes.
When: `/check verify` after `/develop` to run the real app and prove the feature works against the spec; `/check review` before a PR for a senior review on a different model than wrote the code. Any project type.

**test**: Writes a senior test suite for your uncommitted change and saves your framework choice.
When: after building a feature or fixing a bug. Monorepo: resolves the framework per package.

**document**: Writes the human facing prose (PR, changelog, release note, postmortem) from the real diff.
When: a finished change needs writing up. Any project type.

**sync**: Reconciles AGENTS.md, the scope, and spec statuses to what the repo now shows.
When: the last step around merge. Monorepo: reconciles the right workspace.

**debug**: Runs a disciplined root cause loop and hands a regression test to `/test`.
When: anytime something is failing, throwing, or behaving wrong. Not tied to project type.

## Learn more

The **[Workflow Guide](docs/workflow-guide.md)** is the deep dive: how the scope, specs, AGENTS.md, and design system live and who is allowed to change them; the acceptance-criteria thread that ties every stage together; a full worked example from idea to shipped; the debug loop; and how the same flow runs on an existing codebase and in a monorepo.

---

Built with the [Agent Skills](https://agentskills.io) open format.
