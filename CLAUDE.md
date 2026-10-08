# CLAUDE.md — agent orientation

You are working in **HelloWorldApp**, a product built with the **Enate SDLC Factory**.
This file is the *agent* front door (auto-loaded every session); `README.md` is the human one.

## Read this first — and follow the flow

This product is built by walking the Factory's **HITL → AFK** flow. **Before you act, read
the field guide and follow the flow it describes:**

➡️ **[Using the Enate SDLC Factory](https://github.com/enateltd/factory-skills/blob/main/docs/using-the-sdlc-factory.md)**

The guide is the source of truth for *which skill to fire when*. The single rule it hinges on,
which you must never break: **only a human moves a Story to `Agent Ready`** — that is the HITL→AFK
handoff; the orchestrator owns every other transition.

## Where the Factory skills come from

The Factory skills install as the **`enate-sdlc-factory` plugin** from the `enate-skills`
marketplace declared in this repo's `.claude/settings.json` (source:
`enateltd/factory-skills`). A local session loads them once the plugin is installed — in the
desktop app, **+** → **Plugins** → **Add plugin**. A cloud session installs no plugins, so it
gets the skills from its environment's setup script instead. Plugin-loaded skill names carry the `enate-sdlc-factory:` prefix (e.g.
`/enate-sdlc-factory:factory-tdd`); guide references like `/factory-tdd` mean that skill under whatever name
your available-skills list shows.

## The documentation fabric (load before you plan or build)

Authority order (earlier wins): **ADR > Technical-Context > Context.MD > Spec > Plan.**

- **`Technical-Context.MD`** — the engineering contract every code-writing agent must respect
  (principles, secure-coding baseline, branching, and the **Testing & the ratchet** standard).
- **`Context.MD`** — the domain glossary (the project's language).
- **`PRD.md`** — product requirements. The **Roadmap** — the ordered Feature list — lives
  on the tracker as Feature work items, not in a git file.
- **`docs/adr/`** — architectural decisions (highest authority).
- **`docs/superpowers/specs/`** · **`plans/`** — per-Feature Spec and Plan (the Plan carries
  the **Context references** an agent loads).

## Non-negotiables from day one

- **HITL delivery opens and closes its own board state — nothing else will.** Before making
  any change while hand-delivering a work item, set `System.AssignedTo`, `System.IterationPath`,
  and `System.State: Active` (for a Feature, when its first Story starts); set `Resolved`/`Closed` when the delivering PR merges. Full rule:
  `Technical-Context.MD` → *Git Guardrails*.
- **Every PR references its ADO work item with a bare `AB#<id>`**, on its own trailing line,
  never preceded by a transition verb on the same line. Full rule, with the exact footer shape:
  `factory-skills/docs/ado-field-reference.md` → *Linking a PR back to its ADO work item*.

## Dev commands

<!-- TODO(init): fill once the stack is chosen — install / test tiers / run.
     Written by /factory-init-tech-context or the first feature build. -->
_Dev commands not set yet — filled when the stack is chosen (`/factory-init-tech-context`)._   <!-- filled per-product at init: install / test tiers / run -->
