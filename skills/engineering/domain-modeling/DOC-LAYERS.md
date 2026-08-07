# Document layers

Three layers, three different lifetimes. Every piece of writing belongs to exactly one — putting it in the wrong layer is what makes docs rot.

| Layer         | Path              | Mutability                 | Holds                                  |
| ------------- | ----------------- | -------------------------- | -------------------------------------- |
| **Behaviour** | `openspec/specs/` | Mutable — merged on archive | What the system currently does          |
| **Decisions** | `docs/adr/`       | Immutable once accepted    | Why we chose what we chose             |
| **Language**  | `CONTEXT.md`      | Living                     | Glossary only — no implementation detail |

Because `openspec/specs/` owns "current state", the other two layers no longer have to absorb churn. That is what lets ADRs stay frozen and `CONTEXT.md` stay a glossary.

## Routing rules

**Requirements and behaviour go into OpenSpec artifacts only.** Never restate a requirement in an ADR or in `CONTEXT.md`. If you catch yourself writing "the system must…", it belongs in a delta spec under the active change, not here.

**Change-scoped rationale lives in that change's `design.md`**, and archives with the change. Promote it to an ADR only when the trade-off outlives the change — when it will still constrain work six months from now. When you do promote it, link from `design.md` to the ADR rather than duplicating the prose.

**`CONTEXT.md` is a glossary and nothing else.** Not a spec, not a scratch pad, not a decision log.

**An accepted ADR is frozen.** See [ADR-FORMAT.md](./ADR-FORMAT.md) — the only field that may change is `Status`.

## When there is no `openspec/`

If the repo has no `openspec/` directory, the Behaviour layer has no home. Say so, and offer to run `openspec init`. Do not spill behaviour into the other two layers to compensate — an ADR that has absorbed a requirement is worse than a missing spec, because it will be read as a decision forever.
