# Z3 Safety — Website Preview

Static preview build for client review.

**Live:** https://jro424.github.io/z3-safety-preview/ (iframed by https://z3safety.com)

**Office:** 5152 Bolsa Ave Suite 102, Huntington Beach, CA 92649
**Phone:** 714.394.4070
**Email:** sales@z3safety.com

## Files

- `index.html` — single-file preview build (all 5 pages)
- `privacy.html`, `terms.html`, `accessibility.html`, `do-not-sell.html` — California legal disclosure pages (drafted 2026-05-28; counsel review required before publishing)
- `assets/z3-logo.png` — transparent shield logo
- `assets/og-image.png` — 1200×630 social preview
- `sitemap.xml` — XML sitemap (z3safety.com canonical)
- `robots.txt` — crawler rules (AI bots explicitly allowed)
- `llms.txt` — LLM-friendly site summary (emerging standard)

## SEO + AI visibility

- Full Open Graph + Twitter Card meta
- Geographic targeting (CA)
- JSON-LD structured data: LocalBusiness, Organization, WebSite, 5× Service, FAQPage, BreadcrumbList, AggregateRating
- `llms.txt` for direct LLM consumption
- Sitemap submitted via `robots.txt`
- Canonical URL set
- AggregateRating 5.0 / 95 reviews

## Accessibility

- Skip-to-content link
- `:focus-visible` styles
- `prefers-reduced-motion` respected (marquee disables)
- ARIA labels on nav, hero, icon-only buttons
- `aria-current="page"` on active nav
- WCAG AA contrast on all text
- Form labels properly bound

## Responsive breakpoints

- ≤1024px — tablet adjustments
- ≤880px — mobile hamburger nav kicks in
- ≤480px — small phones
- ≤360px — ultra-narrow

`overflow-x: hidden` set on `html` and `body` to prevent any horizontal clipping.

## Pages

- Home
- Services (5 pillars)
- AED Vantage (flagship program)
- About
- Contact

## Notes for production handoff

- Photography brief at top of `index.html` (HTML comments)
- 10 real Google Reviews (verbatim from Z3's GBP, picked from 95 5-star reviews) — will be replaced by the live MarketPushApps widget once Diane connects the Google Business Profile
- All emojis replaced with industrial SVG icons (inline sprite at top of body)

— Built by Verk Vibe
