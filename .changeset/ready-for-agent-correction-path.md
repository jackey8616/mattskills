---
"mattpocock-skills": patch
---

Give `ready-for-agent` a correction path. Three skills touched the label and none could fix it once it was wrong.

`to-tickets` applied it unconditionally — "the tickets are agent-grabbable by construction" — deriving delegability from the ticket being well specified. But `triage`'s own test says a fully specified ticket is still `ready-for-human` if it needs a judgment call, external access, a design decision, or manual testing. Spec completeness and delegability are different properties. `to-tickets` now runs that test as it publishes, and names the reason on anything it sends to `ready-for-human`.

`implement` had no label vocabulary at all — only a scope escalation. It now has a **capability wall** escalation beside it: when the acceptance criteria are clear but unreachable (no route to a source, a login it doesn't have, hardware, a paid account), it records the wall on the issue, moves the label to `ready-for-human`, and stops. Shipping a result *around* the wall — a third-party number behind a disclaimer, a stubbed value — is explicitly not the fallback, since that is what leaves a ticket permanently advertising itself as agent-ready. This is the load-bearing fix: running the ticket is the only moment the label can be falsified, and that moment belonged to the one skill with no way to record the result.

`triage` discovery gains a fourth bucket — `ready-for-agent` with activity since the label was applied — so a ticket someone commented on, or an agent got blocked on, comes back around. It mirrors the existing `needs-info` bucket's shape, and a ticket sitting quietly behind its blockers stays out of it. `docs/engineering/triage.md` needed no change: it already promised to keep that column trustworthy, and the skill now does.

Docs pages for `implement` and `to-tickets` re-synced; `ask-matt` gains the back edge from `implement` to `triage`.
