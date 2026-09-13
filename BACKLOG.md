# Backlog

Changes deferred for later.

## Stop duplicating the navigation and footer

The navigation and footer markup (including their inline scripts) is copied into `index.html`, `about-the-group.html` and `showcase-gallery.html`, so every change to either has to be made three times. Options:

- A small shared script that injects the nav and footer from one file at page load (simple, but the content then depends on JavaScript).
- A static site generator (e.g. Eleventy) that assembles pages from shared includes at build time (no runtime cost, but adds a build step to the GitHub Pages deploy).

Worth doing before adding more pages.
