---
"mattpocock-skills": minor
---

Publish tickets as issues, and gate archiving behind a change-level review.

`to-tickets` now runs *after* the change proposal merges, and writes each ticket twice: into the change's `tasks.md`, which is the source of truth and archives with the change, and as one issue per ticket, which is the execution surface a fresh session picks up. The two stay one-to-one — `tasks.md` first, issues cut from it, issue numbers written back — and a ticket's box is ticked when its PR merges.

`change-review` is new: the gate before archiving. It reviews the whole change on two axes — **Coverage** (every delta requirement built *and* held down by a test) and **Fidelity** (nothing shipped that nobody proposed) — and archives only on a clean verdict. Editing a delta spec to match what got built is explicitly the failure mode it exists to catch.
