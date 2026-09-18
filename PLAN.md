# Plan — Nullius (Factorio) single-file website

Goal: one polished, self-contained `index.html` in this repo about the **Nullius**
Factorio mod, its **two additional-difficulty submods** (Lambent Nil, Tricky Old
Nick), and the **additional-products settings flag** (`lambent-nil-fluorine` /
"Fluorapatite"). No build step, no external assets — inline CSS + JS, works offline.

## 1. Grounding (already researched — verify against these before writing copy)

Local research copies in `/tmp` (fetched from portal + sources):
- `/tmp/nullius_info.json` — Nullius mod metadata (version 2.0.11, deps, links)
- `/tmp/nullius_data.lua`, `/tmp/n_settings.lua` — settings definitions from source
- Plus Lambent Nil / Tricky Old Nick `settings.lua` + `info.json` from codeberg

Key verified facts to use:

**Nullius (main mod)** — by anachrony, v2.0.11, Factorio 1.1–2.0, ~45.8K downloads,
created 2021-02-07, GitHub `GregorSamsanite/nullius`, Discord homepage, license
MIT No Attribution. 62 required deps of 121 (hard deps: base, alien-biomes, angels
graphics, boblogistics, configurable-valves, recipe book & underground-pipe-pack
optional). Prequel story: android terraforms barren planet, seeds life; endgame =
raise O₂, download genomes from originators, seed ecosystem, launch duplicates.
No coal/oil/wood/burners, no free oxygen. Base resources: iron ore, sandstone
(silicon), bauxite (aluminium), calcite (calcium), air, seawater, volcanic gas;
copper & uranium via asteroid-mining tech. Energy: wind (intermittent) → solar
(day/night) → geothermal (volcanic sites) → nuclear (D-T fusion, later fission);
pre-battery storage = stored H₂/O₂ burn + compressed gas. Pollution disabled;
enemy evolution/expansion on at time_factor 0.0000002. Achievement "Easy Breezy":
first wind turbine within 45 min.

**Lambent Nil** — by half_a_cat, v3.2.5, codeberg `cirodrig/lambent-nil`,
2.86K downloads, depends on nullius ≥ 2.0.4 (optional aai-signal-transmission).
Adds phosphorus from phosphorite (rare; early: small amounts; late: recovered from
calcite, industrial & biological waste). Phosphorus traces in vacuum chamber →
silicon circuits; boron doping / electron-beam upgrades; phosphates feed new life.
Early game unchanged until silicon ingots.

**Tricky Old Nick** — by half_a_cat, v1.2.7, codeberg `cirodrig/tricky-old-nick`,
2.40K downloads, depends on nullius ≥ 2.0.4 **and** lambent-nil ≥ 3.2.4 (so it pulls
Lambent Nil in — installing Nick gives both). Nickel = bootstrap metal, substituted
early, later replaced by aluminium/steel but kept for magnetism/thermal/chemical
niche uses. Harder early game; co-op OK, incompatible with alignment mode (can be
disabled in Nullius settings).

**Dependency chain:** Nullius → Lambent Nil → Tricky Old Nick.

**Settings flags (table on the site):**

| flag | type | default | meaning |
|---|---|---|---|
| `nullius-alignment` | bool | true | multiplayer alignment (shared research) vs independent factions + alignment cards |
| `nullius-wind-turbine-energy-multiplier` | double | 1.0 (0.01–100) | wind turbine output scale |
| `nullius-pump-energy-multiplier` | double | 10 (0.1–1000) | pump electricity multiplier |
| `nullius-wind-turbine-volume` | double | 1.0 (0–1) | wind turbine sound volume |
| `nullius-match-wind-turbine-activity-sound` | bool | true | turbine sound follows activity |
| `nullius-hide-void-alt` | bool | true | hide alt vent icons (Chimney/Outfall) |
| `nullius-hide-recipe-signals` | bool | false | hide recipe signals not shown by default |
| **`lambent-nil-fluorine` ("Fluorapatite")** | bool | false | **the additional-products flag** |

The featured flag: when enabled, phosphorite also yields **fluorine**, calcite
carries traces of **fluorite**, and fluorine feeds refrigerants & fluoropolymers —
production/cleanup of these byproducts couples into other resource flows.

## 2. Architecture

- Single file: `index.html` (HTML + `<style>` + `<script>` inline).
- No CDN/fonts/images: system font stack; all artwork = inline SVG (android
  mark, resource icons, dependency chain diagram, timeline).
- Semantic sections, anchor-linked sticky nav, responsive (grid → single column
  ≤720px).

## 3. Sections (top → bottom)

1. **Header/nav** — SVG mark + links: Overview · Resources · Energy · Endgame ·
   Difficulty · Settings · Ecosystem. Active-link highlight on scroll.
2. **Hero** — NULLIUS wordmark, tagline "A prequel to Factorio — terraform,
   seed, survive without coal or oxygen.", stat chips (v2.0.11 · 1.1–2.0 ·
   45.8K downloads · MIT No Attribution), CTAs: Mod Portal / GitHub / Discord.
3. **Overview / lore** — android-prequel summary + "what changes" chips (no
   coal/oil/wood/burners, no free O₂, renewables-first, asteroid mining,
   android duplicates, no pollution, near-dormant enemies).
4. **Resources** — grid: 7 base resources + 2 advanced (copper, uranium) with
   inline-SVG icons and short blurbs (from §1).
5. **Energy** — timeline wind → solar → geothermal → fusion → fission +
   pre-battery storage note + "Easy Breezy" achievement callout.
6. **Endgame** — steps: raise O₂ → re-contact originators → download genomes →
   build biology → seed ecosystem (organisms can out-produce factories) → launch
   duplicates to other planets.
7. **Difficulty — the two submods** — two feature cards (Lambent Nil, Tricky Old
   Nick: version, author, what they add, when to use) + dependency-chain diagram
   + "install both" note (Nick ⇒ Lambent Nil ⇒ Nullius) + alignment-mode caveat.
8. **Additional products flag** — featured callout for `lambent-nil-fluorine`
   ("Fluorapatite"): interactive toggle (pure client-side) showing phosphorite
   output lineup with/without the flag: OFF = phosphorus only; ON = phosphorus +
   fluorine + fluorite traces → refrigerants, fluoropolymers.
9. **Settings reference** — full flag table (from §1), type/default/range/desc,
   with a live search filter; the fluorapatite row highlighted.
10. **Ecosystem** — mod packs (Nullius Momenti: 25 mods; Nullius Maximus: 47
    mods), notable compatible mods as chips, localizations (en, ru, pl, ko, uk,
    zh-CN/TW, es, fr), community links (Discord, 272 discussions, source code).
11. **Footer** — credits (anachrony, half_a_cat, Wube Software), links,
    "unofficial fan site" disclaimer, generated date.

## 4. Design

- Palette: near-black bg `#0b0f14`; panels `#11161d`; text `#dbe4ee`;
  accents: teal `#2dd4bf` (energy/android), amber `#f5b631` (difficulty/ore).
- Subtle background: faint grid + radial glow behind hero (CSS gradients only).
- Cards: 1px border, radius 12px, hover lift. Sticky header w/ backdrop blur.
- Reveal-on-scroll via IntersectionObserver (fade/slide, respects
  `prefers-reduced-motion`).
- Typography: system stack; big condensed wordmark via letter-spacing.

## 5. Build steps

1. Author `index.html` in one pass (structure → CSS → JS).
2. Copy verified numbers/flags exactly from §1 (source-of-truth above).
3. No tests/linters (static page, nothing else in repo).

## 6. Verification (after build)

- Open `index.html` in the browser tool: screenshots of hero, difficulty, and
  settings sections; confirm responsive layout.
- Interact: toggle the Fluorapatite switch (outputs list changes), type in the
  settings search filter (rows filter), click a nav anchor (scrolls + active
  state), check mobile-width rendering.
- Report file path + any issues.
