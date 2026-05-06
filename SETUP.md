# PokeWaarde – Cardmarket Proxy Setup Guide

This guide walks you through deploying the Cardmarket price proxy to Cloudflare Workers (completely free).
Once set up, the app will fetch per-variant, per-language, per-condition prices directly from Cardmarket.

---

## What you'll need

- A Cloudflare account (free, no credit card required)
- The two files from this guide: `worker.js` and the updated `pokedex-collection.html`
- About 15 minutes

---

## Step 1 — Create a Cloudflare account

1. Go to **https://cloudflare.com** and click **Sign Up**
2. Enter your email and a password
3. Verify your email address
4. You do NOT need to add a domain or credit card — the free Workers plan works with Cloudflare's own subdomain

---

## Step 2 — Create the Worker

1. Log in to the Cloudflare dashboard at **https://dash.cloudflare.com**
2. In the left sidebar, click **Workers & Pages**
3. Click **Create** → **Create Worker**
4. Give it a name — e.g. `pokewaarde-proxy`
5. Click **Deploy** (this creates a placeholder worker)
6. You'll land on the worker overview page — click **Edit code**

---

## Step 3 — Paste the worker code

1. In the code editor, delete all existing code
2. Copy the entire contents of **`worker.js`** (the file provided alongside this guide)
3. Paste it into the editor
4. Click **Deploy** (top right)
5. You'll see a green "Deployed" confirmation

---

## Step 4 — Note your worker URL

After deploying, Cloudflare gives your worker a URL like:
```
https://pokewaarde-proxy.YOUR-SUBDOMAIN.workers.dev
```

Copy this URL — you'll need it in the next step.

---

## Step 5 — Add the proxy URL to the app

1. Open the app in your browser (your GitHub Pages URL)
2. Go to the **Instellingen** tab (gear icon)
3. Scroll down to the new **Cardmarket Proxy** section
4. Paste your worker URL into the field
5. Click **Opslaan**
6. Tap **🔍 Test proxy** to verify it's working

---

## Step 6 — How it works after setup

Once configured, the app changes behaviour for cards in your collection:

- **Bladeren / card overlay**: When you open a card detail, the app fetches live Cardmarket prices via your proxy
- **Prices shown**: Trend price, 30-day average, and lowest available price — all filtered to the exact variant (1st Ed / Shadowless / Unlimited) and language (NL / EN) you've selected
- **Listings**: The 10 cheapest matching listings are shown with seller country, condition, and price
- **Cache**: Prices are cached for 24 hours per card+variant+language combination to keep request counts low

---

## Cloudflare free tier limits

The free tier gives you **100,000 requests per day**.

For a personal collection of a few hundred cards, you'll use roughly:
- 1 request per card detail view
- Background price updates: 1 request per card per 24h

You would need thousands of card views per day to approach the limit. In practice this will never be an issue.

---

## Troubleshooting

**"Test proxy" shows an error:**
- Make sure you pasted the full URL including `https://`
- Try opening `https://your-worker.workers.dev/?url=https://www.cardmarket.com/en/Pokemon/Products/Singles/Base-Set/Charizard-V3-BS4` directly in your browser — you should see JSON

**Prices show as null:**
- Cardmarket occasionally changes their HTML structure. If this happens, let me know and I'll update the parser.

**Worker returns 403:**
- The worker only allows cardmarket.com URLs as a safety measure. Make sure the URL being passed is a valid Cardmarket product page.

**Cloudflare shows "CPU time exceeded":**
- This shouldn't happen on normal pages. If it does, the page might be unusually large — try a different card.

---

## Locking down CORS (optional, recommended)

By default the worker accepts requests from any origin (`*`).
To restrict it to only your GitHub Pages site:

1. In the worker code, find this line:
   ```js
   'Access-Control-Allow-Origin': '*',
   ```
2. Replace `*` with your GitHub Pages URL:
   ```js
   'Access-Control-Allow-Origin': 'https://yourusername.github.io',
   ```
3. Click **Deploy** again

This prevents anyone else from using your proxy if they find the URL.

---

## Files in this package

| File | Purpose |
|------|---------|
| `worker.js` | The Cloudflare Worker code — paste this into the Cloudflare editor |
| `pokedex-collection.html` | The updated app with proxy integration |
| `config.json` | Your existing config file — no changes needed |
| `SETUP.md` | This guide |
