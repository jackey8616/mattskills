---
name: to-tickets
description: Break a merged OpenSpec change into tracer-bullet tickets — written into the change's tasks.md and published one-to-one as issues on the tracker, each declaring its blocking edges.
disable-model-invocation: true
---

# To Tickets

Break a merged OpenSpec change into **tickets** — tracer-bullet vertical slices, each declaring the tickets that **block** it.

Each ticket lands in two places, with one of them in charge:

- **`tasks.md` in the change folder is the source of truth.** It archives with the change, so it stays the readable record of what the change actually took.
- **One issue per task** is the execution surface. Each ticket is built in its own fresh session, so it needs a work order that outlives any one context window.

They stay one-to-one. `tasks.md` is written first and the issues are cut from it — never the other way round.

## Precondition

Run this **after the change proposal has merged**, not before. The proposal is what fixes the scope; slicing an unmerged proposal produces tickets for work that may still change under you.

If the change isn't merged yet, say so and stop. If there's no change at all, the work hasn't been proposed — send the user to `/to-spec`.

The issue tracker and triage label vocabulary should have been provided to you — run `/setup-matt-pocock-skills` if not.

## Process

### 1. Gather context

Read the merged change under `openspec/changes/<change-id>/` in full: `proposal.md`, every delta spec under `specs/`, and `design.md` if it has one. The **delta specs are what you slice against** — their requirements and scenarios are the work.

If `opsx` or the user already scaffolded a `tasks.md`, read it as a draft, not as the answer. You are about to reconcile it against the slicing rules below.

### 2. Explore the codebase (optional)

If you have not already explored the codebase, do so to understand the current state of the code. Ticket titles and descriptions should use the project's domain glossary vocabulary, and respect ADRs in the area you're touching.

Look for opportunities to prefactor the code to make the implementation easier. "Make the change easy, then make the easy change."

### 3. Draft vertical slices

Break the work into **tracer bullet** tickets.

<vertical-slice-rules>

- Each slice cuts a narrow but COMPLETE path through every layer (schema, API, UI, tests) — vertical, NOT a horizontal slice of one layer
- A completed slice is demoable or verifiable on its own
- Each slice is sized to fit in a single fresh context window
- Any prefactoring should be done first

</vertical-slice-rules>

Give each ticket its **blocking edges** — the other tickets that must complete before it can start. A ticket with no blockers can start immediately.

Every slice must trace back to a requirement in the delta specs, and every requirement must be covered by some slice. A slice you can't trace is scope drift — it goes back through the propose stage, not into this list. A requirement no slice covers means the breakdown is incomplete.

**Wide refactors are the exception to vertical slicing.** A **wide refactor** is one mechanical change — rename a column, retype a shared symbol — whose **blast radius** fans across the whole codebase, so a single edit breaks thousands of call sites at once and no vertical slice can land green. Don't force it into a tracer bullet; sequence it as **expand–contract**. First expand: add the new form beside the old so nothing breaks. Then migrate the call sites over in batches sized by blast radius (per package, per directory), each batch its own ticket blocked by the expand, keeping CI green batch to batch because the old form still exists. Finally contract: delete the old form once no caller remains, in a ticket blocked by every migrate batch. When even the batches can't stay green alone, keep the sequence but let them share an integration branch that all block a final integrate-and-verify ticket — green is promised only there.

### 4. Quiz the user

Present the proposed breakdown as a numbered list. For each ticket, show:

- **Title**: short descriptive name
- **Blocked by**: which other tickets (if any) must complete first
- **What it delivers**: the end-to-end behaviour this ticket makes work

Ask the user:

- Does the granularity feel right? (too coarse / too fine)
- Are the blocking edges correct — does each ticket only depend on tickets that genuinely gate it?
- Should any tickets be merged or split further?

Iterate until the user approves the breakdown. Nothing is written or published before approval.

### 5. Write `tasks.md`

Write the approved tickets into the change's `tasks.md`, numbered from `1` in dependency order — blockers first — so working the list top to bottom is always legal. Follow the checklist format in the repo's generated OpenSpec agent instructions.

Keep each entry short. `tasks.md` carries the **list**; the issue carries the **brief**.

<tasks-template>

- [ ] 1. <Ticket title> — <one line: the behaviour this makes work> (#<issue>)
- [ ] 2. <Ticket title> — <one line> (#<issue>, blocked by 1)

</tasks-template>

Then validate the change against the OpenSpec toolchain (`openspec validate <change-id> --strict`, or whatever the generated instructions specify).

### 6. Publish one issue per ticket

**On a local-markdown tracker, skip this step.** `tasks.md` is already a local markdown checklist in the repo — a second set of files under `.scratch/` would be pure duplication, and duplication is what the source-of-truth rule exists to prevent. Say that you're stopping at `tasks.md` and move on.

**On a real tracker (GitHub, GitLab, Linear, …)**, publish in dependency order — blockers first — so each ticket's blocking edges can reference real identifiers. Use the platform's native blocking / sub-issue relationship where it has one; otherwise write the "Blocked by" section. Apply the `ready-for-agent` triage label unless instructed otherwise — the tickets are agent-grabbable by construction.

Write the issue numbers back into `tasks.md` as you go, so the two stay linked in both directions.

<issue-template>

## Change

`<change-id>` — task <N> of its `tasks.md`. Read `openspec/changes/<change-id>/` for the full proposal and delta specs before starting.

## What to build

The end-to-end behaviour this ticket makes work, from the user's perspective — not layer-by-layer implementation.

## Requirements covered

The delta spec requirements and scenarios this ticket satisfies, by name.

## Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2

## Blocked by

- A reference to each blocking issue, or "None — can start immediately".

</issue-template>

Do NOT close or modify any parent issue.

Avoid specific file paths or code snippets — they go stale fast. Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it and note briefly that it came from a prototype. Trim to the decision-rich parts — not a working demo, just the important bits.

## Working the tickets

Work the **frontier**: any ticket whose blockers are all done. For a purely linear chain that means top to bottom. Each ticket gets its **own fresh session** — `/implement` builds it, closes out with `/code-review`, and commits the ticket's tick in `tasks.md` alongside the work. Then it merges.

**The merge is what closes a ticket out**: the tick lands in `tasks.md` and the `Closes #` trailer closes the issue, both at the moment the code ships. Until then the issue stays open, because nothing has shipped. After each merge, confirm the box really is ticked — `/change-review` reads those boxes to decide the change is ready to archive, so a tick that never landed stalls the change with all its work already shipped.

Once every box is ticked, run `/change-review` — the gate before archiving the change.

## Work that outlives the change

Some work surfaces during slicing that this change shouldn't carry: a follow-up nobody will pick up during this build, work belonging to another repo, scope deliberately deferred. That goes to the tracker as a **standalone issue** — not into `tasks.md`, and not linked as a task of this change.

Keep the two kinds distinct. A ticket of this change is one that must land for the change to archive; anything else is just an issue that happens to have been noticed here.
