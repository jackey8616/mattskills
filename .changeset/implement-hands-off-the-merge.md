---
"mattpocock-skills": patch
---

Stop `/implement` closing a ticket's issue before anything has shipped.

`implement` ended with "close the ticket out: tick its box in `tasks.md` **and close its issue**". Followed literally in a PR-based repo, a session ended with the work committed to an unmerged branch — and the issue closed. The ticket read as done while nothing had shipped, and `to-tickets` said the opposite: both actions happen *after* the merge.

The two actions were bundled but aren't the same kind of thing. The tick is a file edit that rides the commit and takes effect on merge; closing an issue is external tracker state that takes effect at once. Bundling them meant getting one right and the other wrong was the default outcome.

`implement` now commits the tick **in the same commit as the work**, leaves a `Closes #<issue>` trailer in the commit message and PR body, and leaves the issue open — so the merge lands both at the same moment the code does. It also says what it hands off to: push the branch, open a PR if that's how the repo ships work, and leave the merge to the reviewer.

`to-tickets` and `ask-matt` are re-worded to match — the merge is what closes a ticket out, and the post-merge job is confirming the tick landed, not doing it by hand. Docs pages for `implement` and `to-tickets` re-synced.
