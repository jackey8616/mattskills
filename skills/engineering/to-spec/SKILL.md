---
name: to-spec
description: Turn the current conversation into an OpenSpec change proposal — no interview, just synthesis of what you've already discussed.
disable-model-invocation: true
---

This skill takes the current conversation context and codebase understanding and produces an **OpenSpec change** under `openspec/changes/<change-id>/`. Do NOT interview the user — just synthesize what you already know.

The output does **not** go to the issue tracker. Specs live in OpenSpec; the tracker holds cross-session work only.

## Precondition

This skill writes OpenSpec artifacts, so `openspec/` must be initialized in the repo. If it isn't, stop and tell the user to run `openspec init` — do not fall back to publishing the spec somewhere else.

Before writing, read the agent instructions `openspec init` generated (`openspec/AGENTS.md` or equivalent). **They own the exact artifact format, not this skill** — follow them over anything written here about file shape. Read `openspec/specs/` too: it is the source of truth for what the system does *today*, and the delta you're about to write is measured against it.

## Process

1. Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain glossary vocabulary throughout, and respect any ADRs in the area you're touching.

2. Sketch out the seams at which you're going to test the feature. Existing seams should be preferred to new ones. Use the highest seam possible. If new seams are needed, propose them at the highest point you can. The fewer seams across the codebase, the better - the ideal number is one.

Check with the user that these seams match their expectations.

3. Create the change. Prefer the repo's OpenSpec proposal command (`/opsx:propose`) where it exists — it scaffolds the change folder in the format the toolchain validates. Otherwise write the folder by hand, following the generated agent instructions.

Then place what you've synthesized:

<what-goes-where>

| What you have | Where it goes |
| --- | --- |
| Problem statement, the solution in the user's terms, what's explicitly out of scope | `proposal.md` |
| Every behaviour the change adds, modifies or removes — as delta requirements with scenarios | `specs/<capability>/spec.md` |
| Implementation decisions, the seams and testing approach, rejected alternatives, anything else worth carrying forward | `design.md` |
| The implementation checklist | `tasks.md` — leave it to `/to-tickets`, don't guess at it here |

</what-goes-where>

Be extensive in the delta specs. Every user story you would have written belongs there as a requirement with concrete scenarios — that list is the spec, and it should cover all aspects of the feature.

Do NOT include specific file paths or code snippets. They may end up being outdated very quickly.

Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it in `design.md` within the relevant decision and note briefly that it came from a prototype. Trim to the decision-rich parts — not a working demo, just the important bits.

4. Validate the change against the OpenSpec toolchain (`openspec validate <change-id> --strict`, or whatever the generated instructions specify) before handing back.

## Rationale that outlives the change

`design.md` archives with the change. That's the right home for almost all of it.

Promote a decision to an ADR under `docs/adr/` only when the trade-off is **hard to reverse**, **surprising without context**, and **the result of a real trade-off** — the gate in `/domain-modeling`'s `ADR-FORMAT.md`. When you promote one, link to it from `design.md` instead of repeating the prose. Most changes promote nothing.

Never write a requirement into an ADR. Behaviour belongs to the spec layer, and only there.

## After approval

Once the user approves the proposal, its scope is fixed. If new scope surfaces during implementation, it goes back through the propose stage — amend the change, or open a new one — and gets approved again. Don't absorb it silently into an approved change.
