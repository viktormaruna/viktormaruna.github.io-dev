# Copilot Instructions — dev.viktormaruna.com

Personal tech blog by Viktor Maruna. Hugo static site deployed to GitHub Pages at `dev.viktormaruna.com`.

## Build & Serve

Hugo is installed locally at `~/bin/hugo` (not on system PATH by default — `~/bin` is added via `~/.bashrc`).

```bash
# Build to public/
~/bin/hugo

# Local dev server (live reload)
~/bin/hugo server --bind 0.0.0.0 --port 1313

# Build with drafts
~/bin/hugo -D

# Build for production (minified, used by CI)
~/bin/hugo --gc --minify --baseURL "https://dev.viktormaruna.com/"
```

> **Version mismatch**: local binary is v0.157.0; GitHub Actions CI uses v0.148.0. Test against CI version if deploying.

## Architecture

```
hugo.toml              # Site config — outputs, taxonomies, giscus params, RSS
layouts/               # Project-root overrides (takes priority over theme)
  index.json           # Search index template (JSON output for home page)
themes/vm/
  layouts/
    _default/
      baseof.html      # Master template — ALL CSS, JS, nav, footer live here (~500 lines)
      single.html      # Individual post pages
      list.html        # Post listing pages
    index.html         # Home page template
    partials/
      comments.html    # Giscus integration
content/
  posts/               # Blog articles (Markdown)
static/
  robots.txt
```

**Everything visual is in `baseof.html`** — there are no external CSS files. The entire design system (tokens, nav, dark mode, search, typography, responsive breakpoints) lives as an inline `<style>` block in that single file.

## Key Conventions

### Hugo v0.157 template naming
Output format templates must use the format's media type extension, **not** `.html`:
- ✅ `layouts/index.json` — works in v0.157
- ❌ `layouts/index.json.html` — silently ignored in v0.157 (no warning about the file being wrong, just "no layout found")

Place non-HTML output format templates in the project-root `layouts/` (not the theme), as v0.157 has inconsistent theme lookup for these files.

### Dark mode
Driven entirely by a `data-theme` attribute on `<html>`:
```js
document.documentElement.setAttribute('data-theme', 'dark'); // or 'light'
```
CSS uses `:root` for light and `[data-theme="dark"]` for dark overrides. **Never toggle a CSS class** — only the attribute. An anti-FOUC inline script in `<head>` reads `localStorage` before the stylesheet renders.

### CSS design tokens
All colours and fonts use CSS variables defined in `:root`:

| Token | Purpose |
|---|---|
| `--bg` / `--fg` | Page background / foreground text |
| `--muted` / `--faint` | Secondary text / disabled/placeholder |
| `--accent` / `--accent-h` | Brand colour (teal) / hover state |
| `--border` / `--code-bg` | Dividers / inline code background |
| `--nav-bg` | Slightly tinted navbar background |
| `--selection` | Text selection highlight |

Light: warm cream (`#fefcf8` bg, `#0d9488` accent). Dark: deep charcoal (`#0d0c0a` bg, `#2dd4bf` accent).

### Search
Client-side full-text search:
- Index generated at `/index.json` via `layouts/index.json` (project root)
- Home page outputs: `["HTML", "RSS", "JSON"]` in `hugo.toml`
- JS fetches `/index.json` on first open of the search dropdown, then filters in memory
- Highlights matches with `<mark>` tags styled via `--selection`

### RSS feed
Served at `/feed.xml` (not `/rss.xml`) — configured with `baseName = "feed"` under `[outputFormats.RSS]` in `hugo.toml`.

### Post front matter
```toml
title       = "Post Title"
date        = 2025-01-15T10:00:00Z
draft       = false
description = "Short description for SEO and Open Graph (~160 chars)"
tags        = ["cloud", "data"]
categories  = ["Article"]
```
Use `hugo new posts/my-post.md` to scaffold from the archetype (auto-generates title from filename, sets `draft = true`).

### Giscus comments
Configured under `[params.giscus]` in `hugo.toml`. The `comments.html` partial syncs the Giscus iframe theme whenever the user toggles dark/light mode via `postMessage`.

### Deployment
Push to `main` triggers `.github/workflows/hugo.yml` → builds with `--gc --minify` → deploys to GitHub Pages. The CNAME file sets the custom domain (`dev.viktormaruna.com`).
