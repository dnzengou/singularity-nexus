# Singularity Nexus

**CAS Predictive Risk Intelligence Platform — for climate-insurability bifurcation forecasting.**

[![Deploy](https://github.com/dnzengou/singularity-nexus/actions/workflows/deploy.yml/badge.svg)](https://github.com/dnzengou/singularity-nexus/actions/workflows/deploy.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

- Landing: `https://dnzengou.github.io/singularity-nexus/`
- Dashboard: `https://dnzengou.github.io/singularity-nexus/app.html`
- Sample scenario: `https://dnzengou.github.io/singularity-nexus/app.html?preset=ssp5-85`

---

## What it is

A single self-contained HTML file (~60 KB) that models Complex Adaptive Systems dynamics between climate forcing, infrastructure fragility, and insurance-market stability. Executives get an interactive Insurability Index (II_t) with real-time bifurcation early-warning — no dashboards to build, no backend to run.

## What it ships with

- **CAS Engine** — pure ODE-style computation over 3 inputs (climate forcing, adaptation budget, reinsurance backstop)
- **Glass Earth** — WebGL torus-knot shader driven by risk state, with 2D polyline fallback
- **KafCa** — client-side pub/sub bus decoupling state changes from UI, metrics, and storage
- **KafCade** — 60-sample rolling aggregator emitting trend / volatility / bifurcation signals
- **EvoMetaClaw** — signed trajectory ledger in localStorage (per-session event log, FNV-1a seal)
- **AI Risk Concierge** — keyword-routed responses grounded in current state
- **Commercial pipeline** — free Asset Vulnerability Assessment + paid Resilience Bond Spec, wired to Stripe Payment Links

## Structure

- `index.html` — landing page (marketing surface, OG + JSON-LD, backward-compat redirect for `?preset=` links)
- `app.html` — full interactive dashboard (the product UI)
- `BLUEPRINT.md` — architecture, module manifest, roadmap, changelog

## Run it

Open `app.html` (the dashboard) or `index.html` (the landing) in any modern browser. No build, no npm install, no server.

For local development, any static file server works:

```bash
python -m http.server 8000
# then visit http://localhost:8000
```

## Configure commercial pipeline

Click the ⚙ gear icon in the header to set:

- Stripe Payment Link — Bond Spec ($99 default)
- Stripe Payment Link — Pro ($29/mo default)
- Lead capture webhook (POST JSON to your CRM)
- Display prices

On Stripe redirect, append `?activated=1` to the return URL to auto-flip Pro state.

## Architecture

See [BLUEPRINT.md](BLUEPRINT.md) for the full architecture, module manifest, roadmap, RRSS attestation, and changelog.

## Security

CSP-tightened, XSS-safe report generation, CDN fallbacks, no secrets in source. See [.github/SECURITY.md](.github/SECURITY.md) for the disclosure policy.

## Deploy

Push to `main` → GitHub Pages deploys automatically via `.github/workflows/deploy.yml`.

## License

MIT — see [LICENSE](LICENSE).

---

*Singularity Nexus · © 2026*
