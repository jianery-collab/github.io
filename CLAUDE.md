# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Static personal website for **Dr. Jan Y. Yang** (pricinggoat.com), Pricing Architect. No build step, no package manager, no JavaScript framework. Hosted on GitHub Pages, served directly from the `main` branch root.

**Live domain**: `www.pricinggoat.com` (set in `CNAME`)

## Workflow

The owner reviews every change via pull request. Work on a branch, open a PR, and never push to or merge into `main` directly.

## Deployment

No build command. GitHub Pages serves the repo root (Settings → Pages → Source: main branch / root). To preview locally, any static file server works:

```bash
python3 -m http.server 8000
```

## Architecture

### Pages

| Path | Purpose |
|------|---------|
| `index.html` | Homepage: hero, about, services, capability programme, books, articles, writing, contact |
| `pricinggoat-articles/index.html` | Articles listing page |
| `pricinggoat-articles/*.html` | Individual articles |
| `imprint.html`, `privacy.html` | Legal pages (`noindex`) |

### Two separate design systems

**Main site** (`index.html`, `imprint.html`, `privacy.html`, `pricinggoat-articles/index.html`)
- Cormorant Garamond (serif), DM Mono (labels, nav, buttons) and Noto Serif SC (Chinese) via Google Fonts
- CSS variables: `--ink`, `--paper`, `--accent` (#b5956a warm gold), `--accent-light`, `--muted`, `--line`, `--serif`, `--serif-zh`, `--mono`
- All CSS is inline within each file's `<style>` block; there is no shared stylesheet. Keep it that way.

**Articles** (`pricinggoat-articles/*.html`, except the listing page)
- Editorial style: Lora serif (headings) + DM Sans (body) via Google Fonts
- CSS variables: `--ink`, `--paper`, `--gold` (#b8882a), `--gold-lt`, `--muted`, `--rule`, `--accent`
- Component classes: `.hero`, `.pullquote`, `.callout`, `.axioms` / `.axiom`, `.diagnostic`, `.closing`, `.brand-footer`
- Each article is a self-contained HTML file with all CSS inlined

### Bilingual content

Main-site pages carry both languages in the same HTML. Elements are paired with `.lang-en` / `.lang-zh` classes, and a small inline script toggles `body.zh` (auto-enabled when the browser language starts with `zh`). A `<noscript>` style block shows Chinese content to crawlers without JS. Splitting EN and ZH into separate URLs is planned; see `AUDIT.md`.

### Security headers

`_headers` is not read by GitHub Pages, so nothing in it is enforced. The effective policy is the `<meta http-equiv="Content-Security-Policy">` tag in each main-site page.

### AI discoverability

`llms.txt` is an AI-readable biography and services summary, maintained alongside the HTML content. `robots.txt` explicitly allows all major AI crawlers.

## Brand and editorial rules

- **Less is more.** When in doubt, remove rather than add.
- **No em dashes in body copy.** Use a full stop, comma, colon or parentheses instead.
- **No goat imagery anywhere.** The name is a wordmark only.
- **Evergreen credentials.** No counts that go stale ("12 books", "100+ companies"). Prefer phrasing like "Springer-published author" or "two decades in pricing".
- **All public pages are bilingual EN/ZH.** Every new visible string needs both an EN and a ZH version.

## Adding a new article

1. Copy `pricinggoat-articles/first-principles-pricing.html` as a template
2. Update `<title>`, description, canonical and all Open Graph tags
3. Add an entry to `pricinggoat-articles/index.html` (EN and ZH)
4. Update the articles section in `index.html` if the new article should be featured
5. Add a `<url>` entry to `sitemap.xml` and bump the `lastmod` of every page you touched
