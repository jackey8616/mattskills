---
"mattpocock-skills": patch
---

Teach `setup-matt-pocock-skills` about the spec layer.

Setup configured an issue tracker, triage labels and domain docs, but knew nothing about OpenSpec — so a freshly set-up repo would run `/to-spec` and immediately be told to go run `openspec init`. It now detects `openspec/` during exploration and offers to initialize it (Section D), skipping the question entirely when it's already there.

Two seed templates were out of step with the three-layer contract:

- `domain.md` — the consumer rules every soft-dependency skill reads — described only `CONTEXT.md` and `docs/adr/`, with no mention of `openspec/specs/` and nothing about immutability. It now carries the layer table and the never-edit-an-accepted-ADR rule, so a repo's own config stops contradicting the skills.
- `issue-tracker-local.md` still pointed specs at `.scratch/<slug>/spec.md` and tickets at `.scratch/<slug>/issues/`. Those live in OpenSpec now; `.scratch/` holds incoming requests, work that outlives a change, and wayfinder maps.

`to-tickets` also regains its local-tracker path: on a local-markdown tracker it stops at `tasks.md` instead of publishing issues, since `tasks.md` is already a local markdown checklist and a second copy would be duplication.
