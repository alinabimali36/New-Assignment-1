# Developer Guide — Nexcent Website

This guide is written specifically for you based on the code in this project.
It covers every category of issue found, explains *why* each one matters,
and shows you the corrected pattern so you can apply it going forward.

---

## Table of Contents

1. [What You Did Well](#1-what-you-did-well)
2. [HTML — Bugs and Bad Patterns](#2-html--bugs-and-bad-patterns)
3. [CSS — Bugs and Bad Patterns](#3-css--bad-patterns)
4. [Project Organisation](#4-project-organisation)
5. [Accessibility](#5-accessibility)
6. [Responsive Design (Missing)](#6-responsive-design-missing)
7. [A Prioritised To-Do List](#7-a-prioritised-to-do-list)

---

## 1. What You Did Well

Before the issues, here is what is already solid:

- **Semantic sectioning** — you used `<nav>`, `<section>`, and `<footer>` correctly instead of a wall of `<div>` tags.
- **BEM-style naming** — class names like `footer-col`, `nav-links`, `hero-content` are descriptive and readable.
- **Flexbox and Grid** — you reached for the right layout tools in the right places (flex for the navbar, grid for the footer columns).
- **Hover transitions** — you added `transition` on links, which shows you are thinking about feel, not just looks.
- **External icon library** — pulling in Font Awesome via CDN is the right call instead of managing icon files yourself.

These are genuine strengths. Build on them.

---

## 2. HTML — Bugs and Bad Patterns

### 2.1 Broken attribute syntax

**Found in:** `first.html` lines 98, 105, 111 (original file)

```html
<!-- Wrong — missing the = sign -->
<img src="Assets/icon.svg" alt"icon">

<!-- Correct -->
<img src="Assets/icon.svg" alt="icon">
```

A missing `=` means the browser silently drops the attribute.
Screen readers and search engines cannot read the image at all.
Always validate attributes before committing.

---

### 2.2 Two `<h1>` tags for one heading

**Found in:** `first.html` lines 33–34

```html
<!-- Wrong — two h1s split across lines for visual layout -->
<h1>Lessons and insights</h1>
<h1>from 8 years</h1>
```

A page should have exactly one `<h1>`. Search engines use it to understand
what the page is about. Using two is a semantic error. Use a `<span>` inside
a single `<h1>` to colour part of the text differently:

```html
<!-- Correct -->
<h1>Lessons and insights <span class="highlight">from 8 years</span></h1>
```

```css
.highlight { color: #1cb141; }
```

---

### 2.3 Using `<br>` for spacing

**Found in:** `first.html` line 92 (original)

```html
<!-- Wrong -->
<h1>Manage your entire community<br> in a single system</h1><br>
<p>who is Nextcent suitable for</p>
```

`<br>` is for line breaks inside prose (e.g. a postal address or a poem).
It is never the right tool for adding space between block elements.
Use `margin-bottom` in CSS instead — it is easier to maintain and
responds correctly on different screen sizes.

```css
/* Correct */
.card-header h1 {
  margin-bottom: 12px;
}
```

---

### 2.4 Hardcoded `width`/`height` on images in HTML

**Found in:** `first.html` lines 98, 105, 111, 122

```html
<!-- Wrong -->
<img width="20" height="30" src="Assets/icon.svg" alt="icon">
<img height="250" width="250" src="Assets/bird.jpg" alt="logo">
```

Hardcoded pixel dimensions in HTML override CSS and break responsive layouts.
Control sizing exclusively in CSS so it can adapt to different screens.

```html
<!-- Correct HTML — no dimensions -->
<img src="Assets/icon.svg" alt="icon">
```

```css
/* Correct CSS */
.card-icons img {
  width: 48px;
  height: 48px;
  object-fit: contain;
}
```

---

### 2.5 Missing space before `alt` attribute

**Found in:** `first.html` line 122 (original)

```html
<!-- Wrong — no space between src and alt -->
<img height="250" width="250" src="Assets/bird.jpg"alt="logo">

<!-- Correct -->
<img src="Assets/bird.jpg" alt="logo">
```

Some older browsers will fail to parse the attribute at all.
Always leave a space between attributes.

---

### 2.6 Repeated identical content (placeholder images)

**Found in:** `first.html` — all five client logos use the exact same `src`.
All four social icons use `facebook.png`.

This is not necessarily a code bug, but it signals that the asset work is
incomplete. Before shipping: replace placeholders with real images, and use
distinct icons for each social network (Twitter/X, LinkedIn, Instagram, etc.).

---

### 2.7 Heading hierarchy — wrong level inside sections

**Found in:** `first.html` lines 52, 92

```html
<!-- Wrong — h1 inside a section that already has a page-level h1 -->
<h1>Our Clients</h1>
<h1>Manage your entire community...</h1>
```

The heading hierarchy should be:
- One `<h1>` for the page title (hero heading)
- `<h2>` for section titles
- `<h3>` for card/sub-section headings

```html
<!-- Correct -->
<h2>Our Clients</h2>
<h3>Membership Organisations</h3>
```

---

## 3. CSS — Bugs and Bad Patterns

### 3.1 Missing units on numeric values

**Found in:** `style.css` (original), multiple lines

```css
/* Wrong — unitless values are invalid in these contexts */
max-width: 1200;       /* ignored by browser */
gap: 10;               /* ignored by browser */
font-weight: 450px;    /* px is invalid on font-weight */
```

```css
/* Correct */
max-width: 1200px;
gap: 10px;
font-weight: 450;      /* font-weight never takes a unit */
```

Unitless values (except for `0`, `line-height`, and a few others)
are silently ignored. The browser falls back to the default, so the
layout breaks without any error message to help you debug it.

---

### 3.2 Typo inside `box-shadow`

**Found in:** `style.css` line 154 (original)

```css
/* Wrong — space inside "12 px" breaks the declaration */
box-shadow: 0px 4px 12 px rgba(0,0,0,0.5);
```

```css
/* Correct */
box-shadow: 0 4px 12px rgba(0, 0, 0, 0.07);
```

The entire `box-shadow` declaration was silently ignored because of
that space. The cards had no shadow at all.

---

### 3.3 Class name mismatch between HTML and CSS

**Found in:** HTML uses `.clients-section`, CSS defines `.client-section`.
HTML uses `.card-icons`, CSS defines `.card-icon`.

```html
<!-- HTML -->
<section class="clients-section">
<div class="card-icons">
```

```css
/* CSS — wrong names, styles never applied */
.client-section { ... }
.card-icon { ... }
```

When a class name in CSS does not exactly match the HTML, the styles are
completely ignored. Always cross-check names when you write them. Using
a browser's DevTools Inspector (F12 → Elements tab) will immediately show
you if a style is not being applied.

---

### 3.4 No CSS custom properties for repeated values

The color `#1cb141` appears at least 8 times across the stylesheet.
If the brand color ever changes, you have 8 places to update — and you
will almost certainly miss one.

```css
/* Add this at the top of style.css */
:root {
  --color-primary:   #1cb141;
  --color-primary-dark: #159c35;
  --color-text:      #263238;
  --color-muted:     #717171;
  --spacing-section: 70px 80px;
}

/* Then use it everywhere */
.btn-signup {
  background-color: var(--color-primary);
}
.nav-links a:hover {
  color: var(--color-primary);
}
```

---

### 3.5 Duplicate rule at the bottom of the file

**Found in:** `style.css` lines 298–300 (original)

```css
/* This rule already existed higher up — the duplicate at the bottom
   silently overrides the first one with a different value */
.col-title {
  margin-bottom: 15px;
}
```

Keep each selector defined in one place. If you need to override
something for a specific context, use a more specific selector:

```css
.links-col .col-title {
  margin-bottom: 15px;
}
```

---

### 3.6 Overly large `max-width` values

**Found in:** `style.css` — `.logo-row` had `max-width: 1800px`,
`.card-container` had `max-width: 2000px`.

Most laptop screens are 1280–1440px wide. A max-width of 1800–2000px
means the constraint never actually activates and the layout stretches
across the full viewport on wide monitors.

Use `max-width: 900px–1200px` for content areas, and center with `margin: 0 auto`.

---

### 3.7 Missing Google Fonts import

**Found in:** `style.css` used `font-family: 'Inter', sans-serif`
but never imported Inter.

```css
/* Wrong — Inter is not a system font, it won't load */
body {
  font-family: 'Inter', sans-serif;
}
```

```css
/* Correct — import first, then use */
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap');

body {
  font-family: 'Inter', sans-serif;
}
```

Without the import, the browser silently falls back to the generic
sans-serif font (usually Arial or Helvetica). The design looks different
in every browser.

---

### 3.8 Inconsistent colour choices

The original nav link color (`#944f52`, a mauve/pink) had nothing to do
with the rest of the green-and-dark design system. Mixing unrelated accent
colors creates visual noise.

Rule of thumb: pick one primary accent color, one dark text color, one
muted/grey color, and use nothing else for interactive elements.

---

## 4. Project Organisation

### 4.1 `script.js` is empty

The project has a `script.js` file with no content.
Either fill it with the JavaScript you plan to write, or remove it.
Linking an empty file adds a network request with zero benefit.

### 4.2 Asset naming

```
Assets/icon.svg
Assets/icon2.svg
Assets/icon3.svg
```

Names like `icon2` tell you nothing. Name files after what they represent:

```
Assets/icons/membership.svg
Assets/icons/associations.svg
Assets/icons/clubs.svg
Assets/logos/facebook.svg
Assets/logos/twitter.svg
```

### 4.3 External images that you do not control

The hero image is loaded from a Google cache URL
(`encrypted-tbn0.gstatic.com`). Google can remove or rotate that URL at
any time. Download assets you use and store them in your `Assets/` folder.

---

## 5. Accessibility

Accessibility means the site works for people using screen readers,
keyboard navigation, or other assistive technology. The current code has
several gaps:

| Issue | Fix |
|---|---|
| `alt""` is fine for decorative images but `alt="logo"` is meaningless — describe the actual brand name | `alt="Nexcent company logo"` |
| No `aria-label` on icon-only links (the social icons in the footer) | `<a href="#" aria-label="Follow us on Facebook">` |
| `<button>` with no `type` attribute | Always write `<button type="button">` or `<button type="submit">` |
| Low colour contrast on muted text (`#777` on white) | Check contrast at webaim.org/resources/contrastchecker — aim for 4.5:1 |
| No `:focus` styles on interactive elements | Keyboard users cannot see which element is selected |

```css
/* Add a visible focus ring for keyboard users */
a:focus-visible,
button:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 3px;
}
```

---

## 6. Responsive Design (Missing)

The entire site is built for a desktop viewport. On a phone, the navbar
links will overflow and the hero layout will look broken.

This is the single biggest gap in the current codebase.

### How to start

Add a `<meta viewport>` tag — you already have this, which is good.
Then add breakpoints at the bottom of `style.css`:

```css
/* Tablet — 768px and below */
@media (max-width: 768px) {
  .navbar {
    flex-direction: column;
    gap: 16px;
    padding: 16px 24px;
  }

  .hero-container {
    flex-direction: column;
    text-align: center;
  }

  .hero-content h1 {
    font-size: 32px;
  }

  .card-container {
    flex-direction: column;
    align-items: center;
  }

  .footer-container {
    grid-template-columns: 1fr;
  }
}

/* Mobile — 480px and below */
@media (max-width: 480px) {
  .navbar {
    padding: 14px 16px;
  }

  .nav-links {
    flex-wrap: wrap;
    justify-content: center;
    gap: 12px;
  }

  .hero {
    padding: 40px 20px;
  }
}
```

Test every page on Chrome DevTools' device emulator (F12 → toggle device toolbar)
before considering a feature complete.

---

## 7. A Prioritised To-Do List

Work through these in order. The top items are bugs that affect all visitors;
the bottom items are improvements that make the code easier to maintain.

### Must fix (bugs visible to users)
- [ ] Add `@import` for Google Fonts Inter at the top of `style.css`
- [ ] Fix all `alt"..."` → `alt="..."` attribute typos in `first.html`
- [ ] Replace duplicate `.clients-section` / `.client-section` class name
- [ ] Remove hardcoded `width`/`height` from all `<img>` tags in HTML
- [ ] Add missing `px` unit to `max-width: 1200` in `.hero-container`
- [ ] Add missing `px` unit to `gap: 10` in `.section-header`
- [ ] Remove space from `box-shadow: 0px 4px 12 px`
- [ ] Remove `px` from `font-weight: 450px`

### Should fix (quality and maintainability)
- [ ] Consolidate the two `<h1>` hero tags into one using a `<span>`
- [ ] Change section titles from `<h1>` to `<h2>`
- [ ] Replace all `<br>` spacing with `margin-bottom` in CSS
- [ ] Add `:root` CSS custom properties for all repeated colour values
- [ ] Remove the duplicate `.col-title` rule at the bottom of `style.css`
- [ ] Delete or fill `script.js`
- [ ] Rename `icon.svg`, `icon2.svg`, `icon3.svg` to descriptive names

### Nice to have (polish)
- [ ] Add `@media` breakpoints for tablet and mobile
- [ ] Replace placeholder client logos with real assets
- [ ] Replace the four identical `facebook.png` social icons with correct ones
- [ ] Download the hero image locally instead of using a Google cache URL
- [ ] Add `:focus-visible` styles for keyboard accessibility
- [ ] Add `aria-label` to icon-only social links in the footer
- [ ] Validate the final HTML at validator.w3.org

---

*Guide generated from a code review of `first.html` and `style.css` in this project.*
