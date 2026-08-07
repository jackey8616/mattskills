---
name: to-tickets
description: Break an approved OpenSpec change into tracer-bullet slices, each declaring its blocking edges, written into that change's tasks.md — escalating to the issue tracker only for work that outlives the change.
disable-model-invocation: true
---

# To Tickets

Break a plan, spec, or conversation into a set of **tickets** — tracer-bullet vertical slices, each declaring the tickets that **block** it.

The default home for them is the active change's `tasks.md`. `tasks.md` is intra-change, session-scope work: everything needed to land *this* change and nothing else. The issue tracker is for work that outlives it.

## Process

### 1. Gather context

Work from whatever is already in the conversation context. If the user passes a reference (a change id, a spec path, an issue number or URL) as an argument, fetch it and read it in full.

Identify the **active change** — the folder under `openspec/changes/` this work belongs to. Read its `proposal.md`, its delta specs, and its `design.md` if it has one; the delta specs are what you're slicing against. If there is no active change, the work hasn't been proposed yet — send the user to `/to-spec` first rather than inventing scope here.

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

Every slice must trace back to a requirement in the change's delta specs. A slice you can't trace is scope drift: it belongs back at the propose stage, not in this list.

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

Iterate until the user approves the breakdown.

### 5. Write the approved tickets into `tasks.md`

Write them to the active change's `tasks.md`, in dependency order — blockers first — so working the list top to bottom is always legal. Follow the checklist format in the repo's generated OpenSpec agent instructions; where a blocking edge isn't implied by the ordering, name it inline on the ticket.

<ticket-template>

- [ ] **<NN> — <Ticket title>**
      **What to build:** the end-to-end behaviour this ticket makes work, from the user's perspective — not a layer-by-layer implementation list.
      **Blocked by:** the numbers/titles of the tickets that gate this one, or omitted entirely when nothing gates it.
      **Done when:** the acceptance criteria, one line each.

</ticket-template>

Then validate the change against the OpenSpec toolchain (`openspec validate <change-id> --strict`, or whatever the generated instructions specify).

Work the **frontier**: any ticket whose blockers are all done. For a purely linear chain that means top to bottom. `/implement` builds them one per fresh context window, clearing between each.

### 6. Escalate to the issue tracker — only when the work outlives the change

Some work surfaces here that `tasks.md` can't hold, because `tasks.md` archives when the change does. That work goes to the configured issue tracker instead:

- Work that **spans sessions beyond this change** — a follow-up nobody will pick up during this build
- Work that **belongs to another repo**
- Work that is **out of scope for this change** but shouldn't be lost

The issue tracker and triage label vocabulary should have been provided to you — run `/setup-matt-pocock-skills` if not. Publish one issue per item, use the platform's native blocking / sub-issue relationship where it has one, and apply the `ready-for-agent` triage label unless instructed otherwise.

Escalate deliberately, not by default. A ticket that lands this change belongs in `tasks.md`; duplicating it onto the tracker gives the change two sources of truth. Do NOT close or modify any parent issue.

In either form, avoid specific file paths or code snippets — they go stale fast. Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it and note briefly that it came from a prototype. Trim to the decision-rich parts — not a working demo, just the important bits.
