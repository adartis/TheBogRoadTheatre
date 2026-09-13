# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Static website for BOG ROAD Theatre, an amateur drama group in North Kerry, served at bogroadtheatre.ie (see `CNAME`; repo `adartis/TheBogRoadTheatre`). It was exported from the TeleportHQ site builder and has since been edited and cleaned up by hand.

There is no build, lint, or test tooling and no `package.json`. Files are served exactly as committed, so to preview a change run a static server from the repo root (e.g. `python3 -m http.server`) — opening files directly works for the main pages but not for `404.html`, which uses root-relative paths.

## Architecture

- **Pages:** `index.html`, `about-the-group.html`, `showcase-gallery.html`, `404.html`. Each page loads `style.css` (global design tokens in `:root` plus shared component styles) and its own same-named `.css` file. Links between pages use relative `*.html` paths.
- **No templating — the nav and footer are duplicated.** The `<navigation-wrapper>` and `<footer-wrapper>` markup, including their inline scripts, is copied into the three main pages; any change must be applied to all three. `404.html` is standalone (no nav/footer) and uses **root-relative paths** (`/style.css`, `/`) because GitHub Pages serves it for missing URLs at any depth. Removing the duplication is tracked in `BACKLOG.md`.
- **JavaScript is inline only**, in `<script defer data-name="...">` blocks: `navigation-logic` (mobile menu, toggles `.is-active`), `footer-interactivity`, and `bog-road-interactions` (About page image parallax, skipped under `prefers-reduced-motion`).
- **Class names** like `home-thq-...-elm` are TeleportHQ-generated hooks targeted by the page CSS; keep them when editing markup. Several page CSS rules set fixed pixel heights/widths from the builder. On the home page those fixed sizes only apply at the 1200px desktop layout: `main.home-container3` is `width: 100%; max-width: 1200px`, and below 1200px `index.css` switches the section heights to `auto`.
- **Stylesheet order matters:** each page loads its own CSS *before* `style.css`, so a `style.css` rule beats a page-CSS rule of equal specificity (e.g. the stacked hero's `flex: 0 0 auto` has to live in `style.css` to override `.hero-media-side { flex: 5 }`). Prefer the design tokens in `style.css` (`--color-primary`, `--spacing-*`, `--font-family-heading`, etc.) over hard-coded values.
- **Fonts:** only Cinzel Decorative (400, 700) and Nunito (400/600/700/900, 400 italic) are loaded from Google Fonts. If new CSS uses another weight or style, add it to the font `<link>` on every page.
- `style.css` was purged of unused TeleportHQ rules. When adding markup, check the classes you use actually have rules — don't assume a builder class still exists.
- Inline `<style>` blocks inside page bodies (e.g. the `scrollCarousel` keyframes on the gallery page, `section` padding on the About page) are intentionally left in place.

## Carousels

- Both the home page strip (`.gallery-track` inside `.gallery-carousel-wrapper`, keyframes `scrollGallery` in `style.css`) and the gallery page carousels (`.production-gallery-carousel`, keyframes `scrollCarousel` inline in `showcase-gallery.html`) are pure CSS animations that translate by `calc(-50% - gap / 2)`.
- For the loop to be seamless, **the photo list must appear twice in the same order**, and the second copy's items carry `aria-hidden="true"`. When adding or removing a photo, change both copies. The track must stay `width: max-content` — a fixed or `max-width` width breaks the loop.
- Hover pauses via CSS (`animation-play-state: paused`); `prefers-reduced-motion` stops the animation and makes the row horizontally scrollable.

## Content that gets updated

- **Next/latest show:** the home page hero has a "Next Show" badge (`.hero-teaser-badge`), and `<teaser-section-wrapper>` holds the latest production card (poster, date chip, title, blurb, Facebook link).
- **Production galleries** (`showcase-gallery.html`): one `<section class="production-gallery">` per play.
- **Studio video** (`about-the-group.html`, `.about-the-group-studio`): native `<video controls playsinline preload="metadata">` with a poster and a download fallback. `BogRoadStudio.mp4` is H.264/AAC portrait footage (402px wide) pillarboxed inside a 1280×720 frame, so the player is a 400:720 box with `object-fit: cover` to crop the black bars, and the poster (`BogRoadStudio-poster.jpg`) is pre-cropped. If the file is replaced, match `aspect-ratio`, `max-width` and the `width`/`height` attributes to the new footage (and regenerate the poster); keep it H.264/AAC MP4 so it plays on iOS.
- **Images** live under `public/BRT/`: `general/` (logo, hero), `carousel/` (home strip), and one `play_<name>/` folder per production. Filenames encode the exported size (e.g. `3-500w.jpg`). Every local `<img>` has `width`/`height` matching the file's pixel size and `loading="lazy"` unless it is above the fold — keep that when adding images.
- **Alt text placeholders:** production photos use `alt="<folder>_<file>_correct_it"` placeholders for the site owner to replace with real descriptions; don't overwrite ones that have already been written.
- The Facebook page is `https://www.facebook.com/bogroadtheatre` (links open in a new tab); the contact email `bogroadtheatre@gmail.com` is a `mailto:` link in the home contact section and every footer.

## SEO and metadata

- Each main page has a meta description, canonical URL, Open Graph tags (shared `og:image` is the 200×200 logo, so `twitter:card` is `summary`) and favicon links (`favicon.ico`, `favicon-32x32.png`, `apple-touch-icon.png` at the root).
- `robots.txt`, `sitemap.xml` and `llms.txt` live at the repo root; add new pages to `sitemap.xml` and `llms.txt`.
