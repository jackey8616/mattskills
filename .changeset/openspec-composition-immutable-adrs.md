---
"mattpocock-skills": minor
---

Compose the engineering flow with OpenSpec, and make ADRs immutable.

Three document layers now have one owner each: `openspec/specs/` holds current behaviour, `docs/adr/` holds decisions, `CONTEXT.md` holds language. Because the spec layer absorbs the churn, ADRs no longer have to — so they're frozen once accepted.

- `domain-modeling` gains `DOC-LAYERS.md`, the routing rule for which document a piece of writing belongs in.
- `ADR-FORMAT.md` makes the 1-3 sentence body a hard limit, and adds the immutability rule: an accepted ADR changes only its `Status`. Reversing a decision means a new superseding ADR, never an edit.
- `domain-modeling` and `grill-with-docs` no longer update ADRs inline — they may create a new ADR or change a `Status`, and nothing else.
- `to-spec` produces an OpenSpec change under `openspec/changes/<id>/` instead of publishing a spec to the issue tracker.
- `to-tickets` writes tracer-bullet tickets into the active change's `tasks.md`, escalating to the issue tracker only for work that outlives the change.
