# Fortify Health – Premix Batch Scanner

Mobile-first web app (PWA) for Quality Officers and field staff at partner wheat flour mills.

**Scan → Verify → Enter Quantity → Submit**, with every transaction saved to a Google Sheet in Drive.

## What's new in version 2
- **Reads the printed label text** (no QR code or barcode needed). Take or upload a photo of the premix label and the app reads:
  Product Name, Product Category, Product Code, Version No., Batch No., Mfg. Date, Use By Date, Net Weight,
  Rate of Addition, Iron / Vitamin B12 / Folic Acid levels, Manufacturer and FSSAI Lic. No.
- Sideways and upside-down photos are handled automatically.
- Anything the app could not read is marked **Not read · check label**. The user must tick
  "I have checked these details against the printed label" before submitting.
- A batch that is not yet in the Batch Master is added automatically when its first transaction is submitted.
- **Google Sheet sync**: transactions, batch details and stock levels are sent to your spreadsheet.
  Offline entries are queued on the phone and sent when the connection returns. Retries never create duplicate rows.
- QR / barcode scanning is still available: Profile → Scan Method → QR / Barcode.

## Files
| File | Purpose |
| --- | --- |
| `index.html` | The whole app (HTML, CSS and JavaScript) |
| `google-apps-script/Code.gs` | Script that writes app data into your Google Sheet |
| `manifest.webmanifest` | Lets phones install the app on the home screen |
| `sw.js` | Service worker that caches the app for offline use |
| `icons/` | App icons |
| `sample-label.jpg` | Example premix label for testing |
| `.nojekyll` | Tells GitHub Pages to serve files as they are |

## 1. Publish the app with GitHub Pages
1. Upload every file in this folder to the root of your repository (keep the `icons` and `google-apps-script` folders).
   If you already uploaded version 1, replace the old files.
2. Settings → Pages → Deploy from a branch → `main` and `/ (root)` → Save.
3. Your link: `https://<your-username>.github.io/<repository-name>/`

## 2. Connect the Google Sheet
1. In Google Drive, create a new Google Sheet, for example **Premix Batch Transactions**.
2. In the sheet, open **Extensions → Apps Script**. Delete the sample code and paste the whole of `google-apps-script/Code.gs`. Click Save.
3. Click **Deploy → New deployment**. Choose type **Web app**.
   - Execute as: **Me**
   - Who has access: **Anyone**
   Click **Deploy**, then allow the permissions Google asks for.
4. Copy the **Web App URL** (it ends with `/exec`).
5. In the app: **Profile → Google Sheet → Connect**. Paste the Web App URL and the Sheet link, tap **Test Connection**, then **Save**.

The script creates three tabs on the first sync:
- **Transactions**: Transaction ID, Date & Time, User, Mill, Batch Number, Transaction Type, Quantity (kg), Source, Destination, Remarks, Capture Method, Product Name, Product Code, Vendor, Mfg and Expiry Dates, Override, Edited Fields, Sync Status
- **Batch Master**: every batch with the details read from its label
- **Stock**: available kg per batch and mill

The app also loads batches from the Batch Master tab, so batches you add or correct in the sheet are recognised on every phone.

**Security note:** with access set to "Anyone", anyone who has the Web App URL can send data to the sheet. Share the URL only with your team, and set an `ACCESS_KEY` in `Code.gs` (and the same key in the app) for extra protection. The sheet itself stays private to you and the people you share it with.

If you change `Code.gs` later: Deploy → Manage deployments → Edit (pencil) → Version: **New version** → Deploy. The URL stays the same.

## 3. Taking a good label photo
- Fill the frame with the label, in good light, and avoid glare from the foil.
- Hold steady so the text is sharp. Sideways is fine.
- Reading takes about 10–30 seconds on most phones (longer on older phones). The first reading needs internet to download the label reader (about 10 MB); after that it is cached.
- Always compare the values with the label before submitting. Table values such as nutrient levels are the hardest to read.

## Updating the app
Change `VERSION` in `sw.js` (for example `pmx-v3`) whenever you change `index.html`, so phones pick up the new version.

## Next steps for production
- Replace the sample `MILLS` and user in `index.html` with your real mill list and a sign-in.
- Stock is calculated on each phone and the latest figures are written to the Stock tab. For several phones working on the same mill, read stock back from the sheet or move to a database.
