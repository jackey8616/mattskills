Quickstart:

```bash
npx skills add mattpocock/skills --skill=to-tickets
```

```bash
npx skills update to-tickets
```

[Source](https://github.com/mattpocock/skills/tree/main/skills/engineering/to-tickets)

## What it does

`to-tickets` breaks a **merged** OpenSpec change into a set of **tickets** — each a tracer-bullet vertical slice — written into the change's `tasks.md` and published one-to-one as issues, with every ticket declaring the tickets that block it.

Every ticket is a **tracer bullet** — a thin *vertical* slice that cuts through all integration layers end-to-end (schema, API, UI, tests), never a horizontal slice of one layer. A completed slice is demoable or verifiable on its own, which is what makes each ticket safe to hand to an agent in a session of its own.

## When to reach for it

You invoke this by typing `/to-tickets` — the agent won't reach for it on its own.

Reach for it right after a change proposal merges, when you want it split into buildable slices. Pass a change id and it reads the proposal and delta specs first. If the work hasn't been proposed yet, propose it first — for that, use [to-spec](https://aihero.dev/skills-to-spec).

## Prerequisites

The change must be **merged**, not just written. The merged proposal is what fixes the scope; slicing an unmerged one produces tickets for work that may still move under you. `to-tickets` slices against the change's delta specs and will send you back to [to-spec](https://aihero.dev/skills-to-spec) rather than invent scope of its own.

It publishes issues, so [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) must have configured the tracker and its triage label vocabulary for this repo first. It applies the ready-for-agent label as it publishes.

## Two places, one in charge

Each ticket lands twice, and knowing which copy is authoritative is the whole trick:

- **`tasks.md` is the source of truth.** It archives with the change, so it stays the readable record of what the change actually took, long after the issues are closed. Its ticked boxes are what [change-review](https://aihero.dev/skills-change-review) reads to decide the change is done.
- **The issue is the execution surface.** Each ticket is built in its own fresh session, so it needs a work order that outlives any one context window — with the change id, the requirements it covers, and its blocking edges as native links.

They stay one-to-one, and the direction is fixed: `tasks.md` is written first, issues are cut from it, issue numbers get written back. When a ticket's PR merges you tick its box *and* close its issue — an issue closed without the tick leaves the change looking unfinished.

Work that *doesn't* belong to the change — a follow-up, another repo's problem, scope deliberately deferred — becomes a standalone issue instead, never a task. A ticket of this change is one that must land for the change to archive; everything else is just an issue that happened to be noticed here.

Every slice must trace back to a requirement in the delta specs, and every requirement must be covered by some slice. A slice you can't trace is scope drift and goes back through propose; a requirement no slice covers means the breakdown isn't finished.

Work the **frontier**: any ticket whose blockers are all done. For a purely linear chain that means top to bottom.

## Vertical slices, not horizontal ones

The whole skill turns on one distinction. A **horizontal** slice ships one layer of the change — all the schema, or all the API — and nothing works until every layer lands. A **vertical** slice, the tracer bullet, ships one narrow path through *every* layer at once, so it can be demoed the moment it's done.

Before slicing, `to-tickets` looks for prefactoring — "make the change easy, then make the easy change" — and orders that work first. It then quizzes you on the breakdown (granularity, blocking edges, what to merge or split) before writing anything.

## The wide-refactor exception

One shape breaks the tracer-bullet rule: a **wide refactor** — a single mechanical change (rename a column, retype a shared symbol) whose **blast radius** fans across the whole codebase, so one edit breaks thousands of call sites at once and no vertical slice can land green. `to-tickets` slices it as **expand–contract** instead: expand (add the new form beside the old so nothing breaks), migrate (move call sites over in batches sized by blast radius, one ticket per batch, CI green throughout because the old form still exists), then contract (delete the old form once no caller remains). When even the batches can't stay green alone, they share an integration branch that all block a final integrate-and-verify ticket, and green is promised only there.

## Where it fits

`to-tickets` is a step in the main build chain:

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review → change-review
```

It sits between [to-spec](https://aihero.dev/skills-to-spec), which hands it a merged change with delta specs to slice against, and [implement](https://aihero.dev/skills-implement), which builds each ticket, driving [tdd](https://aihero.dev/skills-tdd) internally to write the tests test-first, before its [code-review](https://aihero.dev/skills-code-review) pass. Work the frontier one ticket per fresh session. Once every box it wrote is ticked, [change-review](https://aihero.dev/skills-change-review) is the gate that archives the change. When you're unsure which skill or flow fits, [ask-matt](https://aihero.dev/skills-ask-matt) routes you.
