# Z3 Safety — Accessibility Audit (WCAG 2.1 Level AA)

**File audited:** `/Users/jromac/JRO's Docs/Unimatrix 01/02_projects/active/Z3 Safety/index.html`
**Auditor:** Claude / specialist agent
**Date:** 2026-05-28
**Scope:** Single-file static HTML (~3,224 lines), 5 logical pages controlled by JS page-switching, deployed to GitHub Pages + Wix embed.

---

## Summary

| Severity | Count | Issues |
|---|---|---|
| **BLOCKER** | 6 | Focus indicator fails contrast; form has no `<form>`/no `for`/`id` pairing; placeholder-as-label pattern; review stars/raw gold below 4.5:1 contrast; footer copy/cert below 4.5:1 contrast (2.56–3.75:1); multiple H2→H4 heading skips. |
| **MAJOR** | 8 | 46 icon SVGs missing `aria-hidden="true"`; duplicate footer `<footer>` in single-file (a11y tree noise but acceptable); footer dead-link buttons have no `onclick` and no `disabled`; form submit lacks `type="button"`, all page-switch `<button>`s lack `type="button"`; tooltip relies on `:hover` (touch users locked out); FAQ `<div>` used as button instead of `<button>` with no role/keyboard handling; mobile nav-toggle `aria-expanded` is set with non-string boolean in JS; hero-trust-item `✓` check is `::before` content (not screen-reader-friendly outside icon font). |
| **MINOR** | 7 | `.skip-link` background uses undefined `--gold` variable position fine; nav-cta border color uses fixed color outside palette; the `:focus` outline removed in CSS line 212 without ensuring `:focus-visible` covers form fields (form fields explicitly clear it); reviews marquee uses `aria-label="Google"` on decorative-context SVGs (10 duplicates inside review cards); no language switching attribute on inline `em` accents; CSS `<br>` linebreaks in headings affect reading flow for AT; missing visible required-field indicator (since no required fields are declared at all). |

**Ship blocker verdict:** YES — do not ship. The form alone (no labels associated, no `<form>` tag, placeholder-only input affordance, submit triggers `alert()` only) is non-compliant for WCAG 1.3.1 / 3.3.2 / 4.1.2 and will fail any reasonable accessibility audit. Focus-indicator contrast is also load-bearing for keyboard users. These two issues plus heading-hierarchy and footer-text contrast are real blockers, not nits.

---

## Detailed Findings

### BLOCKER-1 — Focus indicator gold-bright fails 3:1 contrast on light backgrounds
**Lines:** 213–221
**WCAG:** 2.4.11 Focus Appearance (AA in WCAG 2.2), 1.4.11 Non-text Contrast (AA in 2.1)
**Severity:** BLOCKER

`#E8B82E` (gold-bright) on white = **1.85:1**, on off-white = **1.71:1**. Below the 3:1 minimum for focus indicators on white/cream sections, which is most of the body content (services, pillars, pain cards, FAQ, contact, etc.).

**Current state (line 213):**
```css
    :focus-visible {
      outline: 2px solid var(--gold-bright);
      outline-offset: 3px;
      border-radius: 3px;
    }
    button:focus-visible, a:focus-visible {
      outline: 2px solid var(--gold-bright);
      outline-offset: 3px;
    }
```

**Required fix:** Use a darker gold or paired outline that maintains 3:1 on both white and ink backgrounds. `--gold-deep` (#806208) gives 5.72:1 on white and 3.07:1 on ink (just barely passes). A two-color "ringed" outline is safest.
```css
    :focus-visible {
      outline: 2px solid var(--gold-deep);
      outline-offset: 3px;
      border-radius: 3px;
      box-shadow: 0 0 0 5px rgba(255,255,255,0.85);
    }
    button:focus-visible, a:focus-visible {
      outline: 2px solid var(--gold-deep);
      outline-offset: 3px;
      box-shadow: 0 0 0 5px rgba(255,255,255,0.85);
    }
    /* On dark surfaces, swap to gold-bright which already passes 10.43:1 on ink */
    nav button:focus-visible, .hero button:focus-visible,
    .page-hero button:focus-visible, .about-hero button:focus-visible,
    .aed-page-hero button:focus-visible, .cta-banner button:focus-visible,
    .why-ops-section button:focus-visible, footer button:focus-visible,
    footer a:focus-visible {
      outline-color: var(--gold-bright);
      box-shadow: 0 0 0 5px rgba(10,14,20,0.85);
    }
```

---

### BLOCKER-2 — Form fields have no programmatic label association
**Lines:** 3132–3153
**WCAG:** 1.3.1 Info and Relationships (A), 3.3.2 Labels or Instructions (A), 4.1.2 Name, Role, Value (A)
**Severity:** BLOCKER

Every `<label>` in the contact form is missing `for`, every `<input>/<select>/<textarea>` is missing `id`. There is no `<form>` element wrapping the fields. The submit button uses `onclick="alert(...)"` only.

**Current state (line 3132):**
```html
    <div class="contact-form">
      <h2>Schedule a Site Walkthrough</h2>
      <p class="form-sub">2 minutes to fill out. Same-day follow-up.</p>
      <div class="form-row">
        <div class="form-group"><label>First Name</label><input type="text" placeholder="Maria"></div>
        <div class="form-group"><label>Last Name</label><input type="text" placeholder="Hernandez"></div>
      </div>
      <div class="form-group"><label>Company</label><input type="text" placeholder="Acme Manufacturing Co."></div>
      <div class="form-row">
        <div class="form-group"><label>Work Email</label><input type="email" placeholder="you@company.com"></div>
        <div class="form-group"><label>Phone</label><input type="tel" placeholder="(805) 555-0000"></div>
      </div>
      <div class="form-row">
        <div class="form-group"><label>Number of Employees</label><select><option value="">Select range</option><option>Under 100</option><option>100–200</option><option>200–400</option><option>400+</option></select></div>
        <div class="form-group"><label>Primary Location</label><input type="text" placeholder="City, CA"></div>
      </div>
      <div class="form-group"><label>Your Role</label><select><option value="">Select your role</option><option>Safety Manager</option><option>HR Manager</option><option>Facilities Manager</option><option>Operations / GM</option><option>CFO / Controller</option><option>Owner / Executive</option></select></div>
      <div class="form-group"><label>Biggest safety concern right now</label><select><option value="">Select one</option><option>Training gaps / workforce readiness</option><option>AED compliance and device readiness</option><option>Audit preparation</option><option>Documentation and recordkeeping</option><option>First aid supply management</option><option>Vendor consolidation</option><option>Workers' comp / claim trends</option><option>General walkthrough</option></select></div>
      <div class="form-group"><label>Anything else we should know? (optional)</label><textarea placeholder="Tell us about your current setup, any recent incidents, upcoming audits, or specific challenges you're facing..."></textarea></div>
      <button class="form-submit" onclick="alert('Submitted! (Mockup — not connected to backend)')">Send to Z3 →</button>
      <p class="form-privacy">Confidential. Never shared. We'll be in touch the same business day.</p>
    </div>
```

**Required fix:** Wrap in `<form>`. Add `id` to every field and `for` to every label. Add `required` / `aria-required="true"` to required fields. Add an explicit `name` attribute so the form is actually submittable. Use `type="button"` on the submit until backend is connected (or change to `type="submit"` with a real `action`).

```html
    <form class="contact-form" action="#" method="post" novalidate aria-labelledby="contact-form-title">
      <h2 id="contact-form-title">Schedule a Site Walkthrough</h2>
      <p class="form-sub">2 minutes to fill out. Same-day follow-up. <span aria-hidden="true">*</span> Required.</p>
      <div class="form-row">
        <div class="form-group">
          <label for="cf-first">First Name <span aria-hidden="true">*</span></label>
          <input type="text" id="cf-first" name="first_name" placeholder="Maria" required aria-required="true" autocomplete="given-name">
        </div>
        <div class="form-group">
          <label for="cf-last">Last Name <span aria-hidden="true">*</span></label>
          <input type="text" id="cf-last" name="last_name" placeholder="Hernandez" required aria-required="true" autocomplete="family-name">
        </div>
      </div>
      <div class="form-group">
        <label for="cf-company">Company <span aria-hidden="true">*</span></label>
        <input type="text" id="cf-company" name="company" placeholder="Acme Manufacturing Co." required aria-required="true" autocomplete="organization">
      </div>
      <div class="form-row">
        <div class="form-group">
          <label for="cf-email">Work Email <span aria-hidden="true">*</span></label>
          <input type="email" id="cf-email" name="email" placeholder="you@company.com" required aria-required="true" autocomplete="email">
        </div>
        <div class="form-group">
          <label for="cf-phone">Phone</label>
          <input type="tel" id="cf-phone" name="phone" placeholder="(805) 555-0000" autocomplete="tel">
        </div>
      </div>
      <div class="form-row">
        <div class="form-group">
          <label for="cf-employees">Number of Employees</label>
          <select id="cf-employees" name="employees">
            <option value="">Select range</option>
            <option>Under 100</option><option>100–200</option><option>200–400</option><option>400+</option>
          </select>
        </div>
        <div class="form-group">
          <label for="cf-location">Primary Location</label>
          <input type="text" id="cf-location" name="location" placeholder="City, CA" autocomplete="address-level2">
        </div>
      </div>
      <div class="form-group">
        <label for="cf-role">Your Role</label>
        <select id="cf-role" name="role">
          <option value="">Select your role</option>
          <option>Safety Manager</option><option>HR Manager</option><option>Facilities Manager</option>
          <option>Operations / GM</option><option>CFO / Controller</option><option>Owner / Executive</option>
        </select>
      </div>
      <div class="form-group">
        <label for="cf-concern">Biggest safety concern right now</label>
        <select id="cf-concern" name="concern">
          <option value="">Select one</option>
          <option>Training gaps / workforce readiness</option><option>AED compliance and device readiness</option>
          <option>Audit preparation</option><option>Documentation and recordkeeping</option>
          <option>First aid supply management</option><option>Vendor consolidation</option>
          <option>Workers' comp / claim trends</option><option>General walkthrough</option>
        </select>
      </div>
      <div class="form-group">
        <label for="cf-notes">Anything else we should know? (optional)</label>
        <textarea id="cf-notes" name="notes" placeholder="Tell us about your current setup, any recent incidents, upcoming audits, or specific challenges you're facing..."></textarea>
      </div>
      <button type="button" class="form-submit" onclick="alert('Submitted! (Mockup — not connected to backend)')">Send to Z3 →</button>
      <p class="form-privacy">Confidential. Never shared. We'll be in touch the same business day.</p>
    </form>
```

(Note: also change `<div class="contact-form">` opening tag and matching `</div>` to `<form>` / `</form>`.)

---

### BLOCKER-3 — Heading hierarchy skips levels (H2 → H4) in 5 sections
**WCAG:** 1.3.1 Info and Relationships (A); WCAG best-practice for AT navigation
**Severity:** BLOCKER (collectively)

The page uses `<h4>` directly under `<h2>` in multiple places, skipping `<h3>`. Screen-reader users navigating by heading lose structural context.

**Skips identified:**

1. **Home / AED block (line 2278 h2 → line 2286/2293/2300 h4)**
   - Line 2286: `<h4>Program 1 — Self-Inspection</h4>`
   - Line 2293: `<h4>Program 2 — On-Site Inspection</h4>`
   - Line 2300: `<h4>Both Programs Include</h4>`
   - **Fix:** change all three to `<h3>`.

2. **AED page / Included grid (line 2885 h2 → lines 2887–2890 h4)**
   - **Fix:** change `<h4>` to `<h3>` on those 4 items.

3. **About page hero (line 2964 h1 → line 2968 h3)**
   - Line 2968: `<h3>Z3 By the Numbers</h3>`
   - **Fix:** change `<h3>` to `<h2>`.

4. **About page mission-values (line 2983 h2 → lines 2992/2996/3000/3004 h4)**
   - **Fix:** change all four `<h4>` to `<h3>`.

5. **About page certifications (line 3052 h2 → lines 3054–3057 h4)**
   - **Fix:** change all four `<h4>` to `<h3>`.

6. **Contact page (line 3133 h2 → line 3114 h4 "Primary Service Area")**
   - Line 3114: `<h4>Primary Service Area</h4>` sits structurally under the page's h1, not an h2 directly, but visually parallel to the form h2. To be safe, change to `<h2>` matching the form.
   - **Fix:** change `<h4>` to `<h2>`.

**Edit examples** (apply each as a separate Edit):

```
old_string: <h4>Program 1 — Self-Inspection</h4>
new_string: <h3>Program 1 — Self-Inspection</h3>
```
(Repeat pattern for every flagged line above. Update CSS selectors `.aed-pill h4`, `.included-item h4`, `.mission-value h4`, `.cert-item h4`, `.service-area h4`, `.about-hero-card h3` to match new tag names.)

---

### BLOCKER-4 — Footer copyright/certification text fails 4.5:1 contrast
**Lines:** 541–545
**WCAG:** 1.4.3 Contrast (Minimum) AA
**Severity:** BLOCKER

`rgba(255,255,255,0.3)` over `--ink-deep` (#050810) = **2.56:1** (footer-copy). `rgba(255,255,255,0.4)` = **3.75:1** (footer-cert). Both fail 4.5:1 for normal text.

**Current state (line 541):**
```css
    .footer-copy { font-size: 12px; color: rgba(255,255,255,0.3); }
    .footer-cert {
      font-size: 12px; color: rgba(255,255,255,0.4);
      display: inline-flex; align-items: center; gap: 8px;
    }
```

**Required fix:** bump opacity to at least 0.6 (effective 7.29:1 on ink-deep, passes AAA).
```css
    .footer-copy { font-size: 12px; color: rgba(255,255,255,0.6); }
    .footer-cert {
      font-size: 12px; color: rgba(255,255,255,0.6);
      display: inline-flex; align-items: center; gap: 8px;
    }
```

---

### BLOCKER-5 — Review stars / pure gold `#D4A017` fails 4.5:1 on white
**Lines:** 1070–1071 (`.reviews-rating-stars-row .stars`), 1144 (`.review-stars`), 1071, 1151, 1156 (`review-text::before/::after` decorative quote marks)
**WCAG:** 1.4.3 Contrast (Minimum) AA
**Severity:** BLOCKER

`--gold` (#D4A017) on white = **2.38:1**. Used for the visible 5-star strings and accent quote marks on the review cards. These are decorative-but-informational: the star symbols communicate the rating to sighted users, but they are also text content that AT will read.

**Current state (line 1070):**
```css
    .reviews-rating-stars-row .stars {
      color: var(--gold); font-size: 15px; letter-spacing: 2px;
    }
```
and (line 1143):
```css
    .review-stars {
      color: var(--gold); font-size: 14px; letter-spacing: 2px;
    }
```

**Required fix:** Use `--gold-deep` (5.72:1 on white). Stars stay visually distinct from text.
```css
    .reviews-rating-stars-row .stars {
      color: var(--gold-deep); font-size: 15px; letter-spacing: 2px;
    }
```
```css
    .review-stars {
      color: var(--gold-deep); font-size: 14px; letter-spacing: 2px;
    }
```

Also: add `aria-label="5 out of 5 stars"` and `role="img"` to every `<div class="review-stars">★★★★★</div>` so screen readers don't read "black star black star…" five times. Wrap the `★` content in `<span aria-hidden="true">★★★★★</span>` inside the labeled container.

**Decorative quote marks in `.review-text::before`/`::after`** (lines 1150–1159) also use `--gold` on white — change to `--gold-deep` or use border-left accent. Same fix for `.reviews-rating-summary > svg`-adjacent stars.

---

### BLOCKER-6 — Page-switch buttons + footer dead-link buttons default to type="submit"
**Lines:** 2059, 2067, 2071–2075, 2102–2103, 2232, 2238, 2244, 2250, 2256, 2262, 2280, 2574, 2598–2612, 2735, 2770, 2783, 2853, 2875, 2930, 2941, 3066, 3077, 3151
**WCAG:** 4.1.2 Name, Role, Value (A); 3.2.5 Change on Request (AAA, best practice)
**Severity:** BLOCKER (functional)

A `<button>` without `type` attribute defaults to `type="submit"`. Inside the contact form, `<button class="form-submit" onclick="alert(…)">` will both fire the alert AND submit the form, causing page reload + scroll-jump. Even outside a form, this means screen readers may announce these as "submit button". All page-switch and footer buttons need `type="button"`.

**Required fix:** Add `type="button"` to **every** `<button>` element. Highest priority: the form-submit button (since it lives in the form once BLOCKER-2 is applied), and all `onclick="showPage(…)"` buttons.

Pattern:
```
old_string: <button class="nav-link active" onclick="showPage('home')" aria-current="page">Home</button>
new_string: <button type="button" class="nav-link active" onclick="showPage('home')" aria-current="page">Home</button>
```
Apply to all 30+ `<button class="…" onclick=` and all `<button class="footer-link">…</button>` occurrences.

Also: the dead-link footer buttons (`Lunch & Learn`, `New Business Program`, all area-name buttons in footer cols on every page — lines 2611, 2612, 2618–2622, 2783, 2943, 3079, 3165) have no `onclick` and do nothing. Either give them real routes or change them to `<span>` (not focusable) so keyboard users don't tab to dead buttons. Recommendation: convert to plain `<li>` text or `<a href="#">` stubs with `aria-disabled="true"` until real pages exist. The current state mis-signals affordance.

---

### MAJOR-1 — 46 decorative icon SVGs not hidden from AT
**Lines:** All `<svg class="ic"><use href="#i-…"/></svg>` elements
**WCAG:** 1.3.1 Info and Relationships (A); 4.1.2
**Severity:** MAJOR

Every icon SVG (`<svg class="ic">`) is purely decorative — the adjacent text says the same thing (Training, First Aid, etc.). Without `aria-hidden="true"`, screen readers may announce "image" 46+ times during a page traversal.

**Required fix:** Add `aria-hidden="true"` to the `<svg class="ic">` selector globally. Easiest path: a single Edit using `replace_all`.

```
old_string: <svg class="ic">
new_string: <svg class="ic" aria-hidden="true" focusable="false">
```
`replace_all: true`

Apply the same to inline SVGs with explicit width (e.g., line 2547 `<svg class="ic" style="width:40px;height:40px">` and line 2591/2592/2779 footer-icon `<svg class="ic" style="width:14px;height:14px;…">`) — these need the same `aria-hidden="true" focusable="false"`. Do a manual Edit for each since `replace_all` won't catch the inline-style variants.

Also: the 10 Google logo `<svg class="review-google-icon" …>` elements inside each review card (lines 2440, 2450, 2460, 2470, 2480, 2490, 2500, 2510, 2520, 2530) currently have **no aria attribute**. They are decorative (the review card already says "Verified Google Review"). Add `aria-hidden="true"` to each.

The summary Google SVG on line 2417 has `aria-label="Google"` — that's fine because it's the primary source-attribution affordance.

---

### MAJOR-2 — FAQ accordion uses `<div>` with onclick, not `<button>`, no keyboard support
**Lines:** 2900, 2904, 2908, 2912, 2916, 2920 (`.faq-q`)
**WCAG:** 2.1.1 Keyboard (A); 4.1.2 Name, Role, Value (A)
**Severity:** MAJOR

`<div class="faq-q" onclick="toggleFaq(this)">…</div>` is not keyboard-focusable, has no `role="button"`, and `toggleFaq` adds `aria-expanded` only on first call. Screen readers and keyboard-only users cannot operate the FAQ.

**Required fix:** Convert to `<button>` and add `aria-controls`/`aria-expanded` initial state.

Example for one item (apply pattern to all six):
```
old_string: <div class="faq-q" onclick="toggleFaq(this)"><span>How often are AEDs required to be inspected in California?</span><div class="faq-q-icon">+</div></div>
        <div class="faq-a">California Health &amp; Safety Code requires monthly AED inspections. Both AED Vantage programs are built around that monthly cadence — ensuring you meet the requirement every month, with documentation to prove it.</div>

new_string: <button type="button" class="faq-q" aria-expanded="false" aria-controls="faq-a-1" onclick="toggleFaq(this)"><span>How often are AEDs required to be inspected in California?</span><span class="faq-q-icon" aria-hidden="true">+</span></button>
        <div class="faq-a" id="faq-a-1" role="region">California Health &amp; Safety Code requires monthly AED inspections. Both AED Vantage programs are built around that monthly cadence — ensuring you meet the requirement every month, with documentation to prove it.</div>
```

Repeat with `id="faq-a-2"` … `faq-a-6"`. Also update CSS so `.faq-q` button styling matches the previous div (already mostly compatible). Update the JS `toggleFaq(el)` — it already calls `el.setAttribute('aria-expanded', !isOpen)` but the value `!isOpen` is a JS boolean. Convert to string:
```js
function toggleFaq(el) {
  const isOpen = el.classList.contains('open');
  el.classList.toggle('open');
  el.nextElementSibling.classList.toggle('open');
  el.setAttribute('aria-expanded', String(!isOpen));
}
```

---

### MAJOR-3 — Mobile nav-toggle aria-expanded set with non-string boolean
**Lines:** 3208–3211
**WCAG:** 4.1.2 Name, Role, Value (A)
**Severity:** MAJOR

`toggle.setAttribute('aria-expanded', open)` — `open` is a `boolean`. Browsers coerce to "true"/"false" strings but spec-compliant ATs treat the attribute as exactly the serialized value, which is correct only by accident. Fix explicitly.

**Current state (line 3208):**
```js
    toggle?.addEventListener('click', () => {
      const open = navLinks.classList.toggle('open');
      toggle.setAttribute('aria-expanded', open);
    });
```

**Required fix:**
```js
    toggle?.addEventListener('click', () => {
      const open = navLinks.classList.toggle('open');
      toggle.setAttribute('aria-expanded', String(open));
    });
```

---

### MAJOR-4 — Tooltip on `.tooltip` is hover-only (no touch / tap-to-toggle)
**Lines:** 2146 (the only tooltip-bearing element, "Cal/OSHA Title 8")
**WCAG:** 1.4.13 Content on Hover or Focus (AA); 2.5.1 Pointer Gestures (A)
**Severity:** MAJOR

The element has `tabindex="0"` and the CSS includes `:focus` selectors, so keyboard users can trigger it. Good. BUT it cannot be **dismissed** without moving focus away — fails 1.4.13's "dismissable" requirement. Touch users can't trigger it at all (no `:active` rule, no JS toggle).

**Required fix:** Add a tap-to-toggle JS handler that sets/clears an `.open` class, and let Escape close it.

Add to CSS (line 1823 area):
```css
    .tooltip.open::after, .tooltip.open::before { opacity: 1; transform: translateX(-50%) translateY(0); }
```
Add to JS (inside DOMContentLoaded block, around line 3221):
```js
    document.querySelectorAll('.tooltip').forEach(t => {
      t.addEventListener('click', e => { e.preventDefault(); t.classList.toggle('open'); });
      t.addEventListener('keydown', e => {
        if (e.key === 'Escape') t.classList.remove('open');
      });
    });
```

---

### MAJOR-5 — Service cards trigger nav with `onclick` on `<div>` (not keyboard-accessible)
**Lines:** 2232, 2238, 2244, 2250, 2256, 2262 (`.service-card` on home page)
**WCAG:** 2.1.1 Keyboard (A); 4.1.2
**Severity:** MAJOR

`<div class="service-card" onclick="showPage('services')">` — the entire card is clickable for mice but invisible to keyboard users. The inner `<span class="service-card-link">Training programs →</span>` is a `<span>`, not a link.

**Required fix:** Wrap each card content in a `<button type="button">` or `<a href="#services">` covering the card area, OR add `role="link" tabindex="0"` plus a keydown handler (`Enter`/`Space`) to the card. Cleanest path: convert each `.service-card` opening `<div>` to `<a href="#" role="button" onclick="showPage('services'); return false;" class="service-card">` — but maintain styling. Alternatively, convert the inner `<span class="service-card-link">` into a real `<a>` and remove `onclick` from the card.

Recommended (cleanest): make the link a real `<button>` and remove card-level onclick:
```
old_string: <div class="service-card" onclick="showPage('services')">
          <div class="service-card-icon"><svg class="ic"><use href="#i-cap"/></svg></div>
          <h3>Safety Training</h3>
          <p>On-site Red Cross-certified CPR, AED, and First Aid training plus OSHA 10/30 and industry-specific certifications. Delivered at your facility. Records you can find when you need them.</p>
          <span class="service-card-link">Training programs →</span>
        </div>

new_string: <div class="service-card">
          <div class="service-card-icon"><svg class="ic" aria-hidden="true" focusable="false"><use href="#i-cap"/></svg></div>
          <h3>Safety Training</h3>
          <p>On-site Red Cross-certified CPR, AED, and First Aid training plus OSHA 10/30 and industry-specific certifications. Delivered at your facility. Records you can find when you need them.</p>
          <button type="button" class="service-card-link" onclick="showPage('services')">Training programs →</button>
        </div>
```
Repeat for all six cards. Style `.service-card-link` to look the same as `<button>` (`background: none; border: none; padding: 0;`).

---

### MAJOR-6 — `hero-visual` uses `role="img"` but contains an `<img>` already
**Lines:** 2113–2117
**WCAG:** 4.1.2 (A)
**Severity:** MAJOR

```html
<div class="hero-visual" role="img" aria-label="Z3 Safety shield logo">
  <div class="hero-shield-stage">
    <img src="assets/z3-logo.png" alt="Z3 Safety — California workplace safety operator" class="hero-logo-img" …>
```
Wrapping an `<img>` (which has its own alt text) inside a `role="img"` container hides the inner `<img>` from AT. The container's `aria-label` overrides the image alt, producing redundant/conflicting announcement.

**Required fix:** Remove `role="img"` and `aria-label` from the wrapper. The inner `<img alt="...">` already does the job.
```
old_string: <div class="hero-visual" role="img" aria-label="Z3 Safety shield logo">
new_string: <div class="hero-visual">
```

---

### MAJOR-7 — `nav-logo` button uses empty `alt=""` on its image, label only on button
**Lines:** 2059–2066
**WCAG:** Fine as-is for AT, but the inner fallback `<div>` (injected via `onerror`) contains visible text "Z3" with no AT consideration.
**Severity:** MAJOR (edge case for slow networks)

The button has `aria-label="Z3 Safety — Home"` which is correct. The `<img alt="" aria-hidden="true">` is correct for a button-wrapped logo where the button has its own label. **But:** if the image fails (e.g. blocked by ad-blockers or slow CDN), the `onerror` injects HTML containing visible "Z3" but no AT compensation. Not a blocker, but a fragility.

**Optional fix:** Make the `onerror` HTML include `aria-hidden="true"` on the fallback div to keep the button label authoritative.

---

### MAJOR-8 — Skip-link refers to `#main-content` correctly, but the destination `<main id="main-content">` has no `tabindex` for programmatic focus
**Lines:** 2055, 2079
**WCAG:** 2.4.1 Bypass Blocks (A)
**Severity:** MINOR-to-MAJOR (works in most browsers but not reliably in Safari/iOS)

Adding `tabindex="-1"` makes focus jump explicit.

**Required fix:**
```
old_string: <main id="main-content">
new_string: <main id="main-content" tabindex="-1">
```

---

### MINOR-1 — Placeholder text colors not contrast-tested
**Lines:** Form inputs 3136–3150
**WCAG:** 1.4.3
**Severity:** MINOR

Default browser placeholder ~54% opacity of input text color. On `#111418` over white, that's ~8.5:1 — passes. But with input border `--gray-border` and bg white, the input affordance itself has only ~1.3:1 against the page (white-on-white) — invisible until focus. Since you've removed `:focus-visible` on form fields (line 222), the only focus signal is `border-color: var(--gold)` (line 1754). Gold #D4A017 on white = 2.38:1 — fails 3:1 for UI components.

**Required fix:** Use `--gold-deep` for input focus border (5.72:1) instead of `--gold`.
```css
    .form-group input:focus, .form-group select:focus, .form-group textarea:focus {
      outline: none; border-color: var(--gold-deep);
      box-shadow: 0 0 0 3px rgba(128,98,8,0.18);
    }
```

---

### MINOR-2 — `<br>` in headings affects AT reading flow
**Lines:** 2099, 2162, 2203, 2229, 2278, 2322, 2344, 2375, 2414, 2543, 2641, 2798, 2807, 2831, 2885, 2964, 3093 and others
**WCAG:** Best practice, not a strict failure
**Severity:** MINOR

Headings like `<h1>We Don't Train for Compliance.<br>We Train for <em>Reality.</em></h1>` cause some screen readers to pause oddly. Not a strict violation, but consider visual line breaks via CSS `display: block` on a wrapping span instead. **Skip unless time permits.**

---

### MINOR-3 — Hero-trust-item checkmarks via `::before` `content: '✓'`
**Lines:** 626–629
**WCAG:** 1.3.1 — text-via-CSS is announced by most modern AT, but Safari+VoiceOver historically drops it.
**Severity:** MINOR

The `✓` is decorative reinforcement of the adjacent text. Acceptable but inconsistent with other icons (which are SVGs). **No fix required**.

---

### MINOR-4 — Tap target spacing on mobile nav
**Lines:** 1877–1882 (mobile nav-link)
**WCAG:** 2.5.5 Target Size (AAA, not AA strictly)
**Severity:** MINOR

Each mobile `.nav-link` has `padding: 14px 16px; font-size: 15px;` — that yields a ~47px tall hit area with no inter-item gap. Spec is "spacing or 24×24px gap" (WCAG 2.2 AA 2.5.8). Current is borderline. **Fix optional:** add 4px gap.
```css
      .nav-links { gap: 4px; }
```

---

### MINOR-5 — Service-card hover-only styling for "active" state
**Lines:** 837 `.service-card-link`
**WCAG:** Best practice
**Severity:** MINOR

The `→` arrow only animates on hover via `.service-card:hover .service-card-link { gap: 10px }`. Keyboard focus doesn't trigger this. Add:
```css
    .service-card:focus-within .service-card-link { gap: 10px; }
```

---

### MINOR-6 — Page title / meta description per "page" not switchable
**Lines:** 8, 9
**WCAG:** 2.4.2 Page Titled (A)
**Severity:** MINOR (acknowledged limitation per the audit scope)

Since the 5 logical pages share one `<title>`, AT announces the same title on each "page" switch. JS-driven SPAs ideally update `document.title` on route change. **Flag only.** Recommended (not required by you): in `showPage()` set `document.title` per page:
```js
  const titles = {
    home: "Z3 Safety — Practical Workplace Safety for California Operations",
    services: "Services — Z3 Safety",
    aed: "AED Vantage — Z3 Safety",
    about: "About — Z3 Safety",
    contact: "Contact — Z3 Safety"
  };
  if (titles[page]) document.title = titles[page];
```

---

### MINOR-7 — `<html lang="en-US">` is good, but inline `<em>` accents inside Spanish-style names (e.g. "Maria", "Hernandez", "Hunter Casey") not language-tagged
**Severity:** MINOR / ignorable. No fix needed.

---

## Color Contrast Reference Table (every pair tested)

All ratios computed against the listed background. **FAIL** = below threshold for that text size class.

### Light backgrounds

| Foreground | Background | Ratio | Threshold | Status |
|---|---|---:|---:|---|
| `--text` #111418 | #FFFFFF | 18.47:1 | 4.5 | PASS AAA |
| `--text-light` #4F5763 | #FFFFFF | 7.30:1 | 4.5 | PASS AAA |
| `--text-mid` #2A3240 | #FFFFFF | 12.89:1 | 4.5 | PASS AAA |
| `--gold-deep` #806208 | #FFFFFF | 5.72:1 | 4.5 | PASS AA |
| **`--gold` #D4A017** | #FFFFFF | **2.38:1** | 4.5 | **FAIL** |
| `--gold` (large 18pt+) | #FFFFFF | 2.38:1 | 3.0 | **FAIL** |
| `--text` | `--off-white` #F5F6F8 | 17.08:1 | 4.5 | PASS AAA |
| `--text-light` | `--off-white` | 6.75:1 | 4.5 | PASS AA |
| `--text-mid` | `--off-white` | 11.92:1 | 4.5 | PASS AAA |
| `--gold-deep` | `--off-white` | 5.29:1 | 4.5 | PASS AA |
| `--ink` #0A0E14 | `--off-white` | 17.89:1 | 4.5 | PASS AAA |
| `--ink-deep` | `--gold` (button) | 8.43:1 | 4.5 | PASS AAA |
| `--ink-deep` | `--gold-bright` (button hover) | 10.80:1 | 4.5 | PASS AAA |
| **white** | `--gold` | **2.38:1** | 4.5 | **FAIL** |
| **white** | `--gold-bright` | **1.85:1** | 4.5 | **FAIL** |
| `--ink` | `--gold-pale` #FBF4DE | ~17:1 | 4.5 | PASS AAA |

### Dark backgrounds (over `--ink` #0A0E14)

| Foreground | Background | Ratio | Threshold | Status |
|---|---|---:|---:|---|
| white | `--ink` | 19.34:1 | 4.5 | PASS AAA |
| `--gold-bright` #E8B82E | `--ink` | 10.43:1 | 4.5 | PASS AAA |
| `--gold` #D4A017 | `--ink` | 8.14:1 | 4.5 | PASS AAA |
| **`--gold-deep` #806208** | **`--ink`** | **3.38:1** | 4.5 | **FAIL** (large-text OK at 3:1) |
| `--silver` #B8BCC2 | `--ink` | 10.14:1 | 4.5 | PASS AAA |
| rgba(255,255,255,0.85) | `--ink` | 13.95:1 | 4.5 | PASS AAA |
| rgba(255,255,255,0.78) | `--ink` | 11.78:1 | 4.5 | PASS AAA |
| rgba(255,255,255,0.72) | `--ink` (nav-link) | 10.15:1 | 4.5 | PASS AAA |
| rgba(255,255,255,0.70) | `--ink` | 9.63:1 | 4.5 | PASS AAA |
| rgba(255,255,255,0.68) | `--ink` (hero-lead) | 9.11:1 | 4.5 | PASS AAA |
| rgba(255,255,255,0.65) | `--ink` | 8.40:1 | 4.5 | PASS AAA |
| rgba(255,255,255,0.62) | `--ink` (page-hero p) | 7.67:1 | 4.5 | PASS AAA |
| rgba(255,255,255,0.60) | `--ink` (section-sub) | 7.28:1 | 4.5 | PASS AAA |
| rgba(255,255,255,0.55) | `--ink` (trust-item, aed-pill p, coverage-visual) | 6.27:1 | 4.5 | PASS AA |
| rgba(255,255,255,0.50) | `--ink` (hero-stat-lbl, about-metric-lbl) | 5.30:1 | 4.5 | PASS AA |
| **rgba(255,255,255,0.40)** | **`--ink`** | **3.79:1** | 4.5 | **FAIL** |
| **rgba(255,255,255,0.30)** | **`--ink`** | **2.63:1** | 4.5 | **FAIL** |

### Footer (`--ink-deep` #050810)

| Foreground | Background | Ratio | Threshold | Status |
|---|---|---:|---:|---|
| rgba(255,255,255,0.65) | `--ink-deep` (footer-link, brand-desc) | 8.51:1 | 4.5 | PASS AAA |
| rgba(255,255,255,0.62) | `--ink-deep` (footer-contact-row, brand-desc) | 7.75:1 | 4.5 | PASS AAA |
| rgba(255,255,255,0.60) | `--ink-deep` | 7.29:1 | 4.5 | PASS AAA |
| rgba(255,255,255,0.50) | `--ink-deep` | 5.35:1 | 4.5 | PASS AA |
| **rgba(255,255,255,0.40)** | **`--ink-deep`** (footer-cert) | **3.75:1** | 4.5 | **FAIL** |
| **rgba(255,255,255,0.30)** | **`--ink-deep`** (footer-copy) | **2.56:1** | 4.5 | **FAIL** |

### Focus indicator

| Foreground (outline) | Background | Ratio | Threshold | Status |
|---|---|---:|---:|---|
| **`--gold-bright`** | **white (forms, body)** | **1.85:1** | 3.0 | **FAIL** |
| **`--gold-bright`** | **`--off-white` (pillars, FAQ bg)** | **1.71:1** | 3.0 | **FAIL** |
| `--gold-bright` | `--ink` (hero, nav, CTA) | 10.43:1 | 3.0 | PASS AAA |
| `--gold-deep` (recommended replacement) | white | 5.72:1 | 3.0 | PASS AAA |
| `--gold-deep` | `--ink` | 3.38:1 | 3.0 | PASS just |

### Stars / decorative gold

| Foreground | Background | Ratio | Threshold | Status |
|---|---|---:|---:|---|
| **`--gold` #D4A017 (★★★★★)** | **white (review-card)** | **2.38:1** | 4.5 | **FAIL** |
| `--gold-deep` (recommended) | white | 5.72:1 | 4.5 | PASS AA |

### Buttons / controls

| Element | Pair | Ratio | Status |
|---|---|---:|---|
| btn-gold text | `--ink-deep` on `--gold` | 8.43:1 | PASS AAA |
| btn-white-glow text | `--ink` on white | 18.47:1 | PASS AAA |
| btn-outline-gold text | `--ink` on white | 18.47:1 | PASS AAA |
| btn-outline-gold border | `--gold` 2px on white | 2.38:1 | FAIL non-text (border is the affordance) |
| btn-ghost text | rgba(255,255,255,0.78) on `--ink` | 11.78:1 | PASS AAA |
| nav-cta text | `--ink-deep` on gold gradient | ~8.4:1 | PASS AAA |
| Tooltip text | white on `--ink` | 19.34:1 | PASS AAA |
| Section labels (section-label) | `--gold-deep` on white | 5.72:1 | PASS AA |
| Section labels (on-dark) | `--gold-bright` on `--ink` | 10.43:1 | PASS AAA |

### Form

| Element | Pair | Ratio | Status |
|---|---|---:|---|
| form label | `--ink` on white | 18.47:1 | PASS AAA |
| form input value | `--ink` on white | 18.47:1 | PASS AAA |
| form-sub | `--text-light` on white | 7.30:1 | PASS AAA |
| form-privacy | `--text-light` on white | 7.30:1 | PASS AAA |
| **input focus border** | **`--gold` on white** | **2.38:1** | **FAIL** (3:1 needed for UI) |
| input focus border (recommended) | `--gold-deep` on white | 5.72:1 | PASS |

---

## Recommended apply-order (minimum to ship)

1. **BLOCKER-1**: Replace focus-indicator color (1 CSS block edit).
2. **BLOCKER-4**: Bump footer-copy/cert opacity to 0.6 (2 CSS values).
3. **BLOCKER-5**: Change `.stars` / `.review-stars` / quote-marks to `--gold-deep` (3 CSS values) + add `aria-label`/`role="img"` to star strings.
4. **BLOCKER-6**: Add `type="button"` to every `<button>` (30+ buttons, can be done as multiple Edit replace_alls).
5. **BLOCKER-3**: Fix the 5 heading-level skips (about 15 `<h4>` → `<h3>` swaps + 1 `<h3>` → `<h2>` swap + matching CSS selector updates).
6. **BLOCKER-2**: Restructure form (single large edit).
7. **MAJOR-1**: Add `aria-hidden="true" focusable="false"` to `<svg class="ic">` (single replace_all).
8. **MAJOR-2**: Convert FAQ to real buttons (6 edits + 1 JS fix).
9. **MAJOR-3, MAJOR-5, MAJOR-6, MAJOR-8**: Small targeted edits.
10. MINOR items as time allows.
