# BilliFit — Cosmetic Fork Checkpoint

**This repo is the "Limited Edition App."** It is a cosmetic-only fork of the "Original App"
(`nourish-pwa`, live at `https://ahmed-waheed91.github.io/nourish-pwa/`). Read the Original App's
`plan.md` first — it's the authoritative source for architecture, features, storage limits, and
every real bug fixed to date. This file only documents what's different here and why.

## What this repo is

A copy of the Original App with **name, icon, color palette, and background art changed — nothing
else.** Same `index.html` logic, same data model, same localStorage schema, same features.
**Corrected 2026-08-28 (user's exact words): "any and all changes that change the way the app
functions in any shape or form are for both editions. only the Aesthetic aspect is specific to the
limited Edition."** So any functional/behavioral/logic/data change gets implemented in **both** this
app and Original in the same pass — not "Original first, port later" as a separate step. Only
cosmetic changes (colors, icon, background art, name/branding) are BilliFit-only. See the Original's
plan.md "Limited Edition App" section for the full naming convention.

## Functional features (2026-08-28, 2026-09-04, 2026-09-11, 2026-09-12, and 2026-09-18)

_Last updated 2026-09-18 — no feature currently in progress; everything listed below (including the
2026-09-18 batch) is user-confirmed working on a real device. See Original's "Immediate next steps".
**2026-09-14: Original's icon was replaced with Ahmed's own artwork — this was explicitly scoped as
Nourish-only/cosmetic and nothing in this repo changed because of it.** Same day, the stale
never-committed 2026-08-28 icon regeneration sitting in this repo's working tree was discarded at the
user's request ("we will come back fresh to that at a later stage") — working tree is now clean. See
"Icon work paused" below for the full closure and for why the lessons from Original's session still
matter whenever BilliFit's own icon work resumes. **2026-09-18: three functional fixes/features
(fiber:carbs ratio, an Add-food selection bug, a USDA lookup reliability overhaul) shipped in the
same pass as Original — see "Session features and fixes (2026-09-18)" below.**_

Four functional changes — memory-only export/import with duplicate resolution, cross-tab search in
Add Food, delete-a-past-day in History, and removal of the Memory screen's "Notes" tab — were
implemented here in the same pass as the Original App, per the corrected rule above, and are
**user-confirmed working**. Full implementation detail, rationale, and edge cases live in
**Original's `plan.md`** (search for "Four functional features") — this repo's copy is
logic-identical, just cosmetically different.

**2026-08-28 follow-up fixes** (also logic-identical to Original, see its `plan.md` items 5-6 for
full detail): the cross-tab search fix above only covered the Add Food screen — the user's original
ask was ambiguous and actually meant the **Memory & Library screen's** search (placeholder literally
says "Search memory..."), which got the same treatment (`App.filterLibraryRows`, `#lib-groups`,
`#lib-extras`) after the user reported it was still per-tab. Also fixed: the Trends chart's point
tooltip pushing off-frame for dots near the left/right edge (was positioning in raw SVG viewBox
units treated as px, with no clamping — now a clamped percentage).

**2026-08-28, new feature (working in practice** — user asked for the weight-based follow-up below
rather than reporting a problem, though never explicitly said "confirmed"): log a partial portion
of a Saved Food (e.g. "I only ate half of my usual breakfast"). Plain Saved Foods get a portion
strip (100/75/50/25% chips + custom field) once checked, scaling every macro proportionally;
composite Saved Foods get a "Whole recipe portion" control that rescales every ingredient's weight
together from the food's original saved weights. Same implementation as Original — see its
`plan.md` item 7 for full detail, including why the custom-percentage inputs deliberately skip
`render()`.

**2026-08-28, same-day follow-up (item 8 in Original's `plan.md`), built and self-tested, not yet
confirmed on a real device**: log a portion by weight, not just percentage — plain Saved Foods get
an optional `totalWeightG` field (create/edit form) and a weight box that back-calculates the
percentage; composite Saved Foods get the same weight box on their "Whole recipe portion" control,
with no new field needed since their total weight is just the sum of their own ingredient weights.
Full detail, including a precision gotcha worth reading before touching this again (percentages are
kept as unrounded floats internally to avoid weight-field drift), lives in Original's `plan.md`
item 8.

**2026-08-28, item 9**: "Meal breakdown" now always displays in a fixed Breakfast/Lunch/Snack/Dinner
order regardless of logging order — user asked for manual drag-reorder, a simpler fixed-order
alternative was offered instead and preferred; see Original's `plan.md` item 9 for the reasoning
(worth remembering for future "let me reorder X" requests) and the `sortedMealsForDisplay()` helper
detail. Not yet confirmed by the user on a real device.

**Standing watch item (2026-08-28)**: user asked to be proactively told if this app's overall
size/build weight ever becomes significant enough to matter (not specific to any one feature) —
same instruction applies to this repo, since it's the same architecture (single monolithic
`index.html`, no build step). Full reasoning and the rough size baseline live in Original's
`plan.md`, under "Standing watch item: app size / build weight."

## Three more functional features (2026-09-04)

Three more user-requested, logic-identical-to-Original features, each built and confirmed one at a
time: **backdated logging** (add food/water/weight to a past day, from History → Day/Month view —
`App.startBackdateLog`/`getOrCreateHistoryDay`/`recomputeHistoryDay`, and a "Logging to [date]"
banner in the Add Food screen), **sharing memory items** (a Share icon per row in Memory & Library,
a **Select** mode to share several at once, and a "paste shared text to import" option in Export →
Import memory), and an **Android back button that navigates within the app** as a fixed depth-based
hierarchy (any non-Today tab → Today directly; a closeable sub-panel closes first, then Today).

The share feature and the back button each needed real, multi-round debugging on the real device
before landing correctly — a Web Share API user-activation quirk (can't retry `navigator.share()`
a second time after one call, even a failed one, since it consumes the tap's activation), a
Chromium platform restriction (file-attachment sharing is restricted to a MIME/extension safe list
that excludes `.json`, and `canShare()` can falsely report `true` for a type that then gets
rejected), and a back-button design that needed to be a fixed 2-level depth rather than a replay of
every screen visited. **Full detail, the exact platform gotchas, and what not to reintroduce are in
Original's `plan.md`, under "Three features (2026-09-04)" — read that before touching either area
again**, since the same code shape (and the same traps) exist here.

## Session fixes and a new Archive feature (2026-09-11)

Two real bugs reported from actual on-device use, both fixed and confirmed, followed by a new
Archive section in Memory & library — logic-identical to Original, implemented here in the same
pass per the corrected functional-changes-go-in-both-apps rule. Full detail, including the exact
root causes and the "don't reintroduce" warnings, lives in **Original's `plan.md`, under "Session
fixes and a new Archive feature (2026-09-11)"** — read that before touching any of this again, since
the same code shape (and the same traps) exist here.

- **Add Food search silently reset when selecting a saved food**: tapping a row to select it,
  adjusting its portion via the quick-percent chips, or expanding a composite recipe row all called
  a full `render()`, which wiped the search box (pure DOM state, not tracked in `App.state`) and
  the filtered list along with it. Fixed by patching just the affected row's DOM node
  (`App.patchAddFoodRow`) instead of re-rendering the whole screen — the general pattern to follow
  for any future one-row toggle inside a searchable list.
- **Updates not reaching the device even after a full close/relaunch**: root cause was GitHub
  Pages' `Cache-Control: max-age=600` header combined with the service worker's navigate fetch not
  forcing a real network round-trip — so the browser's own HTTP cache, not the service worker, was
  quietly serving stale HTML for up to 10 minutes after every deploy. **This repo's service worker
  already had the `{ cache: 'no-store' }` fix** (from the earlier 2026-08-27 re-theme cache-busting
  work — see "Follow-up after this shipped" further down this file); Original's was missing it and
  got the same fix applied, closing a real pre-existing gap between the two apps' service workers.
- **New Archive section**: a 4th tab in Memory & library (Saved foods/Ingredients/USDA/Archive) for
  items used occasionally. Archiving sets an `.archived` flag on the item **in place** (it stays in
  its original `foods`/`ingredients`/`usda` array, just hidden from that tab's list and the main
  cross-tab search) rather than moving it to a separate list — so "which section it was archived
  from" needs no extra bookkeeping, and the item is still fully usable elsewhere (e.g. logging it).
  Archive has its own dedicated search box, separate from the main one. Revised twice after the user
  tried it: archiving now shows a confirm card first (matching the existing Move/Delete pattern,
  instead of applying immediately), and the row of 5 separate action icons (Share/Move/Archive/
  Edit/Delete) was collapsed into a single "⋯" button that opens a dropdown — which required
  changing the Saved-foods row's `overflow:hidden` to `overflow:visible` so the dropdown isn't
  clipped by the row's own accordion-clipping wrapper.

## Session features and fixes (2026-09-12)

Five pieces of work, logic-identical to Original, implemented here in the same pass per the
corrected functional-changes-go-in-both-apps rule. Full detail, exact root causes, and the
"don't reintroduce" warnings live in **Original's `plan.md`, under "Session features and fixes
(2026-09-12)"** — read that before touching any of this again, since the same code shape (and the
same traps) exist here.

- **Macro-split explainer popup** on Today, opened via a new (i) button on the "Macro split · share
  of calories" card — shows the calorie-conversion formula and a live per-macro breakdown of today's
  actual numbers. The first popup-with-backdrop in either app, and the first overlay that lives on
  the Today screen itself, which needed real changes to the shared `hasOpenOverlay`/
  `closeOpenOverlay`/`_syncBackStack` back-button plumbing (previously hardcoded to only ever compute
  overlay depth when away from Today) so the hardware/gesture back button closes it correctly.
- **A second round of the item-13 search-reset bug**, this time in the Memory tab's "⋯" kebab menu
  (added in item 14's second revision, after item 13 shipped, so it was never covered by that fix) —
  and, once that was fixed and tested, in the Move/Archive/Delete confirm-card flow the kebab menu
  opens, which item 14's own writeup had already flagged as a known, deliberately-left gap. Both
  fixed with the same per-row DOM-patch pattern as item 13 (`App.patchLibraryRow`, plus a
  `row.remove()` for Delete/Archive's actual commit step, which makes the row disappear for real).
- **"Quick add"**: a new tab on the Log food screen for logging a one-off item (name +
  calories/protein/carbs/fat, fiber/sugar/sodium optional) straight into today's meal without
  creating any Memory entry — for something you won't eat again and don't want cluttering Saved
  foods/Ingredients/USDA. Building it surfaced a real bug in the shared meal-target-chip selector
  (`setTargetMeal` was calling a full `render()`, silently wiping whatever was typed into this new
  form or into any Ingredients/USDA row's weight box) — fixed by patching the chips/button/row
  labels in place instead.
- **Calories target changed from a fixed ceiling to a low-high range** (default 1,500–1,700 kcal,
  same as Original), so it now behaves like Protein/Carbs/Fat — adjustable the same way in Settings,
  with Under/On track/Over states. The target system was already generic enough that this took
  almost no code beyond the Today hero card (which has its own bespoke ceiling-only rendering, unlike
  the small stat tiles) and the desktop dashboard's calorie sparkline. Existing saved data with the
  old ceiling shape is migrated to the new range on next load.
- **Calories card now colored by status**: red when over, amber (`var(--warning)`, same color as the
  "Under" pill everywhere else in the app) when under, brand blue when in range — previously always
  brand blue regardless of status.

## Session features and fixes (2026-09-18)

Three pieces of work, logic-identical to Original, implemented here in the same pass per the
corrected functional-changes-go-in-both-apps rule. Full detail, exact root causes, and the real
USDA API verification (403/429 responses, real Foundation-vs-SR-Legacy nutrient data) live in
**Original's `plan.md`, under "Fiber:carbs ratio, Add-food selection bug, and USDA lookup
reliability overhaul (2026-09-18)"** — read that before touching any of this again.

- **Per-meal fiber:carbs ratio**, shown in the Today → Meal breakdown accordion (both the mobile and
  the separate desktop layout). User chose to trust the existing meal grouping as-is rather than add
  per-item timestamps to enforce "eaten in one sitting" — noted as a possible future revisit, not
  done now. Displays as a rounded `1:X` ratio (carbs per 1g fiber) plus a quality pill: ≤5 Perfect,
  ≤10 Good, ≤15 Acceptable, >15 Bad (Perfect/Good both render with the app's "good" green, since the
  app only has 3 status colors for 4 labels); a meal with carbs but zero fiber shows "No fiber"
  rather than an undefined ratio.
- **Add Food: selecting an item didn't enable the "Add" button until switching tabs.** Root cause:
  `toggleSelected()` patches only the selected row's own DOM node (to preserve the live search
  filter) and skips the full `render()` that would otherwise recompute the bottom "Add to X · N
  items" button's text/disabled state — so the button stayed stale until something else forced a
  full render. Fixed with a new `syncAddFoodBottomButton()`, called after every patched-row
  selection and reused inside `patchAddFoodTargetMeal()` in place of its own duplicated version of
  the same logic.
- **USDA lookup reliability overhaul.** Started from a user-reported bug (raspberries showing 0g
  fiber) root-caused to `fetchUsdaFood()` locking onto the first `dataType` tier with any matching
  food, even when that tier's record omits nutrients entirely rather than reporting them as zero.
  User then asked for a full audit before pushing ("any and all issues related to USDA need to be
  fixed"), which found the same gap could affect any tracked nutrient, plus three more issues — all
  fixed in one rewrite: nutrient-completeness scoring across all tiers (not just fiber) picks the
  most complete match; a second, previously-unrecognized sugar nutrient id (`1063`, used by
  Foundation-tier records) is now accepted alongside the original `2000`; each tier's top-3 search
  results are checked for a description containing every query word (or its de-pluralized stem)
  before trusting the API's own top-ranked pick; and 429 (rate limit) / 401/403 (bad key) responses
  are now surfaced as distinct, actionable states instead of a generic "couldn't reach the internet"
  message. Tiers are now fetched in parallel instead of sequentially.

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
  - `--carbs`: **Pantone 11-0616 TCX "Pastel Yellow" (`#F2E6B1`, RGB 242/230/177)** — the user's
    exact requested shade, after two rounds of Pantone-matched iteration: started as a mustard-gold
    (`#D9A017`), then Pantone Yellow 0131 U (`#FBF59B`), now this. This is **the "yellow" token** —
    FAB, active filter chips, and the carbs macro color all read from it. It's pale enough that it
    only works because those elements use dark `var(--ink)` text/icons on top, never white — check
    that before ever reusing `--carbs` somewhere new. `--label-tint`/`--note-bg` are a separate,
    already-pale sub-family (chip/note surfaces) — not tied to this token, left as-is. **If asked to
    change this again, look up the exact Pantone hex (WebSearch) rather than eyeballing it** — the
    user has been specifying exact Pantone codes each time, not approximate color names.
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

## Icon work paused — waiting on user's own artwork (2026-08-28)

After the flat-yellow/bigger-cat icon shipped (below), the user asked for a further icon redesign
based on a specific reference cat illustration. That reference turned out to be someone else's
copyrighted artwork (visible artist signature on one version) — declined to trace/use it directly,
including after the user asserted personal-use permission, since embedding it in a public,
distributed PWA icon isn't covered by "personal use" and can't be verified from a chat claim
either way. Several original, inspired-by recreations were attempted (hula-hoop pose, then a
minimalist single-line-art cat) and shown as options; none matched what the user wanted, and
hand-plotting a precise single continuous silhouette curve from memory (no iterative visual editor)
proved unreliable — see attempts in the "Icon regenerated" section below for the technique that
worked reasonably (block shapes + double-fill outline trick) vs. the one that didn't (one long
hand-tuned bezier path).

**Current state**: user says the line-art reference (cat with curled paw near cheek + small heart)
*is* their own original work, but doesn't have the file accessible right now. Asked them to send
either (a) one square master PNG, ≥512×512, opaque background filled edge-to-edge, cat centered —
sizes below can be derived from it — or (b) the full 5-file set themselves. **Spec given to user:**
`icon-512.png` 512×512, `icon-192.png` 192×192, `icon-512-maskable.png` 512×512 (keep content
within inner ~66–80%, Android may crop to circle/squircle), `apple-touch-icon.png` 180×180,
`favicon-32.png` 32×32 (keep bold/simple). All PNG, fully opaque, no transparency (see "why are the
edges white" fix below for why that matters).

**Next step**: when the user provides that file, composite it directly (resize/crop per size above)
rather than attempting another from-memory recreation — this whole detour started because a
from-memory approach isn't reliable for a precise character design. **The current live icon is
actually the original 2026-08-27 fork icon** (marigold-gradient, smaller/less-centered cat) — see the
2026-09-14 update below for why the bigger-cat/yellow-background version described further down this
file was never actually live.

As of this pause, the user is shifting focus to **functional** changes/improvements next (not
aesthetic) — per the Original-vs-Limited-Edition convention elsewhere in this file, functional work
normally belongs in the Original App first. Confirm which app before assuming.

**Found 2026-09-12, resolved 2026-09-14 by discarding — read before touching icons again:**
`git log --oneline -- icons/` showed exactly **one** commit ever touching this repo's `icons/`
folder — the original 2026-08-27 fork commit. That meant the "Icon regenerated: bigger/centered cat,
Pantone Yellow 0131 U background" work documented in the section right below **was never committed or
pushed** — the five regenerated PNGs had been sitting as uncommitted changes in this repo's working
tree since 2026-08-28 (confirmed via `git status`/`git diff --stat` on 2026-09-12, file mtimes
matched that same session), invisible to git the whole time, so the live site never actually showed
that version. Left in place uncommitted on 2026-09-12 pending a decision (user said "we'll come back
to icons later," not now).

**2026-09-14: user decided to discard them rather than reconcile** — "we will come back fresh to
that at a later stage." Ran `git checkout -- icons/apple-touch-icon.png icons/favicon-32.png
icons/icon-192.png icons/icon-512-maskable.png icons/icon-512.png` to restore all five to the last
committed (original 2026-08-27 fork) version; working tree is now clean and matches what's actually
live. **The bigger-cat/Pantone-0131U-yellow redesign in the "Icon regenerated" section below is
therefore historical record only, not a pending or current state** — nothing needs reconciling next
time icons come up. Any future icon work (this repo's own redesign, or porting Nourish's new
2026-09-14 artwork/palette here as a starting point) starts fresh from the current live icon, not
from that discarded attempt.

**Also read before touching icons again: Original's `plan.md`, "App icon replaced with custom
artwork (2026-09-14)"** — Ahmed replaced Nourish's icon with his own Photoshop artwork that session
(Nourish-only, nothing here changed), and two things learned there apply identically to this repo's
eventual icon redo:
1. **`icon-512-maskable.png` cannot be transparent, ever** — Android's maskable-icon role requires a
   fully opaque image so it has real pixels to mask; if Ahmed's future BilliFit artwork is supplied
   transparent, flatten the maskable variant onto a solid fill (ideally sampled from the artwork's
   own background, not a hardcoded color) same as Original now does.
2. **Any icon file replaced with different bytes but the same filename needs `CACHE_NAME` bumped in
   `service-worker.js`** (currently `billifit-v2`, see "Follow-up after this shipped" above) or an
   already-installed device keeps serving the old cached icon indefinitely, hard-refresh included —
   this is a separate cache layer from GitHub Pages' HTTP cache (already handled by the `{cache:
   'no-store'}` fix on the navigate handler) and needs its own bump every time.

## Icon regenerated: bigger/centered cat, Pantone Yellow 0131 U background (2026-08-28) — discarded 2026-09-14

⚠️ **This work was never committed, never went live, and was discarded on 2026-09-14 — kept below as
historical record of the technique used, not as a description of the current or a pending icon.**
See "Icon work paused" above for the full story.

The original `make_icons.py` (Pillow) script was never checked into this repo and this machine has
no Python (see "Known environment facts" in Original's plan.md — that fact was true only for the
*original* dev machine; this one has neither Python nor Node). So when the user asked to make the
icon's cat bigger/more centered and change the background to Pantone Yellow 0131 U, the icon set
was regenerated a different way: **Canvas 2D, rendered in the Browser pane and rasterized to PNG**,
not Pillow. The drawing logic (paths for ears/head/body/belt/tail/whiskers, in a 200×200 coordinate
space, same silhouette style as the original hand-drawn icon) was written inline as browser
JavaScript, run once via `javascript_exec`, and each canvas's `toBlob()` output was `fetch()`-
POSTed to a small upload endpoint added temporarily to the local scratch preview server (a
PowerShell `HttpListener` script, not part of this repo) rather than round-tripped through the
assistant's own context as base64 — that part of the workflow is disposable/one-off, not reusable
infrastructure.
- **Background**: flat `#FBF59B` (Pantone Yellow 0131 U — the user's exact requested shade for the
  *icon specifically*; note this is deliberately different from the in-app `--carbs` accent, which
  is Pantone 11-0616 TCX `#F2E6B1` — two separate, independently-chosen yellows, don't conflate
  them). No gradient, unlike the original marigold-gradient icon.
- **Cat**: same silhouette elements as before (plum ears, cream body/head/tail, dark ink face,
  teal tick-marked "measuring tape" belt, coiled tail tip) but scaled up and recentered — the
  original left noticeably more empty background margin, especially top/bottom.
- Regenerated all 5 files, **all full-bleed opaque yellow, no transparency anywhere**:
  `icon-512.png`/`icon-192.png` initially shipped with baked-in rounded corners and transparent
  corner pixels (matching the original icon's style) — user immediately flagged this ("why are the
  edges white?", since transparent PNG corners render as white/whatever the viewer's background is)
  and asked for the corners filled with the same yellow instead. Fixed by dropping the rounded-rect
  clipping entirely and just filling the whole square, same as the other three sizes already were.
  `icon-512-maskable.png` (cat kept inside a safe-zone-scaled version so Android's own masking
  doesn't clip it), `apple-touch-icon.png` (tail omitted, zoomed closer — matches the original's
  cropped composition style), `favicon-32.png` (head/ears only, no whiskers/belt — too fine to read
  at that size). **Don't reintroduce baked-in rounded corners on any of these** — the user
  explicitly wants flat full-bleed color, not a rounded-square-on-transparent look.
- **If asked to tweak the icon again**: the canvas-drawing JS is not saved anywhere in the repo
  (same "disposable one-off script" pattern as the original `make_icons.py` — intentional, per
  existing convention below). Rebuild it from this description rather than trying to find an
  old script.

## If a future session needs to redo or extend this fork

- To re-sync a future Original App change into this fork: diff the relevant section of Original's
  `index.html` against this repo's, and reapply structurally — do **not** blindly overwrite this
  whole file, since the color tokens, font link, cat-symbol/`catBg()` additions, and the 8
  `${catBg(...)}` insertion points listed above are this fork's entire reason to exist and are not
  present in Original at all.
- Icon regeneration: see "Icon regenerated" section above for the current (canvas-based) method —
  supersedes the original `make_icons.py`/Pillow approach, which needs Python this machine doesn't
  have. Either approach is disposable/one-off; neither script is checked into this repo. If icons
  need regenerating again, rebuild from the color values and mascot description in this file rather
  than hunting for an old script.
