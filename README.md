# babus.github.io

Personal portfolio site — live at <https://babu.work/>

## How this site is built

`index.html` is a **self-contained bundle**: a single file with React, the web
fonts, and the profile photo all inlined as base64. There is no build step and
no dependencies to install — the file unpacks itself in the browser on load.

It is a generated artifact. Do **not** hand-edit the markup inside it; regenerate
the bundle from source and re-apply the metadata patch (see below).

## Files

| File | Purpose |
|---|---|
| `index.html` | The whole site, self-contained (~830 KB) |
| `Babu-Somasundaram-Resume.pdf` | Linked from the hero and the contact section |
| `og-image.png` | 1200×630 link-preview card (LinkedIn, Slack, X) |
| `favicon.svg` | Browser tab icon |
| `.nojekyll` | Tells GitHub Pages to serve files as-is, no Jekyll processing |

## Patches applied on top of the bundle

Five changes live on top of the generated file. **If you regenerate `index.html`,
re-apply all three** — otherwise each regression comes back silently.

### 1. Metadata (both heads)

The bundler emits `<title>Bundled Page</title>` and no meta tags, and the inner
template it unpacks carries no `<title>` at all — so after unpacking the tab
title would go empty. Both heads carry the real title, description, canonical
URL, favicon, and Open Graph / Twitter card tags:

- the **outer** head is what crawlers and link unfurlers see (they don't run JS)
- the **inner** template head is what the browser shows once the bundle unpacks

### 2. Contact résumé link

The "Résumé (PDF)" button in the contact section was copy-pasted from the
"Message me on LinkedIn" button next to it and its `href` was never changed, so
it sent people to LinkedIn. Fixed to point at the PDF. **This one should also be
fixed in the design source** — otherwise the next export reintroduces it.

### 3. Mobile alignment

A `<style data-patch="mobile-alignment">` block in the template head, scoped to
`max-width: 640px`. Both rules override inline styles, hence `!important`;
desktop is deliberately untouched.

| Fix | Why |
|---|---|
| Header pill padding → symmetric `16px` | Ships as `20px` left / `12px` right, tuned for the desktop row where the rounded CTA sits flush at the pill's rounded end. Once the row wraps on mobile the CTA moves left and that 8px difference reads as the whole header being off-centre. |
| Header nav links hidden | The anchor links render ~15px tall, far under the 44px minimum tap target, and wrap onto their own row — which is what made the header three rows deep. Hiding them collapses it to a single row: wordmark left, CTA right. Nothing is lost; every section they point at is on this one page, below. |
| Section eyebrow gets its own row | "01 — Work" etc. share a wrapping flex row with the heading under `justify-content: space-between`. When the pair fits, the eyebrow is pushed hard right; when it doesn't, it wraps hard left — so it landed differently in each section. |

### 4. Night Drive section

Adds section `05 — Off the clock` between Stack and Contact, linking the
[Trivandrum Night Drive](https://github.com/babus/trivandrum-night-drive) — a
separate repo served as a project page under the same domain. Contact renumbers
from 05 to 06, and a `#drive` link joins the header nav. The card reuses the
product-card markup so it stays native to the design, and the section eyebrow
follows the same `h2 + div` pattern, so the mobile rule above already covers it.

### 5. SEO fallback and structured data

The bundle unpacks in the browser, so the raw HTML a non-rendering crawler sees
was 17 characters — `BS Unpacking...`. Two additions in the **outer** document,
which is the part served as plain HTML:

- A JSON-LD `Person` block in the head. It needs no JS to be read, so it works
  for every crawler, and it is what feeds name-search rich results.
- A `<noscript>` block in the body carrying the real headings and copy. The
  unpacker replaces the whole document, so browsers never see it; crawlers
  without JS now get 1,560 characters instead of 17. It mirrors the rendered
  content rather than adding to it — keep the two in step if the copy changes.

`robots.txt` and `sitemap.xml` sit at the repo root alongside them.

## Custom domain

Live at **babu.work** (registered at GoDaddy, DNS there). `CNAME` holds the bare
domain; the apex has GitHub's four A and four AAAA records, and `www` is a CNAME
to the apex.

Absolute URLs in `index.html` — `canonical`, `og:url`, `og:image` — point at
`babu.work` and must be updated together if the domain ever moves again. The
night drive is a project page on the same host, so it followed automatically to
`babu.work/trivandrum-night-drive/`.

## Known, not fixed

The header CTA ("Work with me") is 37px tall on mobile — still under the 44px
minimum tap target, though close enough to hit reliably. Bumping it is a
one-line `min-height`, but it changes a deliberately proportioned button, so
it belongs in the design source rather than in a patch here.

## Deploying

GitHub Pages serves the `main` branch from `/`. Push to `main` and it goes live
within a minute or two.

## Custom domain

Add a `CNAME` file containing the bare domain, point DNS at GitHub Pages, then
enable **Enforce HTTPS** in Settings → Pages. Update the `og:url`, `canonical`,
and `og:image` URLs in `index.html` to the new domain at the same time.
