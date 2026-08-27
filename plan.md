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

- **Live URL (install via "Add to Home Screen"):** `https://ahmed-waheed91.github.io/billifit-pwa/`
- **Source repo:** `https://github.com/ahmed-waheed91/billifit-pwa`, git root at the repo root
  (same layout as Original App). GitHub Pages serves from this repo's default branch, root path —
  same config as Original App, except this repo's default branch is **`master`**, not `main` (an
  artifact of how the local repo was initialized — harmless, just don't assume `main` when
  scripting against this repo specifically).
- Ship an update the same way as Original: edit files, `git add -A && git commit -m "..." && git
  push` from this folder.
- Pushing this repo needed a manual `gh auth login` + `gh repo create ... --push` run by Ahmed
  directly in his own terminal — Claude Code's own safety classifier blocked the assistant from
  running `gh repo create --push` itself (a repo-creation/push action), independent of normal
  permission settings. Expect the same for any future from-scratch repo creation.

## What was changed from the Original App (2026-08-27)

- **Name**: "Nourish" → "BilliFit" everywhere it appeared as display text (title, manifest, header,
  backup-file `app` field, in-app copy). Picked from four options discussed with the user
  ("Billi Diet", "BilliFit", "Billi Track", "Chubby Billi") — user chose **BilliFit**.
- **Color tokens** (`:root` in `index.html`): values-only swap through the existing CSS custom
  properties — no selector/structural CSS changed. Originally shipped as a turmeric/marigold +
  plum + teal palette, then **re-themed the same day to pastel blue + yellow** after the user
  reviewed it live — see "Re-themed to pastel blue + yellow" below for the current palette and why.
- **Macro/category colors**: pull from the same brand palette as everything else so the macro
  bars/charts don't clash with it — see the re-theme section below for current values. Semantic
  colors (`--good`/`--warning`/`--critical`) were deliberately left as green/amber-red in both the
  original palette and the re-theme — they carry meaning (good/warning/critical) independent of
  brand color, per general UI-design practice, not an oversight.
- **Typography**: added Fredoka (Google Fonts) alongside the existing Manrope for `.disp` elements,
  for a slightly more rounded/playful display face matching the mascot branding. Body text
  untouched.
- **Icon**: new `icons/*.png` — a rounded-square marigold-gradient icon with a simple cat mascot
  (line-art built from basic shapes: circle/ellipse/triangles/bezier-curve tail, no photo assets)
  wearing a measuring-tape belt around its belly as the "dieting" visual joke. Generated
  programmatically with Pillow (`make_icons.py`, not part of the deployed app — a one-off script,
  same pattern as the Original App's original icon generation). Maskable variant keeps the mascot
  within the safe-zone circle per Android adaptive-icon rules; apple-touch-icon and favicon are
  simplified/full-bleed versions of the same mark. **Not yet updated for the pastel blue/yellow
  re-theme below** — the home-screen icon still shows the original marigold/orange artwork, so it
  no longer matches the in-app palette. Revisit if the user wants it regenerated to match.
- **Background cat art**: a single reusable `<symbol id="cat-face">` SVG (same shapes as the icon)
  is defined once near the top of `<body>`, and a `catBg(screenId)` helper
  (`index.html`, near `renderStage()`) returns a low-opacity, non-interactive
  (`pointer-events:none`), absolutely-positioned (`z-index:-1`) instance of it sized/positioned/
  colored per screen. Each of the 7 mobile screens (`today`, `addfood`, `library`, `history`,
  `trends` ×2 render branches, `exportScreen`, `settings`) got one `${catBg('<screen>')}` call
  inserted as the first child of its `.scroll-area`. **This
  only affects the mobile/phone view** (what Ahmed actually uses day-to-day) — the separate
  desktop dual-pane dashboard (`renderDesktop()`) was left as a plain re-themed surface with no cat
  watermark, since it's a secondary view and doesn't reuse the mobile screens' render functions.
  If cat backgrounds are ever wanted there too, `renderDesktop()` needs its own manual insertion.
  ⚠️ Shipped invisible at first — see "Re-themed to pastel blue + yellow" below for the real bug and
  fix; don't reintroduce `.scroll-area{position:relative}` without `isolation:isolate` (or an
  explicit `z-index`), or the negative-z-index watermark trick silently breaks again.
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

## Re-themed to pastel blue + yellow (2026-08-27, same day as the fork)

The user reviewed the original marigold/plum/teal palette live and asked for a different
direction: **pastel blues and yellow as the base theme**, plus a fix for the per-screen cat
watermark, which wasn't visible to him at all on his real device.

- **New palette** (`:root` in `index.html`) — values-only swap again, same tokens as before:
  - `--brand`/buttons: `#2A6FB0` (a mid cornflower/sky blue — deep enough to pass AA contrast
    (~5.2:1) for the many `background:var(--brand); color:#fff` buttons throughout the app, while
    still reading as "blue" rather than washed-out pastel). `--brand-dark: #1B4D78`.
  - Backgrounds/surfaces: pale pastel sky-blue (`--studio-bg: #EAF3FB`, `--card`/`--surface` near-
    white), ink/text shifted from the old brown-black family to a deep slate-navy (`--ink:
    #1E2E3D`) so text still reads as neutral against the new cool palette.
  - `--protein`: soft indigo (`#6C63A6`). `--fat`: teal (`#2F8F86`, same family as before).
    `--usda`: blue (`#1F6FA0`).
  - `--carbs`: **Pantone Yellow 0131 U (`#FBF59B`, RGB 251/245/155)** — the user's exact requested
    shade, swapped in after an initial mustard-gold (`#D9A017`) attempt. This is **the "yellow"
    token** — FAB, active filter chips, and the carbs macro color all read from it. It's pale
    enough that it only works because those elements use dark `var(--ink)` text/icons on top, never
    white — check that before ever reusing `--carbs` somewhere new. `--label-tint`/`--note-bg` are
    a separate, already-pale sub-family (chip/note surfaces) — not tied to this token, left as-is.
  - Semantic colors (`--good`/`--warning`/`--critical`) left untouched again, per the same
    reasoning as the original fork.
  - A few hardcoded (non-var) leftover colors tied to the *original* Original-App palette —
    the body's dot-grid `background-image`, the FAB's `box-shadow`, and two Trends sparkline chart
    band fills — were also updated to the new blue, since they were never routed through a CSS
    variable in the first place and would otherwise have stayed the old color through both
    re-themes.
- **First round shipped with yellow barely visible** — only a small legend dot/macro-bar sliver for
  Carbs. User asked "where is the yellow?" after reviewing. Fix: gave yellow (`--carbs`) two
  high-traffic, always-visible roles instead — the floating **"+" action button** (`.fab`, bottom-
  right on Today, background `var(--carbs)` with a dark `var(--ink)` icon for contrast) and
  **active/selected filter chips** (`.chip.active` — the Trends metric selector, the Add-Food
  meal-target selector). Deliberate split: **blue** = primary actions & structural chrome (headers,
  hero card, main CTA buttons), **gold** = selection/emphasis (the FAB, active filter chips). Don't
  collapse this back to all-blue without re-confirming — it's what actually made yellow visible.
- **Cat watermark was a real, confirmed-invisible bug, not just "too faint"**: `.cat-bg` used
  `position:absolute; z-index:-1` inside `.scroll-area{position:relative}` — but `position:relative`
  *alone*, without an explicit `z-index`, does **not** establish a new CSS stacking context. So the
  negative z-index escaped `.scroll-area` entirely and was evaluated against the nearest ancestor
  that *does* form one — which ended up being all the way up near the page root, placing the
  watermark **behind `.phone`'s own opaque background**, fully hidden regardless of opacity. Fixed
  by adding `isolation:isolate` to `.scroll-area` (`index.html`, `.scroll-area` CSS rule), which
  contains the negative z-index locally so it paints behind the screen's own content but above the
  phone frame's background, as originally intended. Opacity also bumped `0.07` → `0.14` since even
  correctly-stacked, the original value was hard to see. **Don't remove `isolation:isolate` from
  `.scroll-area`, and don't add any other `position:relative` wrapper between `.scroll-area` and
  `.cat-bg` without checking it doesn't reintroduce the same escape.**
- Manifest (`manifest.json`) `background_color`/`theme_color` and the `<meta name="theme-color">`
  tag updated to match the new blue, so the install splash screen and browser chrome match.
- Verified live in a local static-file preview (this machine has no Python/Node — see "Known
  environment facts" in Original's plan.md, now also true here; used a small inline PowerShell
  `HttpListener` static server instead, scratch-only, not part of the repo) across every mobile
  screen via `App.setScreen(...)` before showing the user and getting explicit approval to push.
- **Follow-up after this shipped: user still saw no cats on the real installed PWA**, even though
  the fix above was confirmed working in the local preview. Root cause was almost certainly
  `service-worker.js`'s cache, not the CSS fix itself: `CACHE_NAME` wasn't bumped in this push, so
  the browser never detected a byte change in the SW script and kept running the already-installed
  worker; separately, its network-first navigate handler called plain `fetch(req)`, which can still
  be satisfied from the browser's own HTTP cache rather than actually hitting the network. Fixed by
  (1) bumping `CACHE_NAME` to `billifit-v2` (forces old cache purge + fresh precache on next load)
  and (2) changing the navigate fetch to `fetch(req, { cache: 'no-store' })` so the app shell always
  revalidates from network when online, never silently serving a stale cached HTML/CSS bundle again.
  **Any future visual/logic change that needs to reach already-installed devices should bump
  `CACHE_NAME` again** — it's cheap insurance against this exact class of "I pushed it but the
  device didn't pick it up" report.

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
