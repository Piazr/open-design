# Design System & Guidelines: Open Design — Book Website

This document defines the visual guidelines, structural layouts, and interactive behaviors of the *Open Design* book website (`https://piazr.github.io/open-design/`). It documents what the site actually implements; it shares the print-inspired editorial template established by *The Agentic Designer* site, with its own warm terracotta/apricot identity.

> **Chapter pages are generated.** Everything under `chapters/` is produced by book-writer's website transformer (`templates/website/open-design.json`) and synced with `content-update release --website-dir`. Hand-edits to `chapters/*.html` will be overwritten on the next sync. Change the transformer template/config, not the generated output.

---

## 1. Core Visual Principles

### 1.1 The "Strict Sharpness" Rule (No Rounded Corners)
* **Banned CSS Property**: `border-radius` (except `0` / Tailwind `rounded-none`).
* **Rule**: No rounded corners on any element — buttons, grids, images, profile photos, code blocks, callouts, cards, inputs. The site's CSS only ever declares `border-radius: 0` (verified across `index.html`, `about.html`, `changelog.html`, and chapter pages). Keep it that way.

### 1.2 Divider & Boundary Lines
* **Rule**: Section dividers, borders, grid lines, and column separators are solid black (`#000` / Tailwind `border-black`) with no transparency.
* **Exception (intentional)**: Hairline separators *inside* components (nav-link left borders, table row rules, figure borders) use `rgba(0,0,0,0.06–0.1)` — structural grid lines stay solid black.
* The black footer's copyright bar uses a solid white border (`border-t border-white`), no transparency.

### 1.3 Buttons & Interactive Defaults
* Header "View on GitHub": white background, solid black border, mono font; inverts to black/white on hover.
* Primary CTAs are flat solid blocks: coral (`bg-coral`) "Read the Book" in the hero; solid black "Read Part N" / "Browse all chapters" blocks elsewhere. No shadows, no gradients on buttons, no rounding.

---

## 2. Typography

| Role | Face | Source |
|---|---|---|
| Display / headings | **Beausite Classic Bold / Medium** | Self-hosted woff2 in `assets/fonts/` (`beausite-bold.woff2`, `beausite-medium.woff2`) |
| Body / reading text | **Beausite Classic Regular** | Self-hosted woff2 (`beausite-regular.woff2`) |
| Mono (labels, code, kickers) | **Geist Mono**, Fragment Mono fallback | Google Fonts CDN |
| Sans fallback | Inter, system-ui | Google Fonts CDN |

Tailwind (CDN build with inline `tailwind.config`) maps `font-sans` → `'Beausite Classic Medium', Inter, system-ui` and `font-mono` → `'Geist Mono', 'Fragment Mono', monospace`.

Recurring patterns:
* **Kicker labels**: `font-mono text-[10px] uppercase tracking-wider text-black/50`.
* **Hero H1**: Beausite Bold, `leading-[0.9]`, negative tracking, up to `text-[6.5rem]`.
* **Footer wordmark**: giant `text-[18vw] md:text-[13vw]`, `leading-[0.85]`, `tracking-[-0.03em]`.
* **Chapter body** (`.book-content`): Beausite Regular 17px / 1.7, `color: rgba(0,0,0,0.75)`, `max-width: 680px`.

---

## 3. Color System

### 3.1 Tailwind Palette (defined inline on every page)
```js
colors: {
  coral: '#fe7141',        // primary accent — links, CTAs, step numbers, Part I
  lavender: '#cdabfe',     // Part II accent, default nav hover slide
  sage: '#597e87',         // Part III accent
  'sage-light': '#d1ddd3',
  'blue-light': '#f5f9fc', // callout background
  'blue-dark': '#153250',  // callout header text
}
```

### 3.2 Part & Section Gradient Mapping
Backgrounds use PNG gradient image files (`assets/gradients/`), not CSS gradients, layered over a solid fallback color:

| Section | Fallback color | Gradient file |
|---|---|---|
| Front matter + Part I (Ch 01–04) | `#fe7141` coral | `gradient_coral_peach.png` |
| Part II (Ch 05–08) | `#cdabfe` lavender | `gradient_lavender_violet.png` |
| Part III (Ch 09–10) | `#597e87` sage | `gradient_sage_yellow.png` |
| Appendix A | `#fe7141` / index card `#cc7373` | `gradient_coral_peach.png` |
| Appendix B | `#4e9b7a` | `gradient_emerald_olive.png` |
| Appendix C | `#cdabfe` / index card `#8d7ab5` | `gradient_lavender_violet.png` |
| Menu drawer (open) | `#FBBF24` closed / amber hover `#F59E0B` | `gradient_menu_yellow.png` |
| About hero | lavender | `gradient_lavender_violet.png` |
| Changelog hero | sage-light | `gradient_sage_yellow.png` |

The canonical per-chapter hero color/gradient assignments live in book-writer's `templates/website/open-design.json`.

### 3.3 Hero Shader Palette (Terracotta/Apricot)
The homepage hero (`#hero-section`, black) renders a WebGL domain-warped cloud shader in a **warm book-cover palette**: terracotta `vec3(0.85, 0.32, 0.15)`, peach, amber, sand, espresso over a warm near-black base, plus film grain. The cursor injects a localized organic cloud whose color interpolates **terracotta `#e05626` → sun gold `#ffcc00`** with mouse speed (spring-physics easing, `spring 0.015 / friction 0.92`). A Canvas 2D particle fallback (`#060403` base, terracotta/peach/amber radial blobs) runs when WebGL is unavailable. This warm scheme is this book's identity — do not reuse *The Agentic Designer*'s purple/yellow shader colors.

---

## 4. Page & Layout Structure

### 4.1 Global Header
Sticky `h-14`, white, `border-b border-black`:
* Black logo block `CC` + mono badge `'26` (links to `index.html`, gets pushed off-screen by the drawer).
* `#menu-placeholder` div (`w-[120px] sm:w-[550px]`) where `assets/menu.js` injects the "Read the Book +" drawer.
* "View on GitHub" button → **book repo** `https://github.com/imehr/open-design`.
* Right-side nav-hover links: About / Changelog / Chapters (chapter pages omit Changelog).

### 4.2 The Split Chapter Layout (Desktop)
Chapter pages (generated) split into a fixed left rail and a right-aligned text column:
* **`.chapter-sidebar-rail`**: `position: fixed`, full-height, centered to a `max-width: 1920px` shell; contains `.chapter-nav` (160px wide) with front-matter links and "In this chapter" section links. Active section gets a sliding horizontal tick (`group-[.active]:w-9 h-px bg-black`), driven by `assets/chapter-sidebar.js`. Hidden under 768px (a `.chapter-mobile-nav` block renders instead).
* **`.chapter-grid`**: 12-column grid, `column-gap: clamp(24px, 3vw, 48px)`, max 1920px.
  * `.chapter-nav-spacer` — `grid-column: 1 / span 2` (reserves rail space).
  * `.chapter-main` — `grid-column: 4 / -1`, white background; `.book-content` inside is `max-width: 680px; margin-left: auto` (right-aligned).
  * Tablet (≤1023px): 6-col grid, main spans `3 / span 4`. Mobile (≤767px): single column, rail hidden.

### 4.3 Chapter Page Anatomy (top to bottom)
1. Sticky site header (4.1).
2. **Chapter hero**: part-gradient background, "All Chapters" back link, mono part/section kicker (e.g. `PART I: FOUNDATIONS & CORE SKILLS · SECTION 01`), Beausite H1, subtitle, reading-time block.
3. **Sticky section nav**: `sticky top-14 z-40 border-b border-black bg-white/95 backdrop-blur-sm`, horizontal scroll of mono section links.
4. Sidebar rail + chapter grid with `.book-content`.
5. **Next-chapter hero banner** (`nextCtaStyle: "hero"` in the transformer config): a second `.chapter-hero` colored with the *next* chapter's gradient; no prev/next footer links (`includePrevNext: false`). Appendix C's bottom banner is `bg-black text-white`.
6. Chapter index grid (all chapters listed with part kickers) + black footer.

### 4.4 Anchor & ID Conventions (generated pages)
* Numbered chapter sections: `ch-NN-S` → `#ch-01-1`, `#ch-01-2` …
* Appendices: `ch-app-a-1`, `ch-app-b-1` …
* Preface: `ch-letter-1` …
* Figures: `chNN-fig-NN` (matching image files `images/chNN-fig-NN--web.webp`).
* Structural ids: `chapter-body`, `chapter-sidebar-rail`, `chapter-sidebar-nav`, `menu-placeholder`, `logo-link`.

### 4.5 Zero-Gap Grids
Stats, appendix cards, coverage lists, and chapter listings are flush grids with no gaps: `gap-0` plus `divide-x` / `divide-y` `divide-black` inside solid black borders (see the homepage stats grid and appendix card row).

### 4.6 Content Components (inside `.book-content`)
All sharp-cornered, defined per-page in the generated chapter CSS:
* **Code blocks**: black 1px border, `#f4f4f2` mono header bar (label + language), `#fafaf8` pre, Geist Mono 13px.
* **Tables**: black-bordered wrapper, mono uppercase headers on `#f4f4f2`, hairline row rules.
* **Callouts**: `blue-light` base with `blue-dark` header; variants `tip` (green `#f0fdf4` / `#22c55e` left bar), `warning` (red `#fef2f2` / `#dc2626`), `my-take` (orange `#fff7ed` / coral `#fe7141`).
* **Compare panels**: side-by-side good (`#f0fdf4`) / bad (`#fef2f2`) grid.
* **Steps**: 28px square coral `#fe7141` number chips.
* **Flow diagrams**: white mono boxes joined by faded arrows.
* **Figures**: full-width, hairline border, faded 13px captions.

### 4.7 Changelog Page
`changelog.html` (hand-maintained; updated per release alongside the chapter sync): sage-yellow gradient hero, left column of `.release-entry` blocks (mono `.release-badge` chips — solid black/white; the "Latest" badge is coral `#fe7141`), section tags (`new-content`, `updated-content`, `infrastructure` chips), `.chapter-ref` mono chips, and a right rail with PDF/EPUB download links pointing at **book repo release assets** (`github.com/imehr/open-design/releases/download/v<X.Y.Z>/...`).

---

## 5. Interactive Behaviors & Animations

### 5.1 Nav Hover (Slide & Vertical Text Swap)
Top navigation links use the dual-action `.nav-hover-link` pattern: an absolutely positioned `.bg-slide` panel (200% wide) slides in horizontally to fill the link with an accent color, while `.nav-text` slides up out of view and `.nav-text-clone` slides in from below (`0.4s cubic-bezier(0.44, 0, 0.56, 1)`).
* Top-level pages (`index`, `about`, `changelog`): slide color **lavender** (`bg-lavender`).
* Chapter pages: slide color is set in CSS to the **chapter's own part color** (`#fe7141` in Part I pages, `#cdabfe` in Part II, `#597e87` in Part III, `#4e9b7a` in Appendix B).

### 5.2 Apple TV-style 3D Tilt (Book Cover)
Homepage cover uses a `perspective-container` (`perspective: 1000px`) wrapping a `.tilt-card` (`transform-style: preserve-3d`): mouse position maps to ±12° rotateX/rotateY with `scale(1.04)`, plus a `.glare` overlay (`mix-blend-mode: overlay`) whose gradient angle follows the cursor. Smooth return on mouseleave (`0.5s ease-out`). The cover links to the **book repository**.

### 5.3 Interactive Organic WebGL Cloud Shader (Hero)
Full-bleed canvas behind the hero (see 3.3 for palette): 4-octave fBm with domain warping, a localized mouse vortex/swirl that "mixes" the clouds, spring-physics cursor lag, speed-reactive terracotta→gold injected color, film grain, and a Canvas 2D particle fallback.

### 5.4 Logo-Pushing Menu Drawer ("Read the Book +")
Injected by `assets/menu.js` into `#menu-placeholder` on every page (path-aware via `../` prefix detection on chapter pages):
1. Closed state: amber `#FBBF24` button (`#F59E0B` on hover) with a `+` icon.
2. On open: the `+` rotates into an `X`; the drawer expands down-left (`clip-path` keyframes, `0.7s cubic-bezier(0.16, 1, 0.3, 1)`) to full viewport height with the `gradient_menu_yellow.png` background and chapter list grouped by part.
3. The site logo (`#logo-link`) is pushed off-screen (`translateX(-100%)`, delayed) as the drawer expands.

### 5.5 Sticky Section Rail Highlighting
`assets/chapter-sidebar.js` tracks scroll position against `ch-NN-S` section anchors and toggles the `.active` class on sidebar links, extending the horizontal tick line beside the active section.

---

## 6. Repository & URL Architecture

Two distinct repositories under **different GitHub accounts**:
* **Website Repository** (this repo, the static site): `https://github.com/Piazr/open-design`
* **Book Repository** (book sources + PDF/EPUB releases): `https://github.com/imehr/open-design`
* **Live site (GitHub Pages)**: `https://piazr.github.io/open-design/`

### 6.1 Link Consistency Rules
* "View on GitHub" header button, footer "GitHub" link, homepage book-cover link, and changelog download links → **Book Repository** (`imehr/...`), never the website repo.
* Cross-links to sibling books use absolute URLs (e.g. *The Agentic Designer*: `https://piazr.github.io/the-agentic-designer/`).
* All intra-site links are relative (GitHub Pages serves from a subdirectory).

### 6.2 AI-Friendly Indexing (`llms.txt`)
Root-level `llms.txt` follows the established format: H1 book title, blockquote summary (author + CC BY-NC-SA 4.0 license), Core Resources block (live site / book repo / website repo), then per-part absolute links to every chapter with one-sentence summaries.

### 6.3 Sync Pipeline
Chapter pages and figure/cover images are produced by the book-writer repo:
* Transformer config: `templates/website/open-design.json` (titles, part labels, per-chapter hero colors/gradients, `contentStyle: "sections"`, `nextCtaStyle: "hero"`, `includePrevNext: false`).
* Sync command: `bun run book -- content-update release --book <book> --run-id <id> --repo-dir <book-repo> --website-dir <this-repo> --run`.
* The shared scaffolding skill for creating new book sites lives in the *book-agentic-design-website* repo at `skills/book-design/SKILL.md`.
