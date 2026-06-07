# 📨 Workflow — Outreach Businesses

A complete, **copy-and-make-it-yours** system for finding local businesses that need a website,
writing them a warm, legal, language-matched email, and handing you a beautiful custom site
preview to attach — **so you just press send.**

It was originally built for Gabriel (Jumping Tech, South Tyrol), but everything personal now lives
in one **`.env`** file, so *anyone* can run it. Change the `.env`, and the whole workflow becomes yours.

---

## 🤔 What is this, in one breath?

> An AI agent (Claude Code) reads a clear instruction manual, finds up to 10 local businesses with no
> website, and for each one builds a little folder containing: **the email to send**, a **gorgeous
> one-page mockup of their future website**, and a **"how I work + price" page**. Then it stops.
> **You** look over the folders and send the emails by hand from your own inbox. That's the whole loop.

The magic trick: the prospect opens an email that already contains a preview of *their own* site, with
*their* name and *their* photos. That converts far better than any sales pitch, because it's proof,
not a promise.

---

## 📁 What's in this folder

```
Workflow - Outreach Businesses/
├── .env                    ← ⭐ EDIT THIS (your copy). Name, brand, prices, mode, OpenAI key.
├── .env.example            ← The template that ships in the repo. Copy it to .env to start.
├── business-outreach.md    ← The full "operating manual" the AI agent follows.
├── README.md               ← You are here. 🙂
├── .claude/skills/         ← The two skills this workflow uses, bundled in:
│   ├── image/              ←   /image — generates the preview photos (reads your OpenAI key from .env)
│   └── impeccable/         ←   /impeccable — designs + quality-gates each preview.html
├── templates/
│   ├── document.html       ← LOCAL client page ("how I work" + price). Home-turf version.
│   └── me.html             ← AWAY client page. Remote/worldwide version.
└── outreach/
    ├── done.md             ← The agent's permanent memory. Who's already been contacted.
    └── businesses/         ← One sub-folder per business gets created here, each run.
```

### 🔑 First-time setup (1 minute)

The repo ships **`.env.example`** (no secrets). To start:

1. **Copy it:** `copy .env.example .env`  (PowerShell) — your real `.env` is git-ignored, so your key never leaves your machine.
2. **Open `.env`** and paste your OpenAI key into `OPENAI_API_KEY="sk-..."`. The bundled `/image` skill reads it straight from this file (`.claude/skills/image/image.py` resolves the project `.env` automatically) to generate each business's preview photos.
3. Make sure the image deps are installed once: `pip install openai python-dotenv pillow`.

> The **two bundled skills** live in `.claude/skills/` so the workflow is self-contained — `/image` for the preview imagery and `/impeccable` for designing and quality-checking every `preview.html` (run `npx impeccable detect` as the final gate). They're copies of the standard skills; `/image` is the only one repointed to read your key from this project's `.env`.

### The two HTML pages — what each is for 📄

Both are the **"how I work" page** you attach *alongside* the website preview. They explain your
process, your "you're happy first, then you pay" guarantee, and — importantly — **carry the price**
(the email itself never mentions money). They auto-switch language between **German, Italian, English
and Romanian** with the buttons at the top.

| File | When you use it | What's different |
|---|---|---|
| **`document.html`** | **LOCAL** mode — you're pitching businesses on your home turf (e.g. South Tyrol). | Uses the "local neighbour" framing, mentions the local provincial website grant tip, and shows your home-market prices (Tier A ≈ €400 / Tier B €1,200–2,000). |
| **`me.html`** | **AWAY** mode — you're pitching anywhere else / internationally. | Drops the local framing, uses a remote/worldwide pitch, and prices at **≈ 30% below the comparable local agency** for that market (you research the real number and fill it in per quote). |

> Think of them as the *same* page with two personalities. `OPERATING_MODE` in `.env` decides which one
> the agent reaches for. Each run, the agent copies the right base page into a business's folder and
> **personalises it** — greeting the business by name and printing the price for that specific job.

---

## 🚀 How it works, step by step

1. **You edit `.env`** — put in your name, brand, website, languages, prices, and pick `LOCAL` or `AWAY`.
2. **The agent loads `outreach/done.md` first** — its memory of everyone already contacted, so nobody
   gets emailed twice. (Empty on day one — that's fine.)
3. **It finds businesses** on Google Maps (using a scraper) — names, towns, phone, rating, and crucially
   *whether they have a website*. No-website businesses are the gold.
4. **It qualifies & scores them**, keeps only the **top 10 this run** (a hard cap, on purpose).
5. **For each of the 10, it builds a folder** under `outreach/businesses/{business}/` containing:
   - `email.md` — the ready-to-send email (in *their* language) with an English "what this is" note on top.
   - `preview.html` — a custom, single-file mockup of their future website (images baked in).
   - `document.html` / `me.html` — the personalised "how I work + price" page.
6. **A compliance gate checks every package** (generic address only, no PEC, honest source line, opt-out,
   no tracking). Only packages that pass get logged to `done.md` as `ready`.
7. **The agent stops. It never sends anything.** ✋
8. **You** open each folder, read the email, attach the two HTML files, and **send it by hand** from your
   own mailbox — about 10 a day. Then you update the status in `done.md` (`sent`, `replied`, `opted_out`…).

There's also a **second, warmer motion**: replying to people who *publicly ask* "anyone know a good web
designer?" on local classifieds. That's solicited contact — lower risk, higher conversion. Same memory
ledger, same rules. (Section 3.6 of the manual.)

---

## 🔒 Why it's built to keep you legal

You'll likely be emailing into **Italy, Germany, and Austria — the strictest cold-email countries in
Europe.** The whole pipeline is designed so the *targeting itself* keeps you compliant:

- ✅ **Generic mailboxes only** (`info@`, `kontakt@`…), never a named person's address.
- ✅ **Never** email an Italian **PEC** (certified-mail) address — instant complaint magnet.
- ✅ Every email states **who you are, where you found them, and how to opt out** (reply `STOP`).
- ✅ **No tracking pixels.** Success is measured by replies, not opens.
- ✅ **Low volume, high relevance** — ~10 genuinely personalised emails a day, sent by a human.

All of this is configured in the `.env` and enforced in the manual. **Don't weaken these unless you
genuinely know the law in your target market.**

---

## ✏️ Make it yours in 2 minutes

Open **`.env`** and change these first:

| Key | Set it to… |
|---|---|
| `OPERATOR_NAME`, `BRAND_NAME`, `MAIN_WEBSITE` | You and your business. |
| `REPLY_TO_EMAIL`, `SIGNATURE_LOCATION` | Where replies go + your postal/location line. |
| `OPERATING_MODE` | `LOCAL` (home turf) or `AWAY` (anywhere else). |
| `PRICE_TIER_A`, `PRICE_TIER_B_MIN/MAX` | Your prices. |
| `OPERATOR_MAIN_LANGUAGE` / `_FLUENT_` / `_BASIC_` | The languages you actually speak. |

Everything in `business-outreach.md` references these with `${...}`, so you never hand-edit the manual.

---

## 💬 The golden rule

> You're not a faceless agency blasting templates. You're a real person showing a local business a
> picture of the website they could have — politely, in their own language, with an easy way to say no.
> Keep the volume low, the relevance high, and **always let the agent build, but you press send.**

Happy outreaching. 🏔️
