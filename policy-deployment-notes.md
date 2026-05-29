# Z3 Safety — Policy Pages Deployment Notes

**Created:** 2026-05-28
**Files added to project root:**
- `privacy.html`
- `terms.html`
- `accessibility.html`
- `do-not-sell.html`

All four pages share a single inline-CSS template that matches the main site brand (Inter + Manrope, ink/gold palette, dark navy header, gold accent border). Each page is fully self-contained — no shared CSS, no JS, no build step. Mobile responsive at 680px breakpoint.

---

## 1. Footer link text to add to `index.html`

Add these 5 links to the footer (last line, after `footer-cert` or as its own row):

```
Privacy  ·  Terms  ·  Accessibility  ·  Do Not Sell or Share My Personal Information
```

The "Do Not Sell or Share" link is the one required by CCPA/CPRA to be conspicuous on the homepage footer (or a separate footer link group). Even though Z3 doesn't sell, the link is still best practice and signals compliance posture.

### Recommended snippet (drop-in replacement for footer-bottom rows)

```html
<div class="footer-bottom">
  <div class="footer-copy">© 2026 Z3 Safety — Practical workplace safety, California operations</div>
  <div class="footer-cert">Cal/OSHA Title 8 · Red Cross Certified · AED Vantage</div>
</div>
<div class="footer-bottom" style="border-top: 1px solid rgba(255,255,255,0.06); padding-top: 18px; margin-top: 18px; justify-content: center; gap: 22px;">
  <a href="privacy.html" class="footer-link" style="font-size: 12.5px; color: rgba(255,255,255,0.45); text-decoration: none;">Privacy</a>
  <a href="terms.html" class="footer-link" style="font-size: 12.5px; color: rgba(255,255,255,0.45); text-decoration: none;">Terms</a>
  <a href="accessibility.html" class="footer-link" style="font-size: 12.5px; color: rgba(255,255,255,0.45); text-decoration: none;">Accessibility</a>
  <a href="do-not-sell.html" class="footer-link" style="font-size: 12.5px; color: rgba(255,255,255,0.45); text-decoration: none;">Do Not Sell or Share My Personal Information</a>
</div>
```

If you want the legal links inline with the existing `footer-bottom` row instead of as a second row, replace the existing `footer-cert` `<div>` with the link row.

---

## 2. Exact `footer-bottom` locations in `index.html`

The index.html file uses a single-page-app pattern with 5 `<page>` sections, each with its own `<footer>` block. The legal links should be added to **all 5 footers** so they appear on every page state (home, services, AED, about, contact).

| Footer # | Page | `<footer>` open | `footer-bottom` row | `</footer>` close |
|---|---|---|---|---|
| 1 | Home | line **2578** | line **2626** | line **2630** |
| 2 | Services | line **2774** | line **2785** | line **2786** |
| 3 | AED | line **2934** | line **2945** | line **2946** |
| 4 | About | line **3070** | line **3081** | line **3082** |
| 5 | Contact | line **3156** | line **3167** | line **3168** |

Footers 2–5 are compressed single-line versions (one `<div class="footer-bottom">…</div>`). Footer 1 (home) is the full multi-line version. When editing, you can either:

**(a)** Add the same compressed legal-link row to all 5 footers (one line each), OR
**(b)** Replace each `footer-bottom` with the recommended snippet above and let the second row wrap on mobile.

Option (a) is the smallest diff. Suggested one-liner to append immediately after each existing `footer-bottom` div (before `</footer>`):

```html
<div class="footer-bottom" style="border-top:1px solid rgba(255,255,255,0.06); padding-top:16px; margin-top:14px; justify-content:center; gap:20px; flex-wrap:wrap;"><a href="privacy.html" style="font-size:12px; color:rgba(255,255,255,0.45); text-decoration:none;">Privacy</a><a href="terms.html" style="font-size:12px; color:rgba(255,255,255,0.45); text-decoration:none;">Terms</a><a href="accessibility.html" style="font-size:12px; color:rgba(255,255,255,0.45); text-decoration:none;">Accessibility</a><a href="do-not-sell.html" style="font-size:12px; color:rgba(255,255,255,0.45); text-decoration:none;">Do Not Sell or Share</a></div>
```

(Line numbers are from snapshot at 2026-05-28. They may shift slightly if index.html has been edited since.)

---

## 3. `sitemap.xml` updates

Add these four entries to `sitemap.xml`. Note that the current sitemap points at `jro424.github.io/z3-safety-preview/` — if you've cut over to `z3safety.com` for production, update the loc URLs accordingly.

```xml
<url>
  <loc>https://z3safety.com/privacy.html</loc>
  <lastmod>2026-05-28</lastmod>
  <changefreq>yearly</changefreq>
  <priority>0.3</priority>
</url>
<url>
  <loc>https://z3safety.com/terms.html</loc>
  <lastmod>2026-05-28</lastmod>
  <changefreq>yearly</changefreq>
  <priority>0.3</priority>
</url>
<url>
  <loc>https://z3safety.com/accessibility.html</loc>
  <lastmod>2026-05-28</lastmod>
  <changefreq>yearly</changefreq>
  <priority>0.3</priority>
</url>
<url>
  <loc>https://z3safety.com/do-not-sell.html</loc>
  <lastmod>2026-05-28</lastmod>
  <changefreq>yearly</changefreq>
  <priority>0.3</priority>
</url>
```

Low priority (0.3) and yearly changefreq is appropriate for legal pages — you want them indexed but not crawled aggressively.

---

## 4. `robots.txt` updates

No change required. The current `robots.txt` is `Allow: /` for all crawlers, which already permits the policy pages.

If you want to *prevent* AI/LLM crawlers from training on these specific pages (some operators choose to do this for legal pages), you could add:

```
User-agent: GPTBot
Disallow: /privacy.html
Disallow: /terms.html
```

I would not recommend this — these pages are public commitments and should be discoverable. Leave robots.txt as-is.

---

## 5. JSON-LD on policy pages — recommendation: **skip it**

The four policy pages do not need JSON-LD structured data. Reasons:
- Legal pages don't benefit from rich-snippet treatment in SERPs.
- Schema.org has no clean type for "Privacy Policy" — `WebPage` is the only fit and adds no SEO value.
- The pages link back to `index.html` where the main `Organization`/`LocalBusiness` JSON-LD lives.

If Jon's SEO instinct says otherwise, the minimum useful addition would be a `BreadcrumbList` on each policy page pointing back to home — but the lift-to-value ratio is poor.

---

## 6. Open questions for counsel and Jon

These were intentionally not assumed in the drafts. Please confirm with counsel and then update the pages where needed:

1. **Legal entity name.** The pages currently say "Z3 Safety" throughout. Is the legal entity "Z3 Safety LLC", "Z3 Safety, Inc.", a DBA of another entity, or something else? The Terms section 1 ("About Z3 Safety") and Privacy Policy contact blocks should reflect the registered legal name.
2. **Registered DBA.** If "Z3 Safety" is a DBA, add the parent legal entity name in the Terms section 1 and Privacy contact block ("Z3 Safety is a fictitious business name of [Parent Entity]").
3. **Medical director identity.** The Terms AED section says "a licensed physician partner." If counsel prefers naming the specific medical director or oversight entity, add that name in Terms section 7.
4. **Analytics deployment.** I assumed Z3 does **not** currently run Google Analytics, Meta Pixel, or other tracking. If any of these are live today (or get added via Wix Studio's marketing tools), update Privacy section 2.3 to disclose them and update Do-Not-Sell page if they constitute "sharing."
5. **Wix Studio cookies.** If Wix Studio sets any analytics or marketing cookies on z3safety.com that I haven't accounted for, the Privacy Policy section 2.3 needs updating. Check Wix's cookie disclosure for the published site.
6. **Liability cap amount.** Terms section 13 caps liability at $100. Counsel may want to align this with the actual services-agreement template or use a different figure (e.g., "fees paid in the prior 12 months" if there's a Master Services Agreement). The $100 floor is the conservative default for a marketing site only.
7. **Arbitration clause.** Terms do **not** include an arbitration/class-action waiver. If Z3 wants one for the marketing site, counsel should draft it. Most B2B safety services companies do not need an arbitration clause on the website itself (it belongs in the services agreement).
8. **Insurance carrier requirements.** Z3's general liability carrier may have specific website-disclaimer language they require. Check before publishing.
9. **AED Vantage program-specific disclaimers.** If the Vantage Program 1/Program 2 service agreements contain disclaimers that should also appear on the website (for example, statements about who owns the medical direction agreement), add them to Terms section 7.
10. **Canonical URL.** All four pages set `<link rel="canonical">` to `https://z3safety.com/[page].html`. If the live deployment hosts at a different path (e.g., behind a Wix Studio route or under `/legal/`), update the canonical URLs on all four files before publishing.

---

## 7. Pre-publish checklist

Before going live:

- [ ] Counsel review of all four pages
- [ ] Resolve open questions in Section 6 above
- [ ] Confirm legal entity name appears correctly
- [ ] Confirm contact email and phone are correct on all four pages (currently `sales@z3safety.com` and `714.394.4070`)
- [ ] Add footer legal-link row to all 5 footers in `index.html` (line numbers in Section 2)
- [ ] Update `sitemap.xml` with the four new URLs (Section 3)
- [ ] Verify pages render correctly inside the Wix Studio iframe
- [ ] Manual keyboard-navigation test on each page
- [ ] Screen-reader spot-check on at least the Privacy and Do-Not-Sell pages
- [ ] Re-confirm "Effective" and "Last updated" dates are correct (currently 2026-05-28)
- [ ] If any tracking/analytics gets added before launch, update Privacy section 2.3
