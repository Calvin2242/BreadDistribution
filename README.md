# 🥖 PPKI Bakery – Bread Distribution System

A simple web app that helps PPKI students sell bread baked by the school bakery. The teacher records what was produced, and students go around the school selling it to staff and teachers, recording every sale on their phone, tablet or laptop.

- **Frontend:** a single `index.html` page, hosted free on GitHub Pages
- **Backend and database:** a Google Sheet, accessed through a small Google Apps Script
- **Cost:** free
- **Security:** none by design. This is a lightweight tool for a school activity (see [Limitations](#limitations))

---

## Table of contents

1. [Features](#features)
2. [How it works](#how-it-works)
3. [Files in this repository](#files-in-this-repository)
4. [Setup guide](#setup-guide)
5. [How to use](#how-to-use)
6. [What the Google Sheet looks like](#what-the-google-sheet-looks-like)
7. [Updating or changing the system](#updating-or-changing-the-system)
8. [Troubleshooting](#troubleshooting)
9. [Limitations](#limitations)
10. [Customisation ideas](#customisation-ideas)

---

## Features

**For the teacher**
- Add the bread produced each day: type, amount and price
- See how many of each bread have been sold and how many are left
- See total sales, split into cash and QR
- Cancel a mistaken sale, which puts the bread back into stock
- Look at any past day with the date picker

**For students**
- Large, touch-friendly buttons designed for phones and tablets
- No login or student name needed: just pick the bread and type the buyer's name
- Tap a bread card to select it. Each card shows the number left
- Choose the quantity with + / − buttons
- Enter the buyer's name and choose payment mode (Cash or QR)
- The total price is calculated automatically
- Sold-out bread is greyed out and can't be selected
- Light and dark mode follow the device setting

---

## How it works

```
 Phone / Tablet / Laptop
 (index.html on GitHub Pages)
            │
            │  fetch() requests (JSON)
            ▼
 Google Apps Script web app  (Code.gs)
            │
            ▼
 Google Sheet  ──  "Stock" tab  +  "Sales" tab
```

1. The page loads and asks the script for today's stock and sales.
2. The script reads the Google Sheet, works out how many of each bread are left, and sends the data back.
3. When a student records a sale, the page sends it to the script. The script checks that enough bread is left, then writes a new row to the **Sales** tab.
4. A lock inside the script makes sure two students saving at the same moment don't overwrite each other.

**Remaining bread = total added to Stock for that date − total sold in Sales for that date.**

---

## Files in this repository

| File | Purpose |
|------|---------|
| `index.html` | The whole web app (HTML, CSS and JavaScript in one file). Hosted on GitHub Pages. |
| `Code.gs` | The Google Apps Script backend. It is **not** hosted on GitHub. You paste it into your Google Sheet (see setup). Keep a copy here for reference. |
| `README.md` | This file. |

---

## Setup guide

You need a Google account and a GitHub account. The whole setup takes about 10–15 minutes.

### Step 1: Create the Google Sheet and add the script

1. Go to [sheets.google.com](https://sheets.google.com) and create a **new blank spreadsheet**. Name it something like `PPKI Bakery Data`.
2. In the sheet, click **Extensions → Apps Script**. A new tab opens with a code editor.
3. Delete everything in the editor (the default `function myFunction() {}`).
4. Open `Code.gs` from this repository, copy all of it, and paste it into the editor.
5. Click the 💾 **Save** icon and give the project a name, e.g. `PPKI Bakery API`.

### Step 2: Deploy the script as a web app

1. In the Apps Script editor, click **Deploy → New deployment**.
2. Click the ⚙️ gear icon next to "Select type" and choose **Web app**.
3. Fill in:
   - **Description:** `v1`
   - **Execute as:** **Me**
   - **Who has access:** **Anyone**
4. Click **Deploy**.
5. Google will ask you to authorise the script:
   - Click **Authorize access** and choose your Google account.
   - If you see *"Google hasn't verified this app"*, click **Advanced → Go to PPKI Bakery API (unsafe) → Allow**. This is normal for your own scripts.
6. Copy the **Web app URL**. It looks like this:
   `https://script.google.com/macros/s/AKfycb.../exec`

> ⚠️ Keep this URL handy. Anyone with the URL can read and write the bakery data, so share it only through the website, not publicly.

### Step 3: Connect the web page to the script

1. Open `index.html` in a text editor.
2. Near the top of the `<script>` section, find this line:

   ```js
   const API = "PASTE_YOUR_WEB_APP_URL_HERE";
   ```

3. Replace the text inside the quotes with your Web app URL from Step 2. Keep the quotes and make sure the URL ends in `/exec`.
4. Save the file.

### Step 4: Host the page on GitHub Pages

1. Create a new repository on GitHub, e.g. `ppki-bakery`. A public repository is the simplest for free GitHub Pages.
2. Upload `index.html`, `Code.gs` and `README.md` (**Add file → Upload files**).
3. Go to **Settings → Pages**.
4. Under **Build and deployment**, set **Source** to **Deploy from a branch**. Choose branch `main` and folder `/ (root)`, then click **Save**.
5. Wait 1–2 minutes. Your site will be live at:
   `https://YOUR-USERNAME.github.io/ppki-bakery/`

> ℹ️ Because the repository is public, the Web app URL inside `index.html` will be visible to anyone who views the source. This is acceptable for this system. See [Limitations](#limitations).

### Step 5: Test it

1. Open your GitHub Pages link.
2. Go to **Add bread**, then add `Roti Kaya`, amount `10`, price `2.50`.
3. Go back to **Sell**. You should see a card showing 10 left.
4. Record a test sale. Check **Sales today** and open the Google Sheet to confirm a row appeared.
5. Cancel the test sale (or delete the test rows in the sheet).

The `Stock` and `Sales` tabs are created automatically the first time the script is used.

**Tip:** on each phone or tablet, open the link and choose **Add to Home Screen** so it works like an app.

---

## How to use

### Teacher: start of the day

1. Open the site and tap **Add bread**.
2. Enter the **bread type**, **amount produced** and **price each (RM)**, then tap **Add to today's stock**.
3. Repeat for each type of bread.

Notes:
- If you add the same bread name again on the same day, the amounts are **added together**. The price used is the **most recent** one entered.
- Bread names must match exactly (`Roti Kaya` and `roti kaya` count as two different breads). Pick the name from the suggestion list to avoid this.

### Students: selling

1. Open the site on the **Sell** tab.
2. Tap the **bread** the buyer wants.
3. Enter the **buyer's name** (e.g. `Cikgu Aminah`).
4. Set the **quantity** with + and −.
5. Choose **Cash** or **QR**.
6. Check the total, then tap **Record sale**.

A green confirmation appears when the sale is saved, and the stock count updates.

### Teacher: end of the day

1. Open **Sales today** to see the total, cash and QR amounts, plus every sale with time, buyer and payment mode.
2. Use the cash and QR totals to check against the money collected.
3. Use the date picker at the top to review earlier days.

### Fixing mistakes

| Problem | How to fix |
|---------|-----------|
| A sale was entered wrongly | **Sales today → Cancel** on that sale, then record it again correctly. The bread goes back into stock. |
| Wrong amount or price in stock | Open the Google Sheet, go to the **Stock** tab, and edit or delete the row. |
| Wrong bread name in stock | Edit the name in the **Stock** tab. If sales were already made under the old name, edit those in **Sales** too. |

---

## What the Google Sheet looks like

Two tabs are created automatically. Don't rename the tabs or change the header row, and don't insert columns.

### `Stock` tab

| Date | Bread | Qty | Price |
|------|-------|-----|-------|
| 2026-10-22 | Roti Kaya | 10 | 2.5 |
| 2026-10-22 | Donut | 20 | 1.5 |

### `Sales` tab

| ID | Date | Time | Bread | Qty | Total | Buyer | Payment |
|----|------|------|-------|-----|-------|-------|---------|
| 1729584000000 | 2026-10-22 | 10:15 | Roti Kaya | 2 | 5 | Cikgu Aminah | Cash |

- The `ID` is generated automatically and is used by the Cancel button.
- Because everything is in a normal Google Sheet, you can make pivot tables, filter by bread or payment mode, or export to Excel for weekly or termly reports.

---

## Updating or changing the system

**Changing the web page (`index.html`):** edit the file on GitHub and commit. GitHub Pages updates within a minute or two. If you don't see the change, refresh the page or clear the browser cache.

**Changing the script (`Code.gs`):**
1. Edit the code in the Apps Script editor and save.
2. Click **Deploy → Manage deployments**, then the ✏️ pencil icon.
3. Under **Version**, choose **New version** and click **Deploy**.

The Web app URL stays the same. If you choose **New deployment** instead, you get a *new* URL and must update `index.html`.

**Time zone:** the script uses the time zone set in **Apps Script → Project Settings**. Set it to `(GMT+08:00) Kuala Lumpur` so sale times are correct.

---

## Troubleshooting

| Symptom | Likely cause and fix |
|---------|---------------------|
| **"Can't load data. Check connection or API link."** | The `API` URL is wrong or missing. Check it was pasted correctly into `index.html`, ends in `/exec`, and that the deployment's *Who has access* is **Anyone**. Also check the device has internet. |
| The page works for you but not for students | The deployment access is probably set to *Only myself*. Redeploy with **Anyone**. |
| Changes to `Code.gs` have no effect | You saved but didn't deploy a new version. See [Updating or changing the system](#updating-or-changing-the-system). |
| "Google hasn't verified this app" warning | Normal for your own script. Click **Advanced → Go to … (unsafe) → Allow**. |
| Bread list is empty | No stock has been added for the date shown at the top. Check the date picker, or add stock. |
| Dates look wrong or stock doesn't match | Don't convert the Date column to a date format in the sheet. It is stored as text (`yyyy-mm-dd`). |
| "Only X left" message | Another student sold some bread first. The screen refreshes with the real stock. |
| New bread added but not showing | Tap another tab and come back, or refresh the page. Data reloads on every tab switch. |
| Page doesn't update after you edited it on GitHub | Wait 1–2 minutes, then hard refresh (Ctrl + Shift + R, or clear the browser cache on mobile). |

---

## Limitations

This system is intentionally simple.

- **No login or PIN.** Anyone who has the website link can add stock or cancel sales. A student could open the *Add bread* tab by mistake.
- **The script URL is visible** in the page source. Someone technical could send data to it directly.
- **Daily stock only.** Leftover bread doesn't carry over to the next day. Add it as new stock if you want to sell it again.
- **Internet is required.** There is no offline mode. If a student's connection drops, the sale won't be saved, so they should wait for the confirmation message.
- **Google Apps Script limits.** The free quota is far above what a school bakery needs, but it isn't designed for thousands of sales per day.

---

## Customisation ideas

- **Add a teacher PIN** to protect the *Add bread* and *Cancel sale* actions.
- **Add a "Paid / Not paid yet" option** for buyers who will pay later.
- **Add a weekly summary tab** in the Google Sheet using a pivot table (total sales per bread or per day).
- **Change colours:** edit the colour variables at the top of the `<style>` section in `index.html` (`--gold`, `--teal`, `--ink`, etc.).
- **Change the title or logo:** edit the `<h1>` and `<title>` in `index.html`.

---

## Credits

Built for the PPKI (Program Pendidikan Khas Integrasi) bakery programme to help students practise real-world skills in a safe, supportive environment.