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
- **Shopping checklist:** confirm the priced list and tick items off in the
  aisle, with a progress bar and a running "left to spend" total
- **Accounts & progress:** sign in (Firebase) to sync your weight log and
  checklist across devices; trend chart with your goal-weight line, working
  for both weight loss and muscle gain

## Deploy to GitHub Pages
1. Create a repository and upload `index.html` (and this README) to the root.
2. **Settings → Pages** → Deploy from a branch → `main` / `/ (root)` → Save.
3. Your app is live at `https://<username>.github.io/<repo>/`.

The app works immediately in **local mode** (browser-only profiles). Connect
Firebase to get real accounts:

## Connect your Firebase project (accounts + sync)
1. Go to [console.firebase.google.com](https://console.firebase.google.com) →
   **Add project** (Analytics optional — you can turn it off).
2. **Build → Authentication → Get started → Sign-in method** → enable
   **Email/Password**.
3. Still in Authentication → **Settings → Authorized domains** → **Add domain**
   → add `<username>.github.io`.
4. **Build → Firestore Database → Create database** → production mode →
   choose a region → Create.
5. Firestore → **Rules** tab → replace the rules with the block below → Publish:
   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /users/{uid} {
         allow read, write: if request.auth != null && request.auth.uid == uid;
       }
     }
   }
   ```
   (Each signed-in user can read and write only their own document.)
6. Project overview → **⚙ Project settings → Your apps → Add app → Web (</>)**
   → register it (no hosting needed) → copy the `firebaseConfig` object.
7. Open `index.html`, find the block marked **`FIREBASE CONFIG — PASTE YOURS
   HERE`** near the top of the script, and replace the placeholder with your
   config. Commit and push.

That's it. The app detects a real config, loads the Firebase SDK from Google's
CDN, and switches the Sign in panel to email/password accounts. Each user's
data lives at `users/{their-uid}` in Firestore: `{ logs: [...], shopping: {...} }`.
If the config is missing or Firebase fails to load, the app quietly stays in
local mode — nothing breaks.

**Is the config safe to commit?** Yes — a Firebase web config is public by
design (it identifies the project, it doesn't grant access). Security comes
from the Firestore rules in step 5 and from Authentication itself.

## Updating prices
All food and price data sits in one array in `index.html` (`FOODS`), built
from the official surveys of 5 Nov, 25 Nov and 12 Dec 2025. Each store entry
is `{ p: pack price BBD, g: pack grams, n: product name }` — omit a store to
mark an item unstocked there. Fresh figures from
[askdealia.gov.bb](https://askdealia.gov.bb/) can be pasted straight in.

## Disclaimer
General wellness guidance, not medical advice. Anyone with diabetes or another
medical condition should review their plan with a doctor or dietitian.
