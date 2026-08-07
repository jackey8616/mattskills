---
name: change-review
description: Review a finished OpenSpec change against its own delta specs before archiving it — every requirement built and tested, nothing shipped that wasn't proposed.
disable-model-invocation: true
---

# Change Review

The gate before a change is archived. Every ticket has merged and every box in `tasks.md` is ticked; this asks the one question no per-ticket review could answer: **did the change, as a whole, deliver what it proposed — and only what it proposed?**

`/code-review` reviews a diff against a ticket. This reviews a **change** against its delta specs. Tickets pass one at a time and still add up to a change with a requirement nobody built, a scenario nobody tested, or three features nobody proposed.

## Precondition

Run this when every task in `openspec/changes/<change-id>/tasks.md` is ticked and its issue closed. If tasks remain open, say which, and stop — this is a gate, not a progress report.

## Process

### 1. Assemble the record

Read the change in full: `proposal.md`, every delta spec under `specs/`, `design.md`, and `tasks.md`. Then get the whole diff the change produced — the merge commits of every ticket, from the base the proposal merged at to `HEAD`.

Read `openspec/specs/` too. The delta describes what should now be different; the current specs describe what was true before.

### 2. Review on two axes

Run these as **parallel sub-agents**, one per axis, so neither pollutes the other. Each gets the record from step 1 and reports findings only — no fixes.

<coverage-axis>

**Coverage** — is every proposed thing actually there?

Walk the delta specs requirement by requirement, and within each, scenario by scenario. For each one, find:

- the code that implements it, and
- a test that exercises it — one that would fail if the behaviour were removed

Report every requirement or scenario missing either. A scenario with no test is the most common finding here: the behaviour works, nothing holds it in place, and the next change silently breaks it.

</coverage-axis>

<fidelity-axis>

**Fidelity** — is anything there that shouldn't be?

Walk the diff and trace each meaningful behaviour change back to a requirement in the delta specs. Report:

- **Scope drift** — behaviour shipped that no requirement asked for. It was decided during implementation, which is exactly where scope isn't supposed to be decided.
- **Abandoned decisions** — `design.md` says one thing and the code does another, with no record of the change of mind.
- **Silent requirement changes** — a requirement implemented differently from how its scenarios describe it.

Refactors, test helpers and plumbing that serve a traced requirement are not drift. Drift is new *behaviour*.

</fidelity-axis>

### 3. Report and decide

Present the findings grouped by axis, most severe first, and put one of three verdicts to the user:

- **Ready to archive** — both axes clean.
- **Fix first** — gaps that belong to this change. Usually a missing test, sometimes a missed scenario. Fix them, then re-run this review.
- **Back to propose** — the change grew scope that should have been proposed. Amend the change or open a new one; don't retroactively bless it by editing the delta specs to match what got built.

Editing a delta spec to match the code is the failure mode this gate exists to catch. The spec is what was agreed; the code is what happened. Where they disagree, the code is the thing that moved.

### 4. Promote anything that outlived the change

Before archiving, re-read `design.md`. Rationale that turned out to constrain work beyond this change should be promoted to an ADR under `docs/adr/` — but only if it clears the gate in `/domain-modeling`'s `ADR-FORMAT.md`: hard to reverse, surprising without context, the result of a real trade-off. Link from `design.md` to the ADR rather than duplicating the prose.

Most changes promote nothing. That's the expected outcome, not a sign the review was shallow.

### 5. Archive

On a **Ready to archive** verdict, archive the change with the OpenSpec toolchain (`openspec archive <change-id>`, or whatever the generated instructions specify), which merges the deltas into `openspec/specs/`.

Then confirm the merge landed: `openspec/specs/` should now describe the behaviour the delta added, and `openspec validate --strict` should pass against it. The specs are the source of truth for what the system does from here on — a bad merge quietly corrupts every change that follows.
