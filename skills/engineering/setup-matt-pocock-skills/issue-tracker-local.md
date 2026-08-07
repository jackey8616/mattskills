# Issue tracker: Local Markdown

Issues for this repo live as markdown files in `.scratch/`.

**Specs and implementation tickets do not.** Specs are OpenSpec changes under `openspec/changes/<change-id>/`, and a change's tickets are its `tasks.md` — `/to-spec` and `/to-tickets` write there regardless of which tracker this repo uses. With a local tracker there is nowhere better to publish tickets than `tasks.md` itself, so `/to-tickets` writes it and stops; there are no separate ticket files to keep in sync.

What `.scratch/` holds is everything the OpenSpec change folder doesn't: incoming bug reports and feature requests for `/triage`, work that outlives a change, and `/wayfinder` maps.

## Conventions

- One effort per directory: `.scratch/<slug>/`
- Issues are one file per ticket at `.scratch/<slug>/issues/<NN>-<slug>.md`, numbered from `01` — never a single combined file
- Triage state is recorded as a `Status:` line near the top of each issue file (see `triage-labels.md` for the role strings)
- Blocking edges are a `Blocked by: NN, NN` line near the top
- Comments and conversation history append to the bottom of the file under a `## Comments` heading

## When a skill says "publish to the issue tracker"

Create a new file under `.scratch/<slug>/issues/` (creating the directory if needed).

## When a skill says "fetch the relevant ticket"

Read the file at the referenced path. The user will normally pass the path or the issue number directly.

## Wayfinding operations

Used by `/wayfinder`. The **map** is a file with one **child** file per ticket.

- **Map**: `.scratch/<effort>/map.md` — the Notes / Decisions-so-far / Fog body.
- **Child ticket**: `.scratch/<effort>/issues/NN-<slug>.md`, numbered from `01`, with the question in the body. A `Type:` line records the ticket type (`research`/`prototype`/`grilling`/`task`); a `Status:` line records `claimed`/`resolved`.
- **Blocking**: a `Blocked by: NN, NN` line near the top. A ticket is unblocked when every file it lists is `resolved`.
- **Frontier**: scan `.scratch/<effort>/issues/` for files that are open, unblocked, and unclaimed; first by number wins.
- **Claim**: set `Status: claimed` and save before any work.
- **Resolve**: append the answer under an `## Answer` heading, set `Status: resolved`, then append a context pointer (gist + link) to the map's Decisions-so-far in `map.md`.
