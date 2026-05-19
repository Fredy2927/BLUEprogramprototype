# 08 — UX Research & Application

Research synthesis for making B.L.U.E. read well and navigate easily, plus a changelog of what was applied to the app.

---

## What modern UX practitioners actually agree on

A content-heavy reference site like B.L.U.E. has more in common with Wikipedia, MDN, Stripe Docs, and the BBC than with a SaaS landing page. The following are the principles that show up consistently across Nielsen Norman Group (NN/g), the Baymard Institute, WCAG 2.2, and the design systems of well-respected content sites.

### 1. Readability is the product
**Line length, font size, and line height matter more than visual style.**

- **Body text: 16–18 px minimum.** WCAG 2.1 treats anything smaller as risky for older readers and accessibility. The NHS app uses 18 px with 27 px line height for AAA compliance.[¹](https://developerux.com/2025/02/12/typography-in-ux-best-practices-guide/) [²](https://scopedesign.com/website-font-size-guide-2025-ux-typography-best-practices/)
- **Line length: 50–75 characters per line** is the universally-cited sweet spot (Bringhurst, Ruder, Baymard, NN/g). The CSS pattern is `max-width: 65ch–70ch`. Going wider on a 4K monitor is the single most common readability mistake.[³](https://medium.com/@wblekhoa/talk-aboutthe-optimal-length-of-text-in-ux-ui-525e689f0b71) [⁴](https://www.uxpin.com/studio/blog/optimal-line-length-for-readability/)
- **Line height: 1.5–1.6 × font size** for body. Larger for long lines, smaller for short ones.
- **Fluid typography:** `font-size: clamp(16px, 1rem + 0.5vw, 20px)` is the modern recipe. Scales gracefully from phone to 4K without media-query rules.
- **Limit typefaces to 2** (one for display, one for UI/body), or just use one well-set sans like Inter.

### 2. Hierarchy through size + weight + space, not color
- A clear modular scale (Major Third 1.25, Perfect Fourth 1.333, Golden Ratio 1.618) makes headings feel related instead of arbitrary.
- Section breaks come from **white space** before the heading, not from a heavy ornament. Whitespace is functional, not waste.
- **Color is supplemental.** Never carry meaning in color alone — pair with icons, labels, or weight changes. (WCAG 1.4.1.)

### 3. Navigation: the only metric is "did the user find it?"
NN/g's three-phase test framework: usability test the current nav → card sort to learn how users naturally group things → tree test the proposed structure.[⁵](https://lovable.dev/guides/website-navigation-best-practices-that-convert)

- **Predictable placement.** Top-left wordmark goes home. Primary nav in the same place on every page.
- **Plain-language labels.** "Become a verifier" beats "Join." Avoid clever names.
- **Breadcrumbs on hierarchical content** — Wikipedia, MDN, and every documentation site uses them.
- **Sticky nav on long-scroll pages.** A user reading a Level 3 guide shouldn't have to scroll back to the top to jump elsewhere.
- **Mobile nav ≠ hidden nav.** A hamburger without the word "Menu" hurts usability (NN/g). On mobile, the nav should be **accessible**, just collapsed — not gone.
- **Search is navigation** for content-rich sites. ⌘K palettes (Algolia/Linear pattern) have become the expected affordance.
- **Skip-to-content link** at the very top — invisible by default, visible on focus — is required for screen-reader/keyboard users.

### 4. Accessibility = better UX for everyone
WCAG 2.2 added concrete requirements:[⁶](https://www.nngroup.com/articles/design-pattern-guidelines/)

- **Focus appearance (SC 2.4.11):** focus rings need a **minimum 2 px outline** with sufficient contrast. Most sites' faint `outline: 1px dotted` violates this.
- **Target size (SC 2.5.5):** interactive elements need **at least 24×24 px** (ideally 44×44 for touch). Tightly-packed chips are a common offender.
- **Text spacing (SC 1.4.12):** users must be able to override line-height, letter-spacing, etc. without breaking layout — implies using `em`/`rem` not pixel values for vertical rhythm.
- **Contrast: 4.5:1 minimum** for normal text, 3:1 for 18 px+ or 14 px-bold. Tools: WebAIM contrast checker.
- **Semantic HTML beats ARIA.** `<nav>`, `<main>`, `<article>`, `<aside>` give screen readers structure for free.
- **Don't disable browser zoom.** Text must remain readable at 200% zoom without horizontal scrolling.

### 5. Scanning patterns shape layout
Eye-tracking studies (Jakob Nielsen) show readers scan in **F-patterns** (long content) and **Z-patterns** (landing pages):

- Critical information goes in the **first 600 px** from the top-left.
- **Front-load lists.** Most users only read the first 2–3 items in a list before deciding.
- **Bold the meaning words** in long paragraphs — but only the meaning words, not whole phrases.
- **Headings every 2–3 paragraphs** in long-form content; F-pattern scanners look for them.

### 6. Progressive disclosure beats information dumps
- Show users what they need to do first. Make secondary/advanced controls discoverable but not loud.
- A tooltip, an `[edit]` link that appears on hover, an accordion for "advanced options" — these all reduce cognitive load without hiding functionality.
- **Avoid the modal trap.** Modals interrupt; prefer inline expansion or routing to a dedicated page.

### 7. Feedback closes the loop
- Every interactive action needs a visible confirmation within 100ms (Doherty Threshold).
- Optimistic UI: update the screen before the server responds (e.g., upvote increments immediately).
- **Toasts** for confirmations. **Inline errors** for validation. **Modal/page** only for things that genuinely require user attention.

### 8. The little details that separate "designed" from "vibe-coded"
- Visited links should look visited (`a:visited` styling). It's free wayfinding for the user.
- "Back to top" button appears after ~600 px of scroll on long pages.
- Smooth scrolling on anchor links: `scroll-behavior: smooth`.
- `:focus-visible` (not `:focus`) so mouse users don't see ugly rings.
- Avoid `cursor: pointer` on non-interactive elements.
- Print stylesheets exist; for a reference site, people print things.
- Reading-time estimate on long content respects the user's time.

---

## Issues identified in the current B.L.U.E. app

After running the app against the checklist above, here's what needed fixing:

| # | Issue | Severity | Fix |
|---|---|---|---|
| 1 | Article body had no max-width — lines became 130+ characters on wide screens | **High** | `max-width: 70ch` on `.article-body`, `.guide-body` |
| 2 | Body font was 15 px | Medium | Bumped to 16 px with `clamp()` for fluid scaling |
| 3 | No focus ring on most buttons | **High (a11y)** | Universal `:focus-visible` with 2 px outline + offset |
| 4 | No skip-to-content link | **High (a11y)** | Added; visible only on focus |
| 5 | Mobile sidebar just disappeared instead of being accessible | **High** | Added hamburger → drawer pattern on mobile |
| 6 | No "back to top" on long guide pages | Medium | Floating button appears after 600 px scroll |
| 7 | TOC wasn't sticky — readers lost place when scrolling | Medium | TOC moves into a sticky right rail on guides |
| 8 | No reading-time estimate | Low | Added to guide subtitle ("8 min read") |
| 9 | Tap targets on small chips were ~22 px | **Medium (a11y)** | Bumped to 28+ px min height |
| 10 | Visited links weren't visually distinct enough | Low | Stronger purple, kept underline-on-hover |
| 11 | No semantic HTML — everything was `<div>` | Medium | Added `<nav>`, `<main>`, `<article>`, `<aside>` |
| 12 | `aria-current="page"` missing on active nav | **High (a11y)** | Added |
| 13 | Icon buttons had no `aria-label` | **High (a11y)** | Added on theme toggle, search, hamburger |
| 14 | `scroll-behavior: smooth` not enabled | Low | Added to `html` |
| 15 | Anchor links in TOC didn't account for sticky header | Low | `scroll-margin-top: 80px` on heading targets |
| 16 | No keyboard escape from modals/drawer | **Medium (a11y)** | `Esc` key handler |
| 17 | Empty states were one-line; users didn't know what to do | Low | Better empty states with primary CTA |
| 18 | No print stylesheet on a reference site | Low | Added `@media print` rules |
| 19 | Hover-only edit links invisible to touch users | Medium | Show on focus too; show always on touch devices |
| 20 | Form validation messages disappeared (toast-only) | Medium | Inline errors below the field stay until corrected |

---

## What was applied in this round

See `blue-system/app/index.html`. The high-impact changes:

1. **`max-width: 70ch` on article body** — single biggest readability win.
2. **Fluid type with `clamp()`** — `font-size: clamp(16px, 1rem + 0.2vw, 17.5px)` on the body.
3. **Skip-to-content link** as first focusable element.
4. **Hamburger drawer** on mobile (≤900 px) — sidebar is no longer hidden, just collapsed behind a button.
5. **Sticky right rail** on guide pages with TOC + jump-to actions.
6. **Floating "↑ Top" button** appears after 600 px scroll.
7. **Reading-time estimate** on every guide (assumes 220 wpm).
8. **Universal `:focus-visible`** with 2 px ring + 2 px offset using `--link` color.
9. **Semantic HTML** — `<nav>`, `<main>`, `<aside>`, `<article>` throughout.
10. **`aria-current="page"`** on active nav, `aria-label` on icon-only buttons.
11. **`Esc` closes** modals, drawer, command palette.
12. **Print stylesheet** — hides chrome, infobox floats inline, link URLs printed after text.
13. **Heading anchor offsets** so TOC clicks don't hide the heading behind the sticky header.
14. **Larger tap targets** — chips and buttons now meet 28 px min height.
15. **Better empty states** with a primary CTA.

---

## What's still on the to-do list (UX-wise)

These didn't make this round but should:

- **Real card sort / tree test** with 5 actual humans on the subject taxonomy.
- **Search ranking improvements** — currently substring-matches; should weight title > body > methods.
- **Reading progress indicator** at the top of long guides (the thin bar Medium uses).
- **Dark mode contrast pass** — currently passes 4.5:1 but some borders are too faint.
- **Reduced-motion respect** — `@media (prefers-reduced-motion: reduce)` for any future animations.
- **Onboarding tour** — currently a modal; could be a 4-step interactive tour for first-time users.
- **Saved-for-later** / bookmark list, so a user can come back to a half-read Level 3 guide.

---

## References used

- Nielsen Norman Group: *[Design Pattern Guidelines](https://www.nngroup.com/articles/design-pattern-guidelines/)*, *[Mobile Subnavigation](https://www.nngroup.com/articles/mobile-subnavigation/)*, *[Consistency and Standards](https://www.nngroup.com/articles/consistency-and-standards/)*.
- W3C: [WCAG 2.2](https://www.w3.org/TR/WCAG22/).
- Baymard Institute: line-length research, mobile-navigation benchmark.
- UXPin: *[Optimal Line Length for Readability (2026)](https://www.uxpin.com/studio/blog/optimal-line-length-for-readability/)*.
- ScopeDesign: *[Website Font Size Guide 2025](https://scopedesign.com/website-font-size-guide-2025-ux-typography-best-practices/)*.
- DeveloperUX: *[Typography in UX](https://developerux.com/2025/02/12/typography-in-ux-best-practices-guide/)*, *[Accessible Color Contrast](https://developerux.com/2025/07/28/best-practices-for-accessible-color-contrast-in-ux/)*.
- Lovable: *[Website Navigation Best Practices](https://lovable.dev/guides/website-navigation-best-practices-that-convert)*.
