# WCAG 2.2 AA Checklist

The repeatable ADA pass for any site IMN builds. Written after the a 16-page demo audit for a direct primary care practice on 2026-08-04, where axe-core reported 1 issue and the manual layer found 6 more, two of them real reflow failures that broke every page at 320px.

> **axe-core is the floor**
> Automated tooling catches roughly a third of WCAG. A clean axe run is the start of the audit, not the end of it. Everything in the manual layer below was invisible to axe.

## Why this matters commercially

ADA web suits overwhelmingly target small businesses, and healthcare is the highest-exposure vertical we sell into. A compliant build is a closing argument, not just hygiene. Bring it up on demo calls.

## Setup

Playwright + axe-core, run against a local static server so the whole site gets swept at once.

```bash
npm i playwright @axe-core/playwright axe-core
npx playwright install chromium
```

Sweep every page at two viewports (1440 and 390) with the full tag set: `wcag2a, wcag2aa, wcag21a, wcag21aa, wcag22a, wcag22aa, best-practice`.

> **Wait for animations before you measure contrast**
> Entrance animations that start at `opacity:0` make axe read blended mid-fade colors and report contrast failures that do not exist at rest. Wait ~2.5s after `networkidle` before analyzing. On the DPC demo this produced a phantom 1.96:1 "serious" hit on a button that is actually 5.37:1.

## The manual layer

Six checks that automation misses. Each one was a real defect on that audit.

1. **Skip link (2.4.1, Level A).** First tab stop on every page, visible when focused. Verify by pressing Tab on a fresh page and reading `document.activeElement`.
2. **Focus rings on dark surfaces (2.4.7, 1.4.11).** The browser default is a thin blue ring that disappears on a dark footer or hero. Set one global `:focus-visible` ring, then flip its color on dark panels in a block at the *bottom* of the stylesheet so it beats the component rules. Never `outline:none` with only a border-color swap: a faint box-shadow does not clear 3:1.
3. **Target size (2.5.8, AA in 2.2).** Standalone small links and range inputs routinely land at 16-20px. Fix with `display:inline-block` + vertical padding on the link itself. Do **not** use an absolutely-positioned `::before` to fake the hit area: a `clip-path` on any ancestor defeats its hit-testing.
4. **Reflow at 320px (1.4.10).** Load every page at a 320px viewport and compare `documentElement.scrollWidth` to `clientWidth`. The two classic causes: inline `style="grid-template-columns:..."` that no media query can override, and a `white-space:nowrap` logo lockup that never shrinks.
5. **Status messages (4.1.3).** Any calculator or filter that rewrites numbers on screen is silent to a screen reader. Add one visually-hidden `role="status"` element carrying a single sentence, debounced ~600ms so dragging a slider does not flood the announcement queue. Do not put `aria-live` on the whole results panel: it re-reads the disclaimer on every tick.
6. **Reduced motion (2.3.3).** Confirm nothing is stranded at `opacity:0` under `prefers-reduced-motion: reduce`. Test with Playwright's `reducedMotion:'reduce'` context, not by reading the CSS.

Also worth a look each pass: alt text that describes provenance instead of content ("Portrait used for Dr X on the practice website"), heading levels that jump h1 to h3, and required fields marked only by color.

## Writing the probes

Automated probes lie in specific ways. Three that cost time on that run:

- `elementFromPoint` returns `null` for coordinates outside the viewport, and `scrollIntoView` is async when `scroll-behavior:smooth` is set. Use Playwright's own hit-testing to check target size instead.
- Calling `.focus()` on one element and then pressing Tab measures the *second* tab stop, not the first. Use a fresh page.
- An element hidden by `display:none` at the test viewport reports `outline-style:none` whether or not a focus ring is defined. Check the nav at desktop width.

## If the deliverable is a PDF, check print media separately

Added after the a therapy-practice site audit 2026-08-07. A wide table inside `overflow-x:auto` passes every screen check and every axe run, then **silently clips at the right margin in the exported PDF**, because print has no horizontal scroll. The a11y sweep will never catch it: on screen the wrapper scrolls, so nothing overflows.

Probe it directly with `emulateMedia({media:'print'})` at Letter content width (816px @ 96dpi) and fail on any element whose `right` exceeds it.

The fix belongs in a `@media print` block: release `min-width` on tables, let `white-space:nowrap` status badges wrap, and shrink cell padding. Better still, do not put long strings inside nowrap badges at all. Short badge plus normal text below reads better and cannot overflow at any width.

### Page breaks: measure them, do not eyeball them

Same audit, 2026-08-08. Rendering every page and measuring trailing whitespace found three near-blank pages a skim would have missed. Method: pdftoppm at 60dpi, then a per-page report of where ink stops plus the left/right ink edges. Anything over ~30% trailing whitespace that is not the cover or the last page is a stranded heading.

> **`break-inside: avoid` on a block taller than a page makes things worse, not better**
> The rule cannot be honoured, so the renderer shunts the whole block to a fresh page and leaves the previous one 85% white. It cascades: one bad `avoid` on a tall element produced two blank pages in a row. **Only guard small units** (`tr`, `.card`, callouts). Never `section`, never a full-width `.panel`, never a two-column card box. Tall things should be allowed to split.

Also worth setting once, in print only:

- `thead{display:table-header-group}` so long tables repeat their header across pages
- `h1,h2,h3,caption{break-after:avoid}` so a heading never orphans at a page bottom
- `p,li{orphans:3;widows:3}`
- **Match the padding on any full-bleed band to the body `.wrap` padding.** Raising `.wrap` padding for print without also raising it on the header and any stat/hero band leaves those sitting ~26px left of every other line. Obvious once rendered, invisible in the markup.

## Fix in the generator

Every fix belongs in the build script, the shared partial, or the stylesheet. Sixteen pages patched by hand drift apart within a week.
