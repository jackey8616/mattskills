---
"mattpocock-skills": patch
---

Close the loop between `implement` and `change-review`.

`to-tickets` said to tick a ticket's box in `tasks.md` when its PR merges — but it says that at slicing time, and the ticket is built later in a fresh session that never reads `to-tickets`. Nobody ticked, and `change-review` is gated on those ticks, so every change stalled at the last step with all its work already shipped.

`implement` now owns closing the ticket out. It also reads the change's proposal and delta specs when the ticket names one, builds only the requirements that ticket covers, and stops rather than absorbing scope the delta specs don't cover.

`improve-codebase-architecture` now defers to `ADR-FORMAT.md` when it offers an ADR, and states that creating one is its only write to `docs/adr/` — including for the ADR a rejected candidate contradicts.
