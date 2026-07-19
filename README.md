# PlateWise — Barbados Meal Planner

A single-file web app that builds evidence-based meal plans and priced shopping
lists for Barbados. No build step, no server, no dependencies — just
`index.html`, ready for GitHub Pages.

## Features
- **Goals:** lose weight · lower blood sugar · gain muscle · full body transformation
- **Targets** from the Mifflin-St Jeor equation using sex, age, height, current
  weight, goal weight, activity level, and an estimated body-fat % from a visual
  body-type picker
- **Blood-sugar mode** caps carbs near 35% of calories and steers the plan to
  low-GI, high-fibre foods (ADA-aligned)
- **Food preferences** — plans are built only from foods you tick, with a
  one-click "Standard selection" of Bajan-friendly staples
- **1–4 week plans, 1–3 meals/day**, with macros per meal, per day, and per week
- **Shopping list** with two pricing modes: *lowest total price* across stores,
  or *one supermarket*. Items a store doesn't stock get a suggested alternative,
  or a "not found — please seek alternative" note

## Deploy to GitHub Pages
1. Create a new repository on GitHub (e.g. `platewise`).
2. Upload `index.html` (and this README) to the repository root.
3. Go to **Settings → Pages**, set Source to **Deploy from a branch**,
   branch `main`, folder `/ (root)`, and save.
4. Your app will be live at `https://<your-username>.github.io/platewise/`.

## Using your own food & price list
All food and price data lives in one array near the top of the `<script>` block
in `index.html`, clearly marked:

```js
const STORES = ["Aone Supermarkets", "Massy Stores", ...];  // 14 vendors
const FOODS = [
  { id:"tuna_water", name:"Canned tuna (in water)", cat:"Proteins",
    role:"protein", std:true, gi:"low",
    per100:{ k:116, p:25.5, c:0, f:1, fib:0 },      // macros per 100 g
    prices:{ "Massy Stores":{ p:4.35, g:142, n:"Brunswick Chunk Light Tuna (In Water) 142g" },
             ... } },                                // cheapest brand per store
];
```

- Omit a store's key in `prices` when that store doesn't stock the item — the
  single-store mode will then suggest an alternative automatically.
- `role` must be one of `protein | carb | veg | fruit | fat` (used to compose meals).
- `gi` (`low | med | high`) controls what the blood-sugar plan will pick.
- `std:true` marks items included in the "Standard selection".

The bundled prices are **real Government of Barbados retail price survey data**
([askdealia.gov.bb](https://askdealia.gov.bb/)) covering 14 supermarkets, from
the surveys of 5 Nov, 25 Nov and 12 Dec 2025. For each generic food the app
stores, per supermarket, the cheapest matching branded product by
price-per-gram (e.g. "Canned tuna" compares Brunswick, Bumble Bee, Eve, etc.
at each store). To refresh prices later, update the entries in `FOODS` —
each store entry is `{ p: pack price BBD, g: pack grams, n: product name }`.

### Why the app doesn't fetch askdealia.gov.bb automatically
A GitHub Pages site is static: it can't scrape another website from the
browser (blocked by CORS), and shipping an AI/API key inside a public page
would expose it to anyone. If you later want live prices, the clean path is a
small backend (e.g. a free Cloudflare Worker or GitHub Action run on a
schedule) that reads prices and writes them into a `prices.json` the page
loads. The `FOODS` structure is already shaped so that's a drop-in change.

## Disclaimer
General wellness guidance, not medical advice. Anyone with diabetes or another
medical condition should review their plan with a doctor or dietitian.
