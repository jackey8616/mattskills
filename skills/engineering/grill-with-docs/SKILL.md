---
name: grill-with-docs
description: A relentless interview to sharpen a plan or design, which also creates docs (ADR's and glossary) as we go.
disable-model-invocation: true
---

# Grill With Docs

Run a `/grilling` session, using the `/domain-modeling` skill.

## What the session may write

The grilling produces the alignment; `/domain-modeling` decides where it lands. Its `DOC-LAYERS.md` is the routing rule — behaviour to `openspec/specs/`, decisions to `docs/adr/`, language to `CONTEXT.md`.

Within a session you may:

- **Update `CONTEXT.md` freely** — it's a living glossary, and terms get written the moment they resolve.
- **Create a new ADR**, when the decision clears the three-part gate in `ADR-FORMAT.md`. 1-3 sentences.
- **Change an existing ADR's `Status` field** — to `deprecated`, or `superseded by ADR-NNNN`.

That is the whole set. You may **never** edit the content of an accepted ADR. If the grilling reverses a past decision, write a new ADR that supersedes it and set the old one's `Status` — the old reasoning is the record of what we believed at the time, and rewriting it destroys the thing the ADR exists for.

Requirements and behaviour never land in either file. They belong to the change proposal.

## Where the output goes

The shared understanding this produces is the input to `/to-spec`, which turns it into an OpenSpec change proposal. Don't write spec content during the grilling — finish the interview, reach the shared understanding, then hand off.
