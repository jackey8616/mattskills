Quickstart:

```bash
npx skills add mattpocock/skills --skill=to-tickets
```

```bash
npx skills update to-tickets
```

[Source](https://github.com/mattpocock/skills/tree/main/skills/engineering/to-tickets)

## What it does

`to-tickets` breaks an approved OpenSpec change into a set of **tickets** — each a tracer-bullet vertical slice — written into that change's `tasks.md`, with every ticket declaring the tickets that block it.

Every ticket is a **tracer bullet** — a thin *vertical* slice that cuts through all integration layers end-to-end (schema, API, UI, tests), never a horizontal slice of one layer. A completed slice is demoable or verifiable on its own, which is what makes each ticket safe to hand to an agent.

## When to reach for it

You invoke this by typing `/to-tickets` — the agent won't reach for it on its own.

Reach for it once a change has been proposed and approved, and you want it split into buildable slices. Point it at the conversation, or pass a change id and it reads the proposal and delta specs first. If the work hasn't been proposed yet, propose it first — for that, use [to-spec](https://aihero.dev/skills-to-spec).

## Prerequisites

There must be an **active change** under `openspec/changes/` to write into — `to-tickets` slices against its delta specs and will send you back to [to-spec](https://aihero.dev/skills-to-spec) rather than invent scope of its own.

For the escalation path only, it also needs an issue tracker: [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) configures that and its triage label vocabulary. A change whose work all fits inside itself never touches the tracker.

## Two homes, one rule

The rule is lifetime. `tasks.md` archives when the change does; the tracker doesn't.

- **`tasks.md`** — everything needed to land *this* change, and nothing else. Session-scope work, written blockers-first so top-to-bottom is always legal.
- **The issue tracker** — only work that outlives the change: follow-ups nobody will pick up during this build, work belonging to another repo, scope deliberately deferred.

Escalating by default is the failure mode: a ticket duplicated onto the tracker gives the change two sources of truth. Every slice must also trace back to a requirement in the delta specs — one that can't is scope drift, and it goes back through propose rather than into the list.

Work the **frontier**: any ticket whose blockers are all done. For a purely linear chain that means top to bottom.

## Vertical slices, not horizontal ones

The whole skill turns on one distinction. A **horizontal** slice ships one layer of the change — all the schema, or all the API — and nothing works until every layer lands. A **vertical** slice, the tracer bullet, ships one narrow path through *every* layer at once, so it can be demoed the moment it's done.

Before slicing, `to-tickets` looks for prefactoring — "make the change easy, then make the easy change" — and orders that work first. It then quizzes you on the breakdown (granularity, blocking edges, what to merge or split) before writing anything.

## The wide-refactor exception

One shape breaks the tracer-bullet rule: a **wide refactor** — a single mechanical change (rename a column, retype a shared symbol) whose **blast radius** fans across the whole codebase, so one edit breaks thousands of call sites at once and no vertical slice can land green. `to-tickets` slices it as **expand–contract** instead: expand (add the new form beside the old so nothing breaks), migrate (move call sites over in batches sized by blast radius, one ticket per batch, CI green throughout because the old form still exists), then contract (delete the old form once no caller remains). When even the batches can't stay green alone, they share an integration branch that all block a final integrate-and-verify ticket, and green is promised only there.

## Where it fits

`to-tickets` is a step in the main build chain:

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review
```

It sits between [to-spec](https://aihero.dev/skills-to-spec), which hands it an approved change with delta specs to slice against, and [implement](https://aihero.dev/skills-implement), which builds each ticket, driving [tdd](https://aihero.dev/skills-tdd) internally to write the tests test-first, before its [code-review](https://aihero.dev/skills-code-review) pass. Work the frontier one ticket per fresh context, clearing between them. When you're unsure which skill or flow fits, [ask-matt](https://aihero.dev/skills-ask-matt) routes you.
