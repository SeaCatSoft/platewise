# PlateWise — Barbados Meal Planner

A single-file web app that builds evidence-based meal plans with full recipes
and priced shopping checklists for Barbados. Hosted free on GitHub Pages, with
optional Firebase accounts for cross-device sync.

## Features
- **Goals:** lose weight · lower blood sugar · gain muscle · full body transformation,
  with targets from the Mifflin-St Jeor equation (sex, age, height, weights,
  activity, and an estimated body-fat % from a visual body-type picker)
- **Eating styles:** Standard, Mediterranean, Pescatarian, Vegetarian, Vegan,
  Keto — for the whole plan or a different style each week (keto weeks
  re-target macros to ~50 g carbs/day)
- **Full recipes** for every generated meal, with real amounts from your plan
  (Bajan-style stews, peas & rice, cutters, porridges, keto plates, vegan bowls)
- **1–4 week plans, 1–3 meals/day**, macros per meal, day and week
- **Real prices:** Government of Barbados retail price survey (askdealia.gov.bb)
  across 14 supermarkets — cheapest brand per item per store; choose the
  lowest total across stores or shop at one supermarket, with automatic
  alternatives ("Not stocked — suggested alternative…") or a
  "not found — please seek alternative" note
- **Complete shopping list:** covers the plated foods *and* every ingredient
  the recipe steps call for — onion, garlic, thyme, lime, scallion and
  seasonings are aggregated across the whole plan and converted into pack
  sizes ("36 cloves — about 4 bulbs"). Items the government survey doesn't
  track are listed in a separate seasonings section, flagged to price in store
- **Shopping checklist:** confirm the priced list and tick items off in the
  aisle, with a progress bar and a running "left to spend" total
- **Virtual pantry:** one-off purchases (oils, peanut butter, seasonings,
  sauces) are remembered with how much the pack contained. Future plans draw
  stock down automatically, items you already have show as "✓ In pantry —
  no purchase needed" (excluded from the total), brand-new staples are
  flagged "★ First one-off purchase", and when anything drops below 20%
  the app asks whether to add a restock to your list. Manage it all in
  My Progress → My pantry (refill after off-plan shops, or remove items)
- **Clients & progress:** a client roster with each client's own stats, plan,
  pantry and weight log, optionally backed up to your Firebase account and
  usable on any device; trend chart with your goal-weight line, working
  for both weight loss and muscle gain

## Deploy to GitHub Pages
1. Create a repository and upload `index.html` (and this README) to the root.
2. **Settings → Pages** → Deploy from a branch → `main` / `/ (root)` → Save.
3. Your app is live at `https://<username>.github.io/<repo>/`.

The app works immediately in **local mode** (clients are kept in the
browser). Connect Firebase to back every client up to your own account:

## Connect your Firebase project (cloud backup)
1. Go to [console.firebase.google.com](https://console.firebase.google.com) →
   **Add project** (Analytics optional — you can turn it off).
2. **Build → Authentication → Get started → Sign-in method** → enable
   **Email/Password**.
3. Still in Authentication → **Settings → Authorized domains** → **Add domain**
   → add `<username>.github.io`.
4. **Build → Firestore Database → Create database** → production mode →
   choose a region → Create.
5. Firestore → **Rules** tab → replace the rules with the contents of
   [`firestore.rules`](firestore.rules) → Publish:
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /users/{uid}/clients/{clientId} {
         allow read, write: if request.auth != null && request.auth.uid == uid;
       }
     }
   }
   ```
   (Each signed-in owner can read and write only their own clients.)
6. Project overview → **⚙ Project settings → Your apps → Add app → Web (</>)**
   → register it (no hosting needed) → copy the `firebaseConfig` object.
7. Open `index.html`, find the block marked **`FIREBASE CONFIG — PASTE YOURS
   HERE`** near the top of the script, and replace the placeholder with your
   config. Commit and push.

That's it. The app detects a real config, loads the Firebase SDK from Google's
CDN, and shows a **Cloud backup** sign-in under the client list. Signed in,
every client is mirrored to `users/{uid}/clients/{client name}` in Firestore
as `{ name, json, updatedAt }` (the client's whole record is stored as a JSON
string, because Firestore doesn't allow nested arrays). The browser keeps a
working copy, so the app still works offline. On sign-in the newer copy of
each client wins, and clients that exist on only one side are copied across.
If the config is missing or Firebase fails to load, the app quietly stays in
local mode — nothing breaks.

**Is the config safe to commit?** Yes — a Firebase web config is public by
design (it identifies the project, it doesn't grant access). Security comes
from the Firestore rules in step 5 and from Authentication itself.

## Recipe ingredients & seasonings
Each recipe declares an `extras` list alongside its steps. Two kinds:
`X("onions",40)` adds grams of a **priced** food to the shopping totals, and
`P("garlic",2)` adds units of a **pantry** item from the `PANTRY` catalogue
(garlic, thyme, scallion, lime, Bajan green seasoning, pepper sauce, bay,
cinnamon, salt, black pepper). Pantry entries define `per`/`pack` so totals
convert into sensible purchases. To add an ingredient, extend `PANTRY` and
reference it in the relevant recipe branch — the shopping list and checklist
pick it up automatically.

## Updating prices
All food and price data sits in one array in `index.html` (`FOODS`), built
from the official surveys of 5 Nov, 25 Nov and 12 Dec 2025. Each store entry
is `{ p: pack price BBD, g: pack grams, n: product name }` — omit a store to
mark an item unstocked there. Fresh figures from
[askdealia.gov.bb](https://askdealia.gov.bb/) can be pasted straight in.

## Disclaimer
General wellness guidance, not medical advice. Anyone with diabetes or another
medical condition should review their plan with a doctor or dietitian.
