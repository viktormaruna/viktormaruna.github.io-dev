# dev.viktormaruna.com

Personal tech blog by Viktor Maruna — Data Engineer & Solution Architect at [Adastra](https://adastracorp.com/).

**Live site → [dev.viktormaruna.com](https://dev.viktormaruna.com)**

---

## About

Technical insights on data engineering, cloud architecture, and AI — with a focus on Microsoft Fabric, Azure Databricks, and real-world platform design. Written from personal experience, not employer communication.

## Tech Stack

| Layer | Technology |
|---|---|
| Static site generator | [Hugo](https://gohugo.io/) v0.157+ (extended) |
| Theme | Custom — `themes/vm/`, built from scratch |
| Hosting | GitHub Pages |
| CI/CD | GitHub Actions (push to `main` → build + link check + deploy) |
| Comments | [Giscus](https://giscus.app/) (GitHub Discussions) |
| Analytics | Microsoft Clarity |
| Fonts | [Inter](https://rsms.me/inter/) + [JetBrains Mono](https://www.jetbrains.com/lego/monospace/) via Google Fonts |

## Features

- ⚡ Zero JS frameworks — vanilla JS only
- 🌙 Dark / light mode, no flash on load (localStorage + prefers-color-scheme)
- 🔍 Client-side full-text search (JSON index at `/index.json`)
- 📖 Auto-generated Table of Contents on long posts
- 📋 One-click copy button on all code blocks
- 📊 Syntax highlighting — GitHub light + dark themes (Chroma)
- 📈 Reading progress bar on posts
- ↩ Prev / Next post navigation
- 🏷 Tag taxonomy with card grid listing page
- 🔗 Share to LinkedIn & X on every post
- 📡 RSS feed at `/feed.xml`
- 🗺 Sitemap at `/sitemap.xml`
- 💬 Giscus comments (GitHub Discussions, theme-aware — no flash)
- 🔏 Dual license — CC BY 4.0 for posts, MIT for code
- 🌐 Webmentions via [webmention.io](https://webmention.io/)
- 📣 Open Graph + Twitter Card meta on every page
- 🤖 Structured data (Schema.org JSON-LD) on every page
- ♿ WCAG AA contrast compliant (light & dark mode)
- 📄 Pagination (10 posts per page)
- 🔎 Internal link checking in CI via `htmltest`

## Local Development

**Prerequisites:** Hugo extended v0.157+

```bash
# Install Hugo (Linux)
wget https://github.com/gohugoio/hugo/releases/download/v0.157.0/hugo_extended_0.157.0_linux-amd64.deb
sudo dpkg -i hugo_extended_0.157.0_linux-amd64.deb
```

```bash
# Clone and serve
git clone https://github.com/viktormaruna/viktormaruna.github.io-dev.git
cd viktormaruna.github.io-dev

hugo server --port 1313          # live reload at http://localhost:1313
hugo server -D --port 1313       # include draft posts
```

```bash
# Production build
hugo --gc --minify
```

## Writing a Post

```bash
hugo new posts/my-post-title.md
```

Edit `content/posts/my-post-title.md`:

```yaml
---
title: "My Post Title"
date: 2026-01-15T10:00:00Z
draft: false
description: "Short description for SEO (~160 chars)"
tags: ["azure", "databricks", "data-engineering"]
---
```

Set `draft: false` when ready to publish. Push to `main` to deploy automatically.

## Project Structure

```text
hugo.toml                        # Site config (outputs, pagination, giscus, highlight)
content/
  posts/                         # Blog articles (Markdown)
  about.md                       # About page (layout: page — no post metadata)
layouts/                         # Project-root overrides (higher priority than theme)
  index.json                     # Search index template
  404.html                       # Custom 404 with recent posts
themes/vm/
  layouts/
    _default/
      baseof.html                # Master template — all CSS (~750 lines), JS, nav, footer
      single.html                # Individual post (breadcrumb, TOC, tags, share, prev/next)
      list.html                  # Section list (posts section)
      taxonomy.html              # Posts under a single tag
      terms.html                 # Tags index (card grid, sorted by post count)
      page.html                  # Static pages (no post metadata, used by about.md)
    index.html                   # Home page (intro + paginated post list)
    partials/
      comments.html              # Giscus integration (dynamic theme injection)
static/
  avatar.jpg                     # Profile photo (homepage intro)
  favicon.svg                    # SVG favicon
  og-default.png                 # Default OG image — replace with real 1200×630 PNG
  robots.txt                     # Allows all crawlers including AI bots
  humans.txt                     # humans.txt attribution
```

## Deployment

Push to `main` triggers `.github/workflows/hugo.yml`:

1. Installs Hugo extended v0.157 + Dart Sass
2. Restores Hugo build cache (keyed on content + theme + config hashes)
3. Builds with `--gc --minify`
4. Validates all internal links with `htmltest`
5. Deploys to GitHub Pages

Pull requests trigger a build-only run (no deploy) for validation.

The `CNAME` file sets the custom domain `dev.viktormaruna.com`.

## Known Issues

- `static/og-default.png` is an SVG file renamed to `.png` — social sharing previews (LinkedIn, Twitter) will not display an image until this is replaced with a real 1200×630 PNG.

## License

- **Blog posts and written content** — [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): share and adapt with attribution
- **Code snippets** — [MIT](LICENSE): use freely

*All content is personal and does not represent the views of my employer.*
