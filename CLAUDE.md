# CLAUDE.md — 3D Print Tools

Static, client-side tools hosted on GitHub Pages at https://borenw.github.io/3d-print-tools/
(`index.html` landing, `viewer.html` 3D browser/editor, `support.html` easy-support). No build step.

## Revision rule (REQUIRED)

Every page shows a `rev N` badge in the top-right (`<a class="rev" ...>rev N</a>` in
`index.html`, `viewer.html`, `support.html`). **On every change pushed to this repo, bump
`rev N` by exactly 1 in all three files, in the same commit.** It's a plain monotonic counter
so a viewer can tell which build is live. Never skip (except the one deliberate jump below) or reuse.

**Milestone jump:** at the user's request the badge was bumped 29 → **100** as a milestone
marker (the "clean move-surface" release). It no longer equals the commit count; keep
incrementing by exactly 1 from 100 on every push.

Current: **rev 101**.
