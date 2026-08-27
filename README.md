# babus.github.io

Personal portfolio site — live at <https://babus.github.io/>

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

## Metadata patch

The bundler emits `<title>Bundled Page</title>` and no meta tags, and the inner
template it unpacks carries no `<title>` at all — so after unpacking the tab
title would go empty. Both heads are patched to carry the real title,
description, canonical URL, favicon, and Open Graph / Twitter card tags:

- the **outer** head is what crawlers and link unfurlers see (they don't run JS)
- the **inner** template head is what the browser shows once the bundle unpacks

If you regenerate `index.html`, re-apply that patch or the tab title and every
link preview break.

## Deploying

GitHub Pages serves the `main` branch from `/`. Push to `main` and it goes live
within a minute or two.

## Custom domain

Add a `CNAME` file containing the bare domain, point DNS at GitHub Pages, then
enable **Enforce HTTPS** in Settings → Pages. Update the `og:url`, `canonical`,
and `og:image` URLs in `index.html` to the new domain at the same time.
