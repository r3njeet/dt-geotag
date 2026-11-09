# DT Geo-Tagging – EDD MOHAMMADI

Mobile web app for field staff to capture GPS coordinates of distribution transformers and write them straight into the two ABP 2026-27 Google Sheets.

| Tab in app | Google Sheet / tab | Columns filled |
|---|---|---|
| **DT INC** | Capacity enhancement → `DT INC` | K = Existing DT Longitude, L = Existing DT Latitude |
| **NEW DT** | Construction of sub-station → `New DT` | I = Existing DT Latitude, J = Existing DT Longitude, M = New DT Latitude, N = New DT Longitude |

Only rows where column E (Name Of Division) = **EDD MOHAMMADI** are shown and can be written. Rows 1–2 are treated as headers.

## How it works

```
Phone browser (GitHub Pages, HTTPS)  ──fetch──▶  Google Apps Script web app  ──▶  Both Google Sheets
```

GitHub Pages hosts the static app (HTTPS is required for GPS). The Apps Script runs under your Google account, so field users don't need sheet access or a Google login.

## Setup (≈10 minutes)

### 1. Deploy the backend (Google Apps Script)
1. Sign in with a Google account that has **edit** access to both sheets.
2. Go to https://script.google.com → **New project**.
3. Delete the sample code and paste the contents of `apps-script/Code.gs`. Save.
4. (Optional) set `ACCESS_KEY: 'yourPIN'` in `CONFIG` to restrict use.
5. Select the function **testSetup** → **Run** → grant permissions. The log should show the row counts for EDD MOHAMMADI.
6. **Deploy → New deployment →** type **Web app**
   - Execute as: **Me**
   - Who has access: **Anyone**
7. Copy the **Web app URL** (ends in `/exec`).

### 2. Publish the frontend (GitHub Pages)
1. Create a new GitHub repository, e.g. `dt-geotag`.
2. Edit `config.js` → paste the Web app URL into `API_URL`. If you set an `ACCESS_KEY`, set `REQUIRE_KEY: true`.
3. Upload `index.html`, `config.js` (and this README) to the repo root.
4. Repo **Settings → Pages → Source: Deploy from a branch → main / (root)** → Save.
5. After a minute the app is live at `https://<your-username>.github.io/dt-geotag/`.

Share that link with field staff. On Android/iPhone they can use **Add to Home screen**.

## Using the app
1. Pick **DT INC** or **NEW DT**.
2. Search/filter (Pending / Completed) and tap a row.
3. Stand at the transformer and tap **📍 Capture current location**. The app listens for up to 15 s and keeps the most accurate fix (accuracy shown; ≤30 m is marked good).
4. Values can also be typed or corrected by hand.
5. Tap **Save to sheet**.

### Safeguards
- Lat/Long are checked against Uttar Pradesh bounds (Lat 23.5–30.6, Long 77–84.8) on both phone and server – catches swapped values.
- Before writing, the server re-checks that the row still belongs to EDD MOHAMMADI and the S.No hasn't shifted.
- Writes are locked so two users saving at once don't collide.
- No network? The save is kept on the phone and synced automatically when back online.

## Updating the backend
After editing `Code.gs`: **Deploy → Manage deployments → ✏️ → Version: New version → Deploy**. The URL stays the same.

## Changing the division or columns
Everything is in the `CONFIG` block at the top of `Code.gs` (`DIVISION`, column numbers, tab names). Column numbers are 1-based (A=1, E=5, K=11…).
