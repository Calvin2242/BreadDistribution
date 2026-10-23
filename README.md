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
- Change the price of any bread at any time (e.g. for promotions) from the **Add bread** tab
- See how many of each bread have been sold and how many are left
- See total sales, split into cash and QR
- Cancel a mistaken sale, which puts the bread back into stock
- Look at any past day with the date picker
- See a bar chart of daily sales (cash and QR) for the last 7, 14 or 30 days, with total, daily average and best day
- Remove unsold bread from stock (for example, a typing mistake or bread that was burnt or dropped)

**For students**
- Large, touch-friendly buttons designed for phones and tablets
- No login or student name needed: just pick the bread and type the buyer's name
- Teacher screens (add bread, prices, sales, graph) are locked behind a 4-digit PIN
- Tap a bread card to select it. Each card shows the number left
- Choose the quantity with + / − buttons
- Enter the buyer's name and choose payment mode (Cash or QR)
- The total price is shown in large text and updates as the quantity changes
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

### Setting the teacher PIN

The starting PIN is `1234` (`DEFAULT_PIN` near the top of `Code.gs`). **Change it in the app:** unlock a teacher tab, tap **🔑 Change PIN**, type the new 4 digits, then type them again to confirm. The new PIN takes effect straight away, with no redeploy.

**Forgot the PIN?** In Apps Script, open **Project Settings (⚙️) → Script Properties** and delete the `PIN` entry. The PIN goes back to `DEFAULT_PIN`.

- **Sell** is open to everyone.
- **Add bread**, **Sales today** and **Report** are locked. Tapping one opens a PIN keypad.
- After unlocking, a **🔒 Lock** button appears at the top. The screens also lock after 10 minutes without touching the screen (change `LOCK_MINUTES` in `index.html`) or when the page is reloaded.
- The PIN is checked by the script and isn't stored in the web page, and the script refuses stock changes, price changes and the sales list without it. It is still a light lock rather than strong security: the PIN is only four digits and there is no limit on wrong tries.

### Adding the school logo

Upload your logo to the **root of the repository** and name it exactly `logo.jpeg` (lowercase). It appears automatically beside the title at the top of the page. Until you add it, the page simply shows no logo.

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

### Teacher: changing prices (promotions)

1. Open **Add bread**. Under **Today's stock & prices**, each bread has a price box.
2. Type the new price (e.g. `1.50` for a promotion) and tap **Save**.
3. The new price applies to all **new** sales straight away. Sales already recorded keep the price they were sold at.
4. To end the promotion, change the price back and tap **Save**.

Prices are set per day. Each new day, the price you enter when adding that day's stock is used.

### Teacher: end of the day

1. Open **Sales today** to see the total, cash and QR amounts, plus every sale with time, buyer and payment mode.
2. Use the cash and QR totals to check against the money collected.
3. Use the date picker at the top to review earlier days.

### Teacher: viewing the sales graph

1. Open the **Report** tab.
2. Choose **7**, **14** or **30 days**. The chart ends on the date selected at the top of the page.
3. Each bar is one day's sales. The **gold** part is cash and the **teal** part is QR. Hover over (or long-press) a bar to see the exact amounts.
4. Below the chart you'll see the total for the period, the daily average and the best day.

Days with no sales show as empty gaps.

### Teacher: removing stock

1. Open **Add bread**. Under **Today's stock & prices**, find the bread.
2. In the **Remove unsold** row, type how many to take out. The box starts with all unsold bread filled in.
3. Tap **Remove** and confirm.

You can only remove bread that hasn't been sold, so the amount can't be higher than the number left. If the bread was already sold by mistake, cancel those sales first in **Sales today**. If you remove all of a bread that had no sales, it disappears from the Sell screen.

### Fixing mistakes

| Problem | How to fix |
|---------|-----------|
| A sale was entered wrongly | **Sales today → Cancel** on that sale, then record it again correctly. The bread goes back into stock. |
| Added too much stock | **Add bread → Remove unsold** (see above). |
| Wrong price | **Add bread → Price → Save**. |
| Anything else in stock | Open the Google Sheet, go to the **Stock** tab, and edit or delete the row. |
| Wrong bread name in stock | Edit the name in the **Stock** tab. If sales were already made under the old name, edit those in **Sales** too. |

---

## What the Google Sheet looks like

Two tabs are created automatically. Don't rename the tabs or change the header row, and don't insert columns.

### `Stock` tab

| Date | Bread | Qty | Price |
|------|-------|-----|-------|
| 2026-10-22 | Roti Kaya | 10 | 2.5 |
| 2026-10-22 | Donut | 20 | 1.5 |
| 2026-10-22 | Donut | -3 | 1.5 |

A negative `Qty` is a removal made with the **Remove** button. The amounts for each day and bread are added together, so the Donut row above means 17 donuts in stock.

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

- **The PIN is basic.** It is only four digits with no limit on wrong tries, so it keeps students out of the teacher screens but won't stop a determined person.
- **The script URL is visible** in the page source. Anyone technical can send *sales* to it directly, since selling needs no PIN.
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