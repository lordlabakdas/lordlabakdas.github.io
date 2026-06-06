# Hugo Site Rebuild Design

**Date:** 2026-06-06  
**Status:** Approved

## Overview

Replace the existing Jekyll/Millennial blog with a Hugo static site using the PaperMod theme. The new site is a personal blog + portfolio with a minimal, clean aesthetic. Deployed to GitHub Pages via GitHub Actions.

## Architecture

- **Framework:** Hugo
- **Theme:** PaperMod (added as a Git submodule at `themes/PaperMod`)
- **Branch:** `gh-pages` (unchanged)
- **Deployment:** GitHub Actions — pushes to `gh-pages` trigger a Hugo build and deploy to GitHub Pages

### Directory Structure

```
content/
  posts/           # Blog posts (markdown)
  projects/        # One markdown file per project
  photography/     # One markdown file per photo/series
  about.md
  contact.md
static/
  img/photography/ # Photo image files
themes/PaperMod/   # Git submodule
hugo.yaml          # Site config
.github/workflows/hugo.yml  # Build & deploy action
assets/css/extended/custom.css  # Minimal style overrides
```

## Pages & Content

### Home
PaperMod profile mode: name, one-line bio, and a feed of recent blog posts below.

### Projects
List of project cards. Each project is a markdown file with frontmatter:
```yaml
title: ""
description: ""
tags: []
url: ""         # link to GitHub or live site
date: ""
```
Rendered as a simple Hugo list — no custom JS.

### Photography
Custom layout partial at `layouts/photography/single.html` (or list override). Images stored in `static/img/photography/`. Rendered as a CSS grid via `custom.css`. Clicking a photo opens full-size via a plain `<a>` tag. No JS lightbox library.

### About
Single freeform markdown page.

### Contact
Single markdown page with email and social links. No form (avoids backend/third-party dependency).

### Twitter/X Integration
- Social icon in header/footer via PaperMod's `socialIcons` config pointing to Twitter/X profile.
- Individual tweet embeds in posts use Hugo's built-in `{{< tweet >}}` shortcode.

## Visual Style

- PaperMod default light theme
- System font stack (no external fonts)
- No header/hero image
- Dark mode toggle retained (ships free with PaperMod)
- `assets/css/extended/custom.css` for overrides: photo grid column count, minor spacing tweaks

## Deployment

GitHub Actions workflow using:
- `peaceiris/actions-hugo` — installs Hugo
- `peaceiris/actions-gh-pages` — deploys built `public/` to GitHub Pages

Triggered on push to `gh-pages`.

## Migration

### Files to delete (Jekyll artifacts)
- `_posts/`
- `_layouts/`
- `_includes/`
- `_data/`
- `_config.yml`
- `Gemfile`
- `Gemfile.lock` (if present)
- `assets/css/`
- `rss-feed.xml`
- `index.html`
- `404.html`

### Content to migrate
- `_posts/2020-05-11-chaos-introduction.md` → `content/posts/2020-05-11-chaos-introduction.md` with updated Hugo frontmatter

## Navigation

Top-level menu: **Projects · Photography · About · Contact**  
Blog posts surface on the home page feed (not a separate nav item).

## Out of Scope

- Comments (no Disqus)
- Google Analytics (can be added later via PaperMod config)
- Contact form
- Lightbox JS for photography
