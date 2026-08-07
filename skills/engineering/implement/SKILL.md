---
name: implement
description: "Implement a piece of work based on a spec or set of tickets."
disable-model-invocation: true
---

Implement the work described by the user in the spec or tickets.

If the ticket names an OpenSpec change, read that change's `proposal.md` and delta specs first. Build the requirements **this ticket** covers — the rest of the change belongs to other tickets, and building ahead is how a tracer bullet stops being one.

Use /tdd where possible, at pre-agreed seams. Every scenario in the requirements you're covering needs a test that would fail if the behaviour were removed.

Run typechecking regularly, single test files regularly, and the full test suite once at the end.

Once done, use /code-review to review the work.

Commit your work to the current branch.

Then close the ticket out: **tick its box in the change's `tasks.md`** and close its issue. `/change-review` reads those ticks to decide the change is ready to archive, so a box nobody ticked stalls the whole change even though the work has shipped.

Scope stops at the ticket. If the work turns out to need something the delta specs don't cover, say so and stop — new scope goes back through the propose stage, not into this session.
