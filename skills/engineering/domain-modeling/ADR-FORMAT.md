# ADR Format

ADRs live in `docs/adr/` and use sequential numbering: `0001-slug.md`, `0002-slug.md`, etc.

Create the `docs/adr/` directory lazily — only when the first ADR is needed.

## Template

```md
# {Short title of the decision}

{1-3 sentences: what's the context, what did we decide, and why.}
```

That's it. An ADR can be a single paragraph. The value is in recording _that_ a decision was made and _why_ — not in filling out sections.

**The 1-3 sentence body is a hard limit, not a target.** If the decision won't fit, that is a signal, not a licence to write more: you are either recording behaviour (which belongs in a spec), bundling several decisions (split them into several ADRs), or drafting a design document (which belongs in the change's `design.md`). Write the ADR to the limit and put the rest where it goes.

Do not add sections that the decision has not earned. An ADR that arrives as a filled-in template — every heading present, most of them thin — is the failure mode this format exists to prevent.

## Optional sections

Only include these when they add genuine value. Most ADRs won't need them.

- **Considered Options** — only when the rejected alternatives are worth remembering
- **Consequences** — only when non-obvious downstream effects need to be called out

Each one you add must carry information the body could not. "We considered doing nothing" is not a considered option.

## Status

`Status` is the one part of an ADR that is meant to change. It goes in YAML frontmatter, above the title, so that changing it produces a diff that touches nothing else:

```md
---
status: superseded by ADR-0007
---

# {Short title of the decision}
```

One of: `proposed | accepted | deprecated | superseded by ADR-NNNN`.

It is optional on a new ADR — an ADR with no `status` is accepted — and becomes load-bearing the moment a decision is revisited. Add the frontmatter at that point rather than seeding every ADR with `status: accepted`.

## Immutability

**An accepted ADR is frozen except for its `Status` field.** Never edit the title, body, or sections of an ADR that has been accepted — not to correct it, not to bring it up to date, not to trim it to this format.

To reverse or replace a decision:

1. Write a **new** ADR with the next number, recording the new decision and, in its 1-3 sentences, why the old one no longer holds.
2. Set the old ADR's `Status` to `superseded by ADR-NNNN`. Change nothing else in it.

An ADR is a record of what we believed and why, at the time we decided it. Rewriting it destroys exactly the thing it exists to preserve: the reader six months from now cannot tell whether the reasoning was sound at the time or has been quietly retrofitted. A superseded ADR that reads as wrong today is doing its job.

The same applies to the churn of ordinary work. When the system's behaviour drifts away from what an ADR describes, the ADR is not stale — `openspec/specs/` is the record of current behaviour, and the ADR is the record of a decision. Only a decision that has actually been reversed needs a new ADR. See [DOC-LAYERS.md](./DOC-LAYERS.md).

## Numbering

Scan `docs/adr/` for the highest existing number and increment by one. Numbers are never reused, even for an ADR that has been superseded.

## When to offer an ADR

All three of these must be true:

1. **Hard to reverse** — the cost of changing your mind later is meaningful
2. **Surprising without context** — a future reader will look at the code and wonder "why on earth did they do it this way?"
3. **The result of a real trade-off** — there were genuine alternatives and you picked one for specific reasons

If a decision is easy to reverse, skip it — you'll just reverse it. If it's not surprising, nobody will wonder why. If there was no real alternative, there's nothing to record beyond "we did the obvious thing."

When a decision fails the gate, do not offer an ADR at all — not as an option, not as a "we could also record this". Declining is the normal outcome; most sessions produce no ADR. The rationale still gets written down, in the active change's `design.md`, where it archives with the change.

### What qualifies

- **Architectural shape.** "We're using a monorepo." "The write model is event-sourced, the read model is projected into Postgres."
- **Integration patterns between contexts.** "Ordering and Billing communicate via domain events, not synchronous HTTP."
- **Technology choices that carry lock-in.** Database, message bus, auth provider, deployment target. Not every library — just the ones that would take a quarter to swap out.
- **Boundary and scope decisions.** "Customer data is owned by the Customer context; other contexts reference it by ID only." The explicit no-s are as valuable as the yes-s.
- **Deliberate deviations from the obvious path.** "We're using manual SQL instead of an ORM because X." Anything where a reasonable reader would assume the opposite. These stop the next engineer from "fixing" something that was deliberate.
- **Constraints not visible in the code.** "We can't use AWS because of compliance requirements." "Response times must be under 200ms because of the partner API contract."
- **Rejected alternatives when the rejection is non-obvious.** If you considered GraphQL and picked REST for subtle reasons, record it — otherwise someone will suggest GraphQL again in six months.

### What does not

- **Requirements and behaviour.** "Orders can be partially cancelled" is a spec, not a decision. It belongs in a delta spec under the active change. An ADR that has absorbed a requirement will be read as a decision forever.
- **Anything a library swap would undo.** Picking a date formatter, a test runner, an assertion library. Reversible in an afternoon — fails gate 1.
- **Rationale that only matters for this change.** It goes in the change's `design.md` and archives with it.
