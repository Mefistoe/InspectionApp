# AURA InspectionApp

IEQ field inspection capture tool — offline-first PWA for iPhone.

## Deploy to GitHub Pages (5 minutes)

1. Create a new **public** repository on GitHub (e.g. `aura-field`)
2. Upload all four files to the repo root:
   - `index.html`
   - `sw.js`
   - `manifest.json`
   - `icon.svg`
3. In the repo → Settings → Pages → Source: **Deploy from a branch** → Branch: `main` / `root`
4. GitHub Pages URL will be: `https://mefistoe.github.io/inspectionapp/`

## iPhone Setup (one time per device)

1. Open the GitHub Pages URL in **Safari** (not Chrome)
2. Tap the Share button (□↑)
3. Tap **Add to Home Screen**
4. Tap **Add**

The app is now cached offline. It works with no network after this step.

## Usage

### Job flow
- Open app → tap **+ New Inspection**
- Enter Job ID (auto-generated from date), client name, address, inspector name
- Inspector name is saved for future jobs

### Capture flow
- Tap location header → select from list (or add custom)
- **PHOTO** button (or Bluetooth shutter) → live viewfinder → tap circle to capture
- **NOTE** button → type finding → mic button for voice-to-text → save
- Tap any note to edit
- Tap any photo thumbnail to expand

### Bluetooth shutter
- Settings (⚙) → Shutter Key Binding → tap "Press to learn key" → press shutter
- In capture mode: shutter fires camera capture
- In note modal: shutter saves the note
- On main screen: shutter opens camera

### S520 flagging
- Tap **⬤ S520** in note modal to manually flag
- Auto-detected from keywords: mold, fungal, rot, rotten, chaetomium, aspergillus, etc.
- Flagged notes appear with a red border in the timeline

### Export
- Tap ⇧ (top right) or Settings → Export JSON
- Exports a structured JSON file with all locations, photos (base64), notes, and S520 flags
- Filename: `{JobID}_{Client}.json`

## JSON Schema

```json
{
  "schema_version": "1.0",
  "job": {
    "id": "R031326A",
    "client": "Smith",
    "address": "123 Main St, City, VA",
    "inspector": "Eva M King",
    "inspection_date": "2026-03-13",
    "exported_at": "2026-03-13T..."
  },
  "locations": [
    {
      "sequence": 1,
      "name": "Basement — Utility / Mechanical Room",
      "entries": [
        {
          "id": "...",
          "type": "photo",
          "timestamp": "2026-03-13T10:23:11Z",
          "image_data": "data:image/jpeg;base64,...",
          "thumbnail": "data:image/jpeg;base64,..."
        },
        {
          "id": "...",
          "type": "note",
          "timestamp": "2026-03-13T10:24:05Z",
          "text": "Water damage and visible mold on subfloor above AHU.",
          "s520_flag": true
        }
      ]
    }
  ]
}
```

## Architecture Notes

- **Storage**: IndexedDB (`aura_field` database, v2) — all data stays on device
- **Photos**: Captured as JPEG via canvas from live `getUserMedia` stream
- **No network required** after initial cache (service worker, cache-first strategy)
- **S520 triggers**: mold, fungal, fungus, mould, moldy, growth, chaetomium, aspergillus, penicillium, stachybotrys, rot, rotted, rotten, remediat, condition 3, cat 3, category 3, iicrc, contamina, cladosporium
- **Bluetooth shutter**: listens for `keydown` event with configurable key binding
