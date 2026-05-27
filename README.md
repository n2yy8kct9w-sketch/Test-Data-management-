# TestData Manager

A fully-offline web app for tracking tractor R&D test applications — planned vs actual run hours / cycles, failure history, and programme-level dashboards. Installs as a real app on Android (Edge / Chrome) and iPhone (Safari) via "Add to Home Screen". No app store required, no internet required after install.

## Features

- **Multi-project** — track several programmes in parallel (e.g. Puddling Special, ROPS canopy, CVT, AC cabin, NVH).
- **Test applications** — field trials, endurance rigs, durability, hot/cold, NVH, hydraulic cycles, transmission endurance, structural.
- **Cumulative run logging** — record actuals as on date; deltas, progress %, completion status compute automatically.
- **Failure history** — component, mode, severity (Minor / Major / Critical), RCA workflow, corrective actions.
- **Dashboards** — project progress, severity mix, failures by hour band, failures by application, MTBF per app.
- **Offline-first** — `localStorage` persistence, Chart.js inlined, service worker caches everything. No network needed.
- **Android-native UX** — system back button closes overlays step-by-step before exiting the app.
- **Export / Import** — JSON backups and CSV exports for sharing with the team.

## Deploy to GitHub Pages

### Option A — From phone, no terminal

1. Open **github.com** in your browser. Sign in.
2. Tap **+** → **New repository**. Name it `testdata-manager`. Set **Public**. **Create**.
3. On the empty repo, tap **"uploading an existing file"** (in the Quick setup section).
4. Upload all eight files from the bundle:
   - `index.html`
   - `manifest.json`
   - `service-worker.js`
   - `icon.svg`
   - `icon-192.png`
   - `icon-512.png`
   - `icon-maskable-512.png`
   - `.nojekyll`
   - `README.md`
5. **Commit changes**.
6. Repo **Settings** → **Pages** → Source: **Deploy from a branch** → `main` / `/ (root)` → **Save**.
7. Wait ~1 minute. Site is live at:
   ```
   https://<your-username>.github.io/testdata-manager/
   ```

### Option B — From a computer with git

```bash
git init
git add .
git commit -m "Initial commit — TestData Manager PWA"
git branch -M main
git remote add origin https://github.com/<your-username>/testdata-manager.git
git push -u origin main
```

Then enable GitHub Pages in **Settings → Pages → Deploy from a branch → main / root**.

## Install on Android (Edge or Chrome)

1. Open the GitHub Pages URL in **Microsoft Edge** (or Chrome).
2. Tap the **⋯** menu → **"Add to phone"** (Edge) or **"Install app"** (Chrome).
3. Confirm. A real app icon appears on your home screen.
4. Open it — runs fullscreen, no browser bars, completely offline. The **system Back button** closes overlays one at a time before exiting the app.

Alternatively, the app's own ⋯ menu has an **"Install App"** option that triggers the same prompt.

## Install on iPhone (Safari)

iPhone Safari doesn't show install prompts the same way — you have to use the share sheet:

1. Open the URL in **Safari** (Edge/Chrome on iOS won't work for install — Apple restriction).
2. Tap **Share** (square with arrow up) → **Add to Home Screen** → **Add**.
3. Open from the home screen icon — fullscreen, offline, identical to Android.

## Updating the app

After pushing changes to GitHub:

1. **Bump `CACHE_VERSION`** in `service-worker.js` (e.g. `'tdm-v3'` → `'tdm-v4'`). This is what tells installed PWAs to invalidate their cache.
2. Push to GitHub. Pages redeploys in ~1 minute.
3. On the phone, fully close the app from the recent-apps view, then reopen. The new code loads.

## Backups

Data lives in `localStorage` on the device (~5 MB limit, plenty for this use case).

- **⋯ menu → Export JSON** — full snapshot to Downloads / Files.
- **⋯ menu → Export CSVs** — applications, log, failures in one file. Good for sharing with the team.
- **⋯ menu → Import JSON** — restore from a backup (replaces all on-device data).

Recommended: monthly JSON export, kept in OneDrive / Google Drive / iCloud.

## File overview

| File | Purpose |
|------|---------|
| `index.html` | The entire app (HTML + CSS + JS + Chart.js, inlined, ~300 KB) |
| `manifest.json` | PWA metadata (name, icons, theme color, app shortcuts) |
| `service-worker.js` | Caches app shell for guaranteed offline use |
| `icon-192.png` | App icon (192×192 — required by Android install) |
| `icon-512.png` | App icon (512×512 — used in splash screen + install dialog) |
| `icon-maskable-512.png` | Maskable icon for Android adaptive icon shapes |
| `icon.svg` | Vector icon (fallback) |
| `.nojekyll` | Tells GitHub Pages to serve files raw, no Jekyll processing |
| `README.md` | This file |

## Tech notes

- No build step — open `index.html` directly in any modern browser and it works.
- All data is local (`localStorage`, ~5 MB limit).
- Chart.js 4.4.1 is inlined; no CDN dependency at runtime.
- Compatible with Edge / Chrome / Firefox / Safari on both desktop and mobile.
- No analytics, no tracking, no external requests once installed.
