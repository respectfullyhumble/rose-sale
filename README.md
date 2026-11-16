# Rose Sale ’27

Celadon’s Rose Sale ordering site. Buyers pick flowers, write a card, choose classroom delivery or booth pickup, and pay with GCash. Orders land in a Google Sheet and receipt screenshots go to a Google Drive folder.

```
index.html            the whole site (HTML, CSS, JS in one file)
img/                  banner pieces, logo, boat, favicon
apps-script/Code.gs   the Google Sheets backend (not used by GitHub Pages)
```

## 1. Set up the Google Sheet (about 5 minutes)

1. Create a new Google Sheet, e.g. **Rose Sale ’27 Orders**. Use the org account if the org should own the data.
2. In the sheet: **Extensions → Apps Script**.
3. Delete what’s in `Code.gs` and paste in everything from `apps-script/Code.gs`. Save.
4. In the function dropdown at the top, pick **setup** and click **Run**.
   Google will ask for permissions (Sheets and Drive). Approve them. If you see “Google hasn’t verified this app”, click **Advanced → Go to (project name)**. That warning is normal for your own scripts.
5. Check the sheet: it now has an **Orders** tab and an **Items** tab. Your Drive has a folder called **Rose Sale ’27 Receipts**.

## 2. Publish the backend as a web app

1. In Apps Script: **Deploy → New deployment**.
2. Click the gear next to “Select type” and choose **Web app**.
3. Set **Execute as: Me** and **Who has access: Anyone**.
4. Click **Deploy** and copy the **Web app URL** (it ends in `/exec`).
5. Paste that URL into your browser. You should see `{"ok":true,"service":"Rose Sale '27 orders"}`.

> **Changed the script later?** Go to **Deploy → Manage deployments → ✏️ → Version: New version → Deploy**. The URL stays the same. If you make a *new* deployment instead, you get a new URL and have to update `index.html`.

## 3. Point the site at your sheet

Open `index.html`, find the `CONFIG` block near the top of the `<script>`, and fill it in:

```js
const CONFIG = {
  deliveryDates: ["2027-02-08", ...],   // delivery days (YYYY-MM-DD)
  pickupDates:   ["2027-02-08", ...],   // booth pickup days
  orderClose: "2027-02-05",            // last day to order
  gcashName: "Celadon Treasurer",      // name shown above the GCash number
  gcashNumber: "09XX XXX XXXX",        // the org's GCash number
  qrImage: "",                         // e.g. "img/qr.png" (put the QR image in img/)
  orderEndpoint: ""                    // ← paste the /exec URL here
};
```

While `orderEndpoint` is empty, the site runs in demo mode: checkout works, but nothing is saved and the order code starts with `DEMO-`.

Products and prices are in the `PRODUCTS` list right below `CONFIG`. They’re last year’s lineup, so update them to this year’s.

## 4. Put it on GitHub Pages to test

1. Create a new repository on GitHub, e.g. `rose-sale-27`. It must be **public** for free GitHub Pages.
2. **Add file → Upload files**, then drag in `index.html`, the `img` folder, and this README. The `apps-script` folder is optional.
3. Go to **Settings → Pages**. Under “Build and deployment”, choose **Deploy from a branch**, branch **main**, folder **/ (root)**. Save.
4. Wait a minute or two. The site will be at `https://<your-username>.github.io/rose-sale-27/`.

## 5. Test an order end to end

1. Open the GitHub Pages link on your phone.
2. Add something to the cart, check out with your own details, and upload any screenshot as the receipt.
3. You should see an order code like **RS27-0001**.
4. In the sheet, the order appears in **Orders** (one row per order) and **Items** (one row per item). The receipt link opens the screenshot in Drive.
5. Delete the test rows when you’re done. Order numbers keep counting up even after rows are deleted, so they never repeat. To start back at 0001 before launch: in Apps Script go to **Project Settings → Script Properties** and delete `ORDER_SEQ`.

## Working the orders

- **Status** column has a dropdown: Pending payment check → Paid → Ready → Out for delivery → Delivered / Picked up / Cancelled.
- **Flags** column warns you when:
  - the GCash reference was already used on another order,
  - no receipt was uploaded,
  - the total doesn’t match the items. Always check the amount in the GCash app; the site can’t verify payments on its own.
- The **Items** tab lists every item with its date and building, so you can filter it into a runner list per day.

## Moving it to the org website

The site is plain HTML with no build step. Copy `index.html` and the `img` folder to wherever the org site is hosted. The sheet and the `/exec` URL stay the same.
