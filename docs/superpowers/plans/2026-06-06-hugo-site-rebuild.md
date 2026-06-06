# Hugo Site Rebuild Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the existing Jekyll/Millennial blog at `lordlabakdas.github.io` with a Hugo + PaperMod site featuring Projects, Photography, About, and Contact sections.

**Architecture:** Hugo static site with PaperMod as a Git submodule. Content lives in `content/` as Markdown files. A custom `layouts/photography/list.html` renders photos as a CSS grid. GitHub Actions builds on every push to `gh-pages` and deploys via the GitHub Pages API (no separate deploy branch).

**Tech Stack:** Hugo Extended v0.147.0, PaperMod theme, GitHub Actions (actions/checkout@v4, actions/configure-pages@v5, actions/upload-pages-artifact@v3, actions/deploy-pages@v4)

---

## File Map

| File | Action | Purpose |
|------|--------|---------|
| `hugo.yaml` | Create | Site config: title, theme, nav menu, social icons, profile mode |
| `themes/PaperMod/` | Create (git submodule) | Theme |
| `content/about.md` | Create | About page |
| `content/contact.md` | Create | Contact page |
| `content/projects/_index.md` | Create | Projects section landing |
| `content/projects/sample-project.md` | Create | Sample project entry (draft, for layout verification) |
| `content/photography/_index.md` | Create | Photography section landing |
| `content/photography/sample-photo.md` | Create | Sample photo entry (draft, for grid verification) |
| `layouts/photography/list.html` | Create | Custom CSS grid layout for photography section |
| `assets/css/extended/custom.css` | Create | Photography grid CSS + minor overrides |
| `content/posts/2020-05-11-introduction.md` | Create | Migrated blog post with Hugo frontmatter |
| `static/favicon.ico` | Move from root | Favicon served by Hugo's static pipeline |
| `static/img/` | Move from `assets/img/` | Images served by Hugo |
| `.github/workflows/hugo.yml` | Create | Build + deploy to GitHub Pages on push |

**Delete (Jekyll artifacts):**
`_posts/`, `_layouts/`, `_includes/`, `_data/`, `_config.yml`, `Gemfile`, `Gemfile.lock`, `assets/css/`, `rss-feed.xml`, `index.html`, `404.html`

---

### Task 1: Remove Jekyll artifacts and scaffold Hugo directory structure

**Files:**
- Delete: all Jekyll-specific files listed above
- Create: `content/posts/`, `content/projects/`, `content/photography/`, `layouts/photography/`, `assets/css/extended/`, `static/img/`, `.github/workflows/`

- [ ] **Step 1: Delete Jekyll-specific files**

```bash
rm -rf _posts _layouts _includes _data assets/css
rm -f _config.yml Gemfile Gemfile.lock rss-feed.xml index.html 404.html
```

Expected: command exits without error. `ls` should no longer show those files/dirs.

- [ ] **Step 2: Move existing assets into Hugo's static directory**

```bash
mkdir -p static/img
mv assets/img/* static/img/ 2>/dev/null || true
rmdir assets/img 2>/dev/null || true
rmdir assets 2>/dev/null || true
mv favicon.ico static/favicon.ico
```

- [ ] **Step 3: Create Hugo directory structure**

```bash
mkdir -p content/posts content/projects content/photography
mkdir -p layouts/photography
mkdir -p assets/css/extended
mkdir -p static/img/photography
mkdir -p themes
mkdir -p .github/workflows
```

- [ ] **Step 4: Verify structure looks right**

```bash
find . -maxdepth 3 -not -path './.git/*' -not -path './docs/*' -not -path './themes/*' | sort
```

Expected output includes: `./content`, `./layouts/photography`, `./static/img`, `./assets/css/extended`, `./.github/workflows`

- [ ] **Step 5: Commit**

```bash
git add -A
git commit -m "chore: remove Jekyll artifacts, scaffold Hugo directory structure"
```

---

### Task 2: Add PaperMod theme and create site config

**Files:**
- Create: `themes/PaperMod/` (git submodule)
- Create: `hugo.yaml`

**Prerequisite — Hugo must be installed locally:**
- macOS: `brew install hugo`
- Linux: `wget https://github.com/gohugoio/hugo/releases/download/v0.147.0/hugo_extended_0.147.0_linux-amd64.deb && sudo dpkg -i hugo_extended_0.147.0_linux-amd64.deb`
- Verify: `hugo version` — output should contain `v0.11x` or higher and `extended`

- [ ] **Step 1: Add PaperMod as a Git submodule**

```bash
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
git submodule update --init --recursive
```

Expected: `themes/PaperMod/` directory populated, `.gitmodules` file created.

- [ ] **Step 2: Create `hugo.yaml`**

Create the file at the repo root `/code/lordlabakdas.github.io/hugo.yaml`:

```yaml
baseURL: "https://lordlabakdas.github.io/"
title: "Siddharth Gangadhar"
theme: "PaperMod"
languageCode: "en-us"
paginate: 5
enableRobotsTXT: true
buildDrafts: false

params:
  env: production
  description: ""
  author: "Siddharth Gangadhar"

  profileMode:
    enabled: true
    title: "Siddharth Gangadhar"
    subtitle: ""

  socialIcons:
    - name: "twitter"
      url: "https://twitter.com/siddugan"
    - name: "github"
      url: "https://github.com/lordlabakdas"

  ShowReadingTime: true
  ShowShareButtons: false
  ShowPostNavLinks: true
  ShowBreadCrumbs: false

outputs:
  home:
    - HTML
    - RSS
    - JSON

menu:
  main:
    - name: "Projects"
      url: "/projects/"
      weight: 10
    - name: "Photography"
      url: "/photography/"
      weight: 20
    - name: "About"
      url: "/about/"
      weight: 30
    - name: "Contact"
      url: "/contact/"
      weight: 40
```

- [ ] **Step 3: Verify Hugo builds without errors**

```bash
hugo server -D
```

Expected: output shows `Web Server is available at http://localhost:1313/` with no `ERROR` lines. Home page at http://localhost:1313/ should load showing the PaperMod profile layout with the name "Siddharth Gangadhar". Stop with Ctrl+C.

- [ ] **Step 4: Commit**

```bash
git add hugo.yaml .gitmodules themes/PaperMod
git commit -m "feat: add PaperMod theme and site config"
```

---

### Task 3: Create About and Contact pages

**Files:**
- Create: `content/about.md`
- Create: `content/contact.md`

- [ ] **Step 1: Create `content/about.md`**

```markdown
---
title: "About"
url: "/about/"
layout: "page"
summary: "about"
---

Hi, I'm Siddharth Gangadhar.

[Add your bio here.]
```

- [ ] **Step 2: Create `content/contact.md`**

```markdown
---
title: "Contact"
url: "/contact/"
layout: "page"
summary: "contact"
---

- Twitter/X: [@siddugan](https://twitter.com/siddugan)
- GitHub: [lordlabakdas](https://github.com/lordlabakdas)
- Email: siddharth.gangadhar@gmail.com
```

- [ ] **Step 3: Verify both pages render**

```bash
hugo server -D
```

Navigate to http://localhost:1313/about/ — page should load with heading "About".
Navigate to http://localhost:1313/contact/ — page should load with the contact links.
Neither should return a 404. Stop with Ctrl+C.

- [ ] **Step 4: Commit**

```bash
git add content/about.md content/contact.md
git commit -m "feat: add About and Contact pages"
```

---

### Task 4: Create Projects section

**Files:**
- Create: `content/projects/_index.md`
- Create: `content/projects/sample-project.md`

- [ ] **Step 1: Create `content/projects/_index.md`**

```markdown
---
title: "Projects"
description: "Things I've built."
---
```

- [ ] **Step 2: Create `content/projects/sample-project.md`**

```markdown
---
title: "Sample Project"
date: 2024-01-01
description: "Replace this with a real project description."
tags: ["example"]
draft: true
---

Brief description of what this project does and why it exists.

[View on GitHub](https://github.com/lordlabakdas)
```

- [ ] **Step 3: Verify projects list renders**

```bash
hugo server -D
```

Navigate to http://localhost:1313/projects/ — should show the "Projects" heading and the sample project card (visible because `-D` includes drafts). Stop with Ctrl+C.

- [ ] **Step 4: Commit**

```bash
git add content/projects/
git commit -m "feat: add Projects section"
```

---

### Task 5: Create Photography section with CSS grid layout

**Files:**
- Create: `content/photography/_index.md`
- Create: `content/photography/sample-photo.md`
- Create: `layouts/photography/list.html`
- Create: `assets/css/extended/custom.css`

- [ ] **Step 1: Create `content/photography/_index.md`**

```markdown
---
title: "Photography"
description: "A collection of photographs."
---
```

- [ ] **Step 2: Add a placeholder image for testing**

Copy any `.jpg` into `static/img/photography/`. If you have none handy, run:

```bash
curl -Lo static/img/photography/placeholder.jpg \
  "https://picsum.photos/800/600"
```

- [ ] **Step 3: Create `content/photography/sample-photo.md`**

```markdown
---
title: "Sample Photo"
date: 2024-01-01
image: "/img/photography/placeholder.jpg"
draft: true
---
```

- [ ] **Step 4: Create `layouts/photography/list.html`**

```html
{{- define "main" }}
<header class="page-header">
  <h1>{{ .Title }}</h1>
  {{- with .Description }}
  <div class="post-description">{{ . }}</div>
  {{- end }}
</header>

<div class="photography-grid">
  {{- range .Pages.ByDate.Reverse }}
  {{- with .Params.image }}
  <a href="{{ . }}" class="photo-item" target="_blank" rel="noopener noreferrer">
    <img src="{{ . }}" alt="{{ $.Title }}" loading="lazy">
  </a>
  {{- end }}
  {{- end }}
</div>
{{- end }}
```

- [ ] **Step 5: Create `assets/css/extended/custom.css`**

```css
.photography-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 0.75rem;
  padding: 1rem 0;
}

.photo-item {
  display: block;
  overflow: hidden;
}

.photo-item img {
  width: 100%;
  height: 220px;
  object-fit: cover;
  display: block;
  transition: opacity 0.2s ease;
}

.photo-item:hover img {
  opacity: 0.85;
}
```

- [ ] **Step 6: Verify photography grid renders**

```bash
hugo server -D
```

Navigate to http://localhost:1313/photography/ — should show the "Photography" heading and the placeholder image rendered in a grid cell. Clicking the image should open the full-size version in a new tab. Stop with Ctrl+C.

- [ ] **Step 7: Commit**

```bash
git add content/photography/ layouts/photography/ assets/css/extended/ static/img/photography/
git commit -m "feat: add Photography section with CSS grid layout"
```

---

### Task 6: Migrate existing blog post

**Files:**
- Create: `content/posts/2020-05-11-introduction.md`

The original post content (retrieved from the Jekyll `_posts/` directory before deletion) was:

```
title: "Introduction"
author: "Siddharth Gangadhar"
categories: facts
tags: [facts, introduction]
image: grinter-farm-2013.jpg

Hi there!

## About blog
Documenting things that interest me.

## Twitter
[My Twitter Bio](http://twitter.com/siddugan/)

## Photo
Timestamp: August 24, 2013
Taken at: [Grinter Farms, Lawrence, Kansas](http://www.kansastravel.org/lawrence/grinterssunflowerfarm.htm)
```

- [ ] **Step 1: Create `content/posts/2020-05-11-introduction.md`**

```markdown
---
title: "Introduction"
date: 2020-05-11
author: "Siddharth Gangadhar"
tags: ["facts", "introduction"]
cover:
  image: "/img/grinter-farm-2013.jpg"
  alt: "Grinter Farms sunflower field, August 2013"
---

Hi there!

## About blog
Documenting things that interest me.

## Twitter
[My Twitter Bio](http://twitter.com/siddugan/)

## Photo
Timestamp: August 24, 2013
Taken at: [Grinter Farms, Lawrence, Kansas](http://www.kansastravel.org/lawrence/grinterssunflowerfarm.htm)
```

- [ ] **Step 2: Verify the post renders**

```bash
hugo server
```

Navigate to http://localhost:1313/ — the "Introduction" post should appear in the home page feed.
Click the post — it should render with the cover image and full content. Stop with Ctrl+C.

- [ ] **Step 3: Commit**

```bash
git add content/posts/2020-05-11-introduction.md
git commit -m "feat: migrate Introduction post to Hugo"
```

---

### Task 7: GitHub Actions deployment workflow

**Files:**
- Create: `.github/workflows/hugo.yml`

**One-time manual setup in GitHub (do this before pushing):**
1. Go to the repository on GitHub → **Settings** → **Pages**
2. Under "Build and deployment" → **Source**, select **GitHub Actions**
3. Click Save

- [ ] **Step 1: Create `.github/workflows/hugo.yml`**

```yaml
name: Deploy Hugo site to Pages

on:
  push:
    branches:
      - gh-pages
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.147.0
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb \
            https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb

      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0

      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5

      - name: Build with Hugo
        env:
          HUGO_CACHEDIR: ${{ runner.temp }}/hugo_cache
          HUGO_ENVIRONMENT: production
        run: |
          hugo --gc --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

- [ ] **Step 2: Commit and push**

```bash
git add .github/workflows/hugo.yml
git commit -m "feat: add GitHub Actions workflow for Hugo deployment"
git push origin gh-pages
```

- [ ] **Step 3: Verify deployment**

1. Go to the GitHub repository → **Actions** tab
2. Watch the "Deploy Hugo site to Pages" workflow run — wait for the green checkmark
3. Visit https://lordlabakdas.github.io/
4. Verify: home page loads with "Siddharth Gangadhar" profile, nav shows Projects / Photography / About / Contact, Twitter and GitHub icons visible

---

## Self-Review Checklist

- [x] Hugo + PaperMod setup — Task 2
- [x] Delete Jekyll artifacts — Task 1
- [x] Projects section — Task 4
- [x] Photography section with grid — Task 5
- [x] About page — Task 3
- [x] Contact page — Task 3
- [x] Twitter/X social icon in nav — Task 2 (`hugo.yaml` socialIcons with `siddugan` handle)
- [x] Navigation: Projects · Photography · About · Contact — Task 2 (`menu.main`)
- [x] Migrate existing post with Hugo frontmatter — Task 6
- [x] GitHub Actions deployment — Task 7
- [x] Favicon moved to `static/` — Task 1
- [x] Existing image (`grinter-farm-2013.jpg`) moved to `static/img/` — Task 1, referenced in Task 6
- [x] No placeholders, TBDs, or "implement later" steps — all steps contain complete code
