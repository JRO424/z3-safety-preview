# Z3 Safety — Responsive QA Report

**Method:** CSS source review against declared breakpoints. Live viewport testing via Chrome MCP was not available this run (tab grouping unsupported on the current Chrome session). Findings below are derived from analyzing layout rules at each declared breakpoint and from prior responsive testing.

**Declared breakpoints:**
- ≤1024px (tablet)
- ≤880px (hamburger nav kicks in, single-column layouts)
- ≤480px (small phones)
- ≤360px (ultra-narrow)

**Overflow protection:** `overflow-x: hidden` set on `html` and `body` — protects against horizontal scroll regardless.

---

## Findings

### BLOCKER — none

No layout-breaking issues found in CSS source review.

### MAJOR — code-review concerns (require live confirmation)

**[M-1] Geo coordinates point to Bakersfield, not Huntington Beach**
- Lines 18–19: `geo.position` = `35.3733;-119.0187` and `ICBM` = `35.3733, -119.0187`
- These are Bakersfield coordinates. Z3's address is 5152 Bolsa Ave Suite 102, Huntington Beach (33.7435; -118.0392).
- Bakersfield was removed from the service area in Phase 1 — these coordinates were not updated.
- Fix:
  ```
  <meta name="geo.position" content="33.7435;-118.0392">
  <meta name="ICBM" content="33.7435, -118.0392">
  ```

**[M-2] Canonical URL points to GitHub Pages staging, not production domain**
- Line 13: `<link rel="canonical" href="https://jro424.github.io/z3-safety-preview/">`
- Same on og:url (line 25)
- Production domain is z3safety.com (Wix-hosted, iframe-embedding the GitHub content). Both URLs serve the same content — this risks duplicate indexing penalty in Google.
- Fix:
  ```
  <link rel="canonical" href="https://z3safety.com/">
  <meta property="og:url" content="https://z3safety.com/">
  ```

**[M-3] Sitemap and robots.txt reference GitHub staging, not production**
- sitemap.xml lines 5, 10, 15, 20, 25, 30: all `loc` entries are jro424.github.io URLs
- Should be z3safety.com URLs (the canonical domain users actually visit)
- robots.txt line 35: `Sitemap: https://jro424.github.io/z3-safety-preview/sitemap.xml` should be `https://z3safety.com/sitemap.xml` once the file is also served on Wix

### MINOR

**[m-1] Nav cramping risk at 880–1024px range**
- Between the 1024px tablet adjustments and the 880px hamburger trigger, nav-link padding drops to `9px 12px` and font to 13.5px. With 5 items + a CTA button, this could feel tight at 880–960px widths.
- Probably acceptable but worth visual check.

**[m-2] Hero badge readability at 480px**
- At ≤480, `.hero-badge` is `font-size: 10.5px` with `letter-spacing: 1.2px`. Very small text + heavy letter-spacing can cross WCAG SC 1.4.4 readability threshold for some users.
- Consider 11.5px with letter-spacing: 1px for better legibility on small phones.

**[m-3] Review marquee animation duration on mobile**
- At ≤880, `.reviews-track { animation-duration: 200s }` — that's 3 min 20 sec per cycle. A first-time mobile visitor may not see Sue B. or Rick N.'s 7-year-tenure cards before bouncing. Consider 90–120s on mobile so the rotation is faster.

**[m-4] Trust bar gap reduction at 880**
- `.trust-bar-inner { gap: 12px 18px }` is fine, but `display: none` on `.trust-divider` removes visual separators. Check that the items don't run together visually — may want to add a vertical rhythm via padding instead.

### Layout points verified OK in CSS

- ✓ All multi-column grids collapse to `1fr` at 880px
- ✓ Footer 4-col → 2-col → 1-col cascade is correct
- ✓ Process steps removes the connecting line (`process-steps::before { display: none }`) on mobile so the numbered cards stack cleanly
- ✓ Hero CTAs stack vertically and become full-width below 880
- ✓ Review cards downsize: 300px → 280px → 260px across breakpoints
- ✓ Hero shield scales 360px → 260px → 220px
- ✓ Hero h1: 64px → 48px → 38px → 32px → 28px cascade
- ✓ Section title: 50px → 36px → 28px → 24px → 22px cascade
- ✓ `nav-logo-sub` hides at ≤1024 to avoid wrapping
- ✓ `prefers-reduced-motion: reduce` is honored on the marquee (per existing memory note)
- ✓ Mobile hamburger has open/closed transform animation

---

## Recommended live-test protocol

When Chrome MCP is restored or in a manual session, smoke-test at these viewports:
- 320 × 568 (legacy iPhone SE / small Android)
- 375 × 667 (iPhone SE 2nd gen)
- 414 × 896 (iPhone Plus)
- 768 × 1024 (iPad portrait)
- 1024 × 768 (iPad landscape, small laptops)
- 1440 × 900 (typical desktop)
- 1920 × 1080 (large desktop)

Per viewport, scroll through: nav → hero → trust bar → problem cards → How We Work → service pillars → AED Vantage → Why Ops Call Z3 → Process steps → Built-for-CA → Reviews marquee → Coverage map → CTA banner → Footer.

Watch for: text clip, image overflow, button wrap, marquee scroll smoothness, hamburger tap area ≥44px.
