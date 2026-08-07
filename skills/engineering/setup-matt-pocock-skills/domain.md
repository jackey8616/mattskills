# Domain Docs

How the engineering skills should consume this repo's domain documentation when exploring the codebase.

## Three layers, three lifetimes

Every piece of writing belongs to exactly one layer. Putting it in the wrong one is what makes docs rot.

| Layer | Path | Mutability | Holds |
| --- | --- | --- | --- |
| Behaviour | `openspec/specs/` | mutable — merged when a change archives | What the system currently does |
| Decisions | `docs/adr/` | **immutable once accepted** | Why we chose what we chose |
| Language | `CONTEXT.md` | living | Glossary only — no implementation detail |

Because the spec layer absorbs the churn of ordinary work, the other two don't have to. Never write a requirement into an ADR or the glossary — an ADR that has absorbed a requirement will be read as a decision forever.

## Before exploring, read these

- **`CONTEXT.md`** at the repo root, or
- **`CONTEXT-MAP.md`** at the repo root if it exists — it points at one `CONTEXT.md` per context. Read each one relevant to the topic.
- **`openspec/specs/`** — the record of what the system does today. Read the capabilities relevant to the area you're about to work in.
- **`docs/adr/`** — read ADRs that touch the area you're about to work in. In multi-context repos, also check `src/<context>/docs/adr/` for context-scoped decisions.

If any of these files don't exist, **proceed silently**. Don't flag their absence; don't suggest creating them upfront. The `/domain-modeling` skill (reached via `/grill-with-docs` and `/improve-codebase-architecture`) creates them lazily when terms or decisions actually get resolved.

## Never edit an accepted ADR

An ADR is frozen once accepted. The only field that may change is its `status` frontmatter (`proposed | accepted | deprecated | superseded by ADR-NNNN`).

If a decision is genuinely being reversed, that is `/domain-modeling`'s job: a **new** ADR is written and the old one's `status` is set to `superseded by ADR-NNNN`. Never correct, update or trim an accepted ADR's content — the record of what was believed at the time is the thing it exists to preserve.

Behaviour drifting away from what an ADR describes is **not** a stale ADR. `openspec/specs/` is the record of current behaviour; the ADR is the record of a decision.

## File structure

Single-context repo (most repos):

```
/
├── CONTEXT.md
├── docs/adr/
│   ├── 0001-event-sourced-orders.md
│   └── 0002-postgres-for-write-model.md
└── src/
```

Multi-context repo (presence of `CONTEXT-MAP.md` at the root):

```
/
├── CONTEXT-MAP.md
├── docs/adr/                          ← system-wide decisions
└── src/
    ├── ordering/
    │   ├── CONTEXT.md
    │   └── docs/adr/                  ← context-specific decisions
    └── billing/
        ├── CONTEXT.md
        └── docs/adr/
```

## Use the glossary's vocabulary

When your output names a domain concept (in an issue title, a refactor proposal, a hypothesis, a test name), use the term as defined in `CONTEXT.md`. Don't drift to synonyms the glossary explicitly avoids.

If the concept you need isn't in the glossary yet, that's a signal — either you're inventing language the project doesn't use (reconsider) or there's a real gap (note it for `/domain-modeling`).

## Flag ADR conflicts

If your output contradicts an existing ADR, surface it explicitly rather than silently overriding:

> _Contradicts ADR-0007 (event-sourced orders) — but worth reopening because…_

Surfacing it is the whole job. Do not resolve the conflict by editing ADR-0007.
