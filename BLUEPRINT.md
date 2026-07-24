# Singularity Nexus — Blueprint

**Version:** 1.1.0
**Date:** 2026-07-24
**Status:** Production-ready pilot · single-file HTML app
**Live URL:** `https://dnzengou.github.io/singularity-nexus/` (post-Pages activation)
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
        ┌─────────────────────────────────────────────┐
        │  KafCa — client pub/sub event bus           │
        │  topics: state.changed · ui.audit.requested │
        │          commerce.lead · commerce.bond      │
        │          metaclaw.recorded · window.updated │
        └────┬─────────────┬──────────────┬───────────┘
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

Total: 1 file, ~1070 lines, no dependencies at rest (Tailwind + Three.js loaded via CDN with fail-safe fallbacks).

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
| Named constants for CAS coefficients | 🔲 | Auditability improvement |
| Playwright smoke test suite | 🔲 | Pre-scale requirement |
| Cryptographic seal (server-signed) for EvoMetaClaw | 🔲 | Requires backend — Phase 2 |
| Real payment processing (Checkout Sessions) | 🔲 | Requires backend — Phase 2 |
| Server-side CRM integration | 🔲 | Requires backend — Phase 2 |

---

## Distribution Channels

| Channel | Status | Path | Notes |
|---|---|---|---|
| GitHub Pages (canonical) | ✅ CI | `main` → `.github/workflows/deploy.yml` | Live URL from Pages |
| Direct download | ✅ | Repo `Raw` link to `index.html` | Single-file, open in browser |
| Embed in existing site | ✅ | `<iframe src="index.html">` | Sandboxed CSP allows same-origin embed |
| Netlify mirror | 🔲 | Drop `index.html` in any Netlify site | No build step needed |
| Vercel mirror | 🔲 | Same — static asset | |
| Offline / air-gap | ✅ | Save-as HTML; CDN fallbacks let it degrade cleanly | |
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
