Quickstart:

```bash
npx skills add mattpocock/skills --skill=to-spec
```

```bash
npx skills update to-spec
```

[Source](https://github.com/mattpocock/skills/tree/main/skills/engineering/to-spec)

## What it does

`to-spec` turns the current conversation and your codebase understanding into an **OpenSpec change proposal** — a folder under `openspec/changes/<change-id>/` holding the problem, the delta specs, and the design rationale.

It does **not** interview you again. By the time you reach for it, the alignment work is done — `to-spec` synthesises what is already known rather than asking a fresh round of questions.

## When to reach for it

You invoke this by typing `/to-spec` — the agent won't reach for it on its own.

Reach for it once a change has been talked through and the domain language is settled, and you want that shared understanding written down before any code is written. If you *haven't* aligned yet, grill first — for that, use [grill-with-docs](https://aihero.dev/skills-grill-with-docs). To split the finished proposal into tickets, use [to-tickets](https://aihero.dev/skills-to-tickets).

## Prerequisites

`to-spec` writes OpenSpec artifacts, so the repo needs `openspec/` initialized — run `openspec init` if it isn't. There is no fallback: if the spec layer has no home, the skill stops rather than publishing the spec somewhere it doesn't belong.

The instructions `openspec init` generates own the artifact format, not this skill. `to-spec` reads them, and reads `openspec/specs/` — the record of what the system does today — before writing the delta.

## The delta, not the world

The proposal describes a **change**, measured against `openspec/specs/`. That's what makes the layer work: specs absorb the churn, so nothing else has to.

Four artifacts, four jobs:

- **`proposal.md`** — the problem, the solution in your own terms, and what's explicitly out of scope.
- **`specs/<capability>/spec.md`** — every behaviour the change adds, modifies or removes, as delta requirements with concrete scenarios. Be extensive here; this list *is* the spec.
- **`design.md`** — implementation decisions, the seams, rejected alternatives. It archives with the change, which is the right home for almost all rationale.
- **`tasks.md`** — left empty. [to-tickets](https://aihero.dev/skills-to-tickets) fills it.

A decision graduates from `design.md` to an ADR under `docs/adr/` only when the trade-off outlives the change — hard to reverse, surprising without context, a real trade-off. Most changes promote nothing. Requirements never become ADRs.

## Deep modules

Before writing, `to-spec` sketches the **seams** at which the feature will be tested and looks for **deep module** opportunities — a lot of functionality hidden behind a small, stable interface. It prefers existing seams to new ones and the highest seam possible, ideally just one across the whole change.

That matters for agentic development: a good interface gives tests something durable to target, so the code underneath can change without the tests moving.

## It's working if

- It starts writing the proposal instead of asking you a fresh round of questions.
- It checks the seams with you before writing, and proposes as few as possible.
- The change validates against the OpenSpec toolchain, and nothing lands in your issue tracker.
- The spec comes back in your project's domain vocabulary, not generic boilerplate.
- Approved scope stays fixed: new scope found later goes back through propose, not silently into the change.

## Where it fits

`to-spec` is a step in the main build chain:

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review
```

Reach for it after the plan and domain language are resolved, and before you break the work into implementation tickets. Its key neighbours are [grill-with-docs](https://aihero.dev/skills-grill-with-docs), which sharpens the context so the spec is precise, and [to-tickets](https://aihero.dev/skills-to-tickets), which turns the proposal into the change's `tasks.md` for [implement](https://aihero.dev/skills-implement) to build. [domain-modeling](https://aihero.dev/skills-domain-modeling) owns the layer boundary this skill writes against. When you're unsure which skill or flow fits, [ask-matt](https://aihero.dev/skills-ask-matt) routes you.
