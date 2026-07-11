# AAI Mauli League — Live Auction Draft Board 🏏

A single-page, drag-and-drop cricket auction/draft board. Players are pulled live from a published Google Sheet, shuffled onto a "stage," and drafted onto team rosters by dragging cards — with automatic face-cropped player avatars and state that survives page refreshes.

> Built as one self-contained HTML file — no build step, no backend, no dependencies to install.

---

## ✨ Features

- **Live player data** — pulled directly from a published Google Sheet (CSV export), no manual data entry.
- **Tiered auction flow** — players are released in tiers/rows; each tier is randomized before the next unlocks.
- **Drag-and-drop drafting** — drag a shuffled player card from the stage onto any of the 4 team boxes.
- **Automatic face-cropping** — uses [face-api.js](https://github.com/vladmandic/face-api) (TinyFaceDetector) in the browser to auto-crop each player photo to their face, client-side, no server required.
- **Per-team branding** — team names and accent colors are read straight from the sheet (`Team Name|#hexcolor` format).
- **Persistent state** — the entire draft (current tier, shuffled pool, team rosters) is saved to `localStorage`, so a refresh or accidental close doesn't lose progress.
- **One-click reset** — clears saved state and starts over.
- **Premium, responsive UI** — glassmorphism panel, animated card dealing on shuffle, staggered entrance effects, and a layout that keeps all 4 team columns visible on one line at every screen size.

---

## 🧱 Tech Stack

| Layer | Choice |
|---|---|
| Markup / Styling | Plain HTML5 + CSS3 (custom properties, CSS Grid/Flexbox, CSS animations) — no framework |
| Logic | Vanilla JavaScript (ES2017+, no build tools, no bundler) |
| Data source | Google Sheets, published as CSV |
| Face detection | [`@vladmandic/face-api`](https://www.npmjs.com/package/@vladmandic/face-api) via CDN |
| Fonts | Oswald (display) + Inter (UI/body) via Google Fonts |
| Persistence | Browser `localStorage` |

No `npm install`, no package.json, no build pipeline — it's one HTML file you can open directly or host anywhere that serves static files.

---

## 📁 Project Structure

```
.
├── AMPL_26_Draft.html   # the entire application: markup, styles, and logic
├── images/              # player photos, named to match sheet entries
│   └── <PlayerName>.jpg
└── README.md
```

Player photos are expected at `images/<PlayerName>.jpg`, where `<PlayerName>` matches the player's name exactly as it appears in the Google Sheet. If a photo is missing, a placeholder silhouette icon is shown automatically.

---

## ⚙️ Setup

### 1. Clone / download this repo

```bash
git clone <your-repo-url>
cd <your-repo-folder>
```

### 2. Add player photos

Drop player images into an `images/` folder next to the HTML file, named to match each player's name in your sheet (e.g. `images/Virat Kohli.jpg`).

### 3. Point it at your Google Sheet

Open `AMPL_26_Draft.html` and find this line near the top of the second `<script>` block:

```js
const SHEET_URL = 'https://docs.google.com/spreadsheets/d/e/.../pub?...&output=csv';
```

Replace it with your own published-to-web CSV link (**File → Share → Publish to web → CSV**, in Google Sheets).

### 4. Run it

Just open the file in a browser, or serve the folder with any static file server, for example:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000/AMPL_26_Draft.html
```

> A static server (rather than `file://`) is recommended so the browser's `fetch()` calls to your Google Sheet and CDN scripts behave consistently.

### 5. Deploy (optional — GitHub Pages)

1. Push this repo to GitHub.
2. Go to **Settings → Pages**.
3. Set the source branch (e.g. `main`) and root folder.
4. Your draft board will be live at `https://<username>.github.io/<repo>/AMPL_26_Draft.html`.

---

## 📋 Expected Google Sheet Format

The app reads the first 20 rows of the published CSV:

- **Rows 1–19**: each row is one auction "tier." Each cell in a row is one player's name for that tier. Empty cells are ignored.
- **Row 20**: the four team definitions, one per column, in the format:

  ```
  Team Name|#hexcolor
  ```

  Example: `Chennai Kings|#facc15`. If a column is blank, a default name (`Team A`/`B`/`C`/`D`) and red accent are used.

The team name can also include a player's name as a substring (e.g. an owner/captain's name) — if it matches a player in the current tier, that player is auto-assigned to that team instead of entering the shuffle pool.

---

## 💾 How State Is Saved

Everything needed to resume a draft in progress is written to `localStorage` under the key `boundaryDraftState`:

```json
{
  "index": 2,
  "isRandomized": true,
  "pool": [{ "name": "Player Name", "num": "3" }],
  "teams": {
    "team-1": ["Player A", "Player B"],
    "team-2": [],
    "team-3": [],
    "team-4": []
  }
}
```

Click **Reset Draft** to clear this and start a fresh draft (with a confirmation prompt to avoid accidental resets).

---

## 🌐 Browser Support

Works in any modern evergreen browser (Chrome, Edge, Firefox, Safari) with support for:
- CSS Grid, Flexbox, custom properties, `backdrop-filter`
- HTML5 Drag and Drop API
- `fetch()`, `localStorage`
- WebAssembly (required by face-api.js's face detection model)

`backdrop-filter` and the glass panel gracefully degrade to a solid panel on older browsers that don't support it.

---

## 🧩 Known Limitations

- Face cropping requires the player image to load successfully and contain a detectable face; otherwise the original image (or fallback icon) is shown.
- The Google Sheet must be published as CSV and publicly readable — this app has no authentication layer.
- State is stored per-browser (`localStorage`), not synced across devices — everyone drafting together should use the same device/browser, or you'll need to add a shared backend.

---

## 🎨 UI Customization

All visual tokens (colors, radii, shadows, easing) live in the `:root` block at the top of the `<style>` tag in `AMPL_26_Draft.html`:

```css
--leather-red: #C81E3A;   /* primary accent */
--gold: #D4AF37;          /* premium gold accent */
--pitch-navy: #070A13;    /* background base */
```

Change these values to retune the whole palette without touching any component rule or any JavaScript.

## 🤝 Contributing

Issues and pull requests are welcome. Since this is a single-file app, please keep functional changes and visual/styling changes in separate commits/PRs where possible to keep reviews easy.

## 📄 License

Add your preferred license here (e.g. MIT) before publishing publicly.