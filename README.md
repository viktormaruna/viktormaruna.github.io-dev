# dev.viktormaruna.com

Personal tech blog by Viktor Maruna — Cloud, Data & AI engineer at [Adastra](https://adastracorp.com/).

**Live site → [dev.viktormaruna.com](https://dev.viktormaruna.com)**

---

## About

This blog covers technical insights on data engineering, cloud architecture, and AI — with a focus on Microsoft Fabric, Azure Databricks, and real-world platform design. Posts are written from personal experience, not employer communication.

## Tech Stack

| Layer | Technology |
|---|---|
| Static site generator | [Hugo](https://gohugo.io/) v0.148+ (extended) |
| Theme | Custom — `themes/vm/`, built from scratch |
| Hosting | GitHub Pages |
| CI/CD | GitHub Actions (push to `main` → auto-deploy) |
| Comments | [Giscus](https://giscus.app/) (GitHub Discussions) |
| Analytics | Microsoft Clarity |
| Font | [Inter](https://rsms.me/inter/) + [JetBrains Mono](https://www.jetbrains.com/lego/monospace/) via Google Fonts |

## Features

- ⚡ Zero JS frameworks — vanilla JS only
- 🌙 Dark / light mode with no flash on load
- 🔍 Client-side search (JSON index at `/index.json`)
- 📖 Auto-generated Table of Contents on long posts
- 📋 One-click copy button on all code blocks
- ↩ Prev / Next post navigation
- 🏷 Tag taxonomy with listing pages
- 🔗 Share to LinkedIn & X on every post
- 📡 RSS feed at `/feed.xml`
- 🗺 Sitemap at `/sitemap.xml`
- 💬 Giscus comments (GitHub Discussions)
- 🔏 Dual license — CC BY 4.0 for posts, MIT for code
- 🌐 Webmentions via [webmention.io](https://webmention.io/)
- 📊 Open Graph + Twitter Card meta on every page

## Local Development

**Prerequisites:** Hugo extended v0.148+

```bash
# Install Hugo (Linux — adjust for your OS)
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
hugo --gc --minify --baseURL "https://dev.viktormaruna.com/"
```

## Writing a Post

```bash
# Scaffold a new post from archetype
hugo new posts/my-post-title.md
```

Edit `content/posts/my-post-title.md` — front matter template:

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
hugo.toml                  # Site config — outputs, giscus, RSS, TOC settings
content/
  posts/                   # Blog articles (Markdown)
  about/                   # About page
  license.md               # License page
layouts/                   # Project-root overrides (higher priority than theme)
  index.json               # Search index template (JSON output)
  404.html                 # Custom 404 page
themes/vm/
  layouts/
    _default/
      baseof.html          # Master template — all CSS, JS, nav, footer
      single.html          # Individual post layout
      list.html            # Post list layout
    index.html             # Home page
    partials/
      comments.html        # Giscus integration
static/
  robots.txt
  humans.txt
  og-default.png           # Default Open Graph image (replace with a real PNG)
```

## Deployment

Pushing to `main` triggers `.github/workflows/hugo.yml`:

1. Installs Hugo extended + Dart Sass
2. Builds with `--gc --minify`
3. Uploads to GitHub Pages

The `CNAME` file sets the custom domain `dev.viktormaruna.com`.

## License

- **Blog posts and written content** — [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): share and adapt with attribution
- **Code snippets** — [MIT](LICENSE): use freely

*All content is personal and does not represent the views of my employer.*
