# PROJECT STATUS — Steel Mini App

> Handoff document generated from a full audit of this repository on **2026-11-10**.
> Audited ref: `main` @ the single commit `Initial commit for Steel Mini App` (2026-09-19).
> Everything below is verified from the code unless marked *(inferred)*.

## 1. Snapshot

| Item | Value |
|---|---|
| What it is | Telegram **Mini App** (Web App) — a steel product catalogue/order picker in Persian (RTL) |
| Type | Single static HTML file (no build, no backend) |
| Maturity | **Prototype / proof-of-concept (~10%)** |
| Branches | `main` only |
| Commits | 1 (2026-09-19) |
| Files | `index.html` (~4.8 KB, inline CSS + JS) |

## 2. Purpose

A minimal Telegram Web App that shows a list of steel products and lets a Telegram user pick one and send the choice back to the bot. Title: «کاتالوگ و ثبت سفارش فولاد» (Steel catalogue & order registration). Products currently hard-coded:

- شمش فولادی (Billet)
- کلاف صنعتی و ساختمانی (industrial/construction wire rod coil)
- میلگرد شاخه (rebar, straight bars)

## 3. Tech stack

- Plain HTML5 + inline CSS + vanilla JS
- `https://telegram.org/js/telegram-web-app.js` (Telegram WebApp SDK)
- Theming uses Telegram CSS variables (`--tg-theme-bg-color`, `--tg-theme-button-color`, …) with dark fallbacks

## 4. Structure

```
index.html   # everything: styles, product cards, selection logic, Telegram bridge
```

Key JS:
- `tg.expand(); tg.ready();` — initialise the Mini App
- `selectItem(name, el)` — highlights a card and enables the submit button
- `sendData()` — `tg.sendData(JSON.stringify({ product: chosen }))` → delivers `{product}` to the bot as a `web_app_data` service message

## 5. How it works end-to-end

1. A Telegram bot (NOT in this repo) shows a keyboard button of type `web_app` pointing to the hosted `index.html`.
2. The user picks a product and taps «تأیید و ارسال انتخاب».
3. Telegram sends `{"product":"..."}` to the bot. **Note:** `sendData` only works when the Mini App was opened from a *keyboard* (reply-markup) button, not from an inline button or menu button.

## 6. Run / deploy

- No build. Host `index.html` on any HTTPS static host (GitHub Pages, Netlify, Cloudflare Pages).
- Register the URL with @BotFather (`/newapp` or as a `web_app` keyboard button on your bot).
- Local preview: open the file in a browser (Telegram APIs will be undefined; the page still renders).

## 7. Done

- [x] Static catalogue UI, RTL Persian, Telegram theme-aware
- [x] Single-select + submit to bot via `sendData`

## 8. Not done / gaps

- [ ] **Bot backend** that receives `web_app_data` and records the order (no code exists anywhere in this repo)
- [ ] Quantity, unit (ton), size/grade (e.g. rebar diameter A3/A2, billet 125/150), delivery city, phone
- [ ] Product data from an API / CMS instead of hard-coded cards
- [ ] Prices / daily price feed (core requirement for an Iranian steel market product)
- [ ] Validation of `initData` on a server (security — otherwise anyone can forge orders)
- [ ] Analytics, error handling, empty states
- [ ] README / deployment config

## 9. Recommended next steps

1. Build the bot side (e.g. Node `grammy`/`telegraf` or PHP) and persist orders (DB or forward to CRM/Sheet).
2. Extend the form: product → spec → quantity → contact → confirm. Use `tg.MainButton` instead of a custom button.
3. Load catalogue + prices from JSON/API so non-developers can update it.
4. Validate `Telegram.WebApp.initData` HMAC on the server for every order.
