# GalacticResource - Claude Code Guide

## Site
- URL: https://galacticresource.com (note: `.com`, not `.net`)
- Repo: https://github.com/corganb/galacticresource
- Host: Cloudflare Pages (auto-deploys from `main`)
- Supabase project: `pxocavaczfjcpvqeiznt` (shared across all sibling sites)
- Engine: Three.js (WebGL, single-page), procedural 80k-point Milky Way

## Repo layout
- `public/index.html` - main app (single-HTML)
- `public/resource-systems-core.js` - shared RS namespace library
- `public/faq.html`, `public/privacy.html`, `public/terms.html`
- `public/_headers`, `public/robots.txt`, `public/sitemap.xml`

## Hard rules
1. **No em dashes.** Anywhere. Use hyphens, colons, or parentheses.
2. **No canvas textures. Ever.** The v1 star field used `document.createElement('canvas')` to draw sprite textures for stars, which created persistent rendering artifacts (obscured / smeared stars) because HTML5 Canvas and WebGL disagree on premultiplied alpha. v2 uses `THREE.Points` with `GL_POINTS` (fragment shader circular alpha) only. Do not re-introduce canvas textures for points, labels, or backgrounds.
3. **Cache-bust core.js after edits.** Bump `?v=N` on the `<script src>` tag. Current: `v=17`.
4. **Verify syntax before pushing.** On the last `<script>` block, check `{` vs `}` balance and even backtick count.
5. **Commit early, commit often.** One logical change per commit.
6. **Python or Edit tool for multi-line edits.** Bash heredocs and repeated sed drop characters on this machine.
7. **Data layers load but stay hidden by default.** Exoplanets, Bright Star Catalog, and notable objects all `fetch()` at startup but are toggled off until the user enables them from the sidebar.
8. **Commit attribution: Corgan Studio, Inc. only.** Never add Co-Authored-By Claude.

## Shared RS.* API (core.js v=17)
- `RS.renderTopBar(opts)` - centralized top nav
- `RS.renderFootLinks()` - consistent footer nav
- `RS.showAnalysisPanel(data)` - click-to-inspect detail panel (used for star/exoplanet inspection via raycasting)
- `RS.openPanorama(imageUrl, title)` - 360 sphere viewer (not used on galactic, but available)
- `RS.checkSession(onAuth)` - Supabase auth init
- `RS.fetchTier()` / `RS.isProUser()` / `RS.hasTier(minTier)`
- `RS.openUpgradeModal(reason, tier)`
- `RS.TIERS = { free:0, researcher:1, pro:1, team:2, enterprise:3 }`

## Galactic quirks
- 80,000 procedural spiral-arm points as the background galaxy
- Three data layers, all fetch-on-load but hidden by default:
  - Exoplanet catalog (NASA Exoplanet Archive)
  - Bright Star Catalog (BSC)
  - Notable objects (Sagittarius A*, galactic center, local group)
- Click-to-inspect via `THREE.Raycaster` against point clouds
- No spacecraft models, no time-scale UI (unlike planet siblings)
- NASA skybox cubemap background

## Tier system
- `profiles.tier` in Supabase: `free` / `researcher` / `team` / `enterprise`
- Free: 3 active layers max. Researcher+: unlimited.

## Deploy
Cloudflare Pages auto-deploys on push to `main`.

## Commit style
- Format: `type: short description` (e.g. `feat:`, `fix:`, `nav:`)
- No em dashes, no Co-Authored-By Claude
- Attribution: Corgan Studio, Inc.
