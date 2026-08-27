# BilliFit — Cosmetic Fork Checkpoint

**This repo is the "Limited Edition App."** It is a cosmetic-only fork of the "Original App"
(`nourish-pwa`, live at `https://ahmed-waheed91.github.io/nourish-pwa/`). Read the Original App's
`plan.md` first — it's the authoritative source for architecture, features, storage limits, and
every real bug fixed to date. This file only documents what's different here and why.

## What this repo is

A copy of the Original App with **name, icon, color palette, and background art changed — nothing
else.** Same `index.html` logic, same data model, same localStorage schema, same features. If a
functional bug or feature request comes up, it almost certainly belongs in the Original App first,
then gets ported here — see the Original's plan.md "Limited Edition App" section for the naming
convention and workflow this project follows.

## Live deployment

- **Live URL:** `https://ahmed-waheed91.github.io/nourish-pwa/` — replace with the actual URL once
  the repo is created and Pages is enabled (see setup steps in the commit that added this file).
- **Source repo:** `https://github.com/ahmed-waheed91/billifit-pwa`, git root at the repo root
  (same layout as Original App).
- Ship an update the same way as Original: edit files, `git add -A && git commit -m "..." && git
  push` from this folder.

## What was changed from the Original App (2026-08-27)

- **Name**: "Nourish" → "BilliFit" everywhere it appeared as display text (title, manifest, header,
  backup-file `app` field, in-app copy). Picked from four options discussed with the user
  ("Billi Diet", "BilliFit", "Billi Track", "Chubby Billi") — user chose **BilliFit**.
- **Color tokens** (`:root` in `index.html`, roughly line 2 onward): every value swapped for a
  turmeric/marigold base with a plum accent and a teal secondary accent, replacing the Original's
  green/blue palette. All colors flow through CSS custom properties already, so this was a
  values-only swap — no selector/structural CSS changed. Notably `--brand` became a **deep**
  marigold (`#B25E12`), not the bright turmeric used for large surfaces — bright turmeric on white
  text fails contrast (~3.2:1) for the many buttons styled `background:var(--brand); color:#fff`
  throughout the app; `#B25E12` passes AA (~4.7:1) while staying in the yellow/gold family. The
  actual bright turmeric (`#E8A93A`/`#E3A426`-ish) lives in the gradient app icon and the
  `--brand`→`--brand-dark` gradient, not as flat white-on-yellow anywhere in-app.
- **Macro/category colors**: `--protein`→plum, `--carbs`→marigold, `--fat`→teal (previously
  blue/orange/green) so the macro bars/charts pull from the same new palette instead of clashing
  with it. `--usda` moved from blue to a dark teal for the same reason. Semantic colors
  (`--good`/`--warning`/`--critical`) were deliberately left as green/amber-red — they carry
  meaning (good/warning/critical) independent of brand color, per general UI-design practice, not
  an oversight.
- **Typography**: added Fredoka (Google Fonts) alongside the existing Manrope for `.disp` elements,
  for a slightly more rounded/playful display face matching the mascot branding. Body text
  untouched.
- **Icon**: new `icons/*.png` — a rounded-square marigold-gradient icon with a simple cat mascot
  (line-art built from basic shapes: circle/ellipse/triangles/bezier-curve tail, no photo assets)
  wearing a measuring-tape belt around its belly as the "dieting" visual joke. Generated
  programmatically with Pillow (`make_icons.py`, not part of the deployed app — a one-off script,
  same pattern as the Original App's original icon generation). Maskable variant keeps the mascot
  within the safe-zone circle per Android adaptive-icon rules; apple-touch-icon and favicon are
  simplified/full-bleed versions of the same mark.
- **Background cat art**: a single reusable `<symbol id="cat-face">` SVG (same shapes as the icon)
  is defined once near the top of `<body>`, and a `catBg(screenId)` helper
  (`index.html`, near `renderStage()`) returns a low-opacity (`opacity:0.07`), non-interactive
  (`pointer-events:none`), absolutely-positioned (`z-index:-1`) instance of it sized/positioned/
  colored per screen. Each of the 7 mobile screens (`today`, `addfood`, `library`, `history`,
  `trends` ×2 render branches, `exportScreen`, `settings`) got one `${catBg('<screen>')}` call
  inserted as the first child of its `.scroll-area`, with `.scroll-area{position:relative}` added
  so the `z-index:-1` trick anchors correctly instead of escaping behind the whole page. **This
  only affects the mobile/phone view** (what Ahmed actually uses day-to-day) — the separate
  desktop dual-pane dashboard (`renderDesktop()`) was left as a plain re-themed surface with no cat
  watermark, since it's a secondary view and doesn't reuse the mobile screens' render functions.
  If cat backgrounds are ever wanted there too, `renderDesktop()` needs its own manual insertion.
- **Service worker cache name**: bumped to `billifit-v1` (was `nourish-v3`) so this app's cache
  doesn't collide with anything — purely defensive, these are different origins anyway once
  deployed to a separate repo/URL.
- **`localStorage` key — real conflict found and fixed before first push**: both this repo and the
  Original App are GitHub Pages *project* sites under the same username, i.e. both live at
  `ahmed-waheed91.github.io/<repo>/` — same scheme+host+port, differing only by path. Browsers
  scope `localStorage` (and `sessionStorage`) to **origin only, not path**, so the two apps are
  **not** automatically isolated the way separate repos might suggest. The Original App saves
  everything under one fixed key, `LOCAL_STORAGE_KEY = 'nourish_backup_v2'` — this repo started as
  a byte-for-byte copy, so it initially had the exact same key, which would have meant both apps
  installed on the same phone silently read/wrote the same data bucket. Fixed by changing this
  repo's key to `'billifit_backup_v1'` (search `LOCAL_STORAGE_KEY` in `index.html`) before ever
  deploying it. **Any future cosmetic fork of either app must repeat this check** — Cache Storage
  is fine on its own (different `CACHE_NAME` strings per app), but `localStorage` needs an explicit
  distinct key since nothing about "different repo" enforces that automatically.
- Nothing else changed: no logic, no data model, no new features. Verified by running both apps
  side-by-side locally and exercising every screen (`App.setScreen(...)` through each) with no new
  console errors beyond the pre-existing, tooling-only service-worker-registration failure that
  also affects the Original App inside this Browser-pane test tool (see Original's plan.md).

## If a future session needs to redo or extend this fork

- To re-sync a future Original App change into this fork: diff the relevant section of Original's
  `index.html` against this repo's, and reapply structurally — do **not** blindly overwrite this
  whole file, since the color tokens, font link, cat-symbol/`catBg()` additions, and the 8
  `${catBg(...)}` insertion points listed above are this fork's entire reason to exist and are not
  present in Original at all.
- The `make_icons.py` script used to generate `icons/*.png` was run from a scratch/temp location
  and is not checked into this repo (matches how the Original App's own one-off icon-generation
  script wasn't kept either). If icons need regenerating, a new script can be written from the
  color values and mascot description in this file.
