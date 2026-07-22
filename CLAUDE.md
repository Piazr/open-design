# Developer Instructions: Open Design — Book Website

This instructions file outlines the commands, files, and core styling rules you must adhere to when modifying or extending this book website. The design system is documented in full in [DESIGN.md](DESIGN.md).

## 1. Project Management & Commands

### 1.1 Local Development
This is a static site with no build step. Launch a local HTTP server from the repo root:
* Python 3: `python3 -m http.server 8000`
* Node (http-server): `npx -y http-server -p 8000`

### 1.2 Git & Deployment
Deploying to GitHub Pages (hosted under `piazr.github.io/open-design/`):
* Make commits to `main`: `git add . && git commit -m "..." && git push origin main`
* GitHub Pages serves the static files automatically upon push.

### 1.3 Repository & Website Naming Conventions
This book uses **two different GitHub accounts** for its two repositories. Do not mix them up:
* **Website Repository** (this repo, hosts the preview web pages): `https://github.com/Piazr/open-design`
* **Book Repository** (hosts the downloadable book sources — HTML, PDF, EPUB releases): `https://github.com/imehr/open-design`
* **GitHub Pages Website**: `https://piazr.github.io/open-design/`
* **CRITICAL LINK DIRECTION**: The "View on GitHub" header button, footer "GitHub" links, the clickable 3D book cover on the homepage, and the changelog's PDF/EPUB download links all target the **Book Repository** (`imehr/open-design`), **NOT** this website repository. Download links use release assets, e.g. `https://github.com/imehr/open-design/releases/download/v0.2.0/open-design.pdf`.

### 1.4 Generated Content — Do Not Hand-Edit Chapters
* **`chapters/*.html` pages are GENERATED** by book-writer's website transformer, driven by `templates/website/open-design.json` (in the book-writer repo) and synced via `content-update release --website-dir`. Any hand-edits to `chapters/*.html` **will be overwritten on the next sync**. To change chapter content or chapter-page chrome, change the book source or the transformer template/config in book-writer, then re-run the sync.
* Hand-maintained pages: `index.html`, `about.html`, `changelog.html`, `llms.txt`. The sync only writes `chapters/*.html` and copies figure/cover images into `images/` — changelog release entries are updated by hand (or by the orchestrating agent) as part of each release.

### 1.5 Shared Scaffolding Skill
The reusable book-website scaffolding skill (`skills/book-design/SKILL.md`) lives in the **book-agentic-design-website repo** (the website repo for *The Agentic Designer*), not here. Automated chapter sync for this site is handled by book-writer's website transformer (`templates/website/open-design.json`). Do not copy the skill into this repo.

---

## 2. Hard Constraints & Style Conventions
Refer to [DESIGN.md](DESIGN.md) for full descriptions. Below are the critical instructions:

* **Strict Sharpness (No Rounded Corners)**: Never add rounded corners. All border radii are `0` (Tailwind `rounded-none`, `border-radius: 0`). The existing CSS only ever declares `border-radius: 0` — keep it that way.
* **Solid Black Dividers**: Layout borders, section separators, grid lines, and card division lines must be solid black (`#000` / `border-black`), without opacity/transparency.
* **Relative Links**: All stylesheet, script, image, and page links **must be relative** (e.g. `assets/fonts/beausite-medium.woff2`, `../assets/menu.js`, `chapters/01.html`). Never use absolute URLs beginning with `/` (e.g. `/assets/...`), as this breaks subdirectory routing on GitHub Pages.
* **Self-Hosted Beausite Fonts**: Body and display type is Beausite Classic (Regular/Medium/Bold woff2 files in `assets/fonts/`); monospace is Geist Mono with Fragment Mono fallback (Google Fonts). Do not substitute other typefaces.
* **Chapter Pages Structure** (generated, but the transformer must preserve this):
  * Text body (`.book-content`, max-width 680px) right-aligned via `margin-left: auto`.
  * Fixed sidebar rail navigation on the left (`.chapter-sidebar-rail`), hidden below 768px.
  * Sticky horizontal section nav directly under the site header (`top-14`).
  * Section anchors use the `ch-NN-S` scheme (e.g. `#ch-01-1`); appendices use `ch-app-a-1` style; the preface uses `ch-letter-1` style; figures use `chNN-fig-NN`.
  * Top menu drawer (`assets/menu.js`) and nav hover animations must exist and function smoothly.
* **Footer Borders**: The copyright container border at the bottom of the black footer must be solid white (`border-white`) with **no transparency**.
* **Hero Shader Palette**: The homepage hero is a warm terracotta/apricot WebGL cloud shader on a near-black base — terracotta, peach, amber, sand, espresso — with a cursor-reactive color that shifts terracotta (`#e05626`) → gold (`#ffcc00`) with mouse speed. Do not swap in the purple/yellow scheme used by *The Agentic Designer*'s site.

---

## 3. Directory Structure
* `/index.html` — Homepage (hand-maintained)
* `/about.html` — About page (hand-maintained)
* `/changelog.html` — Release history page (hand-maintained per release)
* `/llms.txt` — LLM / agent indexing metadata file (absolute URLs to live pages)
* `/chapters/` — Book chapters (`01.html` … `10.html`, appendices `A.html`–`C.html`, front matter `preface.html`, `copyright.html`) — **GENERATED, do not hand-edit**
* `/assets/` — Global JS, fonts, and gradients
  * `/assets/menu.js` — JS logic for the yellow menu drawer animation
  * `/assets/chapter-sidebar.js` — JS logic for the sticky section-highlight rail
  * `/assets/fonts/` — Self-hosted Beausite Classic woff2 files
  * `/assets/gradients/` — PNG gradient background assets
* `/images/` — Book figures (`chNN-fig-NN--web.webp`), cover (`cover.png`), author photo (`mehran.png`)
* `/scratch/` — Local dev scratch space (gitignored; never deploy or link to it)
