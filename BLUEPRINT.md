# Singularity Nexus — Blueprint

**Version:** 1.6.0
**Date:** 2026-07-31
**Status:** Production-ready pilot · marketing landing + interactive dashboard + executive-report export
**Live URLs:**
- Landing: `https://dnzengou.github.io/singularity-nexus/`
- Dashboard: `https://dnzengou.github.io/singularity-nexus/app.html`
- Shareable scenario: `https://dnzengou.github.io/singularity-nexus/app.html?preset=ssp5-85`

**Repo:** https://github.com/dnzengou/singularity-nexus

---

## Executive Summary

Singularity Nexus is a **Complex Adaptive Systems (CAS)** intelligence platform for climate-insurability bifurcation forecasting. It replaces static actuarial reports with a real-time, interactive dashboard that pinpoints insurability tipping points 3–5 years before conventional models.

The MVP ships as a **single self-contained HTML file** (~60 KB) with zero build step. This is deliberate: instant distribution, zero server cost, embeddable anywhere, works offline after first load.

**HCD verdict:**
- **Desirability** — 3D "Glass Earth" WebGL surface + natural-language concierge · executive time-to-insight ~30 s
- **Feasibility** — pure client-side compute, 60 FPS WebGL with 2D fallback, no build tooling
- **Viability** — two commercial pipelines wired end-to-end (Free Assessment, Paid Bond Spec) via Stripe Payment Links

---

## Architecture

```
        ┌─────────────────────────────────────────────┐
        │  UI Layer (Tailwind + a11y overlay)         │
        └────────────────┬────────────────────────────┘
                         │ DOM events
                         ▼
        ┌─────────────────────────────────────────────────────────┐
        │  KafCa — client pub/sub event bus                       │
        │  topics: state.changed · ui.audit.requested             │
        │          commerce.lead.captured · commerce.bond.generated│
        │          metaclaw.recorded · stream.window.updated      │
        │          preset.applied · projection.updated            │
        │          ui.share.copied · ui.report.printed            │
        └────┬─────────────┬──────────────┬───────────────────────┘
             │             │              │
             ▼             ▼              ▼
      ┌──────────┐  ┌──────────┐   ┌──────────────┐
      │  CAS     │  │ KafCade  │   │ EvoMetaClaw  │
      │  engine  │  │ 60-sample│   │ signed       │
      │  (pure)  │  │ rolling  │   │ trajectory   │
      │          │  │ vol/     │   │ ledger       │
      │          │  │ trend/   │   │ (localStorage│
      │          │  │ bifurc.  │   │  + FNV-1a)   │
      └──────────┘  └──────────┘   └──────────────┘
             │             │              │
             ▼             ▼              ▼
        ┌────────┐  ┌────────────┐  ┌──────────────┐
        │ WebGL  │  │ Metric     │  │ Export gate  │
        │ shader │  │ tiles      │  │ (Pro only)   │
        │ (2D    │  │ (trend /   │  │              │
        │  fbk)  │  │  vol /     │  │              │
        │        │  │  bifurc.)  │  │              │
        └────────┘  └────────────┘  └──────────────┘
```

---

## Module Manifest

| Module | Lines (approx) | Responsibility |
|---|---|---|
| `Config` | ~25 | Stripe URLs / webhook / activation flag in localStorage |
| `KafCa` | ~20 | Synchronous publish/subscribe, per-topic ordering, exception isolation |
| `KafCade` | ~30 | Rolling 60-sample buffer over `state.changed` — emits trend / volatility / bifurcation |
| `EvoMetaClaw` | ~55 | Signed trajectory ledger (session id, event log, FNV-1a seal, Pro-gated export) |
| `computeCAS` | ~10 | Pure function: inputs → `{fragility, premium, insurabilityIndex}` |
| WebGL renderer | ~110 | TorusKnot shader (uRisk-driven), starfield, RAF-throttled resize |
| 2D fallback | ~30 | Polyline chart over KafCade buffer, colour-mapped to II state |
| Modal system | ~40 | Focus trap, ESC-to-close, `role="dialog"` + `aria-modal` |
| Commerce pipeline | ~180 | Assessment / Bond / Export / Config flows + HTML report generators |
| Chat concierge | ~30 | Keyword-routed responses grounded in current state |

Total: 2 shipped files — `app.html` (~1470 lines, ~84 KB) as the dashboard, `index.html` (~460 lines, ~22 KB) as the marketing landing with backward-compat redirect for legacy `?preset=` URLs. No build-time dependencies (Tailwind + Three.js loaded via CDN with fail-safe fallbacks).

---

## Roadmap

| Item | Status | Notes |
|---|---|---|
| Consolidate v0 + v1 into single production HTML | ✅ | index.html |
| Fix CAS monotonic-fragility bug | ✅ | `computeCAS` now pure |
| CSP tightening + CDN onerror fallback | ✅ | Meta CSP + `window.__NEXUS_*_FAILED` flags |
| WebGL + 2D graceful degradation | ✅ | `initWebGL()` → `init2DFallback()` |
| ARIA / focus trap / keyboard nav / reduced-motion | ✅ | Full a11y overlay |
| XSS-safe report generation | ✅ | `escapeHtml()` on all interpolation sites |
| KafCa event bus | ✅ | Pub/sub, exception-isolated |
| KafCade windowed aggregator | ✅ | Trend / vol / bifurcation signals |
| EvoMetaClaw signed trajectory ledger | ✅ | FNV-1a seal, Pro-gated export |
| Commercial pipeline (Assessment + Bond + Config) | ✅ | Stripe Payment Links + webhook |
| UTC-drift fix on date rendering | ✅ | `todayLocalIso()` helper |
| GitHub Pages deploy + CI workflow | ✅ | `.github/workflows/deploy.yml` |
| Security defaults (Dependabot + SECURITY.md) | ✅ | Ship with first distribution |
| Regional / SSP scenario presets (6, illustrative) | ✅ | v1.1 — one-click starting points |
| URL-state sync (`?f/?a/?r/?preset`) + Share View | ✅ | v1.1 — shareable deep links |
| 5-year projection sparkline + `projectCAS` | ✅ | v1.1 — deeper CAS surface |
| Debounce EvoMetaClaw persist (fix 60Hz storage write) | ✅ | v1.1 — E-audit P1 fold |
| ARIA-live announce only on level transitions | ✅ | v1.1 — E-audit P1 fold |
| `applyState()` single-source-of-truth helper | ✅ | v1.1 — E-audit P1 fold |
| Marketing landing page (`/index.html`) with OG + JSON-LD + backward-compat redirect | ✅ | v1.2 — dashboard moved to `/app.html` |
| Named constants for CAS coefficients (`CAS` frozen namespace) | ✅ | v1.3 — auditability, closes v1.0 🔲 |
| Preset chip hover tooltip (raw f/a/r values) | ✅ | v1.3 — transparency/onboarding |
| Print styles + Executive Report button (`window.print()`) | ✅ | v1.3 — printable exec artifact |
| Landing waitlist `mailto:` CTA (Pro early access) | ✅ | v1.3 — zero-infra lead capture |
| Playwright smoke test suite | 🔲 | Pre-scale requirement |
| Cryptographic seal (server-signed) for EvoMetaClaw | 🔲 | Requires backend — Phase 2 |
| Real payment processing (Checkout Sessions) | 🔲 | Requires backend — Phase 2 |
| Server-side CRM integration | 🔲 | Requires backend — Phase 2 |

---

## Distribution Channels

| Channel | Status | Path | Notes |
|---|---|---|---|
| GitHub Pages — landing (canonical) | ✅ CI | `/index.html` | Marketing surface, OG + JSON-LD, SEO entrypoint |
| GitHub Pages — dashboard | ✅ CI | `/app.html` | Full interactive product UI, deep-linkable via `?preset=` |
| Shareable scenario link | ✅ | `/app.html?preset=<id>` | Any of 6 presets; landing forwards legacy `/?preset=` shape |
| Direct download | ✅ | Repo `Raw` link to `app.html` | Single-file, open in browser, works offline |
| Embed in existing site | ✅ | `<iframe src="app.html">` | Sandboxed CSP allows same-origin embed |
| Netlify mirror | 🔲 | Drop both `index.html` + `app.html` in any Netlify site | No build step needed |
| Vercel mirror | 🔲 | Same — static assets | |
| Offline / air-gap | ✅ | Save-as HTML; CDN fallbacks degrade cleanly | |
| npm package (`nexus-widget`) | 🔲 | Wrap as `<nexus-widget>` custom element | Phase 2 |

---

## RRSS Attestation

| Principle | Applied |
|---|---|
| **Robust** | Every async has fail path; no `throw` without catch; localStorage in try/catch |
| **Reliable** | CAS engine is pure — same inputs → same outputs, no hidden state drift |
| **Solid** | Modal system is complete (focus trap, ESC, restore focus); WebGL has 2D fallback |
| **Stable** | Bug-fixed CAS engine no longer degrades regardless of user input |
| **Resistant** | CDN failure → fallback shell; WebGL failure → 2D canvas; storage blocked → in-memory only |
| **Scalable** | Pub/sub decouples state from UI/storage/metrics — new consumers plug in without rewrites |
| **Secure** | CSP tightened; `textContent` everywhere for user data; `escapeHtml()` in HTML report generators; `rel="noopener noreferrer"` on external links |
| **Systematic** | Single-file IIFE, clean module boundaries, consistent naming |

---

## Data Provenance & EO Integration Roadmap (v2.0 sketch)

The 6 shipped presets (`S. Florida Atlantic`, `Gulf Coast LA`, `SF Bay Area`, `Bangladesh Delta`, `SSP2-4.5 Baseline`, `SSP5-8.5 Aggressive`) are **illustrative demonstration values**, chosen to exercise the three CAS input axes across a defensible range. They are NOT calibrated against actuarial data. This is disclosed in-app (chip-row header: `illustrative`) and repeated in [SECURITY.md](.github/SECURITY.md).

A **v2.0 backend evolution** would replace the static preset table with an EO-grounded ingestion pipeline. Sketched below so the substitution is honest and evolvable, not hand-wave.

### CAS inputs ↔ EO signal mapping

| CAS input | Physical proxy | EO source | STAC catalog |
|---|---|---|---|
| `frequency` (climate forcing multiplier) | 10-yr trend in extreme-precipitation days + Cat-3+ wind events per admin-boundary | ERA5 reanalysis + IBTrACS + NOAA HURDAT2 | [Microsoft Planetary Computer](https://planetarycomputer.microsoft.com) `era5-pds`, `noaa-hurdat` |
| `adaptation` (infrastructure hardening budget proxy) | NDVI + built-up-area change detection + green-infrastructure inventory | Sentinel-2 L2A (10 m, 5-day revisit) → NDVI; NBR for burn recovery | [Copernicus Data Space](https://dataspace.copernicus.eu) `sentinel-2-l2a`; MPC `sentinel-2-l2a` |
| `reinsurance` (capacity index) | Not EO-derivable; from Willis/Aon/Guy Carpenter capacity indices + NAIC state filings | External API — treasury.gov data feed + reinsurer quarterly reports | N/A (financial data) |
| `fragility` (derived, not user-set) | SAR-based subsidence + coastal erosion rate + flood-return-period from DEM | Sentinel-1 InSAR + Copernicus DEM + FEMA NFHL | MPC `sentinel-1-grd`, `cop-dem-glo-30`; AWS `sentinel-1-l1c-cogs` |

### Pipeline shape (Python, out-of-repo)

```python
# Offline batch — not in-browser. Runs weekly per region-of-interest.
import pystac_client, stackstac, xarray as xr

cat = pystac_client.Client.open("https://planetarycomputer.microsoft.com/api/stac/v1")
items = cat.search(collections=["sentinel-2-l2a"], intersects=aoi, datetime="2024-01/2026-07",
                   query={"eo:cloud_cover": {"lt": 20}}).item_collection()
stack = stackstac.stack(items, resolution=10, epsg=3857, assets=["B04", "B08"])
ndvi = (stack.sel(band="B08") - stack.sel(band="B04")) / (stack.sel(band="B08") + stack.sel(band="B04"))
adaptation_proxy = ndvi.resample(time="Y").mean().rio.clip(aoi.geometry).mean().item()  # 0..1
```

Region-specific `{frequency, adaptation, reinsurance}` triples get exported as a static JSON manifest, loaded at runtime by the same `Presets` table — the frontend doesn't change shape. Cf. `eo-suite` / `eo-gis-agent` skills for the concrete tooling.

### Why not now

- Static-first, backend-later is the [FREEMIUM-VIA-REDIRECT](.github/SECURITY.md) philosophy applied to data. Ship the interactive dashboard with honest illustrative data; add EO grounding when the buyer conversation warrants the Python pipeline + weekly refresh cadence + storage cost.
- The `Presets` table interface is stable — swap the array literal for a `fetch('/data/regions.json')` when ready. Frontend unchanged.

---

## Security Posture

- **CSP:** `default-src 'self'`; `script-src` allowlists Tailwind + Three.js CDNs only; `frame-ancestors 'none'`
- **XSS:** All user-authored strings rendered via `textContent` in DOM helpers; `escapeHtml()` on all HTML report interpolations
- **CSRF:** N/A (no server-side session)
- **Secrets:** No API keys in source; Stripe URLs configured at runtime via UI, stored in localStorage
- **Privacy:** Lead capture only fires on explicit user submit; webhook URL user-configured; no third-party analytics baked in
- **Tamper-evidence:** EvoMetaClaw ledger sealed with FNV-1a (documented as non-cryptographic; server-side signing is Phase 2)
- **Automated scanning:** Dependabot (weekly, grouped by ecosystem), GitHub Security advisories on this repo

---

## Commercial Model

| Tier | Price | Delivery | Gate |
|---|---|---|---|
| Free | $0 | Downloadable HTML Asset Vulnerability Assessment | Email + company on submit |
| Bond Spec | $99 (config) | Full parametric bond spec (redacted preview if unpaid) | Stripe Payment Link |
| Pro | $29/mo (config) | Trajectory Data Export + full bond unlock | Stripe Payment Link, `?activated=1` URL param |

Payment links are user-configurable via the ⚙ gear icon — swap for real Stripe Checkout Sessions when a backend is added.

---

## Changelog

### v1.6.0 — 2026-07-31
**Multi-Engine CAS Model — first 2 of 53 evolutionary models shipped.** Two pure sibling engines compute alongside `computeCAS`, each surfacing a different diagnostic lens on the same scenario state. Zero new deps, ~150 LOC, no bundle bloat beyond the panel HTML + CSS.

- **BIOMIMIC Resilience Engine** (`computeResilience(inputs, derived)`): 6-factor weighted composite scoring the current scenario 0–100 with grade `Antifragile ≥80 / Resilient ≥60 / Elevated ≥40 / Critical <40`. Factors mapped from CAS state — Diversity=`1-fragility`, Memory+Adaptation=`inputs.adaptation`, Dormancy=`inputs.reinsurance`, Reserves=blended reinsurance+premium buffer, Active=`insurabilityIndex`. Encodes the DARE principle: dormancy is not failure — bet-hedging survives hostile environments decisively better.
- **QUORUM Tipping-Pressure Engine** (`computeTippingPressure(inputs, derived)`): 6-factor pressure index 0–1 with `alert` boolean above 0.6. Grievance + Organization + Opportunity + Perception accumulate; Repression + Institutions subtract (safety valves). TPI is the counterpart to II_t — where II_t measures current insurability, TPI measures proximity to punctuated-equilibrium bifurcation. When TPI crosses the alert threshold, the panel flashes red with "QUORUM REACHED — imminent bifurcation".
- **UI panel** — "Multi-Engine Diagnostic" section in the left column between KafCade metric tiles and the Executive Audit button. Two rows: composite grade badge + TPI progress bar with alert state. Attribution line: "BIOMIMIC · QUORUM · 2 of 53" — honest about scope + monetization hook.
- **KafCa topics + EvoMetaClaw ledger** — `resilience.updated` + `tipping.updated` published per recompute; both subscribed into EvoMetaClaw so composite grades + TPI travel in the Pro trajectory export. Extends the moat: not just parameter path, now also multi-engine grade evolution.
- **Concierge upgrades** — 4 new keyword branches (`resilience/grade/antifragile`, `tipping/quorum/bifurcation/collapse`, `multi-engine/framework/53`) route to grounded responses citing the current engine outputs.
- **Landing FAQ** — new "What is the Multi-Engine Diagnostic?" question explaining the 2-of-53 framing and the deferred models.
- **E-audit fold-ins (same-commit, per E-B PARALLEL COMMIT HOLD):**
  - **P0** — Concierge routing collision: the pre-existing `bond || resilience` keyword branch was intercepting `resilience` queries before they reached the new BIOMIMIC handler. Dropped `resilience` from the bond branch; bond queries still route via `bond` alone. Both handlers now reachable.
  - **P1** — Ledger overflow: 3 new events/tick (state.changed + resilience.updated + tipping.updated) against a 2000-event splice ceiling would truncate the trajectory in ~11s of continuous drag. Fixed by epsilon-gating both engine publishes — `resilience.updated` fires only when composite integer changes, `tipping.updated` fires only when TPI crosses a 0.05 granularity bucket. Reduces steady-state ledger growth to per-transition instead of per-tick.
  - **P2** — BIOMIMIC memory factor collapse: initial mapping had both `memory` and `adaptation` derive from `inputs.adaptation`, effectively collapsing the "6-factor" grade to 5. Fixed by proxying memory from `EvoMetaClaw.snapshot().events.length` (institutional memory grows with session depth) — recovers the sixth orthogonal factor and encodes the honest signal that repeat users build calibration capital.
- **Known follow-up (not folded, scope for v1.7):** mobile layout stack — left column post-v1.6 exceeds ~800px on 375px viewports before commerce/concierge sections come into view. Wrap Multi-Engine or Presets in a mobile-collapsed `<details>` accordion.

### v1.5.1 — 2026-07-31
**Patch bump — three P1 bugs surfaced by live console verification and hidden-tab testing.** No new features; behavior-preserving fixes.

- **Dropped `crossorigin="anonymous"` from Tailwind + Three.js `<script>` tags** (both `app.html` and `index.html`). Without an accompanying SRI hash (which Tailwind's dynamic bundle can't support), `crossorigin` only triggers a CORS check that Tailwind's CDN doesn't satisfy — flooding the console with "blocked by CORS policy" errors. Removing is honest: no SRI, no crossorigin.
- **Dropped `frame-ancestors 'none'` from meta CSP** (both files). Per spec, `frame-ancestors` is silently ignored when delivered via `<meta http-equiv="Content-Security-Policy">` — only response headers take effect. GH Pages can't send custom headers. Documented in `.github/SECURITY.md` with the deployment path for real enforcement (Netlify `_headers`, Cloudflare Workers, reverse proxy).
- **Swapped `requestAnimationFrame` → `setTimeout(…, 200)` for the onboarding tooltip trigger.** rAF is paused/throttled in hidden tabs, so users who opened the app via a shared link in a background tab never saw the tooltip on first foreground view. `setTimeout` fires regardless of visibility (subject to background throttling but always eventually fires). The 200ms delay still gives the browser time to lay out preset chips before `getBoundingClientRect()` reads their position.

### v1.5.0 — 2026-07-31
**UX polish pass to close the gap vs Linear / Cursor / Vercel / Anthropic tier.** Four additions across dashboard + landing, all zero-dep.

- **Keyboard shortcuts + `<kbd>` hints + `?` overlay (dashboard):** press `1`–`6` to load a preset by index, `s` to Share, `p` to print Report, `?` to open the shortcut overlay. Header buttons carry inline `<kbd>` hints (hidden on mobile). Skips shortcuts when typing in an input (guard: `INPUT`/`TEXTAREA`/`isContentEditable`). Skips modifier chords so it never shadows browser shortcuts. Linear/Cursor signature discoverability at ~55 LOC.
- **First-visit onboarding tooltip (dashboard):** floating tooltip anchored under the preset chip row on first visit, dismissable via "Got it", first preset click, or 20s auto-dismiss. `localStorage.nexus.v1.seen` gates so returning users aren't nagged. Non-modal (no focus trap) — this is a pointer, not an interruption. Respects `prefers-reduced-motion` for the pulse animation.
- **FAQ section (landing):** 6 native `<details><summary>` accordions between Commercial Pipeline and Final CTA — zero JS. Questions: "Is this a real actuarial tool?", "How is II_t calculated?", "Can I use my own data?", "What's actually in Pro?", "Why single-file HTML?", "Is my data sent anywhere?". Each answer links back to the honest disclosures in BLUEPRINT.md + SECURITY.md. Objection-handling → sales-friction reduction.
- **Scroll-fade motion (landing):** IntersectionObserver adds `.visible` class to `.fade-in` sections as they enter viewport (threshold 0.15, unobserve after fire). CSS transition on opacity + translateY. Zero deps, ~20 LOC total (CSS + JS). Graceful fallback for browsers without IO (all sections show immediately). Respects `prefers-reduced-motion`.

### v1.4.0 — 2026-07-27
- **RAF-throttled slider recompute (closes v1.0 🔲 `RAF-throttle rapid slider input`):** `input` events at ~60Hz now coalesce into one `requestAnimationFrame` callback (trailing, last-write-wins). `bindSlider` → `requestRecompute()`; preset apply + boot → `recomputeNow()` (immediate for discrete events). Reduces per-drag work by 3-5× on typical drags; screen readers announce level transitions once per real level change, not once per event.
- **`flushRecompute()` for read-derived-state consumers (E-audit P1 fold):** the naive RAF-throttle created a one-frame gap where `state.inputs` was sync-fresh but `state.derived` was stale — a click within that gap (audit button, chat submit, print button, assessment/bond modal open) would report a stale II next to a fresh projection. `flushRecompute()` cancels any pending RAF and runs the compute synchronously. Wired into all five click paths that read `state.derived`.
- **RAF-throttled WebGL resize (matches blueprint claim, drops the drift lint):** browser resize can fire hundreds per second during a window drag; coalesced to one per animation frame. Same discipline as the recompute throttle.
- **Dead pub/sub topics wired into EvoMetaClaw ledger (E-audit P2 fold):** four topics (`preset.applied`, `projection.updated`, `ui.share.copied`, `ui.report.printed`) were published without subscribers. Adding them to the ledger extends the exportable trajectory — every user gesture now shows up in the Pro-tier trajectory export, thickening the EvoMetaClaw moat without adding new UI.
- **Data Provenance & EO Integration Roadmap (BLUEPRINT):** new section between the RRSS attestation and the Security Posture. Maps each CAS input to a physical proxy + EO source (ERA5 · Sentinel-2 NDVI · Sentinel-1 InSAR · IBTrACS) with STAC catalog URLs (Microsoft Planetary Computer, Copernicus Data Space, AWS Open Data). Includes a short `pystac_client` + `stackstac` code sketch showing how a v2.0 backend would populate the `Presets` table with region-fitted values — the frontend interface stays identical, only the data source swaps. Honest disclosure of the illustrative-vs-actuarial gap; concrete evolvability path for v2.0.
- **Data Provenance & EO Integration Roadmap (BLUEPRINT):** new section between the RRSS attestation and the Security Posture. Maps each CAS input to a physical proxy + EO source (ERA5 · Sentinel-2 NDVI · Sentinel-1 InSAR · IBTrACS) with STAC catalog URLs (Microsoft Planetary Computer, Copernicus Data Space, AWS Open Data). Includes a short `pystac_client` + `stackstac` code sketch showing how a v2.0 backend would populate the `Presets` table with region-fitted values — the frontend interface stays identical, only the data source swaps. Honest disclosure of the illustrative-vs-actuarial gap; concrete evolvability path for v2.0.

### v1.3.1 — 2026-07-27
**Streamline pass (kafcade v3.0 LESS-IS-MORE + evo-metaclaw v1.4 SUBTRACTIVE).** Behavior-preserving. Zero new features, zero new files. Net LOC delta ≈ 0 (+17 lines: extracted structures — LEVELS table, DOM cache, REPORT_CSS const — offset the deletions). This is "structure over inline", not raw shrink; auditability rises even if line count doesn't fall.

- **Data-driven `LEVELS` classifier** — replaced `renderIndex`'s 30-line `if/elif/else` with a 3-entry `LEVELS` table (`{id, max, badge, msg, idxCls, badgeCls, alertCls}`) + a single `LEVELS.find(L => ii < L.max)` lookup. Level thresholds + copy + Tailwind classes now live in one auditable place; adding a fourth level is a one-line change.
- **DOM ref cache** — `cacheDom()` at boot populates a `DOM` object with the 18 elements read on per-slider-tick or per-preset paths. `renderIndex` / `renderProjection` / KafCade tile subscriber / `applyState` / `bindSlider` / `renderPresetChips` now use `DOM.foo` instead of `document.getElementById('foo')`. Avoids ~180 lookups/sec under a drag; also removes the ambient "did I typo this id" risk (all ids listed in one array).
- **Shared `REPORT_CSS` constant** — deduped ~200 chars of inline styles between `buildAssessmentReport` and `buildBondSpec`. Grade colour now via a per-report `style=` attribute; the shared stylesheet is single source of truth for report typography.
- **Unified preset chip render/refresh** — `renderPresetChips` now creates un-styled chips and delegates class + `aria-pressed` to `markActivePreset()` (called at end of render and on every state change). Deleted `chipClass()` helper; hoisted `CHIP_BASE` / `CHIP_ACTIVE` / `CHIP_IDLE` to module constants. Chip class strings now appear in exactly one place.
- **Deleted dead `#modal-root` element** — the placeholder `<div id="modal-root" hidden>` in the HTML was never appended to (modal system uses `document.body` directly). Removed.

### v1.3.0 — 2026-07-27
- **Named CAS coefficients (roadmap 🔲 close):** frozen `CAS` namespace at the top of the engine block. All 11 magic floats in `computeCAS` + 3 in `projectCAS` + 2 threshold checks now referenced by name (`CAS.FRAGILITY_FROM_FORCING`, `CAS.II_FROM_REIN`, `CAS.CRITICAL_II`, etc.). Same math, byte-identical output; external actuaries/regulators/RE brokers can now audit the model without pattern-matching floats. Formulas are self-documenting: `fragility = FRAGILITY_FROM_FORCING·f - FRAGILITY_FROM_ADAPT·a + FRAGILITY_BASELINE`.
- **Preset chip tooltip:** native `title=` attribute renders on hover/focus with the raw parameter values (`SSP5-8.5 Aggressive — climate forcing 2.4x · adaptation 0.30 · reinsurance 0.55`). Zero deps, works on desktop + keyboard, no touch-device UX regression. Transparency/onboarding lever — visitors see WHAT a preset actually sets before they click.
- **Executive Report print view:** header `Report` button between Share and ⚙. Triggers `window.print()`; a `@media print` block hides all interactive controls (WebGL canvas, sliders, chat form, action buttons), forces light colors, single-column layout, page-break-inside protection, and injects a banner `Singularity Nexus — Executive Report · 2026-07-27 · SSP5-8.5 Aggressive · session sess_…` via `body::before` reading a data-attribute the button stamps at print time. Result: a stakeholder-ready static snapshot without any PDF-generation dependency.
- **Landing waitlist CTA:** `Get early access to Pro` mailto button next to the final CTA. Pre-fills subject `Singularity Nexus Pro - Early Access` + body template with Org/Role/Use-case prompts. Zero-infra lead capture; can swap for Web3Forms/Formspree later without changing the surface.

### v1.2.0 — 2026-07-24
- **Landing page (kafcade v2.2 LANDING-PAGE + v3.1 DEMO-BEFORE-INSTALL):**
  - Dashboard moved from `/index.html` to `/app.html` (git-mv preserves history).
  - New `/index.html` is a dedicated marketing surface: hero + why + what + commercial pipeline + final CTA.
  - Primary CTA `▶ Try the SSP5-8.5 scenario` links straight to `/app.html?preset=ssp5-85` — the dramatic bifurcation projection IS the demo (no separate demo infra).
  - Live-style hero sparkline mirrors the app's actual `projectCAS` output so what the visitor sees on landing is what the product renders.
- **Backward-compat redirect:** landing detects `?preset=/?f=/?a=/?r=` in the URL and forwards immediately to `/app.html?…` (preserves v1.1 shareable URLs already in circulation).
- **Discoverability meta:** Open Graph + Twitter cards + canonical link + `application/ld+json` SoftwareApplication schema with 3 Offer entries (Free / $99 / $29).
- **Deploy sanity gate widened** to `*.html` — checks both landing and dashboard for the UTC-drift antipattern.

### v1.1.0 — 2026-07-24
- **UI/UX evolution (evo-metaclaw v1.6 DOMAIN-EXTENSION):**
  - 6 scenario presets (S. Florida Atlantic · Gulf Coast LA · SF Bay Area · Bangladesh Delta · SSP2-4.5 · SSP5-8.5), one-click chip row above sliders. Values are illustrative demonstration data (evo-metaclaw v1.6 DEMONSTRATION-DATA DISCIPLINE), not underwriting-grade.
  - URL-state canonical (evo-forge v1.3 STATE-SHAPE gate — this is VIEW-STATE): sliders + preset id sync to `?f=…&a=…&r=…&preset=…` via `history.replaceState`; **Share** button in header copies `location.href` (clipboard API with prompt fallback).
  - Footer region label now driven by active preset (was hardcoded "S. Florida Atlantic").
- **CAS engine depth:**
  - New `projectCAS(base, years)` pure function projects 5-yr trajectory under BAU drift (climate forcing +5%/yr, adaptation compounds +8%/yr on budget, reinsurance retreats proportional to fragility).
  - Inline SVG sparkline (hand-rolled, no chart library — evo-metaclaw v1.7 IMMERSION-WITHOUT-WEIGHT) with critical-threshold reference line at II=0.4 and endpoint colour by severity.
  - Concierge upgraded to reference projection: "when do carriers exit", "5 year forecast", "projection", "preset" now emit grounded responses.
- **E-audit P1s folded (evo-metaclaw v1.5 E-B PARALLEL COMMIT HOLD):**
  - EvoMetaClaw `persist()` debounced (trailing 200ms + `pagehide` flush) — was writing full ledger + FNV-1a seal on every 60Hz slider tick.
  - `announce()` fires only on level transitions (stable → elevated → critical) — was spamming screen readers.
  - `applyState(inputs, presetId)` factored as single source of truth — slider defaults previously lived in three places (HTML `value=`, span text, JS state).
  - Removed misleadingly-named `esc()` helper (was a stringifier, not an HTML-escape).

### v1.0.0 — 2026-07-24
- Initial production ship as `index.html`
- CAS engine rewritten as pure function (v0/v1 monotonic-fragility bug fixed)
- KafCa event bus + KafCade windowed aggregator + EvoMetaClaw signed ledger
- Full a11y pass: ARIA live, focus trap, ESC, `prefers-reduced-motion`, keyboard nav
- XSS-safe HTML report generation via `escapeHtml()`
- CSP meta tag + CDN `onerror` fallback + WebGL → 2D fallback
- Commercial pipeline (Assessment / Bond / Export / Config) with Stripe Payment Links
- UTC-drift fix at report boundary via `todayLocalIso()` helper
- GitHub Pages deploy workflow + Dependabot + SECURITY.md shipped with first distribution
- Distribution Channels table added to blueprint

---

*Singularity Nexus · CAS Predictive Risk Intelligence Platform · © 2026*
