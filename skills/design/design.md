# Design — build Solt Wagner–style marketing sites (explicit manual)

You are building a landing page that must look like it belongs next to
screen.movie, supaste.com, revone.app, runey.app, frameblox.com, dock.cool.
Read this file top to bottom and follow it literally. Do not improvise
layout, type scale, section order, or copy formulas — they are specified
below with exact values measured from those sites.

## 0. What you are cloning (facts, not vibes)

- screen.movie, supaste.com, revone.app, frameblox.com, dock.cool ship
  `<!-- Made in Framer -->` with `<meta name="generator" content="Framer …">`.
  They are Framer sites using the Frameblox UI kit (drag-and-drop sections).
  runey.app is the only custom-coded one (Vite-style `/assets/*.webp`).
- The look is 100% repeatable without Framer or Frameblox: it is a fixed
  page skeleton + a fixed type scale + one accent color + fixed copy
  formulas. This file gives you all three.
- Copy the formula, never their assets, screenshots, testimonials, or
  paid Frameblox components.

## 1. Brand first — Serhii's tokens override Solt's

- Before anything else, read `~/.claude/skills/brand/SKILL.md`
  (mirror: `~/Projects/personal brand/brand.md`). It wins every conflict.
- Font mapping (same roles, Serhii's faces):
  - Solt's display face (Inter Display 600 / Satoshi 700 / Poppins 600)
    → Montserrat 600–700. Headings only, never body.
  - Solt's body face (Inter 400/500) → Open Sans 400, 600 for emphasis.
  - Solt's mono eyebrow face (Fragment Mono 400, 12–13px labels/pills)
    → Fragment Mono is allowed ONLY for tiny eyebrow pills and labels.
    Everything else is Montserrat/Open Sans.
  - Solt's serif-italic accent word (Instrument Serif / Zilla Slab italic)
    → allowed for exactly ONE accent word per H1, italic. If in doubt,
    skip it rather than substitute another serif.
- Color mapping: page `--bg` `#F4F6F5`, cards `--surface` `#FFFFFF`,
  text `--text` `#172B35`, secondary `--muted` `#69757A`,
  borders `--border` `#C7DDE2`, the ONE accent → `--accent` `#6F9EAA`
  (fills/highlights/bars only), `--warm` `#795F50` sparingly.
- Contrast law (computed, not opinion): `--accent` is NEVER text.
  `--muted` small text only on white cards (4.74:1), never on `--bg`
  (4.37:1, fails). Labels on accent fills are navy, never white.
- Light mode only. screen.movie and revone.app are dark sites — do NOT
  copy their dark backgrounds. Build the light supaste.com / dock.cool
  look. A dark treatment needs Serhii's explicit approval.
- Cyrillic coverage is mandatory in any font subset or embed.

## 2. Free stack (pick one, both are $0)

**A. Framer Free (fastest clone, no code).** 1 site on a `framer.website`
subdomain, ~1 GB bandwidth, `Made in Framer` badge, no custom domain.
Use free Framer templates + free Frameblox components as layout reference.
Custom domain requires paid Basic — prototype here, rebuild in B for real.

**B. Code + free hosting (real site, still $0).** Astro + Tailwind v4 +
Motion, hosted on Cloudflare Pages, Vercel Hobby, or GitHub Pages. The
only real cost on the internet is the domain (~$10/yr).
Components: shadcn/ui or Tailwind free sections. Icons: Lucide, never
emoji as UI icons. Motion: CSS + motion.dev + Lenis. Forms/checkout:
Polar.sh or Stripe payment links, Tally forms — no backend for a landing.

**Head boilerplate (all six sites ship this — copy the set):**
`lang`, charset, viewport, `<title>` (≤60 chars, reasoning),
meta description (the one-sentence product summary, reused as og:description
and twitter:description), canonical, og:type/url/title/description/image,
`twitter:card=summary_large_image` + title/description/image, favicon +
apple-touch-icon (ship light AND dark variants), `preconnect` fonts.

## 3. Global tokens (measured — use these numbers)

- Container: `max-width:1200px`, centered, side padding 24px desktop /
  20px mobile. (frameblox.com uses 1400px for its component showcase
  only — default to 1200px.)
- Breakpoints: mobile ≤809px (design at 390px), tablet 810–1199px
  (check at 810px), desktop ≥1200px. These are Framer's own breakpoints
  on all five sites.
- Section rhythm: `padding:112px 0` desktop (`py-20 md:py-28`), cards
  `padding:24–32px`, grid gaps `24px`. One idea per section; a section
  that needs two ideas becomes two sections.
- Radii: pills/badges `30px`, feature cards `20px`, small chips/inputs
  `8–10px`, floating nav bar `18–40px`, avatars `50%`.
- Borders: 1px `--border` on cards. Shadows: one soft level only, never
  stacked double shadows.
- Type scale (desktop / tablet / mobile):
  - H1 hero: 60–80px, weight 600–700, leading 1.02–1.08, tracking −0.02em.
    (supaste/screen: 80px; revone/dock/frameblox: 60px. frameblox's own
    preset steps 60 → 48 → 30.) Implement as
    `clamp(1.875rem, 5vw + 1rem, 5rem)`.
  - H2 section titles: 40–51px, same weight/leading as H1.
  - H3/card titles: 20–24px. Body 16–18px, leading 1.5–1.6.
  - Eyebrow pill: 12–13px, 600–700, uppercase or mono, letter-spacing 0.08em.
  - Caption/microcopy: 13–14px secondary color.
- Color discipline: near-black text, gray secondary, white/off-white
  surfaces, and exactly ONE accent color used for primary CTA fill,
  highlights, active states, and at most one gradient. Accent never
  exceeds ~5% of pixels. No second hue except tiny badge logos.

## 4. Page skeleton — build in this order, skip none

### 4.1 Sticky nav
Floating rounded bar (`radius 18–40px`), blurred translucent white
(dock.cool: `rgba(255,255,255,0.8)` + blur), thin border. Contents:
wordmark left; 3–4 anchor links center (Features, Pricing, FAQ, Updates);
maker/personal link; ONE dark CTA button right
(`Download` / `Download for macOS` / `Start for free` / `Get all access`).
Under 809px: logo + CTA + hamburger only.

### 4.2 Hero (the whole sale happens here)
Stack, centered, in this exact vertical order:
1. Mono eyebrow pill: category line. Template: `[Product] app for macOS`
   (real examples: `Supaste app for macOS`, `Cooldock app for macOS`).
2. H1, two short beats on two lines, ≤12 words total. Formula:
   `[Outcome]. [Outcome].` Real examples: `Copy once. Reuse anytime.`
   / `All your revenue. One place.` / `Your smart second Dock` /
   `Create beautiful movies from your screen.` First beat may carry the
   single serif-italic accent word.
3. Subhead: ONE sentence, ≤140 chars. Formula: `[Product] [verb]s your
   [object] [where], so you can [benefit] in seconds.`
   (Real: `Supaste saves your clipboard and screenshots in a beautiful
   visual history… so you can search, find, and paste anything back
   in seconds.`)
4. CTA row: primary dark button (`Download for macOS` / `Start for free`)
   + secondary ghost button (`See features`). Min height 44px.
5. Microcopy row under CTAs, 13–14px secondary:
   `One-time purchase · Fully offline and private · macOS Sonoma 14.0 or later`
   (adapt platform facts, keep 3 items separated by middots).
6. Product visual — MANDATORY, hero never ships without it: app
   screenshot in device-ish frame or 10–20s muted looping video,
   radius 20–24px, soft shadow, sitting on a subtle gradient/mesh glow.
   supaste/screen show a `09:41` status-bar style frame — mimic the
   framing (status bar + rounded screen), not their pixels.

### 4.3 Proof strip (directly under hero)
Badges row, grayscale, small: Product Hunt rank pill
(`#4 on Product Hunt` — real) + award mentions
(`Honorable Mention` — real Awwwards link on supaste). Use whatever real
proof exists; 2–4 items; never invent awards.

### 4.4 Feature intro + capability pills
H2 (40–51px) + one-line sub, then a wrapping row of small pills naming
every capability (`Local first, Privacy first, Search anything,
Screenshots, …` — supaste ships ~14). Pills: 13px, border, radius 30px.

### 4.5 Deep features (2–4 blocks, alternating)
Each block: mono kicker (optional), H2, 2–3 sentence paragraph, 3–5
checkmark bullets, visual beside/below (screenshot, loop, or mini-card
mock). Real H2s: `Smarter, Faster, and Connected Across Your Mac` /
`One Beautiful Place for Everything You Save` / `See all your income
across every account` / `Make your Dock truly yours`.

### 4.6 All-features grid ("Everything you need…")
H2 + sub, then a 3-col bento: 2–3 large cards (`col-span-2`) + small
cards. Every card: 20px radius, white, 1px border, icon or mini-visual
on top, 20–24px title, ONE-line desc. 6–12 cards. Real H2:
`Everything you need to understand your revenue` /
`Everything useful, one Dock away`.

### 4.7 Audience segments (6 cards, if the product serves many roles)
supaste/runey pattern: Designers / Developers / Content and Marketing /
Sales and Support / Founders and Operators / Personal Use. Each: title +
2-line `Keep/reuse …` description. Skip only for single-audience tools.

### 4.8 Testimonials (3 quotes minimum)
Real H2: `Loved by Mac Users` / `Loved by founders, freelancers and
studios` (+ `Real feedback from people using X every day.`).
Each card: 1–3 sentence quote in quotes, name + role/job title.
Marquee on desktop (pause on hover), static grid on mobile.

### 4.9 Final CTA repeat
H2 restating the outcome (`Make your Mac Dock more useful` /
`Start your business with a beautiful invoice`) + primary CTA again +
microcopy (`Free trial. No credit card required.`).

### 4.10 Pricing (one card — this exact anatomy)
1. H2: `One price. Lifetime access.` (all four app sites use this
   verbatim) + sub: `One-time payment. No subscription.`
2. Risk line: `Try it risk-free. If [Product] doesn't fit your workflow,
   email us within 14 days and we'll refund your purchase.`
3. Price row: struck-through old price + big new price
   (real: `$15–$39` now, `$29–$79` struck).
4. Scarcity line with party emoji, verbatim pattern:
   `Limited offer for early users 🥳` + `[N] spots left before the price
   increases to $[X]` (real N: 5, 6, 13, 89).
5. Bullets (verbatim set, adapt platform): `One-time payment` /
   `14-day money-back guarantee` / `Lifetime updates included` /
   `All features unlocked from day one` / `Native macOS app` /
   `[N] device license`.
6. CTA button to Polar.sh/Stripe link + `Secure checkout by Polar.sh,
   powered by Stripe. Prices are in USD…` microcopy.

### 4.11 FAQ (accordion, 8–12 questions)
H2: `Frequently Asked Questions` + one-line sub. Real question bank
(copy the SHAPE, write true answers in 2–3 sentences): What is X? /
Is it a subscription? / Where is my data stored? / Does it upload my
content? / What does 1 Device license mean? / Can I use it on multiple
Macs? / What are the system requirements? / How do I get the app after
purchase? / Is there a money-back guarantee? First item open by default,
one open at a time, keyboard accessible.

### 4.12 Cross-promo + footer
Solt cross-links sister products after the FAQ (supaste embeds a full
Cooldock block; footers list the network: screen.movie, supaste.com,
dock.cool, revone.app, runey.app, frameblox.com, icoon.co, …).
Mirror this ONLY with Serhii's real products/links — never link Solt's.
Footer columns: Product (Home, Features, FAQ, Pricing) / Resources
(Updates, Contact, Support, Roadmap) / Legal (Privacy, Terms) +
`© 2026 [Product]. All rights reserved.` + built-with line.

## 5. Responsive behavior (what changes at 809px)

- All 3-col grids → 1 column; bento `col-span-2` cards go full-width.
- H1 follows the clamp down to 30px; H2 to ~32px; never below.
- Nav → logo + CTA + hamburger; anchors move into the menu.
- Hero visual full-bleed, `max-height:60vh`, keeps radius.
- Testimonials marquee → static stacked grid (marquee libraries break
  touch scroll — disable, don't restyle).
- Pricing card full-width, bullets stay 1-col.
- Verify at 390px and 1440px: zero horizontal scroll, no clipped pills,
  CTA reachable without scrolling sideways. This is the exit gate.

## 6. Motion (entertaining, free, restrained — all Solt-legal)

- Section entrances: fade + rise 12–16px, 200–300ms ease-out, once per
  section, staggered ≤80ms between sibling cards. Nothing slides from
  the sides, nothing bounces.
- Exactly ONE marquee per page (testimonials or logo strip), slow,
  pause on hover/focus.
- Exactly ONE ambient hero effect: subtle parallax on the hero visual
  OR a slow gradient/shader glow behind it (revone's footer gradient
  `linear-gradient(180deg, black 53%, accent)` is the reference for
  gradient usage). Never both, never video background + parallax.
- Hover: lift 2px or scale 1.02 over 150ms on cards/buttons only.
- `prefers-reduced-motion`: kill marquee, parallax, and reveals
  (render final state). No autoplay audio, ever. Motion never gates
  content — everything is readable with JS disabled.

## 7. Assets and performance budgets

- Images: WebP/AVIF, compressed, `loading="lazy"` below the fold,
  explicit width/height (no layout shift). Hero video <5 MB or ship a
  poster image with click-to-play.
- Fonts: Google Fonts `<link>` with `display=swap`, Arial/Times fallback
  stacks (Framer ships `Inter Placeholder → Arial` the same way). Any
  strict-CSP target must self-host woff2 (with Cyrillic) — never assume
  the CDN loaded.
- Icons: Lucide line icons at 20–24px in cards; emoji only inside the
  `🥳` scarcity line and body copy where Solt uses it — never as UI icons.
- Analytics: single lightweight snippet (all sites run one gtag ID).
  No cookie banner unless tracking beyond essentials (runey is the only
  one with a banner — that's why).

## 8. Copy voice (write like Solt)

- H1 states the OUTCOME, never the feature. Sub states how + for whom.
- Titles 2–4 words. Descriptions one line. Zero jargon, zero `leverage`,
  zero `cutting-edge`, zero `all-in-one platform`.
- Numbers beat adjectives: `40+ motion templates`, `2 cameras`,
  `4K export`, `14-day guarantee`, `[N] spots left`.
- Scarcity and guarantee lines are PURPOSE-BUILT (see 4.10) — reuse the
  sentence shapes with true numbers only. Fake countdowns are forbidden.
- FAQ answers end with the action (`email us`, `open Settings → …`).

## 9. Ship checklist (all boxes or it doesn't ship)

- [ ] 390px + 1440px checked, zero horizontal overflow
- [ ] Section order matches §4 (nav → hero → proof → pills → deep →
      grid → audiences → quotes → CTA → pricing → FAQ → footer)
- [ ] Hero has eyebrow + 2-beat H1 + 1-line sub + 2 CTAs + microcopy
      row + visual — six elements, none missing
- [ ] Type scale matches §3 clamp; headings Montserrat 600–700, body
      Open Sans 400/600, mono confined to pills
- [ ] Contrast passes brand law (§1); accent is fills-only
- [ ] Exactly one accent color; light theme (supaste/dock look)
- [ ] Motion: reveals + ≤1 marquee + ≤1 ambient hero effect +
      reduced-motion respected
- [ ] Pricing card has all six anatomy items from §4.10 with TRUE numbers
- [ ] FAQ 8–12 items, first open, keyboard operable
- [ ] Meta set from §2 (title/desc/OG/Twitter/canonical/favicons)
- [ ] One H1, logical H2/H3 order, alt text on every visual
- [ ] Images lazy + sized; hero video <5 MB or poster fallback
- [ ] No Solt assets, screenshots, testimonials, or product links anywhere

## 10. Don'ts (instant fail)

- No dark theme. No new palette. No new font. No gradient body text.
- No second accent hue. No emoji-as-icons. No fake awards.
- No invented testimonials, prices, spot counts, or rankings.
- No paid Frameblox components or copied screenshots.
- No sections 10–12 repeating sections 4–6. Cut instead of stacking.
- No `Made in Framer` badge on coded builds; no Framer subdomain links.
