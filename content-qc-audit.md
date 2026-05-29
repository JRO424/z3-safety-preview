# Z3 Safety — Content QC + Schema Audit

Audit date: 2026-05-28
Files audited: `index.html` (3,224 lines), `README.md`, `llms.txt`, `sitemap.xml`, `robots.txt`
Assets confirmed present: `assets/z3-logo.png`, `assets/og-image.png`

---

## Summary

- **BLOCKER:** 8
- **MAJOR:** 13
- **MINOR:** 11

The site is structurally clean (JSON-LD parses, all 5 `id="page-*"` elements exist, all 39 `showPage()` calls map to valid IDs, robots/sitemap don't block crawlers). Most issues fall into three buckets:

1. **Canonical / URL mismatch** — every URL in `<head>`, JSON-LD, sitemap.xml, robots.txt still points to `jro424.github.io/z3-safety-preview/`. Spec calls for `https://z3safety.com`. **Needs Jon's call**: is this preview-only or are we shipping production canonical now?
2. **Stale service area in `llms.txt`** — still lists Santa Maria, Bakersfield, Central Valley, Border Region; AHA Certified; "100–400 employees / 100–200 sweet spot." These were the placeholders. Mechanical fix.
3. **Missing street address** — JSON-LD `address` block only has region + country. Spec says it should include 5152 Bolsa Ave Suite 102, Huntington Beach, CA 92649. README also missing address. Mechanical fix.

Other findings: `Mexican Border` appears in coverage map + footer tag + FAQ (not in spec's allowed area list — Jon's call on whether to keep). `100–200` / `200–400` / `400+` still in contact form's employee-range dropdown (probably want to refresh to a `25+` baseline). Photography brief HTML comment block still references "Bakersfield / Santa Maria" — minor.

---

## BLOCKER

### Canonical / URL block — preview URL everywhere instead of z3safety.com
Spec says: "Canonical URL points to https://z3safety.com (not the GitHub Pages URL)." This affects multiple lines. If Jon confirms the production canonical, apply all of these.

```
[BLOCKER] line 13: canonical points to preview URL
- old: "<link rel=\"canonical\" href=\"https://jro424.github.io/z3-safety-preview/\">"
- new: "<link rel=\"canonical\" href=\"https://z3safety.com/\">"
```

```
[BLOCKER] line 25: og:url points to preview URL
- old: "<meta property=\"og:url\" content=\"https://jro424.github.io/z3-safety-preview/\">"
- new: "<meta property=\"og:url\" content=\"https://z3safety.com/\">"
```

```
[BLOCKER] line 28: og:image absolute URL points to preview
- old: "<meta property=\"og:image\" content=\"https://jro424.github.io/z3-safety-preview/assets/og-image.png\">"
- new: "<meta property=\"og:image\" content=\"https://z3safety.com/assets/og-image.png\">"
```

```
[BLOCKER] line 37: twitter:image absolute URL points to preview
- old: "<meta name=\"twitter:image\" content=\"https://jro424.github.io/z3-safety-preview/assets/og-image.png\">"
- new: "<meta name=\"twitter:image\" content=\"https://z3safety.com/assets/og-image.png\">"
```

```
[BLOCKER] lines 63-193: every JSON-LD @id / url / item references jro424.github.io/z3-safety-preview/
- old (find all): "https://jro424.github.io/z3-safety-preview"
- new (replace all): "https://z3safety.com"
(15 occurrences in the <script type="application/ld+json"> block, lines 63, 66, 69, 73, 122, 123, 126, 133, 141, 149, 155, 158, 170, 176, 189–193)
```

```
[BLOCKER] sitemap.xml lines 5, 10, 15, 20, 25, 30: all URLs point to preview
- old (find all): "https://jro424.github.io/z3-safety-preview"
- new (replace all): "https://z3safety.com"
```

```
[BLOCKER] robots.txt line 35: sitemap URL points to preview
- old: "Sitemap: https://jro424.github.io/z3-safety-preview/sitemap.xml"
- new: "Sitemap: https://z3safety.com/sitemap.xml"
```

```
[BLOCKER] llms.txt line 19: service area still lists old (placeholder) territory
- old: "Central and Southern California: Santa Maria, Bakersfield, Central Valley, Greater Los Angeles, Orange County, Inland Empire, San Diego, Border Region."
- new: "Central and Southern California. Primary hubs: Orange County and Coachella Valley. Full coverage: Santa Barbara, Ventura, Los Angeles, Inland Empire, San Diego."
```

---

## MAJOR

```
[MAJOR] llms.txt line 13: stale employee range (was placeholder)
- old: "- California industrial operations with 100–400 employees (sweet spot: 100–200)"
- new: "- California industrial operations from 25+ employees up"
```

```
[MAJOR] llms.txt line 72: AHA Certified should be Red Cross Certified
- old: "- AHA Certified — American Heart Association CPR/AED"
- new: "- Red Cross Certified — American Red Cross CPR/AED"
```

```
[MAJOR] llms.txt line 89: keyword line references Bakersfield / Santa Maria (removed cities)
- old: "California workplace safety, Cal/OSHA Title 8 partner, AED compliance California, AED inspection program, CPR AED training Bakersfield, CPR AED training Santa Maria, first aid cabinet service California, OSHA 10 training onsite, OSHA 30 training onsite, workplace safety vendor consolidation, audit-ready safety documentation, mid-size California safety partner, integrated workplace safety provider, industrial safety company California."
- new: "California workplace safety, Cal/OSHA Title 8 partner, AED compliance California, AED inspection program, CPR AED training Orange County, CPR AED training Coachella Valley, first aid cabinet service California, OSHA 10 training onsite, OSHA 30 training onsite, workplace safety vendor consolidation, audit-ready safety documentation, mid-size California safety partner, integrated workplace safety provider, industrial safety company California."
```

```
[MAJOR] index.html line 17: geo.placename is OK but coordinates on line 18/19 are Bakersfield (35.3733, -119.0187)
- old: "  <meta name=\"geo.position\" content=\"35.3733;-119.0187\">\n  <meta name=\"ICBM\" content=\"35.3733, -119.0187\">"
- new: "  <meta name=\"geo.position\" content=\"33.7175;-117.8311\">\n  <meta name=\"ICBM\" content=\"33.7175, -117.8311\">"
  (33.7175,-117.8311 = Huntington Beach, the listed HQ city; consider Orange County centroid 33.7175;-117.8311 or Santa Ana 33.7455;-117.8677)
```

```
[MAJOR] index.html line 79: JSON-LD address is missing streetAddress / addressLocality / postalCode
- old: "        \"address\": {\"@type\": \"PostalAddress\", \"addressRegion\": \"CA\", \"addressCountry\": \"US\"},"
- new: "        \"address\": {\"@type\": \"PostalAddress\", \"streetAddress\": \"5152 Bolsa Ave Suite 102\", \"addressLocality\": \"Huntington Beach\", \"addressRegion\": \"CA\", \"postalCode\": \"92649\", \"addressCountry\": \"US\"},"
```

```
[MAJOR] index.html line 74: JSON-LD description still says "Central and Southern California" (matches map copy but doesn't surface primary hubs)
- old: "        \"description\": \"California workplace safety operator. Practical safety systems for industrial operations — training, AED compliance, first aid, PPE, and ongoing support. Cal/OSHA Title 8 expertise. Serving Central and Southern California.\","
- new: "        \"description\": \"California workplace safety operator. Practical safety systems for industrial operations — training, AED compliance, first aid, PPE, and ongoing support. Cal/OSHA Title 8 expertise. Primary hubs in Orange County and Coachella Valley, with full coverage across Santa Barbara, Ventura, Los Angeles, Inland Empire, and San Diego.\","
```

```
[MAJOR] index.html line 9: meta description says "and Southern California" (vague). Spec wants explicit areas.
- old: "  <meta name=\"description\" content=\"Z3 Safety builds practical workplace safety systems for California operations — Red Cross-certified training, AED compliance, First Aid, PPE, and ongoing support. Cal/OSHA Title 8 expertise. Same-day response. Serving Orange County, Coachella Valley, and Southern California.\">"
- new: "  <meta name=\"description\" content=\"Z3 Safety builds practical workplace safety systems for California operations — Red Cross-certified training, AED compliance, First Aid, PPE, and ongoing support. Cal/OSHA Title 8 expertise. Same-day response. Primary hubs in Orange County and Coachella Valley; full coverage across Santa Barbara, Ventura, Los Angeles, Inland Empire, and San Diego.\">"
```

```
[MAJOR] index.html line 3145: employee dropdown still uses old 100–200 / 200–400 / 400+ ranges (spec says 25+)
- old: "        <div class=\"form-group\"><label>Number of Employees</label><select><option value=\"\">Select range</option><option>Under 100</option><option>100–200</option><option>200–400</option><option>400+</option></select></div>"
- new: "        <div class=\"form-group\"><label>Number of Employees</label><select><option value=\"\">Select range</option><option>25–100</option><option>100–250</option><option>250–500</option><option>500+</option></select></div>"
```

```
[MAJOR] index.html line 27: og:description uses "first aid" lowercase, body uses "First Aid"
- old: "  <meta property=\"og:description\" content=\"We don't train for compliance. We train for reality. Practical safety systems for California operations — training, AEDs, first aid, and readiness support, delivered on-site.\">"
- new: "  <meta property=\"og:description\" content=\"We don't train for compliance. We train for reality. Practical safety systems for California operations — training, AEDs, First Aid, and readiness support, delivered on-site.\">"
```

```
[MAJOR] index.html line 74: JSON-LD description uses "first aid" lowercase
- old: "training, AED compliance, first aid, PPE, and ongoing support"
- new: "training, AED compliance, First Aid, PPE, and ongoing support"
```

```
[MAJOR] index.html line 108: knowsAbout uses "First aid program management" (sentence case ok in list, but inconsistent with brand capitalization elsewhere)
- old: "          \"OSHA 10 and OSHA 30 training\", \"First aid program management\", \"Workplace audit preparation\","
- new: "          \"OSHA 10 and OSHA 30 training\", \"First Aid program management\", \"Workplace audit preparation\","
```

```
[MAJOR] index.html line 2230: services section subhead lowercases "First aid"
- old: "      <p class=\"section-sub\">Training. AEDs. First aid. PPE. Ongoing safety support.</p>"
- new: "      <p class=\"section-sub\">Training. AEDs. First Aid. PPE. Ongoing safety support.</p>"
```

```
[MAJOR] index.html line 2242: services card link lowercases "First aid service"
- old: "          <span class=\"service-card-link\">First aid service →</span>"
- new: "          <span class=\"service-card-link\">First Aid Service →</span>"
```

```
[MAJOR] index.html line 2182: pain-card lowercases "First aid"
- old: "          <div class=\"pain-card-q\">\"First aid cabinets get refilled when someone notices something's missing.\"</div>"
- new: "          <div class=\"pain-card-q\">\"First Aid cabinets get refilled when someone notices something's missing.\"</div>"
```

```
[MAJOR] index.html line 3149: form dropdown uses "First aid supply management" (lowercase)
- old: "<option>First aid supply management</option>"
- new: "<option>First Aid supply management</option>"
```

```
[MAJOR] index.html line 3094: contact lead lowercases "first aid"
- old: "      <p>Start with a site walkthrough. We come to your facility, look at training records, AEDs, first aid cabinets, and documentation. Give you a clear picture of where you stand. No pressure. No upsell.</p>"
- new: "      <p>Start with a site walkthrough. We come to your facility, look at training records, AEDs, First Aid cabinets, and documentation. Give you a clear picture of where you stand. No pressure. No upsell.</p>"
```

```
[MAJOR] index.html line 2350: process step lowercases "First Aid cabinets" (currently capitalized but verify after upstream changes)
  (already correct — flagged only because surrounding sentences vary; no change required)
```

```
[MAJOR] llms.txt line 3: lowercases "first aid"
- old: "> Z3 Safety is a California workplace safety operator serving mid-size industrial operations across Central and Southern California. We build practical safety infrastructure — training, AED compliance, first aid, PPE, and ongoing operational support — for employers who need real readiness, not paper compliance."
- new: "> Z3 Safety is a California workplace safety operator serving mid-size industrial operations across Central and Southern California. We build practical safety infrastructure — training, AED compliance, First Aid, PPE, and ongoing operational support — for employers who need real readiness, not paper compliance."
```

```
[MAJOR] index.html line 3110: "Service Territory" still says "Central & Southern California" — fine, but inconsistent with header `Central and Southern` elsewhere
- old: "          <div><div class=\"contact-detail-label\">Service Territory</div><div class=\"contact-detail-value\">Central &amp; Southern California</div></div>"
- new: "          <div><div class=\"contact-detail-label\">Service Territory</div><div class=\"contact-detail-value\">Central and Southern California</div></div>"
```

```
[MAJOR] index.html line 3167: contact footer cert "Central & Southern California" — same inconsistency
- old: "    <div class=\"footer-bottom\"><div class=\"footer-copy\">© 2026 Z3 Safety</div><div class=\"footer-cert\">Central &amp; Southern California</div></div>"
- new: "    <div class=\"footer-bottom\"><div class=\"footer-copy\">© 2026 Z3 Safety</div><div class=\"footer-cert\">Central and Southern California</div></div>"
```

---

## MINOR

```
[MINOR] index.html line 2017: photography brief comment still references Bakersfield / Santa Maria
- old: "  - Bakersfield / Santa Maria / industrial CA setting preferred"
- new: "  - Orange County / Coachella Valley / industrial CA setting preferred"
```

```
[MINOR] index.html line 3137: form last name placeholder "Hernandez" — generic placeholder OK, but flagged because spec asked to check for placeholder names. Not removing — it's a form placeholder, not orphan content.
  (no change required, but verify with Jon)
```

```
[MINOR] index.html line 3136: form first name placeholder "Maria" — same as above
  (no change required, but verify with Jon)
```

```
[MINOR] index.html lines 2553-2560: Coverage map shows "Mexican Border" — Extended coverage. Not in the spec's allowed area list.
- old: "            <div class=\"coverage-area-item\"><div class=\"dot\"></div><div><span>Mexican Border</span><small>Extended coverage</small></div></div>"
- new: (delete this line, OR keep — Jon's call)
```

```
[MINOR] index.html line 3123: contact page service tag "Mexican Border"
- old: "          <span class=\"service-tag\">Mexican Border</span>"
- new: (delete OR keep — match decision from line 2560)
```

```
[MINOR] index.html lines 183, 2921: FAQ answer says "extending to the Mexican border" — match decision from above
- old: "Central and Southern California, extending to the Mexican border. Primary hubs in Orange County and Coachella Valley, with full coverage across Santa Barbara, Ventura, Los Angeles, Inland Empire, and San Diego."
- new: "Primary hubs in Orange County and Coachella Valley, with full coverage across Santa Barbara, Ventura, Los Angeles, Inland Empire, and San Diego."
```

```
[MINOR] index.html line 2553-2557 / footer line 2620: coverage map and footer Service Area column omit Santa Barbara and Ventura, but the FAQ and JSON-LD include them. Footers vary across pages:
  - Home footer (line 2618-2622): Orange County, Coachella Valley, Los Angeles, Inland Empire, San Diego (missing SB, Ventura)
  - Services footer (line 2783): Orange County, Coachella Valley, Los Angeles, San Diego (missing SB, Ventura, IE)
  - AED footer (line 2943): Orange County, Coachella Valley, Greater Southern CA
  - About footer (line 3079): Orange County, Coachella Valley, Greater Southern CA
- recommendation: standardize all four footers to the same five-area list (OC, Coachella, LA, IE, San Diego — or add SB + Ventura).
```

```
[MINOR] index.html line 3142: phone placeholder "(805) 555-0000" — 805 is Santa Barbara/Ventura area code, fine
  (no change required)
```

```
[MINOR] README.md line 13: robots.txt description says "(AI bots explicitly allowed)" — accurate
  (no change required)
```

```
[MINOR] README.md is missing the company address that the spec calls for
- old: (line 5) "**Live:** https://jro424.github.io/z3-safety-preview/"
- new: "**Live:** https://jro424.github.io/z3-safety-preview/\n\n**Office:** 5152 Bolsa Ave Suite 102, Huntington Beach, CA 92649\n**Phone:** 714.394.4070\n**Email:** sales@z3safety.com"
```

```
[MINOR] llms.txt is missing the company address
- old: (line 80) "- Response time: Same business day"
- new: "- Address: 5152 Bolsa Ave Suite 102, Huntington Beach, CA 92649\n- Response time: Same business day"
```

```
[MINOR] index.html lines 2479-2483 review card by "CC" includes company name "Waterfront Restaurant · Huntington Beach" — this is the anonymization handled per spec; OK
  (no change required)
```

```
[MINOR] index.html lines 2611-2612: footer has buttons "Lunch & Learn" and "New Business Program" with no onclick handler — dead buttons
- old: "          <li><button class=\"footer-link\">Lunch &amp; Learn</button></li>\n          <li><button class=\"footer-link\">New Business Program</button></li>"
- new: "          <li><button class=\"footer-link\" onclick=\"showPage('services')\">Lunch &amp; Learn</button></li>\n          <li><button class=\"footer-link\" onclick=\"showPage('services')\">New Business Program</button></li>"
```

```
[MINOR] index.html lines 2618-2622, 2783, 2943, 3079: every footer Service Area / Areas Served column is `<button>` with no onclick handler — buttons that do nothing
- recommendation: either make them onclick="showPage('contact')" or convert to `<span>` so they don't read as interactive
```

```
[MINOR] index.html line 3165: footer phone and email as `<button>` with no handler
- old: "      <div><div class=\"footer-col-title\">Contact</div><ul class=\"footer-links\"><li><button class=\"footer-link\">714.394.4070</button></li><li><button class=\"footer-link\">sales@z3safety.com</button></li></ul></div>"
- new: "      <div><div class=\"footer-col-title\">Contact</div><ul class=\"footer-links\"><li><a class=\"footer-link\" href=\"tel:7143944070\">714.394.4070</a></li><li><a class=\"footer-link\" href=\"mailto:sales@z3safety.com\">sales@z3safety.com</a></li></ul></div>"
```

```
[MINOR] index.html line 2628: footer cert text uses · separator, others use the same — consistent
  (no change required)
```

```
[MINOR] index.html line 3203: window.scrollTo(0, 0) in showPage — fine
  (no change required)
```

---

## Things Jon needs to decide (not mechanical)

1. **Canonical URL.** Spec says z3safety.com, but every URL in head / JSON-LD / sitemap / robots is the GitHub Pages preview URL. Need explicit go-ahead before swapping (affects ~15 lines in index.html plus sitemap.xml plus robots.txt).
2. **"Mexican Border" inclusion.** Listed in coverage map, contact-page tag, FAQ answer, but not in spec's allowed area list. Keep or kill?
3. **Footer Service Area columns.** Inconsistent across the 4 pages (5 items, 4 items, "Greater Southern CA"). Recommend standardizing to 5: OC, Coachella, LA, IE, San Diego (matching the home footer) — but Santa Barbara and Ventura appear in JSON-LD and FAQ, so could justify 7.
4. **Form placeholder names** ("Maria", "Hernandez"). These are HTML input placeholders, not orphan content — but spec asked to check for placeholder names. Fine to leave as form placeholders; flag for awareness.
5. **Dead footer buttons** ("Lunch & Learn", "New Business Program", all service-area items, contact phone/email as buttons). Either wire them up to `showPage('services')` / tel: / mailto:, or convert to non-interactive.
6. **Capitalization of "First Aid"**. Going through the file, ~6 places use lowercase "first aid" and ~12 use "First Aid". Pick a rule (recommend "First Aid" when referring to the service line/product/cabinet; lowercase only in mid-sentence colloquial usage). Spec rule: "First Aid" capitalized as a service/brand term. Going with capitalize.

---

## Verified clean

- All JSON-LD parses as valid JSON (Python json.loads succeeds).
- All 5 `id="page-home"`, `id="page-services"`, `id="page-aed"`, `id="page-about"`, `id="page-contact"` elements exist.
- All 39 `showPage()` calls reference one of those 5 IDs.
- `tel:7143944070` and `mailto:sales@z3safety.com` are formatted correctly.
- Phone `714.394.4070` is identical across all 14 occurrences in index.html and llms.txt.
- Email `sales@z3safety.com` is identical across all 7 occurrences.
- Review count is consistent: JSON-LD `"reviewCount": "95"` (line 115), rating-text "Based on 95 verified Google Reviews" (line 2428), README "5.0 / 95 reviews" (line 24), llms.txt "95 verified reviews" (line 85).
- No placeholder names from the old set (Marcus, Lisa D, Tony C, Karen B, Eduardo M, Jennifer S, Daniel R, Maria H, Brett K, Connie P) appear anywhere.
- No "AHA Certified" in index.html (only stale reference is in llms.txt line 72 — flagged above).
- No "TODO / TBD / FIXME / lorem" in any content file (only "placeholder" appears in HTML comments and as HTML `placeholder=` attributes, all legitimate).
- `assets/og-image.png` exists in the assets folder and is referenced consistently.
- `assets/z3-logo.png` exists and is referenced 7 times in index.html (favicon, apple-touch-icon, JSON-LD logo, nav, hero, and 4 footer logos).
- robots.txt has `Allow: /` for all major crawlers, no Disallow lines.
- Smart-quotes vs straight-quotes: site uses straight apostrophes (`don't`, `can't`) consistently. No mixing.
- Em-dash usage: all `—` (proper em-dash). No `--` found anywhere in body text.
- Cal/OSHA spelling: consistent — no `CalOSHA` or `Cal-OSHA` variants anywhere.
- "AED Vantage" capitalization: consistent throughout (no `AED vantage`).
- "Z3 Safety" capitalization: consistent throughout (no `Z3 safety`).
- "Red Cross Certified": appears in index.html (lines 2107, 2628, 3055 area) — consistent; only llms.txt line 72 still has the old "AHA Certified" (flagged above).
