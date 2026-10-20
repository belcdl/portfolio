# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A static personal portfolio site (Belén Cabrero — 3D visualization / interior design / web dev) built on top of the **Kitzu** ThemeForest personal-portfolio template (jQuery + Bootstrap). No build step, no package manager, no bundler — it's plain HTML/CSS/JS served as-is (deployed to GitHub Pages at `belcdl.github.io/portfolio/`, per the `hreflang` links in `index.html`).

There is no test suite, linter, or build command in this repo. "Developing" here means editing HTML/CSS/JS directly and opening the files in a browser (or serving the folder with any static file server) to check the result.

## Bilingual site structure — the thing to get right

The site exists in two language copies that are **separate physical files, manually kept in sync** — there is no i18n templating:

- `index.html` — Spanish (`lang="es"`), the canonical/default version at the site root.
- `en/index.html` — English (`lang="en"`), same page, translated and self-contained (its own copy of every section).

They are *not* identical line-for-line (currently 818 vs 789 lines, slightly different portfolio item counts) — content has drifted between them over time. **When changing portfolio content, copy, or markup structure, apply the change to both files**, or deliberately note that you're only updating one.

Both files reference assets with root-relative-style paths (`img/...`, `css/...`, `js/...`); `en/index.html` reaches back up to the shared `img/`, `css/`, `js/` directories at the repo root rather than having its own copies.

Files named with a trailing underscore (`index_.html`, `video_.html`) are **local untracked backup copies** (not in git) — don't treat them as part of the site; the tracked `index.html`/`video.html` are canonical.

## Page inventory

- `index.html` / `en/index.html` — the live one-page site (home, about, portfolio grid, contact info), loaded via lightbox-style sections (`.lightbox-wrapper`) rather than separate pages.
- `video.html` — an **unused leftover template demo page** from the original Kitzu theme (still has placeholder "Emma Smith" content, a different nav with Resume/Blog sections, YouTube background video). Not linked from the live site's nav — leave it alone unless specifically asked to repurpose it.

## Portfolio grid (Isotope.js filtering)

The portfolio section in `index.html` is an [Isotope](https://isotope.metafizzy.co/) grid wired up in `js/main.js`. Items carry a category class and are shown/hidden via `data-filter` buttons:

- `.brand` → "3D visualización" (images from `img/viz/IMGV*.webp`)
- `.photos` → "Photos" (images from `img/photos/IMG*.webp`)
- `.design` → "IA" — before/after AI image-editing comparisons, each a `<a class="portfolio-item design" href="#compare-aiN" data-lity>` opening a Lity lightbox modal (`<div id="compare-aiN" class="lity-hide">`) that shows a before/after pair. Source images live in `img/ai/` as `IMGxx.webp` / `IMGxx_after.webp` pairs.

Adding a new portfolio item means: add the image(s) under the right `img/` subfolder, add a `.single-item` block with the right category class in both `index.html` and `en/index.html`, and (for IA items) add a matching `#compare-aiN` modal block plus bump the `N` suffix.

## Color scheme

`index.html` and `video.html` pick a palette by swapping which stylesheet under `css/colors/` is linked as `#color-scheme` (currently `main-teal.css` for the live site, `main-darkgreen.css` for the unused `video.html`). The other ~20 files in `css/colors/` are unused template presets — don't delete them, they're just the theme's palette options, but don't assume more than one is "active" per page.

Page-specific overrides go in `css/custom.css` / `js/custom.js` — these are intentionally thin (the Kitzu template convention is "put your customizations here, leave `main.css`/`main.js` as vendor code").

## Contact form — currently dormant

`php/contact.php` is a PHP mail-handler (reads `$_POST['name'|'email'|'subject'|'message']`, sends via `mail()` to `belencabrerodelucas@gmail.com`, echoes `'success'` or an error string), and `js/main.js` has matching AJAX wiring that POSTs a `.contact-form`'s serialized data to its `action` attribute and expects that same `'success'` response.

However, the live `#contact` section in `index.html`/`en/index.html` currently has **no `<form>`** — it only shows static contact info (mailto/tel links). The PHP handler and JS AJAX logic are template leftovers, not wired to anything right now. If asked to "make the contact form work," you need to add an actual `<form class="contact-form" action="php/contact.php">` with matching field names — don't assume it already exists.

## robots.txt

Disallows `css/`, `js/`, `php/`, `pdf/`, and a few oddly-specific filenames (`import_os.json`, `re.txt`, etc.) that don't correspond to anything in this repo's web content — likely copied from elsewhere. `img/ai/`, `img/favicons/`, `img/photos/`, `img/viz/`, `index.html`, and `video.html` are explicitly allowed.
