# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Static personal website for **Dr. Jan Y. Yang** (pricinggoat.com) — Pricing Architect. No build step, no package manager, no JavaScript framework. Deploy by pushing files to the repo root; Netlify serves them directly.

**Live domain**: `www.pricinggoat.com` (set in `CNAME`)

## Deployment

No build command. Netlify publish directory is `/` (root). To preview locally, any static file server works:

```bash
python3 -m http.server 8000
# or
npx serve .
```

One Netlify-specific requirement: in **Site Settings → Build & Deploy → Post processing**, disable **Email address obfuscation** — Cloudflare's obfuscation corrupts the mailto link.

## Architecture

### Two separate design systems

**Main site** (`index.html`, `imprint.html`, `privacy.html`)
- Minimalist monospace aesthetic — `font-family: 'Courier New', monospace`; no Google Fonts
- CSS variables: `--ink`, `--paper`, `--accent` (#b5956a warm gold), `--muted`, `--line`, `--mono`
- All CSS is inline within each file's `<style>` block — no shared stylesheet

**Articles** (`pricinggoat-articles/`)
- Editorial magazine style — Lora serif (headings) + DM Sans (body) via Google Fonts
- CSS variables: `--ink`, `--paper`, `--gold` (#b8882a), `--gold-lt`, `--muted`, `--rule`, `--accent`
- Reusable CSS component classes: `.hero`, `.pullquote`, `.callout`, `.axioms` / `.axiom`, `.diagnostic`, `.closing`, `.brand-footer`
- Each article is a self-contained HTML file with all CSS inlined

### Content Security Policy

`_headers` enforces a strict Netlify CSP. The constraints that matter most when editing:
- **No external scripts** — `script-src 'self' 'unsafe-inline'` only; do not add third-party `<script>` tags
- **No external images** — `img-src 'self' data:` only; images must be local files
- **Google Fonts allowed** — `style-src` and `font-src` permit `fonts.googleapis.com` and `fonts.gstatic.com`
- **No iframes** — `frame-ancestors 'none'`

### Bilingual content

The full main site (when built out beyond the Coming Soon page) is bilingual EN/ZH. Legal pages (`imprint.html`, `privacy.html`) contain both English and German text — this is a GDPR/§5 TMG requirement for German-resident operators.

### AI discoverability

`llms.txt` is an AI-readable biography and services summary, intentionally maintained alongside the HTML content. `robots.txt` explicitly allows all major AI crawlers.

## Adding a new article

1. Copy `pricinggoat-articles/first-principles-pricing.html` as a template
2. Update all `<meta>` tags (og:title, og:description, og:url, description)
3. Add a `<url>` entry to `sitemap.xml`
4. Register the article URL in `pricinggoat-articles/index.html` if an article index exists

## Known placeholders

- `imprint.html`: search for `[to be added]` (EN) and `【待补充】` (ZH) — Steuernummer not yet filled in
- `index.html`: search for `your-baidu-verification-code` — Baidu Search Console verification pending
