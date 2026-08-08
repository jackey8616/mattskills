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

Commit your work to the current branch. When the ticket belongs to an OpenSpec change, **tick its box in that change's `tasks.md` in the same commit** — the tick rides the work, so it lands exactly when the work lands. `/change-review` reads those ticks to decide the change is ready to archive, so a box nobody ticked stalls the whole change once the work ships.

Then hand off: push the branch, and open a PR if that's how this repo ships work. Where the ticket has an issue, put `Closes #<issue>` in the commit message and the PR body so the tracker closes it the moment the work reaches the default branch — and **leave the issue open until then**. Your session ends with the work on a branch — the merge ships it, and the merge is the reviewer's to make.

Scope stops at the ticket. If the work turns out to need something the delta specs don't cover, say so and stop — new scope goes back through the propose stage, not into this session.

A **capability wall** stops you the same way, for a different reason: the acceptance criteria are clear, but you can't satisfy them from here — no route to a required source, a console behind a login, hardware, a paid account. Record what the wall was on the issue, move its triage label to `ready-for-human`, and stop. Running the ticket is the only moment `ready-for-agent` can be tested, and you're the one running it — a wall nobody recorded is one the next fresh session rediscovers from scratch.

That label move is the tracker write worth making here: it records that you tried and were blocked, which happened. Leave the issue itself open — closing it would claim the work shipped, which didn't. And shipping a result *around* the wall — a third-party number behind a disclaimer, a stubbed value, a plausible guess — is not the fallback; that is what turns one blocked run into a ticket that keeps advertising itself as agent-ready.
