# Backlog

Changes deferred for later.

## Stop duplicating the navigation and footer

The navigation and footer markup (including their inline scripts) is copied into `index.html`, `about-the-group.html` and `showcase-gallery.html`, so every change to either has to be made three times. Options:

- A small shared script that injects the nav and footer from one file at page load (simple, but the content then depends on JavaScript).
- A static site generator (e.g. Eleventy) that assembles pages from shared includes at build time (no runtime cost, but adds a build step to the GitHub Pages deploy).

Worth doing before adding more pages.

## Fix lopsided layouts caused by CSS clashes and fixed heights

Found by measuring all three pages in headless Chrome at widths from 1440px to 375px. The fix below was tested on a scratch copy: afterwards the nav spanned the full width and stayed pinned, nothing overflowed sideways, and no content was cut off at any width. It is CSS only; no HTML changes.

**Problems:**

- **`navigation-wrapper` class clash (all pages).** The outer `<navigation-wrapper>` tag and the inner nav bar share the class. Page CSS makes it `display: contents`, but `style.css` (loaded second) makes it a 72rem centred flex box, and that wins on both elements. The nav bar is boxed instead of full width, and it scrolls away instead of staying pinned.
- **Footer `height: 508px` (all pages).** On mobile the footer content is about 990px tall, so "Explore", "Community" and the copyright line are hidden.
- **Gallery page fixed sizes.** Sections are `height: 569px` and descriptions `width: 684px`, so on tablets and phones the descriptions are pushed off-centre and the bottom of each section is cut.
- **Home page fixed heights** (2518/1935/431/526px) stop sections sizing to their content, and the teaser's `align-self: center` makes the "latest show" band narrower than the rest of the page.

**Fix:**

1. In `index.css`, `about-the-group.css` and `showcase-gallery.css`:
   - change `.navigation-wrapper { display: contents; }` to `navigation-wrapper.navigation-wrapper { display: contents; }`;
   - change `.navigation-thq-navigation-wrapper-elm` `height: 75px` to `min-height: 75px`;
   - delete the mobile `height: 12vh` on `.navigation-thq-navigation-container-elm`;
   - delete `height: 508px` on `.footer-thq-footer-container-elm`.
2. In `showcase-gallery.css`:
   - delete the `height` on the `-elm` sections (569px, plus the mobile 491/493/497px) and on `.showcase-gallery-thq-production-gallery-container-elm1`–`4`;
   - replace `width: 684px; height: 35px` on `.showcase-gallery-thq-section-content-elm1`–`4` with `max-width: 684px; margin: 0 auto`, and delete their mobile heights.
3. In `index.css`:
   - delete the heights on `.home-container2` (2518px), `.home-container3` (1935px), `.home-thq-contact-section-elm` (431px), `.teaser-section-thq-teaser-section-elm` (526px) and the mobile `.home-thq-hero-subtitle-elm` (116px);
   - change `.teaser-sectionroot-class-name` `align-self: center` to `stretch`. This is a visible design change: the latest-show card becomes full content width on desktop and crops a little more of the poster. Skip this line to keep the narrow card.
4. Update `CLAUDE.md` to match.

## Enable HTTPS on bogroadtheatre.ie

`https://bogroadtheatre.ie` returns a certificate that doesn't match the domain; plain http works. DNS is correct (the apex domain points at GitHub's four IPs, and `www` is a CNAME to `adartis.github.io`). In the repo's Settings → Pages, tick "Enforce HTTPS". If that option is greyed out, remove the custom domain, save, and add it back so GitHub issues a new certificate.

## Note: stale CSS right after a push

GitHub Pages serves files with `Cache-Control: max-age=600`, so for up to 10 minutes after a push a browser can combine the new HTML with the old CSS. Hard-refresh (Cmd+Shift+R) when checking a deploy. If this keeps causing confusion, add a version query to the stylesheet links (e.g. `style.css?v=2`) and bump it whenever the CSS changes.
