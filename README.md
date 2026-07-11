# โปรอีสาน ซีรีย์ — Sales Tracker 🍢

A single-file web app to track daily sales of the 3 new Isaan-series menus across
**24 branches**, toward a company goal of **25,806 dishes** sold between
**10 Jul – 30 Sep 2026**, with a **฿5-per-dish staff incentive** calculated automatically.

- **Store staff** log in to their own branch and type each day's dish counts.
- **Management** logs in to one dashboard that rolls up all 24 branches live:
  days left, total sold vs. 25,806, remaining, required run-rate/day, incentive accrued, charts, and CSV export.

Everything is in **`index.html`** — no build step, no server code.

---

## 1) Try it right now (Demo mode)

Just double-click **`index.html`** to open it in a browser.
It runs in **🟡 Demo mode**: data is saved only on that one device/browser (via localStorage).
Great for testing. To make stores and management share data across phones and computers,
turn on Firebase (Section 3).

**Default logins**

| Who | Username | Password / PIN |
|---|---|---|
| Management | `manager` | `isaan2026` |
| Each store | (pick branch from the dropdown) | its **branch code** (e.g. branch 5001 → PIN `5001`) |

> ⚠️ Change these before real use — see Section 5.

---

## 2) How the numbers are calculated

| Metric | Formula |
|---|---|
| **Days left** | Whole days from today through 30 Sep 2026 (inclusive) |
| **Total sold** | Sum of all dish counts entered by all branches, all days |
| **Remaining** | 25,806 − Total sold |
| **Required / day** | Remaining ÷ Days left |
| **On-pace check** | Compares % of goal reached vs. % of the selling period elapsed |
| **Incentive** | Dishes sold × ฿5 (shown per branch and company-wide) |

There is **no per-branch quota** — 25,806 is a single shared company goal, and each
branch simply logs its actual sales. (This matches what you asked for.)

---

## 3) Turn on real multi-device sync (Firebase) — ~10 minutes, free

Firebase is Google's free realtime database. When configured, any store can log in
from any phone or PC, and management sees all branches update live.

1. Go to **https://console.firebase.google.com** and sign in with your Google account
   (eawealthy@gmail.com works).
2. Click **Add project** → name it e.g. `isaan-tracker` → you can disable Google Analytics → **Create**.
3. In the left menu open **Build → Realtime Database** → **Create Database** →
   choose location **Singapore (asia-southeast1)** → start in **Test mode** → **Enable**.
4. Click the **⚙️ gear → Project settings**. Scroll to **Your apps**, click the **`</>` (Web)** icon,
   give it a nickname, **Register app**. You'll see a `firebaseConfig = { ... }` block. **Copy those values.**
5. Open **`index.html`** in any text editor, find the `firebase: { ... }` section near the top
   (inside `CONFIG`), and paste your values:

   ```js
   firebase: {
     apiKey:      "AIza........",
     authDomain:  "isaan-tracker.firebaseapp.com",
     databaseURL: "https://isaan-tracker-default-rtdb.asia-southeast1.firebasedatabase.app",
     projectId:   "isaan-tracker",
     appId:       "1:1234567890:web:abcdef"
   }
   ```

   > `databaseURL` is the one that must be filled — it's shown at the top of the Realtime Database page.

6. Save the file. Reopen it — the banner should now read **🟢 เชื่อมต่อระบบซิงก์ออนไลน์**.

### Security rules (important)

Test mode expires after ~30 days. Before that, in **Realtime Database → Rules**, paste this
so the app keeps working through the promo (open read/write — acceptable for a short internal promo,
since only people with the link + PINs use it):

```json
{
  "rules": {
    "sales": {
      ".read": true,
      ".write": true,
      ".validate": "true"
    }
  }
}
```

If you want it locked to your promo end date instead, set the rules' `.write` to
`"now < 1790787600000"` (that's the end of 30 Sep 2026, Thailand time). Ask me and I'll tune it.

---

## 4) Publish on GitHub (free hosting via GitHub Pages)

The easiest way (no command line needed):

1. Create a free account at **https://github.com** and click **New repository** →
   name it e.g. `isaan-sales-tracker` → **Public** → **Create repository**.
2. On the new repo page click **uploading an existing file** → drag in **`index.html`**
   (and this `README.md`) → **Commit changes**.
3. Go to **Settings → Pages** → under **Branch** pick **`main`** and **`/ (root)`** → **Save**.
4. Wait ~1 minute. Your live link appears at the top of that Pages screen, like:
   **`https://YOURNAME.github.io/isaan-sales-tracker/`**
5. Share that link with the 24 stores and management. Done. ✅

**Prefer the command line?** This folder is already a git repo. Run:

```bash
cd isaan-sales-tracker
git remote add origin https://github.com/YOURNAME/isaan-sales-tracker.git
git branch -M main
git push -u origin main
```

then enable Pages as in step 3.

> Do the Firebase setup (Section 3) **before** publishing, so the live site syncs.
> Note: your Firebase web keys are safe to include in a public page — access is controlled by the database rules, not the key.

---

## 5) Customize (all in the `CONFIG` block at the top of `index.html`)

- **Change store PINs / manager password** — edit each branch's `pin` and `manager.pass`.
  Give each store a private PIN and only share theirs with them.
- **Change the goal / dates / incentive** — `target`, `startDate`, `endDate`, `incentive`.
- **Rename menus or branches** — edit the `menus` and `branches` arrays.

---

## 6) Opening-day data (10 July) is pre-loaded

The 10 July sales from your Excel (total **299 dishes** — 175 / 36 / 88) are built into
the `SEED` list near the top of `index.html`. The **first time** the app runs on an empty
database (demo or Firebase) it loads them once automatically and marks itself seeded, so it
never duplicates — even across devices. Stores can edit their 10 July numbers like any other day.

To start completely blank instead, set `const SEED = [];`.

## 7) Good to know

- **Store view** shows only that branch's own input and history; it can edit any past day within the window.
- **Management view** is read-only reporting: KPIs, per-branch table (sortable), two charts, and **Download CSV**.
- Thai text and fonts (Noto Sans Thai) load from Google Fonts; charts use Chart.js — both from public CDNs, which work fine on GitHub Pages.
- Days-left counts down to 30 Sep 2026. If you open it after that date it shows 0 — that's expected.

Built with Claude Code.
