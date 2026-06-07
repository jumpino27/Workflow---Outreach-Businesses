# Local Business Outreach Agent — Operating Manual

> **⚙️ All operator-specific values live in `.env`.**
> This manual references them as `${VAR_NAME}`. To make this workflow your own,
> edit **`.env`** only — never hard-code your name, prices, or paths in here.
> See `README.md` for a friendly walkthrough of how the whole thing fits together.

**Operator:** `${OPERATOR_NAME}` — `${BRAND_NAME}` (`${MAIN_WEBSITE}`)
**Based in:** `${HOME_BASE}`
**Purpose:** An end-to-end runbook an AI agent can follow to find local businesses (with or without a website), enrich and qualify them, and produce a ready-to-send package per business (a language-matched email + a custom site preview) — **which the operator then sends by hand** — all without breaking EU/Italian/German law.

> This document assumes the operator already knows how to build and host websites. It does **not** cover building or hosting. It covers everything *before* the build: finding the lead and winning the conversation.

---

## 0. Read this first — the one thing that kills this whole system

You may be operating from **Italy**, and many prospects will be in **Italy (South Tyrol), Austria, Germany, and Switzerland** — i.e. the strictest cold-email jurisdictions in Europe. The pipeline below is designed so the *targeting itself* keeps you compliant. If the agent ignores the legal section and just blasts named personal addresses, the operator is personally exposed to GDPR/ePrivacy enforcement (fines scale to the tens of thousands for small operators, and Italy's regulator, the Garante, actively enforces marketing-consent violations).

**Golden rule:** prefer **generic business addresses** (`info@`, `office@`, `kontakt@`, `ristorante@…`) over named-person addresses, keep volume low and relevance high, identify yourself honestly, state where you got the contact, and offer a one-click way out (`${OPT_OUT_KEYWORD}`) in every single message. Do that and you are on solid ground. Skip it and you are gambling.

---

## 1. Operator identity (pulled from `.env` — use everywhere)

| Field | `.env` key | Default value |
|---|---|---|
| Name | `OPERATOR_NAME` | Gabriel Stancuta |
| Brand | `BRAND_NAME` | Jumping Tech |
| Main site | `MAIN_WEBSITE` | jumpinotech.com |
| Home base | `HOME_BASE` | South Tyrol (Südtirol / Alto Adige) |
| Main language | `OPERATOR_MAIN_LANGUAGE` | **Italian** (native) — best language for real back-and-forth |
| Fluent also | `OPERATOR_FLUENT_LANGUAGES` | English, Romanian |
| Basic only | `OPERATOR_BASIC_LANGUAGES` | **German** — simple conversations only; prefer Italian/English |
| App platforms | `APP_PLATFORMS` | Windows, Android, Browser |
| App platforms NOT built | `APP_PLATFORMS_EXCLUDED` | iOS, macOS |
| Positioning hook | `POSITIONING_HOOK` | A local who builds modern sites + apps and corresponds personally |

**Three "languages" must never be confused:**
1. **The outreach email's language** → follows the *prospect's* own language (Section 6.1). The AI writes it, so a polished German email to a German-speaking *Gasthof* is fine even if the operator doesn't speak fluent German.
2. **The site's language** → whatever the *client* wants (German, Italian, English, Romanian, Ladin, anything).
3. **The client's instructions to the operator** → the client may write in any language; no need to switch.

**Reply-handling caveat:** when a basic-language prospect (e.g. German) replies and a real conversation starts, gently offer to continue in a fluent language using `${SWITCH_LANGUAGE_NOTE}` — honest, friendly, avoids overpromising.

**What the operator builds:** not just websites — also **web applications and applications**, but **only for `${APP_PLATFORMS}`** (no `${APP_PLATFORMS_EXCLUDED}`). Anything beyond a standard website (app, web app, tool, portal, booking system) is **custom-priced based on needs**, not the standard website price.

**Why "local" is the whole pitch (LOCAL mode):** South Tyrol is ~70% German-speaking, ~26% Italian-speaking, ~4–5% Ladin. The two biggest towns (Bozen/Bolzano, Meran/Merano) lean Italian; the valleys and smaller villages lean heavily German, sometimes German-only. A web agency that emails a Pustertal *Gasthof* in fluent Südtirol-aware German, signed by someone "from here," beats a Milan or Berlin agency before the first sentence ends. **Lead with proximity and language. That is the unfair advantage.**

---

## 2. Legal guardrails (NON-NEGOTIABLE — agent must enforce on every send)

The legal basis for B2B cold email in the EU is **legitimate interest** (GDPR Art. 6(1)(f), Recital 47). But the **ePrivacy** layer is per-country and stricter; Italy/Germany/Austria sit on the strict end. Treat the rules below as hard filters.

### 2.1 Country rules the agent must apply per prospect

| Country | Practical rule for this pipeline |
|---|---|
| **Italy** (incl. South Tyrol) | Strict. Send to **generic company addresses only**. Always include sender identity, data source, and opt-out. Relevant + low-volume. |
| **Germany** | Strictest in the EU (UWG). Effectively wants prior consent even B2B; enforcement is complaint-driven. **Only generic business addresses, genuinely relevant, ready to stop instantly.** When in doubt, prefer phone/postcard over email. |
| **Austria** | Treat like Germany. Generic addresses, high relevance, instant opt-out. |
| **Switzerland** | Similar caution; generic addresses + opt-out. |
| **France, UK, Ireland, Netherlands** | More permissive for B2B under legitimate interest. Still: identify yourself, disclose source, include opt-out. |

**Default behaviour:** if `DEFAULT_TO_STRICTEST_RULE=true`, when unsure which rule applies, **default to the strictest standard (Germany).** Always safe.

### 2.2 The Italian PEC trap (critical — most guides miss this)

Italian businesses have a **PEC** address (*Posta Elettronica Certificata*) in the public *Registro Imprese*. PEC is the **legal equivalent of registered mail**. It is publicly scrapable — **and you must never send marketing to it.** With `DISCARD_PEC_ADDRESSES=true`, the agent discards any address on a `@pec.*` / certified-mail domain. **Never use PEC for outreach.**

### 2.3 The five compliance signals that must appear in EVERY email

1. **Honest sender identity** — real name (`${OPERATOR_NAME}`), real brand (`${BRAND_NAME}`), real reply-to (`${REPLY_TO_EMAIL}`).
2. **A physical/postal reference** — `${SIGNATURE_LOCATION}` in the signature.
3. **Data-source disclosure** — one short line (`${SOURCE_LINE_MAPS_*}`): *"I found your business on Google Maps / your public listing."*
4. **A working, one-click opt-out** — *"Reply '`${OPT_OUT_KEYWORD}`' and I'll never contact you again,"* honoured immediately and permanently.
5. **A non-deceptive subject line** — no fake "Re:", no fake "Fwd:", no clickbait.

### 2.4 Documentation the operator keeps on file

- **Legitimate Interest Assessment (LIA)** — short 3-part note per campaign: (a) *purpose*, (b) *necessity*, (c) *balancing*. Your defence if anyone complains.
- **Source + timestamp per lead** — where the data came from and when.
- **Suppression list** — every opt-out and bounce, kept forever, checked before every send.
- **Retention** — purge leads that never engaged after `${LEAD_RETENTION_YEARS}` years (shorter is safer).

### 2.5 Hard "never" list

- ❌ Never email a **PEC** address.
- ❌ Never email a **named individual's personal-style address** in IT/DE/AT (`mario.rossi@…`) — use the company mailbox.
- ❌ Never **buy lists** — no lawful basis.
- ❌ Never use **tracking pixels** (`TRACKING_PIXELS=off`). Measure replies, not opens.
- ❌ Never ignore or delay an opt-out.
- ❌ Never spoof, fake-thread, or disguise that this is a commercial message.

---

## 3. Pipeline overview

> Two motions share one `done.md` ledger: **(A) outbound** — the cold pipeline (Google Maps); and **(B) inbound / intent capture** (Section 3.6) — catching people *already asking* for a web designer. Run both.

```
[0] LOAD done.md         →  build "already-seen" set (Section 3.5). MUST run first.
        ↓
[1] Define target (niche + town + language zone)
        ↓
[2] Scrape Google Maps  →  raw listings
        ↓
[2b] DEDUP vs done.md    →  drop every listing already logged
        ↓
[3] Filter & qualify    →  no-website OR weak-website + active + reachable → opportunity score
        ↓
[3c] CAP TO ${MAX_BUSINESSES_PER_RUN}  →  top N by score THIS RUN. Hard limit.
        ↓
[4] Enrich              →  find generic email, VERIFY it, drop PEC, detect language
        ↓
[5] BUILD PACKAGE (per business):
        a. /outreach/businesses/{business-slug}/
        b. preview.html  (single self-contained file, images base64-embedded)
        c. email.md      (subject + recipient + body, in their language)
        d. document.html / me.html  (personalised client "how I work" page + price)
        ↓
[6] Compliance gate     →  5 signals + address type + country rule + NOT in done.md + package complete
        ↓
[7] APPEND done.md      →  log the business (status: ready). The AGENT DOES NOT SEND.
        ↓
[8] HAND OFF            →  operator reviews the folders and sends emails HIMSELF, by hand.
        ↓
[9] Track & convert     →  operator updates each done.md row (sent / replied / opted_out).
```

> **Hard rule: max `${MAX_BUSINESSES_PER_RUN}` businesses fully processed per run.** Each gets a real, custom, AI-designed preview. A business is written to `done.md` **only after** its complete package (folder + `preview.html` + `email.md` + `document.html`) is generated — status `ready`. If any part fails, it is NOT logged, so the next run retries it. **The agent never sends; the operator sends every email manually.**

---

## 3.5 ★ State, memory & output structure (READ BEFORE EVERY RUN)

The agent has **no memory between sessions**. To avoid re-processing the same business, it persists state on disk. **`done.md` (`${DONE_LEDGER}`) is the source of truth, read first every run.**

Everything lives under `${OUTREACH_ROOT}` so every session finds it.

### Output layout

```
${OUTREACH_ROOT}/
├── done.md                         ← permanent, append-only ledger (cross-session memory)
├── run-index-YYYY-MM-DD.md         ← optional: index of what this run produced
└── businesses/
    ├── gasthof-sonne-bruneck/      ← ONE folder per business (slug = name+town, lowercased, hyphens)
    │   ├── email.md                ← subject + recipient + full body (their language)
    │   ├── preview.html            ← single self-contained file, images embedded as base64
    │   └── document.html           ← client "how I work" page (LOCAL=document.html / AWAY=me.html)
    │                                  personalised + priced by scope (see 4.0a + 8.6)
    └── … (max ${MAX_BUSINESSES_PER_RUN} per run)
```

### `email.md` format (inside each business folder)

At the very top of every `email.md`, in **ENGLISH**, write a short **"WHAT THIS IS"** block so the operator instantly understands the context before reading the (possibly German/Italian) email below:

- **OUTBOUND** — *I found them*, they didn't ask. From Google Maps. No website → offer to build; weak/old site → offer redesign; or an app opportunity.
- **INBOUND** — *they asked first* (publicly looking for a web designer/app), and I'm replying **"I'm here, I can do this."** Note the source and link the original post.

```markdown
---
business: Gasthof Sonne
town: Bruneck
to: info@gasthof-sonne.it
email_type: generic
language: de
subject: kurze Idee für Gasthof Sonne
preview_file: preview.html
lead_type: outbound          # outbound | inbound
channel: maps                # maps | hds | subito | bazar | kleinanzeigen-st | kleinanzeigen-de | ejob | facebook | marketplace | x | other
goal: website                # website | website-redesign | app
site_scope: landing          # landing (Tier A) | full (Tier B) | app (Tier C) — see 4.0a
quoted_price: "400 €"        # the concrete number printed on THIS business's document.html
source_link:                 # inbound: URL of their post; outbound: their Maps URL
status: ready                # ready | sent | replied | opted_out | bounced
---

> **WHAT THIS IS (read first):**
> OUTBOUND — I found Gasthof Sonne on Google Maps. They have NO website yet,
> so I'm reaching out to offer to build them one. They did not contact me first.

Hallo Gasthof-Sonne-Team,

…full email body here, in the business's language, ending with the
source line + opt-out…
```

**Pick the right "WHAT THIS IS" sentence:**
- Outbound · no website → *"OUTBOUND — found on Google Maps, has NO website. Offering to build one. They didn't contact me."*
- Outbound · weak site → *"OUTBOUND — found on Google Maps, has an old/weak website. Offering a redesign."*
- Outbound · app → *"OUTBOUND — found on Google Maps. App/web-app opportunity (${APP_PLATFORMS}). Price by needs."*
- Inbound · website → *"INBOUND — they publicly asked for a web designer on {source}. Replying 'I'm here.' Post: {link}."*
- Inbound · app → *"INBOUND — they publicly asked for an app on {source}. Scope + price to be discussed. Post: {link}."*

### Dedup key (how "same business" is decided)

A business is "already done" if **any** match a row in `done.md`, in order:
1. `place_id` (Google's stable ID — most reliable)
2. `maps_url` (stable fallback)
3. `email` (catches the same shop under a different name)
4. normalized `name + town` (lowercased, accents/punctuation stripped) — last-resort fuzzy catch

If any match → **skip entirely.**

### Session start procedure (mandatory, in order)
```
1. Open done.md. If missing, create it with the header row.
2. Parse every row → build an in-memory SEEN set of {place_id, maps_url, email, name+town}.
3. Begin scraping (Stage 2).
4. Drop any scraped listing that hits SEEN, before qualification.
5. After scoring, keep only the TOP ${MAX_BUSINESSES_PER_RUN} remaining businesses this run.
```

### Session end procedure (mandatory)
```
6. For each: build businesses/{slug}/ with preview.html + email.md + document.html.
7. Run the compliance gate on each package.
8. If it passes → append one row to done.md (status=ready), per business.
9. NEVER append a business whose package failed to build.
10. The operator sends the emails himself, by hand, and updates each
    row's status in done.md (sent / replied / opted_out / bounced).
```

### `done.md` format (permanent ledger — never re-contact)

```markdown
# DONE — do not re-contact. Append-only. Last updated: YYYY-MM-DD

| place_id | maps_url | business | town | email | channel | status | first_contacted | last_update |
|----------|----------|----------|------|-------|---------|--------|-----------------|-------------|
| ChIJabc… | <url> | Gasthof Sonne | Bruneck | info@gasthof-sonne.it | maps | emailed | 2026-06-06 | 2026-06-06 |
```

**Status values:** `ready` · `sent` · `emailed` · `replied` · `booked` · `won` · `opted_out` · `bounced` · `dormant` — all mean "do not surface again." `opted_out` and `bounced` are hard compliance suppressions; never re-contact.

---

## 3.6 ★ Second motion — inbound / intent capture

Catching people *publicly asking* for someone to build them a website/app. Runs **alongside** outbound, feeds the **same `done.md`**. Replying to a public request is **solicited** — far lower legal risk and converts much better (~15–25% vs ~3% cold).

### 3.6.0 ⛔ DO NOT USE: Reddit and LinkedIn
| Platform | Why off-limits |
|---|---|
| **Reddit** | Walls content off from search/AI tools; requires gated API access. Skip entirely. |
| **LinkedIn** | Scraping/automating breaks ToS — bans + civil lawsuits. **Never** scrape, automate, or post. |

### 3.6.1 Where to watch (South Tyrol-local first)
Use each site's own saved-search / email alert, or periodic manual scan.

| Source | What it is |
|---|---|
| **hds-bz.it** | South Tyrol retail & services association free ad portal. |
| **kleinanzeigen-suedtirol.com** | Bilingual DE/IT classifieds. Scan services + "Gesuche". |
| **bazar.it** | Trentino-Alto Adige weekly classifieds (servizi). |
| **subito.it** → BZ / Trentino-Alto Adige | Use "salva ricerca" alerts for *sito web / web designer*. |
| **kleinanzeigen.de** → "Südtirol" + radius | "Suchauftrag" alert for *Webdesigner gesucht*. |
| **ejob.civis.bz.it** | Province's official job board. |
| **Facebook local groups** | Manual only. Where owners ask "wer macht mir eine Webseite?" |

**Wider/optional:** Upwork, Fiverr, Freelancer, Malt, freelancermap/Twago, Addlance/ProntoPro, Dribbble, X/Twitter saved searches, Google Alerts.

### 3.6.2 Search phrases (multilingual) — watch website AND app requests
```
WEBSITE
IT: "cerco web designer", "mi serve un sito", "chi mi fa un sito", "sito web per la mia attività"
DE: "suche Webdesigner", "brauche eine Webseite", "Homepage für mein Geschäft", "Webseite erstellen lassen"
EN: "looking for a web designer", "need a website", "recommend a web developer"
RO: "caut web designer", "am nevoie de un site", "cine face site-uri"

APP / WEB APP
IT: "cerco sviluppatore app", "mi serve un'app", "applicazione web", "sistema di prenotazione"
DE: "suche App-Entwickler", "brauche eine App", "Web-App", "Buchungssystem entwickeln lassen"
EN: "need an app", "build a web app", "need a booking system / portal / internal tool"
RO: "caut dezvoltator de aplicații", "aplicație web", "sistem de rezervări"
```
When an inbound lead is an **app** request, tag it `app` and reply that scope (`${APP_PLATFORMS}`) and price are **discussed by needs** — don't quote the website price.

### 3.6.3 How to respond (warm, not spammy)
1. **Answer their actual question first** — be useful even if they don't hire you.
2. **Then** introduce yourself briefly: local, modern multilingual sites, a quick relevant example.
3. **Show, don't pitch:** link `${MAIN_WEBSITE}` or a quick custom `preview.html`.
4. **Follow each site's rules** — DM if public promo is banned.
5. **Never paste the same canned reply twice.**

### 3.6.4 Dedup & ledger
Add the `channel` field to `done.md`. Check `done.md` before replying. The **N-per-run cap applies to outbound only** — inbound replies are human-paced. **No `reddit`, no `linkedin` channels.**

### 3.6.5 Compliance note
Replying to a public request is **solicited**. Still identify yourself, stay relevant, respect platform rules. Don't harvest emails from these posts for a *separate* cold sequence.

---

## 4. Stage 1 — Targeting (ICP)

### 4.0 ★ Operating area (`OPERATING_MODE` in `.env`)

| Mode | When | What changes |
|---|---|---|
| **LOCAL** (default) | Home turf (South Tyrol) | South Tyrol language-zoning (4.3), local inbound venues (3.6), **"local neighbour" pitch**, client page `${CLIENT_PAGE_LOCAL}`. Pricing by SCOPE: Tier A ≈ `${CURRENCY_SYMBOL}${PRICE_TIER_A}`; Tier B `${CURRENCY_SYMBOL}${PRICE_TIER_B_MIN}–${PRICE_TIER_B_MAX}`; apps custom (+`${CURRENCY_SYMBOL}${PRICE_YEARLY_UPKEEP}`/yr upkeep). |
| **AWAY** | Pointed elsewhere | Drop the "local neighbour" angle, use the **remote/worldwide** pitch + `${CLIENT_PAGE_AWAY}`, price `${AWAY_DISCOUNT_PERCENT}%` below local market. Detect language from the prospect's own locale. |

**Pricing when operating AWAY:**
1. **Never quote the South Tyrol € numbers abroad.**
2. **Research first:** look up the typical local web-design price for that market.
3. **Then propose ≈ `${AWAY_DISCOUNT_PERCENT}%` below** that benchmark. Apps stay custom.
4. `me.html` states "≈ `${AWAY_DISCOUNT_PERCENT}%` less than comparable local agencies, exact quote after a quick look at your market" — do the research and put a real number in the reply.

**Legal still scales:** apply the *destination's* rule (Section 2.1), default to strictest when unsure.

### 4.0a ★ Pricing model — the AGENT decides per business, by what the site DOES

Price follows **scope**, not whether the business already has a site.

| Tier | What it is | Includes | Price (LOCAL) |
|---|---|---|---|
| **A — Small landing page** | ONE beautiful page that routes to phone/email/Maps/booking. No backend. | 1 page, responsive, basic SEO, generated images, click-to-call/email/book | **≈ `${CURRENCY_SYMBOL}${PRICE_TIER_A}`** |
| **B — Full website** | Multi-page **or** long LP with **real functions**: contact form + email, reviews, gallery, booking/menu, long content. | everything in A + form+email, reviews, multi-page, richer SEO, business email | **`${CURRENCY_SYMBOL}${PRICE_TIER_B_MIN}–${PRICE_TIER_B_MAX}`** |
| **C — App / web-app** | Anything beyond a website for `${APP_PLATFORMS}`. | custom build | **custom — by needs** |

Add **+ `${CURRENCY_SYMBOL}${PRICE_YEARLY_UPKEEP}`/year** (domain + business email + upkeep) to A and B. **How to choose A vs B:** if the only "function" is linking out → **A**. The moment they need to *capture* something (form/email, bookings, reviews) or genuinely need several pages → **B**. When unsure, propose **A** and note B is available.

> **AWAY mode:** same A/B/C shape, but price ≈ `${AWAY_DISCOUNT_PERCENT}%` below the comparable **local** benchmark — don't reuse the € figures abroad.

### 4.1 Two prospect types
| Type | Pitch angle | Difficulty |
|---|---|---|
| **No website at all** | "You're invisible beyond Google Maps. Let's fix that." | **Easiest.** Start here. |
| **Weak / broken website** | One concrete flaw: not mobile, slow, no booking, dead since 2018, no HTTPS. | Harder — you're replacing something. |

In trades/service niches, **40–70% of local listings have no website**. That's your volume.

### 4.2 Best starting niches in South Tyrol
- Gastronomy: *Gasthof, Pizzeria, Restaurant, Bar, Hofschank, Buschenschank*
- Accommodation: *Pension, Garni, Ferienwohnung, Bauernhof/agriturismo*
- Trades: *Tischler, Maler, Elektriker, Installateur, Dachdecker*
- Beauty & wellness: *Friseur, Kosmetik, Massage, Studio*
- Retail & producers: *Metzgerei, Bäckerei, Hofladen, Weingut, Imkerei*

### 4.3 Geographic + language zoning (LOCAL / South Tyrol-specific)
- **Italian-leaning:** Bozen/Bolzano, Meran/Merano, Leifers/Laives, Salurn/Salorno, Unterland south
- **German-leaning (often German-only):** Pustertal, Vinschgau, Eisacktal villages, Sarntal, most valleys
- **Ladin valleys:** Gröden/Val Gardena, Gadertal/Alta Badia → default German, mention Italian/Ladin
- **Always:** detect from the listing's own language before trusting the zone (6.1).

---

## 5. Stage 2 — Scraping Google Maps

### 5.1 Legality (short)
Scraping **publicly visible business data** is broadly defensible; it does breach Google's ToS (risk: IP/account blocks), and GDPR applies if you collect personal data on individuals. Company-level data is low-sensitivity; a named person's email is personal data — another reason to prefer generic addresses.

### 5.2 Three ways to get the data
| Method | Use when | Notes |
|---|---|---|
| **Google Places API** | Small, clean, ToS-compliant pulls | Free credit; structured; won't hand you emails. Enrichment, not bulk. |
| **No-code scrapers** (Apify "Businesses Without Websites", Outscraper, Scrap.io, Get Map Leads) | Volume + no-website filter | Fastest. Many output an opportunity score + no-website flag. |
| **Open-source / self-host** | Own the stack | Free, 50+ fields incl. emails; respect rate limits. |

### 5.3 The 120-result cap
A single Maps search returns **max ~120 results**. Split queries **by town and by ZIP/CAP**, then dedupe. Matrix: `{niche} in {town}` for each town.

### 5.4 Fields to capture per listing
```
business_name, category, address, town, cap_zip,
phone, website_url (or null), maps_url, coordinates,
rating, review_count, last_review_date,
has_website (bool), language_signals (raw text sample)
```

---

## 6. Stage 3 + 4 — Qualify, enrich, verify

### 6.1 Language detection (BEFORE writing)
1. The business's **own website language** → match exactly.
2. The **language of their reviews / replies / listing text**.
3. The **town's primary-language zone** (4.3) as fallback.
4. If mixed/unclear in South Tyrol → **default to German**, optionally add one Italian sentence.
5. Outside DACH/IT → match site/country language; default English only if nothing else fits.

Output: `email_language ∈ {de, it, en, …}`.

### 6.2 Find a generic email (order of preference)
1. Email on the Google listing.
2. `info@`, `office@`, `kontakt@`, `mail@`, role mailbox on their domain.
3. Email on Facebook/Instagram "about".
4. If only a **named** email exists in IT/DE/AT → **skip email, use phone/postcard.**

**Never** synthesise `firstname.lastname@` guesses for IT/DE/AT.

### 6.3 VERIFY every address
Run each email through a verifier (Hunter, Bouncer, NeverBounce, DeBounce, Snov). **Drop anything that isn't "valid."** Target **< `${TARGET_BOUNCE_RATE_PERCENT}%` bounce**. Then discard all PEC addresses (2.2).

### 6.4 Opportunity score (rank, work top-down)
```
+30  no website at all
+20  website exists but no HTTPS / not mobile / clearly old (pre ~2018)
+20  review_count between 30 and 200 (real customers, not a giant chain)
+15  rating >= 4.0 (good business that just looks bad online)
+10  recent review (< 90 days = active)
+5   reachable generic email verified
-50  PEC-only or named-personal-only contact  (→ phone/postcard, not email)
```
Work the 80+ scores first.

---

## 7. Stage 5 — Writing the email (the conversion engine)

### 7.1 Copy rules
- **Length:** 50–125 words; first touch **under ~`${FIRST_TOUCH_MAX_WORDS}` words.**
- **Subject:** 5–7 words, specific, local, no clickbait, lowercase/human.
- **Opener:** one sentence **entirely about them** — their place, reviews, town.
- **One idea, one ask.** Name one gap, offer one next step.
- **Proof over adjectives:** a demo link beats "we make beautiful sites".
- **Soft CTA:** yes/no question or "send a preview" — not "book a 30-min discovery call".
- **Tone:** local, plain, peer-to-peer. Warmer Südtirol register, not stiff *Hochdeutsch*.
- **No tracking pixels.** Judge success by replies.

### 7.2 The preview-led move (core of the system)
Every business gets a **real, custom `preview.html`** (Section 8.5) — not a stock template. The email links/attaches it.

**Critical wording — speak to a layperson, never a developer:**
- Call it a **rough first impression / sample / "kleine Vorschau" / "anteprima"**.
- Clarify it's **not the finished site** — the real one would be **built properly, fast and secure** — in *plain words*. **Never** use "HTML", "framework", "code", "static file".
- Good: *"This is just a quick sample to show the direction — the real site would be built properly, much faster and fully yours."*
- Bad: *"Here is an HTML prototype; the production build would use a Next.js framework."* ❌

### 7.3 Required structure
```
Subject:  {specific, local, 5–7 words}
1. Opener   — about THEM, in their language
2. Gap      — one concrete problem (no site / not mobile / can't book online)
3. Value    — what fixing it does for THEM
4. Proof    — the preview: "I put together a quick sample for you" + plain-words caveat
5. Soft ask — one easy yes/no question
6. Signature— ${OPERATOR_NAME} · ${BRAND_NAME} · ${HOME_BASE} · ${MAIN_WEBSITE}
7. Source + opt-out line  (one line, honest — ${OPT_OUT_KEYWORD})
```

---

## 8. Email templates (first touch)

> Placeholders in `{ }`. The agent fills from lead data. Adjust the German register to feel local.

### 8.1 German — no website (primary)
```
Betreff: kurze Idee für {business}

Hallo {business}-Team,

ich bin über euer Google-Profil in {town} gestolpert — {review_count}
Bewertungen mit {rating} Sternen, richtig stark. Aufgefallen ist mir nur,
dass ihr (noch) keine eigene Website habt. Viele Gäste schauen heute kurz
online nach, bevor sie kommen — und finden dann nur die Telefonnummer.

Ich bin ${OPERATOR_NAME_FIRST} aus Südtirol und baue moderne Webseiten für
lokale Betriebe hier. Für {business} habe ich schon mal eine kleine Vorschau
zusammengestellt — nur ein erster Eindruck, wie eure Seite aussehen
könnte (im Anhang). Die richtige Seite würde ich dann sauber aufbauen,
deutlich schneller und ganz euch gehörend.

Soll ich sie euch kurz erklären / passt ein kurzer Anruf diese Woche?

Viele Grüße
${OPERATOR_NAME} — ${BRAND_NAME}, Südtirol
${MAIN_WEBSITE}

PS: ${SOURCE_LINE_MAPS_DE}
Wenn ihr keine Mails von mir wollt, antwortet einfach „${OPT_OUT_KEYWORD}" — dann melde
ich mich nie wieder.
```

### 8.2 Italian — no website
```
Oggetto: una piccola idea per {business}

Salve team di {business},

ho visto il vostro profilo Google a {town}: {review_count} recensioni a
{rating} stelle, complimenti davvero. Ho notato solo che non avete ancora
un sito vostro. Oggi molti clienti cercano online prima di venire e trovano
soltanto il numero di telefono.

Mi chiamo ${OPERATOR_NAME_FIRST}, sono dell'Alto Adige e realizzo siti moderni
per attività locali come la vostra. Per {business} ho già preparato una
piccola anteprima — solo un primo assaggio di come potrebbe essere il
vostro sito (in allegato). Quello vero lo realizzerei poi come si deve,
molto più veloce e completamente vostro.

Ve la spiego in due parole / facciamo una breve chiamata questa settimana?

Un saluto
${OPERATOR_NAME} — ${BRAND_NAME}, Alto Adige
${MAIN_WEBSITE}

PS: ${SOURCE_LINE_MAPS_IT} Se non
desiderate ricevere mie email, rispondete "${OPT_OUT_KEYWORD}" e non vi scriverò più.
```

### 8.3 English — no website (non-DACH/IT leads)
```
Subject: quick idea for {business}

Hi {business} team,

I came across your Google profile in {town} — {review_count} reviews at
{rating} stars, really solid. The one thing I noticed: you don't have your
own website yet. Most people check online before they visit, and right now
they only find your phone number.

I'm ${OPERATOR_NAME_FIRST}, based in ${HOME_BASE_SHORT}, and I build modern
websites for local businesses. I already put together a quick preview for
{business} — just a first impression of how your site could look (attached).
The real one I'd build properly: much faster, and fully yours.

Want me to walk you through it, or is a short call this week easier?

Best,
${OPERATOR_NAME} — ${BRAND_NAME}, ${HOME_BASE_SHORT}
${MAIN_WEBSITE}

PS: ${SOURCE_LINE_MAPS_EN} If you'd rather not
hear from me, just reply "${OPT_OUT_KEYWORD}" and I won't contact you again.
```

### 8.4 Weak-website variant
> Replace the "no website" lines with one concrete flaw (not mobile-friendly, slow, no booking, dead since 2018, no HTTPS).

---

## 8.5 ★ Building `preview.html` (the custom site mockup — one per business)

For each business, generate **one self-contained `preview.html`** inside its folder.

### 8.5.1 Use the two skills — MANDATORY (do not freehand)
1. **The `impeccable` skill** — `/impeccable craft` → `/impeccable polish` to design+build the preview from the business's details. Optional: `/impeccable critique`, `/impeccable audit`, `/impeccable bolder`, `/impeccable animate`. **Deterministic gate:** run `npx impeccable detect businesses/{slug}/preview.html` and fix until it exits 0. Honour impeccable's anti-patterns (no Inter/Arial/system fonts, no purple-to-blue gradients, no pure black/gray, no cards-in-cards, no bounce easing).
   - **jsdom caveats** (the detect tool runs in jsdom): use the **physical `padding` shorthand in `rem`/`px`** (never `clamp`/`vw`/logical) on elements with a visible background + text; give image-backed text sections a solid `background-color`; avoid `overflow:hidden` wrapping positioned children (use CSS `background-image` + `::before` instead of absolute `<img>`); avoid uppercase letter-spaced eyebrows above the hero `h1`, numbered `01/02/03` markers, `letter-spacing > 0.05em`/uppercase on long runs, and `line-height < 1.3`.
2. **The image skill (`/image`)** — generate a hero shot, 1–2 section images, and an icon/logo themed to *that specific business*. Embed as base64 (8.5.2). Use `/crop` for transparency.

> If either skill is unavailable, **stop and flag it** rather than ship a low-quality preview.

### 8.5.1a ★ Distinctiveness rule
No two previews in a run may look like the same template recolored. Vary the **architecture** per business: theme (light/dark), hero paradigm (full-bleed vs split vs minimal), section structure & rhythm, type system (distinctive non-overused faces, different pairing each), color strategy (OKLCH, tinted neutrals, never pure `#000`/`#fff`), and one **signature move** per preview. **3–6 generated images** per preview, all WebP, base64-embedded, single file under ~5 MB.

### 8.5.2 Single-file + embedded images (non-negotiable)
- **CSS:** inline in one `<style>` block.
- **JS:** inline in a `<script>` block (a webfont `<link>` is the only allowed external reference; include a system fallback).
- **Images:** embed every image as a base64 data URI. No external URLs, no relative paths.
- **Keep it reasonable:** WebP, ~1280px, quality ~80, single file under ~4–5 MB, 3–6 images.

### 8.5.3 What the mockup contains
- Header: business name wordmark + nav (Home / {menu or services} / Contact).
- Hero: generated image + headline in the business's language + CTA ("Tisch reservieren" / "Prenota un tavolo" / "Book now").
- 1–2 sections: about / signature offer / gallery — seeded with real details (town, type, star rating as a trust badge: *"4.7★ on Google"*).
- Contact block with their real phone + town.
- Mobile-responsive, fast-feeling, polished.

### 8.5.4 Placeholders to fill from lead data
`{business_name}`, `{town}`, `{category}`, `{rating}`, `{review_count}`, `{phone}`, `{language}`. All visible copy in the business's language.

---

## 8.6 ★ The client "how I work" page — `document.html` / `me.html` in every folder

Alongside `preview.html`, put a tailored client page in each business folder. It explains the process, the guarantee ("happy first, then you pay") and the **price** in the prospect's language (auto-switches DE/IT/EN/RO). **Base page:** LOCAL → `${CLIENT_PAGE_LOCAL}`, AWAY → `${CLIENT_PAGE_AWAY}` (these template files live in `./templates/`).

**Every page is per-business — two things change:**
1. **Personalise it to THEM.** Inject the business name + town into the hero intro (all four languages): *"Hallo {Business} in {Town}! …"*. Optionally weave one concrete line from their listing.
2. **Set the price + scope from §4.0a:**
   - **Tier A (≈ `${CURRENCY_SYMBOL}${PRICE_TIER_A}`):** set the price token; frame as a compact landing page.
   - **Tier B (`${CURRENCY_SYMBOL}${PRICE_TIER_B_MIN}–${PRICE_TIER_B_MAX}`):** set the chosen number; describe the full website.
   - **Tier C (app):** custom by needs — usually discussed, not printed.
   - Keep the **+ `${CURRENCY_SYMBOL}${PRICE_YEARLY_UPKEEP}`/year** line for A and B.

**Website-only (default):** unless the lead is an app, delete the App/Web-App callout from the DOM **and** remove the `app_label` / `app_body` keys from every language dict.

**How to produce it (script it, don't hand-edit 4 languages):** read the base page, apply edits to BOTH the static HTML and the `I18N` dictionary (`de/it/en/ro`). Replace the `intro` / `p1_amt` / `p1_label` / `p1_body` values in language order, and the German-default `data-i18n` spans. Keep process steps, SEO explainer, business email + chatbot, and the South Tyrol grant tip (LOCAL only). Single self-contained file.

> The price lives on `document.html`/`me.html`, **not** in the email body — the email keeps its soft, price-free CTA (§7.1).

---

## 9. Stage 6 — Sending (MANUAL — the operator sends, the agent never does)

**No SMTP, no sending automation, no separate sending domain.** The agent's job ends at producing the package. **The operator opens each `email.md`, copies the subject + body, attaches `preview.html` + `document.html`, and sends from his own mailbox.**

1. **Send in small, human batches** — `${DAILY_SEND_VOLUME}` genuinely personalised emails a day, by hand.
2. **Use a sensible "from" address** (`${REPLY_TO_EMAIL}`).
3. **Attach the preview + the client page** (which carries the price). The email copy stays price-free.
4. **Respect the compliance rules every time** (Section 2).
5. **After you send, update `done.md`** (`ready` → `sent`). Anyone who replies "`${OPT_OUT_KEYWORD}`" → `opted_out`, never contact again.

---

## 10. Stage 7 — Follow-up sequence (also sent by hand)

**4–6 emails over 3–4 weeks.** Stop the instant someone replies or opts out. The agent can *draft* a follow-up into the business folder (`followup-2.md`); the operator sends it.

| Touch | Day | Purpose |
|---|---|---|
| 1 | 0 | Opener + (optional) demo |
| 2 | +3–4 | Gentle reminder + **new** info |
| 3 | +7–8 | Standalone value (a tip they can use even if they say no) |
| 4 | +12–14 | Direct ask ("Worth a 10-minute call?") |
| 5 | +20 | Breakup ("I'll stop here — if it's ever useful, you know where I am.") |

Each follow-up keeps the same language, the same opt-out line, and adds something.

---

## 11. Stage 8 — Reply handling & conversion
- **Positive reply →** `done.md` status `replied`; confirm the demo, propose two concrete time slots, move to build.
- **"How did you get my email?" →** answer honestly + offer opt-out. That's why the source line exists.
- **"Not interested" / "${OPT_OUT_KEYWORD}" →** status `opted_out` **permanently**, confirm once, never contact again.
- **Bounce →** status `bounced`; never retry that address.
- **No reply after the sequence →** status `dormant`, retain per policy.

---

## 12. CRM / data schema (agent maintains)
```json
{
  "business_name": "", "place_id": "", "maps_url": "", "category": "",
  "town": "", "cap_zip": "", "country": "IT",
  "language_zone": "de|it|ladin", "email_language": "de|it|en",
  "phone": "", "email": "", "email_type": "generic|named|pec",
  "email_verified": true, "has_website": false, "website_url": null,
  "site_flaws": ["no_mobile","slow","no_https","outdated","none"],
  "site_scope": "landing|full|app", "quoted_price": "",
  "rating": 4.6, "review_count": 84, "last_review_days": 12,
  "opportunity_score": 0, "source": "Google Maps public listing",
  "source_date": "2026-06-06", "lia_campaign_id": "", "sequence_stage": 0,
  "status": "new|sent|replied|booked|won|opted_out|bounced|dormant",
  "suppressed": false
}
```

**Acceptance gate (pass ALL before logging `ready`):**
```
NOT in done.md   (by place_id / maps_url / email / name+town)   ← checked FIRST
this run's processed count <= ${MAX_BUSINESSES_PER_RUN}
package complete: preview.html EXISTS + self-contained + passes `npx impeccable detect`
package complete: email.md EXISTS (English "WHAT THIS IS" header + subject + recipient + body)
package complete: document.html EXISTS (personalised + priced by scope per 4.0a)
email_type == "generic"  AND  email_type != "pec"
email_verified == true   AND   NOT suppressed
country rule satisfied (DE/AT/IT → generic only)
email body contains: sender identity + location + source line + opt-out + honest subject
email copy avoids the words HTML / framework / code (layperson framing)
no tracking pixels referenced
```
If any check fails → **do NOT write to done.md**, flag for the operator (next run retries it).

---

## 13. Benchmarks
- **Reply rate:** aim for **`${TARGET_REPLY_RATE_PERCENT}%`+** (industry avg ~3–5%). Below 3% → fix targeting/deliverability, not copy.
- **Bounce:** keep **< `${TARGET_BOUNCE_RATE_PERCENT}%`**.
- **Spam complaints:** keep **< 0.1%**.
- **Daily volume:** `${DAILY_SEND_VOLUME}` per inbox. Scale *relevance* before volume.

---

## 14. Quick-start checklist (operator)
1. ☐ **Edit `.env`** with your name, brand, prices, mode.
2. ☐ **Load `done.md` first** (create if missing); build the already-seen set.
3. ☐ Pick one niche + one language zone.
4. ☐ Scrape Google Maps, split by town/CAP, **drop anything in `done.md`**, apply the no-website filter.
5. ☐ Enrich generic emails, **verify**, **drop PEC**, detect language, score, sort.
6. ☐ **Keep only the top `${MAX_BUSINESSES_PER_RUN}` this run.**
7. ☐ For each: create `businesses/{slug}/`, build `preview.html` (impeccable + image skills, base64, single file), write `email.md`, build personalised `document.html`/`me.html`.
8. ☐ Run the acceptance gate → append passing ones to `done.md` as `ready`. **Agent stops here — never sends.**
9. ☐ **You send the emails by hand** from your own mailbox. Update each `done.md` row to `sent`.
10. ☐ Send follow-ups by hand; stop on reply/opt-out.
11. ☐ Keep the LIA, source log, and `done.md` ledger. Always.

---

*The competitive edge isn't the tooling — anyone can scrape Maps. It's that you're a real, local person talking to your neighbours about a problem they can see. Lead with that, stay compliant, keep volume low and relevance high.*
