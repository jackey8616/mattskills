---
"mattpocock-skills": patch
---

Name the OpenSpec package explicitly where the skills tell you to install it.

The skills said to run `openspec init`. Following that literally lands on the bare `openspec` package on npm, which is an unrelated 0.0.0 placeholder that installs no working binary — `npx openspec` fails with "could not determine executable to run". The CLI is published as `@fission-ai/openspec`.

Every place that asks the user to initialize now says `npx @fission-ai/openspec@latest init` and notes the placeholder trap: `setup-matt-pocock-skills` Section D, `to-spec`'s precondition, and `DOC-LAYERS.md`. Post-init commands (`openspec validate`, `openspec archive`) keep the bare binary name, which is correct once it's installed.
