# dev.viktormaruna.com

Personal tech blog by Viktor Maruna — Data Engineer & Cloud Solution Architect at [Adastra](https://adastracorp.com/).

**Live site → [dev.viktormaruna.com](https://dev.viktormaruna.com)**

---

## About

Technical insights on data engineering, cloud architecture, and AI — with a focus on Microsoft Fabric, Azure Databricks, and real-world platform design.

## Tech Stack

| Layer | Technology |
|---|---|
| Static site generator | [Hugo](https://gohugo.io/) v0.157+ (extended) |
| Theme | Custom — `themes/vm/`, built from scratch, zero dependencies |
| Hosting | GitHub Pages |
| CI/CD | GitHub Actions — build, lint, link check, deploy |
| Comments | [Giscus](https://giscus.app/) (GitHub Discussions, theme-aware) |
| Analytics | Microsoft Clarity |
| Monetisation | [Buy Me a Coffee](https://www.buymeacoffee.com/viktormaruna) (About page) |
| Fonts | [Inter](https://rsms.me/inter/) + [JetBrains Mono](https://www.jetbrains.com/lego/monospace/) via Google Fonts |

## Features

**Performance & Architecture**

- ⚡ Zero JS frameworks — vanilla JavaScript only (~130 lines total)
- 🎨 All CSS inline in a single template (~770 lines) — no external stylesheets, no build tools
- 📈 Reading progress bar on individual posts
- 📄 Pagination (10 posts per page)

**Theming & Accessibility**

- 🌙 Dark / light mode with no flash on load (localStorage + `prefers-color-scheme`)
- ♿ WCAG AA contrast compliant in both modes
- 🎨 Design tokens via CSS custom properties (`--bg`, `--fg`, `--accent`, etc.)
- ✍️ GitHub-style syntax highlighting — separate light and dark Chroma themes

**Navigation & Discovery**

- 🔍 Client-side full-text search (Ctrl/Cmd+K) — JSON index at `/index.json`, search by title, summary, or tags
- 🏷 Tag taxonomy with card-grid listing page (sorted by post count)
- ↩ Prev / Next post navigation within sections
- 🍞 Breadcrumb navigation on individual posts

**Content Features**

- 📖 Auto-generated, collapsible Table of Contents on long posts (h2–h4)
- 📋 One-click copy button on all code blocks
- 🔗 Share to Email, LinkedIn, X, and Bluesky on every post
- 💬 Giscus comments on posts — theme syncs live with dark/light toggle

**SEO & Discoverability**

- 📣 Open Graph + Twitter Card meta on every page
- 🤖 Structured data (Schema.org JSON-LD): `WebSite`, `BlogPosting`, `CollectionPage`
- 📡 RSS feed at `/feed.xml` with autodiscovery `<link>` tag
- 🗺 Sitemap at `/sitemap.xml`
- 🌐 Webmentions via [webmention.io](https://webmention.io/)
- 🤖 AI-friendly: `robots.txt` allows all crawlers + AI bots (GPTBot, Claude, Bard, Perplexity)

**Mobile**

- 📱 Responsive layout with hamburger nav (collapsible links, persistent search & theme toggle)
- 📱 Mobile nav dropdown uses absolute positioning — no layout shift on open/close

**Quality**

- 🔎 Internal link checking in CI via [htmltest](https://github.com/wjdp/htmltest)
- 📝 Markdown linting in CI via [markdownlint-cli2](https://github.com/DavidAnson/markdownlint-cli2)
- 🔏 Dual license — CC BY 4.0 for content, MIT for code

## Local Development

**Prerequisites:** Hugo extended v0.157+

```bash
# Install Hugo (Linux — .deb)
wget https://github.com/gohugoio/hugo/releases/download/v0.157.0/hugo_extended_0.157.0_linux-amd64.deb
sudo dpkg -i hugo_extended_0.157.0_linux-amd64.deb

# Or download the binary to ~/bin
wget -qO- https://github.com/gohugoio/hugo/releases/download/v0.157.0/hugo_extended_0.157.0_linux-amd64.tar.gz \
  | tar xz -C ~/bin hugo
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
hugo.toml                        # Site config (outputs, pagination, giscus, syntax highlight)
content/
  about.md                       # About page (type: staticpage — no post metadata)
  posts/
    _index.md                    # Posts section landing page
    *.md                         # Blog articles (Markdown with YAML front matter)
layouts/                         # Project-root layout overrides (higher priority than theme)
  _default/                      # (empty — all templates live in theme)
  index.json                     # Search index template (generates /index.json)
  404.html                       # Custom 404 page with recent posts
themes/vm/
  layouts/
    _default/
      baseof.html                # Master template — all inline CSS (~770 lines), JS (~130 lines),
                                 #   nav HTML, footer, <head> meta, structured data, Clarity script
      single.html                # Individual post (breadcrumb, TOC, meta, tags, share, prev/next, comments)
      list.html                  # Section list (posts)
      taxonomy.html              # Posts under a single tag
      terms.html                 # Tags index (card grid, sorted by post count)
    staticpage/
      single.html                # Static page template (About) — avatar header, no post metadata,
                                 #   cert date pill styling, Buy Me a Coffee widget
    index.html                   # Home page (avatar intro, social links, paginated post list)
    partials/
      comments.html              # Giscus integration (dynamic theme injection, live sync on toggle)
static/
  avatar.jpg                     # Profile photo (homepage + about page)
  favicon.svg                    # SVG favicon
  og-default.png                 # Default Open Graph image (1200×630)
  robots.txt                     # Allows all crawlers including AI bots
  humans.txt                     # humans.txt attribution
.github/
  workflows/
    hugo.yml                     # Build + link check + deploy to GitHub Pages
    lint.yml                     # Markdown linting (markdownlint-cli2)
  copilot-instructions.md        # GitHub Copilot context for this repo
.htmltest.yml                    # htmltest config (internal links only)
.markdownlint.json               # markdownlint rules (MD013/MD036/MD041/MD060 disabled)
CNAME                            # Custom domain: dev.viktormaruna.com
```

## Architecture Notes

**Single-file theme.** All CSS and JS lives inline in `baseof.html` (1000 lines). There are no external stylesheets, no build pipeline, and no npm dependencies. Design tokens are CSS custom properties under `:root` / `[data-theme="dark"]`.

**Template lookup.** The About page uses `type: "staticpage"` in its front matter, routing it to `themes/vm/layouts/staticpage/single.html`. This avoids the Hugo gotcha where `_default/page.html` would match all regular pages (kind = "page") and override `single.html` for blog posts.

**Mobile navigation.** On screens ≤640px, the hamburger button collapses only the nav links. Search and theme toggle remain persistently visible in a `nav-utils` container. The dropdown uses `position: absolute` to float below the 56px nav bar without causing layout reflow.

**Search.** The search index is a JSON file generated at build time (`layouts/index.json`). Client-side JS fetches it lazily on first search interaction, then filters by title, summary, and tags with match highlighting. Opens with Ctrl/Cmd+K or the search icon.

**Comments.** Giscus loads dynamically after page load. Theme is read from `data-theme` at injection time. When the user toggles dark/light mode, the script sends a `postMessage` to the Giscus iframe to sync the theme without reload.

**Syntax highlighting.** Uses Hugo's Chroma with `noClasses = false` — CSS classes are emitted, and separate light/dark rulesets in `baseof.html` handle theming via `[data-theme="dark"]` selectors. Both themes follow GitHub's colour scheme.

## Deployment

Push to `main` triggers `.github/workflows/hugo.yml`:

1. Installs Hugo extended v0.157 + Dart Sass v1.89.2
2. Restores Hugo build cache (keyed on content + theme + config + static hashes)
3. Builds with `--gc --minify`
4. Validates all internal links with htmltest v0.17
5. Deploys to GitHub Pages

A separate `lint.yml` workflow runs markdownlint-cli2 on `content/**/*.md` and `README.md`.

Pull requests trigger build + lint (no deploy) for validation.

The `CNAME` file sets the custom domain `dev.viktormaruna.com`.

## Known Issues

- `static/og-default.png` is an SVG file renamed to `.png` — social sharing previews (LinkedIn, X) may not display an image until replaced with a real 1200×630 PNG.

## License

- **Blog posts and written content** — [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/): share and adapt with attribution
- **Code and templates** — [MIT](LICENSE): use freely
