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
| `og-card.png` | 1200×630 link-preview card (LinkedIn, Slack, X). Regenerate from `og.html` in the scratchpad if the copy or domain changes — the domain is drawn into the artwork, not just the meta tags. |
| `favicon.svg` | Browser tab icon (outlined paths, no font dependency) |
| `apple-touch-icon.png` | 180×180 home-screen icon for iOS |
| `.nojekyll` | Tells GitHub Pages to serve files as-is, no Jekyll processing |

## Patches applied on top of the bundle

Eight changes live on top of the generated file. **If you regenerate `index.html`,
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

### 4a. Tamal in Off the clock

Section 05 now holds two cards side by side: the Night Drive and
[Tamal](https://github.com/babus/tamal), a Chrome extension and web page that
rewrite Malayalam script as Tamil script. Like the drive, Tamal's web page is a
project page on the same host, at `babu.work/tamal/`. The heading covers both
("A city you can actually drive. A script you can finally read."), the header nav
link reads "Off the clock" (the anchor stays `#drive`), and the `<noscript>`
fallback and `sitemap.xml` carry the new entry. The cards sit in an auto-fit grid
with a 420px minimum, so they stack on phones. The card's figures are counted
from the Tamal repo: 94 lines in `extension/tamal.js`, 24 cases in `test.js`.

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

### 6. White flash and the loading mark

Three separate things, measured rather than guessed:

- **`color-scheme: dark`** on `html` in both documents, plus the matching meta.
  Before any stylesheet parses, the browser paints its own base canvas — white
  unless told otherwise. A `background` on `html` cannot reach that frame; only
  `color-scheme` can. This is the fix for the flash on every reload.
- **`html { background: #08080a }`** in the outer style. The outer document set a
  background on `body` only, and the canvas inherits that just once body paints,
  so a post-paint white window existed too (`html` computed to `rgba(0,0,0,0)`
  for the first 285 ms).
- **The loading mark.** The bundler emitted `sc-camel-view-box` instead of
  `viewBox`, so the SVG never scaled: its 1200×800 user units rendered 1:1 as a
  420 px lime ring anchored at (600,400) rather than a centred mark. Fixed the
  attribute and sized it to 132×88 with a slow opacity breathe.

Two things were tried and rejected, both with numbers:

- A fade-in on the incoming body. The runtime injects its stylesheet more than
  once (3 → 6 `<style>` elements between 137–240 ms) and each insertion restarts
  a CSS animation, so it faded to 0.45, snapped to 0, then faded in again.
- Splitting the 750 KB payload into an external file. A stub build a tenth the
  size painted only 48 ms sooner throttled, 80 ms unthrottled. Not worth losing
  the single-file property.

Roughly 240 ms of the remaining time-to-first-paint is parse and paint of the
document and is not addressable from here.

### 7. Variotype, the toolkit section and the Nov 2026 numbers

- **DesignDuel → Variotype.** Same product and codebase, renamed. The product card, its link
  (`variotype.com`), the Track-record point and the `<noscript>` paragraph all carry the new name;
  nothing on the page says DesignDuel any more.
- **Numbers recounted 13 Nov 2026** to match both résumé cuts and `linkedin-profile.md` in the
  social-content workspace, which holds the ledger and how each row is counted. Evidence strip:
  1,799 merges, 17,000+ tests, 215,000+ agent turns from 4,370+ prompts, 49B+ tokens. The AI-usage
  figures are floors (two transcript windows, the gap between them not retained), hence the `+`
  and the wording of the note under the strip. The revert figure is gone from the Method card.
- **New section `03 — Toolkit`** (`#toolkit`) between Method and Track record: the engineering
  toolkit every OGP Labs codebase runs on, with its own dated stat strip (counted from the
  toolkit's git history, 24 Apr – 12 Nov 2026) and four cards. It is plain markup in the
  template, not data, and follows the `h2 + div` eyebrow pattern so the mobile rule covers it. Its
  card grid uses `minmax(min(100%,420px),1fr)`: 2 × 2 on desktop, one column on phones. Track
  record, Stack, Off the clock and Contact renumber to 04–07, and a `Toolkit` link joins the nav.
- `Babu-Somasundaram-Resume.pdf` is the CoFounder-Architect `-Portfolio` build (photo kept, email
  and phone stripped).

### 8. Ink theme with a Light / Dark / System switch

- **Colours are CSS variables now.** Every hard-coded colour in the bundle (both documents) was
  rewritten to `var(--bg)`, `var(--text)`, `var(--accent)`, `rgba(var(--ov-rgb),a)` and so on. The
  values live in one `<style data-patch="theme">` block, injected into **both** heads (the outer
  document for the loading screen, the template for the page). A new colour is one edit there.
- **Light (ink) is the default:** paper `#f6f4ef`, ink `#16171a`, deep green `#1f5f4a`. **Dark ink:**
  `#0f1312`, `#eef1ee`, mint `#7fd3ad`. Lime is gone, including from `favicon.svg`.
- **The switch** sits in the header before "Work with me": Light / Dark / System. The choice is kept in
  `localStorage` (`theme`); with nothing stored the page is Light. System follows
  `prefers-color-scheme` live. It is wired by one delegated click listener in the theme `<script>`,
  not by template handlers, because the unpacker swaps `<html>` and re-runs head scripts; the script
  always reads `document.documentElement` fresh for that reason. The template has no `class`
  attributes, so the switch is styled with `data-theme-switch` / `data-theme-set` selectors.
- **Phones:** at <=640px the switch collapses to sun / moon / half-circle icons (CSS masks, with
  `aria-label`s); at <=430px the header "Work with me" is hidden so the header stays one row (the
  hero's "Start a conversation" is right below).
- The SVG loading mark's `stroke` / `fill` attributes became `style` properties, because `var()`
  does not work in SVG presentation attributes.
- **Evidence strip numbers** are `clamp(28px,2.9vw,44px)`, `white-space:nowrap`, in a
  `minmax(min(100%,170px),1fr)` grid. At the old size "215,000+" ran into "49B+"; checked clean at
  1920, 1440, 1180, 900 and 500px.
- **Photo** in the manifest is the cleaned headshot from `social-content/resume/headshot.jpg` (face
  lifted, redness taken out of the whites of the eyes; original kept as `headshot-original.jpg`).
- **Stack:** AI engineering adds direct OpenAI, Claude Platform and Google AI Studio (Gemini) APIs and
  bring-your-own-key, all verified in the Consilo and Variotype code.
- **`og-card.png` redrawn in ink** (1200×630): same layout as before, rendered in headless Chrome from
  an HTML page using the bundle's own Space Grotesk / Instrument Serif / JetBrains Mono, with the
  cleaned headshot. To redo it, rebuild that page (fonts come out of the manifest) and screenshot at
  1200×630; keep `babu.work` drawn into the artwork.
- **`favicon.svg` redrawn:** ink-green tile, paper "BS" as **outlined paths** taken from Space Grotesk
  600 with fontTools, so it no longer depends on the visitor having the font installed (the old SVG
  used `<text>` and fell back to Helvetica). **`apple-touch-icon.png`** (180×180, square; iOS rounds
  it) is new and linked from both heads.

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

### 9. Product descriptions follow the marketing pages (13 Nov 2026)

- The Consilo and Variotype card copy, and their `<noscript>` paragraphs, now follow what
  consilo.ai and variotype.com say about themselves. Consilo is "your AI growth team" (social media,
  blog, SEO, PR and lead gen, getting named in ChatGPT, every piece approved first); the AI Council
  and email/calendar agents are no longer mentioned. Variotype is "your AI product designer"
  (studies users, gathers inspiration, several fully coded designs, up to five agents, reviewed for
  brief fit, usability and craft). When either marketing page changes, change these with it, plus
  both résumé cuts and `linkedin-profile.md` in the social-content workspace.
- `Babu-Somasundaram-Resume.pdf` rebuilt with the same wording.
