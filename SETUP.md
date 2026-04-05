# 📋 Formular 230 – Setup Guide

## 🏗️ Technology Stack

| Layer | Technology | Why |
|-------|-----------|-----|
| Frontend | HTML + Tailwind CSS (CDN) | No build step, works on GitHub Pages |
| Signature | signature_pad v4 | Canvas-based, touch & mouse |
| Backend / DB | Google Apps Script + Google Sheets | Free, acts as REST API, exports as .xlsx |
| Hosting | GitHub Pages + GitHub Actions | Free, automatic deploys on push |
| Excel export | SheetJS (xlsx.js) | Client-side .xlsx generation |
| PDF fill | pdf-lib | Client-side PDF manipulation (admin) |

---

## 🚀 Step 1 – Create a GitHub Repository

1. Go to [github.com](https://github.com) → **New repository**
2. Name it `formular230` (or any name you prefer)
3. Set visibility to **Public** (required for free GitHub Pages)
4. Do **NOT** initialize with README (you already have files)

---

## 🚀 Step 2 – Push your files to GitHub

Open a terminal in the project folder and run:

```bash
git init
git add .
git commit -m "Initial commit – Formular 230"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/formular230.git
git push -u origin main
```

Replace `YOUR_USERNAME` with your GitHub username.

---

## 🚀 Step 3 – Enable GitHub Pages

1. In your GitHub repo → **Settings** → **Pages**
2. Under **Source**, select **GitHub Actions**
3. The workflow (`.github/workflows/deploy.yml`) will trigger automatically on push
4. Your site will be live at: `https://YOUR_USERNAME.github.io/formular230/`

---

## 🚀 Step 4 – Set up Google Sheets Backend

### 4a. Create the Google Sheet

1. Go to [sheets.google.com](https://sheets.google.com)
2. Create a new spreadsheet, name it **Formular230**
3. Copy the **Spreadsheet ID** from the URL:
   ```
   https://docs.google.com/spreadsheets/d/SPREADSHEET_ID_HERE/edit
   ```

### 4b. Create the Google Apps Script

1. Go to [script.google.com](https://script.google.com)
2. Click **New project**
3. Open the file `apps-script/Code.gs` from this project
4. Copy & paste the entire contents into the Apps Script editor
5. Replace `YOUR_GOOGLE_SHEET_ID_HERE` with your actual Spreadsheet ID

### 4c. Deploy as Web App

1. In Apps Script editor → **Deploy** → **New Deployment**
2. Click the gear icon next to "Type" → select **Web app**
3. Configure:
   - **Description:** Formular 230 API
   - **Execute as:** Me
   - **Who has access:** Anyone
4. Click **Deploy**
5. **Copy the Web App URL** (looks like `https://script.google.com/macros/s/ABC.../exec`)
6. Click **Authorize access** and grant permissions

### 4d. Connect the URL to your web page

Open `index.html` and replace:
```javascript
const APPS_SCRIPT_URL = 'YOUR_APPS_SCRIPT_URL_HERE';
```
with:
```javascript
const APPS_SCRIPT_URL = 'https://script.google.com/macros/s/YOUR_SCRIPT_ID/exec';
```

Do the same in `admin.html`.

Then commit and push:
```bash
git add index.html admin.html
git commit -m "Connect Google Apps Script backend"
git push
```

---

## 🚀 Step 5 – Set Organization Details (Optional)

In `index.html`, pre-fill the entity fields:
```html
<input id="entityName" value="Asociația Dumneavoastră" ... />
<input id="entityCIF"  value="12345678" ... />
```

---

## 🔐 Step 6 – Secure the Admin Page

The current admin page uses a simple client-side password (`admin230`).
**Change it** in `admin.html`:
```javascript
const ADMIN_PASSWORD = 'your-strong-password-here';
```

For production, consider:
- [Firebase Authentication](https://firebase.google.com/docs/auth)
- [Netlify Identity](https://docs.netlify.com/visitor-access/identity/)
- Moving admin to a private repo

---

## 📁 Project Structure

```
formular230/
├── index.html              ← Public form (GitHub Pages)
├── admin.html              ← Admin dashboard (PDF, Excel export)
├── apps-script/
│   └── Code.gs             ← Google Apps Script (deploy separately)
├── .github/
│   └── workflows/
│       └── deploy.yml      ← Auto-deploy to GitHub Pages
└── SETUP.md                ← This file
```

---

## 🗂️ Data Flow

```
User fills form (index.html)
        │
        ▼
POST request (JSON + base64 signature)
        │
        ▼
Google Apps Script (Code.gs)
        ├── Appends row to Google Sheet
        ├── Saves signature PNG to Google Drive
        └── Sends confirmation email to user
        │
        ▼
Admin (admin.html)
        ├── Reads data from Google Sheet via Apps Script
        ├── Exports to .xlsx (SheetJS)
        └── Auto-fills ANAF PDF (pdf-lib)
```

---

## 📤 Exporting Data as Excel

- In the **admin page**, click **Export Excel** to download a `.xlsx` file
- Google Sheets also natively exports: **File → Download → Microsoft Excel (.xlsx)**

---

## 🧩 Future: PDF Auto-fill Calibration

The ANAF Form 230 PDF has specific coordinates for each field.
To calibrate:
1. Open the official PDF in the admin page
2. The current coordinates in `admin.html` are estimates — adjust `x` and `y` values
3. Use the [pdf-lib coordinate tool](https://pdf-lib.js.org/) to find exact positions

---

## ❓ Troubleshooting

| Issue | Solution |
|-------|----------|
| Form submits but no data in Sheet | Re-deploy Apps Script as new version |
| CORS error | Make sure Apps Script is deployed with "Anyone" access |
| Signature not saving | Check Drive permissions in Apps Script |
| GitHub Pages shows 404 | Enable Pages in repo Settings → Pages |
