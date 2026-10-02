# Site audit

Audit of www.pricinggoat.com as of 2026-10-02 (branch `claude/foundations-articles-audit`). Nothing below has been fixed unless marked **Fixed in this PR**. Items are ranked by likely impact on search traffic; each has a one-line fix.

## Urgent, but not a search issue

**Fixed in PR `claude/fix-legal-pages`:** both endings were rebuilt (no complete copy existed in git history). The page content itself was intact.

| Issue | Fix |
|---|---|
| `imprint.html` is truncated: the file ends at `<script`, so the language toggle never runs and the Chinese imprint is unreachable. The `.page-eyebrow` EN and ZH labels also both show, because `.page-eyebrow { display:flex }` overrides `.lang-zh { display:none }`. | Restore the closing script from `index.html` and wrap the eyebrow text in `span.lang-en` / `span.lang-zh`. |
| `privacy.html` is truncated mid-tag (`</d`): no closing `</div>`, footer, script, `</body>` or `</html>`. The toggle doesn't work and the Chinese policy is unreachable. The file has been broken since commit `2cd9bf1`. | Restore the missing tail (footer, toggle script, closing tags) from git history or rebuild it from `imprint.html`. |

## Ranked by impact on search traffic

### High

1. **Fixed in the EN/ZH split PR.** **Both languages share one URL, toggled by JavaScript.** Google indexes the page as English and treats the hidden Chinese as secondary or ignores it. The `hreflang="en"` and `hreflang="zh-Hans"` tags (in the page and in `sitemap.xml`) both point to the same URL, so they cancel out. Chinese queries (杨一安, 定价架构师, 定价制胜) have no page of their own to rank.
   *Fix:* split into `/` and `/zh/` with reciprocal hreflang (see the plan below).

2. **Fixed in the EN/ZH split PR.** **Hidden text block.** `#site-summary` in `index.html` sets `font-size:0.01px; color:transparent`. This is textbook hidden text under Google's spam policies and risks a manual action that would demote the whole domain.
   *Fix:* delete the block. Its content already lives in the meta description, JSON-LD and `llms.txt`.

3. **Google Fonts block rendering and are unreliable in mainland China.** Every page loads `fonts.googleapis.com` as a render-blocking stylesheet, and the homepage also loads the large Noto Serif SC family. From China the request often stalls, which delays first paint for Chinese visitors and slows Baidu crawling.
   *Fix:* self-host subsetted WOFF2 files (or fall back to system serif fonts for ZH) and drop the Google Fonts links.

4. **The article was orphaned and still links nowhere.** Before this PR nothing linked to `first-principles-pricing.html`. **Fixed in this PR:** it's now linked from the homepage and from the articles index. Still open: the article itself has zero outbound links (no home, no articles index, no contact), so it passes no authority back and has no conversion path.
   *Fix:* link the `brand-footer` wordmark to `/` and add an "All articles" link.

### Medium

5. **Fixed in the EN/ZH split PR.** **The article's metadata is broken.** There's no `<link rel="canonical">`. It has two conflicting sets of `og:title`, `og:description`, `og:type` and `og:url` (one `og:url` points to the homepage on the non-www host). `og:image` points to `/favicon.png`, which doesn't exist.
   *Fix:* keep one OG set, add a self-canonical, and point `og:image` to `/og-image.png`.

6. **No structured data on the article.** There's no `Article` or `BlogPosting` JSON-LD, no `article:published_time`, and no visible date.
   *Fix:* add `BlogPosting` JSON-LD with `author`, `datePublished` and `inLanguage`.

7. **Content stays invisible without JavaScript.** Every `.reveal` element starts at `opacity:0` and only becomes visible through `IntersectionObserver`. The `<noscript>` block un-hides ZH but doesn't reset `.reveal`. Crawlers that don't run JS (Baidu, most AI crawlers) see the text in the DOM but styled as invisible.
   *Fix:* add `.reveal { opacity:1; transform:none; }` to the `<noscript>` style block.

8. **Mobile horizontal overflow.** At 375px the homepage renders 509px wide, because `.footer-links` never wraps. Google indexes mobile-first, and this fails its mobile usability checks.
   *Fix:* add `flex-wrap: wrap; justify-content: center;` to `.footer-links` in the 900px media query.

9. **The homepage meta description is too long.** At 301 characters it's cut off at about 155 in results, and the important part (who and what) gets lost after the em dash.
   *Fix:* rewrite it to 140 to 155 characters, leading with "Pricing Architect".

10. **Fixed in the EN/ZH split PR.** **Two `<h1>` elements on the homepage** (EN and hidden ZH). This is minor on its own, but it compounds issue 1.
    *Fix:* resolved by the URL split, where each page gets one `<h1>`.

11. **The article exists only in English,** which breaks the bilingual rule and leaves Chinese search with no article content.
    *Fix:* publish a ZH version at the `/zh/` equivalent URL with reciprocal hreflang.

### Low

12. **Stale and inconsistent counts.** "10+ books" (homepage x5, `og-image.png`), "12 books" (homepage FAQ, `llms.txt` x2), "100+ companies", "20+ years", ">20× ROI". The counts contradict each other and break the evergreen rule. Inconsistent facts weaken trust signals for both Google and AI answers.
    *Fix:* replace them with evergreen phrasing everywhere, including the JSON-LD, `llms.txt` and the OG image.

13. **Em dashes in body copy** break the editorial rule: 30 on the homepage, 24 in the article, plus meta descriptions.
    *Fix:* replace them with full stops, commas or colons during the URL split rewrite.

14. **`robots.txt` uses outdated AI crawler names.** `Claude-Web` and `anthropic-ai` are retired; `ClaudeBot`, `Claude-SearchBot`, `Claude-User` and `OAI-SearchBot` are missing. Everything is already allowed through `User-agent: *`, so this has no practical effect.
    *Fix:* simplify to `User-agent: *` / `Allow: /` plus the sitemap line.

15. **Partly fixed in the EN/ZH split PR** (`description:zh` and `keywords:zh` are gone). **Non-standard meta tags.** `meta keywords`, `description:zh`, `keywords:zh` and `<link rel="sitemap">` are ignored by Google and Bing.
    *Fix:* delete them (less is more); use a real `/zh/` description instead.

16. **JSON-LD inconsistencies.** The Person `name` is "Jan Yang" while the page uses "Jan Y. Yang". `award` holds a sentence about being an author, which isn't an award.
    *Fix:* use "Jan Y. Yang" everywhere and remove `award`.

17. **Fixed in the EN/ZH split PR.** Moved to `/articles/`, with redirect stubs left at the old URLs. **The `/pricinggoat-articles/` path was redundant** (brand name repeated in the path).
    *Fix:* decide during the split whether to move to `/articles/`; if so, add redirect stubs (see below).

18. **No custom 404 page.** Broken links land on GitHub's generic 404 with no way back.
    *Fix:* add a minimal bilingual `404.html` linking home.

19. **Partly fixed in the EN/ZH split PR** (`_config.yml` deleted; `_headers` remains). **Dead files.** `_headers` (not read by GitHub Pages) and `pricinggoat-articles/_config.yml` (Jekyll ignores subfolder configs) do nothing.
    *Fix:* delete both.

20. **`README.md` is stale.** It still recommends Netlify, says the site deploys to `yourusername.github.io` and lists completed launch tasks.
    *Fix:* trim it to a short pointer to `CLAUDE.md`.

### Owner decision, not a search fix

- **Superlatives.** "中国定价第一人" and "unparalleled" appear in the homepage copy, JSON-LD and `llms.txt`. Article 9(3) of China's Advertising Law prohibits superlatives such as 国家级, 最高级 and 最佳 in commercial promotion; regulators routinely apply it to 第一, and Baidu and WeChat moderate such claims. Consider whether ZH pages should carry the phrase.
- **Chinese titles for English-only books.** Titles such as 定价即人 and 定价罗盘 read like published Chinese editions. Consider marking them as translations (for example 英文版) so readers don't search for editions that don't exist.

## EN/ZH split: done

Done in the EN/ZH split PR, following the plan that was here. Owner decisions applied: articles live at `/articles/`; the ZH homepage title leads with 杨一安博士; the article's ZH translation comes later.

| EN | ZH |
|---|---|
| `/` | `/zh/` |
| `/articles/` | `/zh/articles/` |
| `/articles/first-principles-pricing.html` | not yet (no hreflang until it exists) |
| `/imprint.html` | `/zh/imprint.html` |
| `/privacy.html` | `/zh/privacy.html` |

### Still open after the split

- **Baidu and Google:** submit `/zh/` and the new sitemap in Baidu 搜索资源平台 and Google Search Console, and request indexing for `/zh/` and `/articles/`.
- **Chinese-language visitors now land on English.** The old page switched to Chinese automatically; now they must click 中文. If that's a problem, add a small dismissible "中文版" link on `/` for browsers set to Chinese (never an automatic redirect).
- **ZH imprint has no "Professional title" block.** The English imprint has one; the Chinese one never did. The owner should supply the Chinese wording.
- **Article ZH translation** at `/zh/articles/first-principles-pricing.html`, then reciprocal hreflang.
- **ZH JSON-LD** carries the Person, WebSite, Books and ProfilePage blocks. The FAQ and services blocks are English-only and were left off `/zh/`; add Chinese versions if wanted.
