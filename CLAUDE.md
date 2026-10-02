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

Every public page exists twice: English at the root, Chinese under `/zh/`.

| EN | ZH | Purpose |
|----|----|---------|
| `index.html` | `zh/index.html` | Homepage: hero, about, services, capability programme, books, articles, writing, contact |
| `articles/index.html` | `zh/articles/index.html` | Articles listing page |
| `articles/*.html` | (none yet) | Individual articles, English only for now |
| `imprint.html`, `privacy.html` | `zh/imprint.html`, `zh/privacy.html` | Legal pages (`noindex`) |

`pricinggoat-articles/` holds only redirect stubs for the old article URLs. Keep them; don't add content there.

### Two separate design systems

**Main site** (homepages, legal pages and articles listing, EN and ZH)
- Cormorant Garamond (serif) and DM Mono (labels, nav, buttons) via Google Fonts; `/zh/` pages also load Noto Serif SC
- CSS variables: `--ink`, `--paper`, `--accent` (#b5956a warm gold), `--accent-light`, `--muted`, `--line`, `--serif`, `--serif-zh`, `--mono`
- All CSS is inline within each file's `<style>` block; there is no shared stylesheet. Keep it that way.

**Articles** (`articles/*.html`, except the listing page)
- Editorial style: Lora serif (headings) + DM Sans (body) via Google Fonts
- CSS variables: `--ink`, `--paper`, `--gold` (#b8882a), `--gold-lt`, `--muted`, `--rule`, `--accent`
- Component classes: `.hero`, `.hero-nav` (links back to the articles index and home; keep it on every article), `.pullquote`, `.callout`, `.axioms` / `.axiom`, `.diagnostic`, `.closing`, `.brand-footer`
- Each article is a self-contained HTML file with all CSS inlined

### Bilingual content

Each page holds one language only. A change to visible copy must be made in both the EN file and its `/zh/` counterpart.

Every EN/ZH pair carries, on both pages:
- `<html lang="en">` or `<html lang="zh-Hans">`
- a self-referencing canonical (never point `/zh/` at the EN page)
- reciprocal `hreflang` links for `en`, `zh-Hans` and `x-default` (the EN URL)
- a language switch that is a plain link to the counterpart page (`中文` / `EN`), with no auto-redirect

Only add `hreflang` to a page whose counterpart exists. The articles have no ZH version yet, so they carry none.

Use root-absolute paths (`/favicon.ico`, `/articles/`) for anything shared between the two folders. Links between pages of the same language can stay relative.

### Security headers

`_headers` is not read by GitHub Pages, so nothing in it is enforced. The effective policy is the `<meta http-equiv="Content-Security-Policy">` tag in each main-site page.

### AI discoverability

`llms.txt` is an AI-readable biography and services summary, maintained alongside the HTML content. `robots.txt` explicitly allows all major AI crawlers.

## Brand and editorial rules

- **Less is more.** When in doubt, remove rather than add.
- **No em dashes in body copy.** Use a full stop, comma, colon or parentheses instead.
- **No goat imagery anywhere.** The name is a wordmark only.
- **Evergreen credentials.** No counts that go stale ("12 books", "100+ companies"). Prefer phrasing like "Springer-published author" or "two decades in pricing".
- **All public pages are bilingual EN/ZH.** Every new visible string needs both an EN and a ZH version. One exception: an individual article may exist in one language only (see below).

## Articles: source and policy

- **Source:** the owner picks pieces already published on LinkedIn (EN) or WeChat 定价制胜-Dr. Pricing (ZH). Claude polishes them and publishes them here. There is no other source.
- **Polish, don't rewrite.** Keep the owner's voice, argument and structure. Fix typos and grammar, tighten wording, add headings where they help, and apply the editorial rules above (no em dashes, no stale counts). Anything beyond that, such as cutting or adding a paragraph or changing a claim, is proposed to the owner, not done silently.
- **One language is fine ("relaxed" rule, owner decision 2026-10-02).** An article may exist only in the language it was written in. List it on both `articles/index.html` and `zh/articles/index.html`, with the title and summary in each page's language and a badge (`EN` / `ZH`) for the article's language. Add a translation only when the owner asks; then add reciprocal `hreflang`.
- **Credit the original.** End every article with one muted line naming where and when it first appeared: `First published on LinkedIn, March 2026.` or `首发于微信公众号「定价制胜-Dr. Pricing」，2026年3月。` Ask the owner for the date if it isn't given.
- **Language folders:** EN articles go in `articles/`, ZH articles in `zh/articles/`. A ZH-only article page uses `<html lang="zh-Hans">` and Chinese UI labels.

## Adding a new article

1. Copy `articles/first-principles-pricing.html` as a template
2. Update `<title>`, description, canonical and all Open Graph tags
3. Add an entry to both `articles/index.html` and `zh/articles/index.html`
4. Update the articles section in `index.html` and `zh/index.html` if the new article should be featured
5. Add a `<url>` entry to `sitemap.xml` and bump the `lastmod` of every page you touched
6. A ZH translation goes at `zh/articles/<same-name>.html`; then add reciprocal `hreflang` to both versions and `xhtml:link` alternates in the sitemap
