Quickstart:

```bash
npx skills add mattpocock/skills --skill=change-review
```

```bash
npx skills update change-review
```

[Source](https://github.com/mattpocock/skills/tree/main/skills/engineering/change-review)

## What it does

`change-review` is the gate before an OpenSpec change is archived. Every ticket has merged and every box in `tasks.md` is ticked; this asks the one question none of those tickets could answer — did the change, as a whole, deliver what it proposed, and only what it proposed?

It reviews a **change**, not a diff. [code-review](https://aihero.dev/skills-code-review) checks one ticket's diff against that ticket. Tickets can pass one at a time and still add up to a change with a requirement nobody built, a scenario nobody tested, or three features nobody proposed — because nothing in a per-ticket review ever looks at the delta specs as a whole.

## When to reach for it

You invoke this by typing `/change-review` — the agent won't reach for it on its own.

Reach for it when every task in the change is ticked and its issue closed, and you're about to archive. If tasks are still open it tells you which and stops — it's a gate, not a progress report. For reviewing a single branch or PR against a fixed point, use [code-review](https://aihero.dev/skills-code-review) instead.

## Prerequisites

A finished OpenSpec change: `openspec/changes/<change-id>/` with its proposal, delta specs and a fully ticked `tasks.md`. It also needs the change's whole diff — every ticket's merge commits from the base the proposal merged at to `HEAD` — so run it somewhere that history is intact.

## Two axes

The review splits into two independent sub-agents so neither pollutes the other:

- **Coverage** — walk the delta specs requirement by requirement, scenario by scenario, and find the code that implements each one *and* a test that would fail if the behaviour were removed. The most common finding is a scenario with no test: the behaviour works, nothing holds it in place, and the next change breaks it silently.
- **Fidelity** — walk the diff and trace each behaviour change back to a requirement. What doesn't trace is **scope drift**: behaviour decided during implementation, which is exactly where scope isn't supposed to be decided. Refactors and plumbing serving a traced requirement aren't drift; new *behaviour* is.

## The verdict, and the one forbidden fix

Three outcomes: **ready to archive**, **fix first** (gaps belonging to this change — usually a missing test), or **back to propose** (the change grew scope that should have been proposed).

Editing a delta spec to match what got built is the failure mode this gate exists to catch. The spec is what was agreed; the code is what happened. Where they disagree, the code is the thing that moved — so it goes back through propose rather than getting retroactively blessed.

On a clean verdict it archives, merging the deltas into `openspec/specs/`, then confirms that merge landed. That merge is what makes the specs the source of truth for the next change, so a bad one quietly corrupts everything after it.

## It's working if

- It refuses to run while tasks are still open.
- It names specific requirements and scenarios, not general impressions of the diff.
- Untested-but-working scenarios get reported, not passed.
- Scope that appeared during implementation is sent back to propose rather than written into the specs.
- Most runs promote nothing to an ADR — the bar for that stays high.

## Where it fits

`change-review` is the last step of the main build chain, and the only one scoped to the whole change:

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review → change-review
```

Its neighbours are [code-review](https://aihero.dev/skills-code-review), which it complements rather than repeats — that one guards each ticket, this one guards the change — and [to-tickets](https://aihero.dev/skills-to-tickets), whose `tasks.md` checkboxes are the precondition it reads. It also reaches into [domain-modeling](https://aihero.dev/skills-domain-modeling) at the end, to decide whether any rationale in `design.md` outlived the change and earns an ADR. When you're unsure which skill or flow fits, [ask-matt](https://aihero.dev/skills-ask-matt) routes you.
