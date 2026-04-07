# pricinggoat.com

Personal website of **Dr. Jan Y. Yang** — Pricing Architect, Pricing by Design.

## Contents

| File | Description |
|------|-------------|
| `index.html` | Main site — bilingual EN/ZH, all sections |
| `imprint.html` | Impressum (legally required in Germany, §5 TMG) |
| `privacy.html` | Privacy Policy / Datenschutzerklärung (GDPR-compliant) |
| `wechat-qr.jpg` | WeChat Official Account QR code (定价制胜-Dr. Pricing) |

## Deployment

This is a static site — no build step required. Deploy by pushing all files to the root of this repository, then connect via Netlify or GitHub Pages.

### Netlify (recommended)
1. Connect this repo in Netlify
2. Set publish directory to `/` (root)
3. No build command needed
4. In **Site Settings → Build & Deploy → Post processing**, disable **Email address obfuscation** to prevent Cloudflare from corrupting email links

### GitHub Pages
1. Go to Settings → Pages
2. Set Source to `main` branch, `/ (root)` folder
3. Site will be live at `https://yourusername.github.io/pricinggoat/`
4. Point your custom domain `pricinggoat.com` in the Pages settings

## Before going live

- [ ] Add your Steuernummer to `imprint.html` (search for `[to be added]` in EN and `【待补充】` in ZH)
- [ ] Verify `pricinggoat.com` DNS points to your host
- [ ] Submit sitemap to Google Search Console and Bing Webmaster Tools
- [ ] Register with Baidu Search Console and add your verification code (search for `your-baidu-verification-code` in `index.html`)

## Contact

jan.yang@pricinggoat.com
