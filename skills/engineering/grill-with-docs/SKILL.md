---
name: grill-with-docs
description: A relentless interview to sharpen a plan or design, which also creates docs (ADR's and glossary) as we go.
disable-model-invocation: true
---

# Grill With Docs

Run a `/grilling` session, using the `/domain-modeling` skill.

## Load both, and check

This file is a delegation: `/grilling` supplies the interview, `/domain-modeling` supplies the writing. Confirm both actually loaded before the first round. A skill that names another skill does not reliably pull it in, and the usual failure is `grilling` arriving while `domain-modeling` silently doesn't — you get a good interview that writes nothing down. If either is missing, load it by name now.

The rules below have to hold whether or not `domain-modeling` arrived. They condense its [DOC-LAYERS.md](../domain-modeling/DOC-LAYERS.md) and [ADR-FORMAT.md](../domain-modeling/ADR-FORMAT.md) — read those when it did.

## Where each thing goes

| The sentence you're writing                                          | Where it goes                                      |
| -------------------------------------------------------------------- | -------------------------------------------------- |
| "In this project, the word *X* means…"                                | `CONTEXT.md`, immediately                           |
| "We chose A over B, and undoing it would be expensive"                | A new ADR — only if it clears the gate below        |
| "We chose A over B, for reasons that stop mattering once this ships"  | The change's `design.md`, at the close              |
| "The system does X when Y"                                            | **Not here.** That's the spec layer; `/to-spec` writes it |

**Requirements and behaviour never land in `CONTEXT.md` or in an ADR.** If you catch yourself writing "the system must…", it belongs to the change, not to these files. An ADR that has absorbed a requirement will be read as a decision forever, and nobody can tell later which of its sentences still describe the system.

## What the session may write

- **`CONTEXT.md`, freely** — it's a living glossary, and terms get written the moment they resolve rather than batched to the end.
- **A new ADR**, when the decision is **hard to reverse**, **surprising without context**, *and* **the result of a real trade-off**. All three, or no ADR. Write it as numbered decision points, 1-3 sentences each; most have exactly one. Declining is the normal outcome — most sessions write none, and you don't mention the one you decided against.
- **An existing ADR's frontmatter** — `superseded` for a single decision point, `status` for the whole document.

That is the whole set. You may **never** edit the title, the context line, or any decision point of an accepted ADR. If the grilling reverses a past decision, write a new ADR and mark the old one's frontmatter — the old reasoning is the record of what we believed at the time, and rewriting it destroys the thing the ADR exists for. Behaviour drifting away from what an ADR describes is not a reversal; that's a spec update.

## Closing the session

The interview is finished when the frontier is empty. Then, and not before — writing any of this earlier anchors the rounds you haven't run yet:

1. **Scaffold the change.** Prefer the repo's own OpenSpec proposal command where one exists; otherwise follow the agent instructions `openspec init` generated. The toolchain owns the folder format, not this skill.
2. **Write the Proposal** (`proposal.md`) — the problem, the solution in the user's own words, and what is explicitly out of scope. Not requirements, not scenarios: those are the delta, and `/to-spec` writes them.
3. **Write the trade-offs into `design.md`** — what was rejected, and why. `/to-spec` appends the technical half later (the seams, the testing approach), so leave the file open rather than closing it off.

If the repo has no `openspec/`, say so and stop at the shared understanding. It stays in the conversation, which is where it lived before this step existed. Don't invent another home for it, and don't push it into `CONTEXT.md` or an ADR to compensate.

## Where the output goes

`/to-spec` picks the work up from the Proposal — it does not interview you again. Because the Proposal is a file rather than conversation, the close of the grilling is a clean place to `/clear`.
