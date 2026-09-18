# Plan v0.2 — patch `index.html` to full PLAN.md compliance

Date: 2026-09-16. Companion to `PLAN.md` (v0.1 spec). This document converts the
verification findings into a concrete patch list for the single-file page
`index.html` (all line numbers refer to the current file, 1208 lines).

## Grounding (source of truth for copy)

Still present in `/tmp` — re-read before writing any copy, do not trust memory:

- `/tmp/nullius_info.json` — Nullius metadata (v2.0.11, deps, links)
- `/tmp/nullius_data.lua` — prequel lore text (android, endgame steps)
- `/tmp/n_settings.lua` — Nullius settings: 7 flags. Note
  `nullius-wind-turbine-energy-multiplier` has `hidden = true` (line 15) — the
  page's "hidden from the in-game settings UI" note is **verified correct**, keep it.
- `/tmp/lambent-nil.info.json`, `/tmp/lambent-nil.settings.lua` — Lambent Nil
  metadata + `lambent-nil-fluorine` bool, startup, default false.
- PLAN.md §1 for Lambent Nil / Tricky Old Nick lore (codeberg sources).

All patch items preserve the architecture invariants: one file, inline
`<style>`/`<script>`, system fonts, no CDN/images, offline-safe.

## Findings → item map

| ID | Sev | Finding |
|---|---|---|
| A1 | P0 bug | 390px viewport: `scrollWidth 593 > 390` — Overview `pre.code` min-content (~527px) stretches the 1fr grid track (items default `min-width:auto`); cards clip off-screen, `body{overflow-x:hidden}` only hides the scrollbar |
| A2 | P0 fact | "No biters" (hero L492, overview L531) contradicts PLAN.md §1: enemies exist, near-dormant (`time_factor 0.0000002`); "near-dormant enemies" what-changes chip absent |
| A3 | P0 gap | "Easy Breezy" achievement callout (first wind turbine within 45 min) missing from Energy |
| A4 | P0 gap | Alignment-mode caveat missing: TON incompatible with alignment mode (can be disabled in Nullius settings); `nullius-alignment` table row has no meaning |
| A5 | P0 gap | Fluorapatite ON state omits PLAN.md §1 coupling: phosphorite→fluorine, calcite carries fluorite traces, fluorine feeds refrigerants & fluoropolymers |
| A6 | P0 gap | Settings table: 5 of 8 rows lack the plan's "meaning" text; fluorapatite row not highlighted |
| B1 | P1 | No dependency-chain diagram (PLAN.md §2/§3.7) and no explicit "install both" note |
| B2 | P1 | Advanced resources collapsed into one "Asteroids" card; plan wants copper and uranium as two cards (7 + 2 grid) |
| B3 | P1 | Submod author `half_a_cat` missing from both difficulty cards; footer says "the Nullius community" instead |
| B4 | P1 | Ecosystem: pack mod counts (Momenti 25 / Maximus 47), "272 discussions", Spanish localization missing |
| B5 | P1 | Hero stat chips omit "MIT No Attribution" (plan's 4th chip) |
| B6 | P1 | No SVG android mark (PLAN.md §2/§3.1 "SVG mark + links") |
| C1 | P2 | PLAN.md §2 "all artwork = inline SVG (android mark, resource icons, dependency chain diagram, timeline)" — page has zero `<svg>` elements |
| C2 | P2 | Palette deviates from PLAN.md §4 exact hexes |
| C3 | P2 | Card hover changes border only — plan specifies hover lift |
| C4 | P2 | Single-column breakpoint is 640px; plan specifies ≤720px |

Execute in order A → B → C, verifying after each tier (see §Verification).

---

## A — P0: bugs and factual accuracy

### A1. Fix mobile horizontal overflow
- **Where:** CSS `.card` rule, L183–189.
- **Change:** add `min-width:0;` to `.card`. Grid/flex items resolve
  `min-width:auto` to their content's min-content; the unbreakable
  `pre.code` line in the "A clean void" card was stretching the shared 1fr
  track to 575px. With `min-width:0` the `pre` (already `overflow-x:auto`)
  scrolls internally instead.
- **Verified:** injecting this rule at 390px changes `documentElement.scrollWidth`
  593 → 390; all four Overview cards measure 354px.
- **Check:** at 360px and 390px, `scrollWidth === innerWidth`; Overview and
  Settings screenshots show nothing clipped at the right edge.

### A2. Replace "No biters" with near-dormant-enemies content
- **Where:** hero tagline L491–492; Overview card 1 L530–533.
- **Change (hero L491–492):**
  `An android wakes on a barren world. No coal. No oil. <em>Biters sleep.</em> Seed it with life.`
  (keeps the rhythm; "sleep" encodes "present but near-dormant".)
- **Change (Overview card 1, L531):** remove "no biters, " →
  "No life means no coal, no oil, no wood, and no free oxygen in the
  atmosphere — so there is no burner technology either."
- **Add (new element between `sec-head` L516–521 and the card grid L523):**
  a "what changes" chip row using existing `.tag` styles, exactly the seven
  chips from PLAN.md §3.3:
  `no coal / oil / wood / burners` · `no free O₂` · `renewables first` ·
  `asteroid mining` · `android duplicates` · `no pollution` ·
  `near-dormant enemies`
  below it one muted line:
  "Biters still exist — evolution and expansion run at `time_factor 0.0000002`,
  so the colony can grow before they notice it."
  (use `.chip` for the `time_factor` value)
- **Check:** grep confirms zero matches for `no biters` (case-insensitive);
  "dormant" present.

### A3. "Easy Breezy" achievement callout in Energy
- **Where:** after the energy grid, before `</section>` (after L692).
- **Change:** full-width amber callout, modeled on `.quote` (new modifier
  `.quote.amber` — left border + glow using `--amber` instead of `--teal`):
  **Achievement — "Easy Breezy"**: "Put your first wind turbine to work within
  45 minutes of the game starting." small: "— Nullius achievement"
- **Check:** visible in Energy screenshot at 1280px.

### A4. Alignment-mode caveat
- **Where (difficulty):** TON card `.note`, L794–796.
  Append: "Incompatible with alignment mode — it can be disabled in the Nullius
  settings if you need aligned factions. Co-op plays fine."
- **Where (settings):** `nullius-alignment` row L920–927 — replace the "—"
  notes cell with:
  "Multiplayer alignment: shared research for everyone, or independent
  factions + alignment cards. (Tricky Old Nick requires this off.)"
- **Check:** grep "alignment" hits in both sections; "incompatible"/
  "independent factions" present.

### A5. Fluorapatite ON state — full PLAN.md §1 coupling
- **Where:** `#fluorDemo` chains L844–887; JS status strings L1176–1178;
  table note L982–983.
- **Change (chains):**
  1. Phosphorus chain (always visible, L845–859): when ON, the phosphorite
     node gains a second output — restructure flows to
     `phosphorite rock → phosphorus + fluorine` (add an `fchip f` "fluorine"
     after phosphorus), and phosphorus feeds `electronics · biochemistry ·
     exotic-element separation` as today.
  2. New `only-on` chain "Calcite carries fluorite":
     `calcite → calcium + fluorite traces`.
  3. New `only-on` chain "Fluorine byproducts":
     `fluorine → refrigerants · fluoropolymers`, note: "production and
     cleanup of these byproducts couples into other resource flows."
  4. Keep the `only-off` "Fluorine stays out" chain as the OFF picture.
- **Change (JS L1176–1178):** ON status →
  `ON — phosphorite also yields fluorine; calcite carries fluorite traces;
  fluorine feeds refrigerants & fluoropolymers.`
  OFF status → `OFF — standard phosphorus chain; no fluorine, fluorite, or
  fluoropolymers.`
- **Change (table note, L982–983):**
  `"Fluorapatite" — phosphorite also yields fluorine, calcite carries fluorite
  traces, feeding refrigerants & fluoropolymers.`
- **Check:** toggling ON shows all three chains + fluorite/refrigerant/
  fluoropolymer text; OFF shows phosphorus-only.

### A6. Settings table: meanings + highlighted row
- **Where:** table rows L920–984; CSS after L383; `data-text` attrs.
- **Change (notes column, per PLAN.md §1 table):**
  | row | notes text |
  |---|---|
  | nullius-alignment | (A4 text) |
  | nullius-wind-turbine-energy-multiplier | "Wind turbine output scale. Hidden from the in-game settings UI." |
  | nullius-hide-void-alt | "Hides the alternate vent icons (Chimney / Outfall)." |
  | nullius-hide-recipe-signals | "Hides recipe signals not shown by default." |
  | nullius-match-wind-turbine-activity-sound | "Turbine sound follows turbine activity." |
  | nullius-wind-turbine-volume | "Wind turbine sound volume." |
  | nullius-pump-energy-multiplier | "Pump electricity multiplier." |
  | lambent-nil-fluorine | (A5 text) |
- **Change (highlight):** add `class="featured"` to the fluorapatite `<tr>`
  (L976) and CSS:
  `tbody tr.featured{background:rgba(255,187,92,.05)}`
  `tbody tr.featured td:first-child{box-shadow:inset 3px 0 0 var(--amber)}`
  (survives the row-hover rule; no change to search behavior — `data-text`
  unchanged).
- **Check:** search "wind" → 3 rows; search "fluorapatite" → 1 row;
  fluorapatite row visibly amber-marked with search empty.

---

## B — P1: plan completeness

### B1. Dependency-chain diagram (inline SVG) + "install both" note
- **Where:** Difficulty section, after the two submod cards (insert before
  `</div></section>` at L808–810).
- **Change:** full-width inline SVG, `viewBox="0 0 760 140"`, width 100%
  (max-width 760px, like `.ladder`):
  - three rounded-rect nodes left→right: **Nullius** 2.0.11 (teal border),
    **Lambent Nil** 3.2.5 (amber), **Tricky Old Nick** 1.2.7 (blue);
    node fill `var(--panel)`, border 1.5px in the accent, label + version
    as `<text>` using the sans stack;
  - arrows between nodes labeled `≥ 2.0.4` and `≥ 3.2.4` (marker-end arrow,
    `var(--teal-dim)`).
- **Below it** a `.stack`-style note: "One install, whole chain: adding Tricky
  Old Nick pulls in Lambent Nil ≥ 3.2.4, which pulls in Nullius ≥ 2.0.4 — all
  three mods activate."
- **Check:** diagram renders at 1280px and 390px (SVG scales); arrows and
  version labels legible.

### B2. Copper + Uranium cards (7 base + 2 advanced)
- **Where:** resources grid L616–628.
- **Change:** replace the "Asteroids" card (L616–621) with two cards:
  - **Copper** (amber, `advanced`): "A heavier element drawn from asteroids
    once asteroid-mining technology is reached — feeds wiring and electronics."
  - **Uranium** (amber, `advanced`): "Harvested from asteroids after
    asteroid-mining tech; fuels the fission endgame of the energy ladder."
  Delete the "Submod resources" card (L622–628) — its content lives in the
  Difficulty cards; keeps the grid a clean 3×3 (7 + 2).

### B3. Author credits
- **Where:** LN card meta L760–765, TON card meta L782–787, footer L1118–1120.
- **Change:** add `by <b>half_a_cat</b>` to both submod `.meta` rows;
  footer sentence → "Nullius by anachrony; Lambent Nil and Tricky Old Nick by
  half_a_cat."

### B4. Ecosystem completeness
- **Where:** packs card L1056–1064; links L1103–1107 (and footer link list
  L1124–1130); localization card L1086–1097.
- **Change:**
  - pack chips → `Nullius Momenti (25 mods)`, `Nullius Maximus (47 mods)`;
  - add a portal-discussions chip linking
    `https://mods.factorio.com/mod/nullius/discussion` labeled
    "Portal discussions (272)";
  - localization chips: after `中文` add `繁體中文` (plan's zh-CN/TW) and
    `Español` (plan's es); keep `日本語`.

### B5. Hero license chip
- **Where:** stats block L497–503.
- **Change:** add a sixth chip: `<b>MIT No Attribution</b><span>license</span>`
  after the downloads chip (flex-wrap handles 6 chips at all widths).

### B6. SVG android mark
- **Where:** hidden `<svg>` defs block right after `<body>` (L466–467); nav
  brand L471; hero L489–490.
- **Change:** define `<symbol id="mark-android" viewBox="0 0 32 32">` —
  simple android head: rounded dome, two dark eyes, two antennae; stroke
  `currentColor`, no gradients. Use it:
  - nav: 16px inline before the `NULLIUS` text in `.brand` (teal);
  - hero: 72px mark centered above the eyebrow with a teal drop-shadow glow
    (CSS `filter: drop-shadow(0 0 14px rgba(...))`).

---

## C — P2: design fidelity to PLAN.md §4

### C1. Inline SVG resource + timeline icons
- **Where:** defs block (with B6); resource cards L572–629; energy rungs
  L644–674.
- **Change:** add `<symbol>` icons, 24×24, stroke 1.5, round caps,
  `currentColor` (one symbol each): `r-iron` (hex crystal), `r-sand` (strata
  layers), `r-bauxite` (crystal cluster), `r-calcite` (angled crystal),
  `r-air` (wind swirl), `r-seawater` (waves), `r-volcano` (plume),
  `r-copper` (ingot), `r-uranium` (nucleus + orbit); energy: `e-wind`
  (turbine), `e-solar` (sun), `e-geo` (volcano + steam), `e-fusion` (spark),
  `e-fission` (split nucleus).
- Replace `<span class="dot">` in resource cards with
  `<svg class="ricon"><use href="#r-…"/></svg>` (18px; keep the teal/amber/blue
  color coding via a class on the svg). Add a small `e-*` icon before each
  rung's `<h3>`. Keep `.dot` for the submod/other cards (or convert those
  too — B3's cards may keep dots).
- **Check:** icons legible at 18px in screenshots; no broken `<use>` refs
  (check `document.querySelector('use').getBoundingClientRect()` non-zero).

### C2. Exact palette from PLAN.md §4
- **Where:** `:root` L9–27.
- **Change:** `--bg:#0b0f14` (drop `--bg2` or set `#0d1219`), `--panel:#11161d`
  (fold `--panel2` to `#151c26`), `--text:#dbe4ee`, `--teal:#2dd4bf`,
  `--amber:#f5b631`; re-derive `--teal-dim` (~`#1f8a79`), `--teal-glow`
  `rgba(45,212,191,.14)`, `--amber-glow` `rgba(245,182,49,.13)`, `--line`/`--line2`
  to keep ~#1c2939/#2a3d54 contrast; `--blue` unchanged (no spec in plan).
- **Risk:** small contrast shift — re-screenshot hero/cards/table after.

### C3. Card hover lift
- **Where:** `.card:hover` L190; transition L188.
- **Change:** `.card:hover{border-color:var(--line2);transform:translateY(-2px)}`
  and add `transform` to the `.card` transition list.

### C4. Breakpoint ≤720px
- **Where:** media query L448.
- **Change:** `@media (max-width: 640px)` → `@media (max-width: 720px)`
  (keep the 900px step). Re-run the 390px/360px overflow checks from A1.

---

## Execution notes
- Single file, no build step, no linters/tests in repo — edit in place with
  line-anchored patches; keep the existing class naming and CSS structure.
- All new copy must come from the `/tmp` grounding files or PLAN.md §1 — no
  invented facts.
- Order: A1–A6 → verify → B1–B6 → verify → C1–C4 → final verify.
- Do not touch the JS scrollspy / search / toggle logic except the A5 status
  strings.

## Verification (after each tier)
Browser (headless Chromium, `file://`), widths 1280×900 and 390×844 (+360×740 for A1/C4):
1. Screenshots: hero, Energy (Easy Breezy visible), Difficulty (SVG chain
   diagram), Fluorapatite, Settings, Ecosystem, footer.
2. Overflow: at 360/390px `document.documentElement.scrollWidth ===
   window.innerWidth` on every page scroll position.
3. Interactions (regression): Fluorapatite toggle (all three ON chains, status
   text per A5); settings search "wind" → 3 of 8, "alignment" → 1 with new
   note; nav anchor click scrolls + sets `.active`; reduced-motion → reveals
   instant.
4. Grep assertions on the final file:
   - present: `Easy Breezy`, `half_a_cat`, `fluorite`, `refrigerant`,
     `fluoropolymer`, `dormant`, `Español`, `MIT No Attribution`,
     `(25 mods)`, `(47 mods)`, `272`, `<symbol`, `<svg` (≥10 occurrences)
   - absent: `no biters` (case-insensitive)
5. Report file path + any residual deviations.
