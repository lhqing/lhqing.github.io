# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Hanqing Liu's personal academic website (https://lhqing.github.io), built on the [al-folio](https://github.com/alshedivat/al-folio) Jekyll theme. It is a static site; most "development" here is editing content (bibliography, about page, data files) rather than writing code.

## Commands

```bash
bundle install                          # install Ruby gem dependencies (first-time setup)
bundle exec jekyll serve --lsi          # local dev server with live reload (--lsi enables related-posts indexing)
bundle exec jekyll build --lsi          # build static site into _site/
bin/cibuild                             # exact build command CI runs (jekyll build --lsi)
docker-compose up                       # alternative: run via prebuilt Docker image (no local Ruby needed)
```

There is no test suite; `bin/cibuild` building successfully is the validation step. Pre-commit hooks (`.pre-commit-config.yaml`) run trailing-whitespace, end-of-file, check-yaml, and large-file checks.

## Deployment

Pushing to `master` triggers `.github/workflows/deploy.yml`: GitHub Actions builds with Jekyll and force-pushes the result to the `gh-pages` branch, which GitHub Pages serves. **Never edit `gh-pages` directly** — it is generated. Pull requests trigger a build-only check (no deploy).

## Where content lives

Editing these covers the large majority of real changes:

- **`_bibliography/papers.bib`** — the publication list. The `/publications/` page is generated entirely from this BibTeX file via the `jekyll-scholar` plugin. Custom BibTeX keywords control rendering: `selected` (show on homepage), `preview` (thumbnail image in `assets/img/publication_preview/`), `abbr`, `abstract`, `arxiv`, `pdf`, `code`, `html`, `bibtex_show`, `altmetric`, `dimensions`, etc. These keywords are stripped from displayed BibTeX via `filtered_bibtex_keywords` in `_config.yml` (handled by `_plugins/hideCustomBibtex.rb`).
- **`_pages/about.md`** — the homepage (`about` layout).
- **`_pages/publications.md`** — the publications landing page wrapper.
- **`_data/*.yml`** — `coauthors.yml` (auto-links co-author names in publications), `cv.yml`, `repositories.yml`, `venues.yml`.
- **`_config.yml`** — site-wide settings: author identity, `scholar:` block (author name-matching for highlighting/bolding own name, citation style), social links, enabled features, plugin config. **A `_config.yml` change requires restarting `jekyll serve` to take effect.**

## Architecture notes

- **Jekyll structure**: `_layouts/` (page templates: `about`, `bib`, `cv`, `distill`, `page`, etc.), `_includes/` (reusable Liquid partials), `_sass/` + `assets/css/main.scss` (styles; theme color is `--global-theme-color` in `_sass/_themes.scss`), `assets/js/` (front-end JS including the Distill template).
- **Collections**: `news` and `projects` are Jekyll collections defined in `_config.yml`. News items render on the homepage; projects render as a grid.
- **Custom plugins** in `_plugins/`:
  - `external-posts.rb` — fetches external blog posts (e.g. Medium) via RSS at build time into the `posts` collection.
  - `hideCustomBibtex.rb` — Liquid filter stripping internal BibTeX keywords from rendered output.
  - `details.rb` — adds a `{% details %}` Liquid block tag rendering a collapsible `<details>` element.
- **Images**: `jekyll-imagemagick` generates responsive WebP variants of images in `assets/img/` at build time (requires ImageMagick installed).

## Conventions

- This repo tracks an upstream theme; large structural changes risk conflicts when syncing from `alshedivat/al-folio`. Prefer content/config edits over restructuring layouts/includes unless intentionally diverging from the theme.
- Don't commit `_site/`, `vendor/`, or `Gemfile.lock` (all gitignored).
