# AI Agent Onboarding (`0_START.md`)

> **Target Audience**: AI coding agents working in this repository. Read this first to understand the layout, asset manifest format, and how the configurator app operates.

## 1. Project Overview

This repository hosts curated RPG item icons and a lightweight, static web configurator (`www/spritesheet.html`) for building 2D inventory spritesheets (PNG/WebP) paired with localized `Inventory.config` JSON payloads for games (e.g., Locandy).

There is no build step, bundler, or Node/npm dependency tree. Everything under `www/` is served directly as static files (e.g., `python3 -m http.server 8000` or GitHub Pages).

---

## 2. Directory Structure

```text
rpg-inventory-icons/
├── docs/
│   └── 0_START.md          # AI agent onboarding & architecture reference (this file)
├── www/                    # Static web root
│   ├── spritesheet.html    # Self-contained SPA configurator (HTML + CSS + AngularJS 1.x)
│   ├── angular.min.js      # Local AngularJS 1.x runtime
│   ├── icons/              # Medieval/fantasy RPG icon pack
│   │   ├── icons.json      # Manifest & localized descriptions for `icons/`
│   │   └── i_*.png         # Transparent item icon PNGs
│   └── stoneage/           # Stone-age / prehistoric icon pack
│       ├── icons.json      # Manifest & localized descriptions for `stoneage/`
│       └── i_*.png         # Transparent item icon PNGs
├── bin/                    # Reserved for utility/automation scripts
├── originals.nogit/        # Raw/unprocessed AI generations & reference images (git-ignored via `*.nogit`)
├── .gitignore              # Ignores `*.nogit`
├── README.md               # Human-facing project overview and usage instructions
└── LICENSE                 # MIT License
```

### Naming Conventions
- Icon image files use the `i_<name>.png` prefix convention (e.g., `i_sword_fire.png`, `i_flint.png`).
- Any file or directory ending in `.nogit` is ignored by git (`*.nogit` in `.gitignore`). Keep raw source images or scratch assets in `*.nogit` directories unless asked otherwise.

---

## 3. `icons.json` Format (Input Manifest)

Each asset folder under `www/` (such as `www/icons/` or `www/stoneage/`) exposes an `icons.json` manifest at `<folder>/icons.json`.

### Schema
The root value is a JSON object mapping **image filenames** (including extension, relative to that folder) to a **translations object** keyed by ISO language code (e.g., `"en"`, `"de"`):

```json
{
  "i_gold.png": {
    "en": "A small, glittering pile of valuable gold coins.",
    "de": "Ein kleiner, glitzernder Haufen wertvoller Goldmünzen."
  },
  "i_sword_new.png": {
    "en": "A new polished iron sword fresh from the blacksmith. It has never been used.",
    "de": "tbd"
  }
}
```

### Key Rules & Behaviors
- **Order matters**: `Object.keys(data)` determines the display order in the UI list and the left-to-right, top-to-bottom cell order in the generated spritesheet when items are selected.
- **Item ID derivation**: `spritesheet.html` strips the file extension (`filename.replace(/\.[^/.]+$/, "")`) to produce the internal item ID (e.g., `"i_gold.png"` -> `"i_gold"`).
- **Dynamic language discovery**: All language keys present across entries in the loaded `icons.json` are collected into a `Set` to populate the UI **Language** dropdown automatically. Missing translations fall back to `""` (or placeholder strings like `"tbd"` if written in JSON).
- **Adding a new icon**: Place `i_<item>.png` in `www/<folder>/` and add its filename key + language descriptions to `www/<folder>/icons.json`. Files present on disk but omitted from `icons.json` will not appear in the UI; entries in `icons.json` whose image fails to load will still appear in the list (with an empty thumbnail/cell) without crashing the app.

---

## 4. Overview of `www/spritesheet.html`

`www/spritesheet.html` is a single-file AngularJS (`myApp`, controller `Main as m`) application styled with Basscss (via CDN) and inline CSS.

### UI Sections
1. **Top Panel — Spritesheet Preview & Controls**:
   - **Folder (`selectedFolder`)**: Fixed `<select>` in HTML switching between asset folders (currently `icons` and `stoneage`). Changing this triggers `m.loadIcons()`. Adding a new icon pack folder requires adding an `<option>` here.
   - **Columns (`cols`)**: Numeric input clamped to `1..8` (default `6`).
   - **Language (`selectedLang`)**: Dropdown populated from language codes discovered in `<selectedFolder>/icons.json` (defaults to `"en"`).
   - **Background (`bgMode`, `bgColor`)**: Toggles canvas background between `"transparent"` and `"color"` (with color picker, default `#000000`).
   - **Export Buttons**: Downloads `<spritesheetCanvas>` via `canvas.toDataURL()` as `.png` (`image/png`) or `.webp` (`image/webp`, quality `0.92`).
   - **Canvas (`#spritesheetCanvas`)**: Fixed width of `1024px`. Cell size is `1024 / cols`, `rows = Math.max(1, Math.ceil(checked.length / cols))`, and `canvasHeight = Math.round(rows * cellSize)`.
2. **Middle Panel — Icons Checkbox List**:
   - Lists all items from `iconModels` with thumbnail, derived `id`, and description for `selectedLang`.
   - Supports individual toggle as well as **Select All** / **Deselect All** (`m.toggleAll(true/false)`).
3. **Bottom Panel — Generated `Inventory.config` JSON**:
   - Editable `spritesheet.image` filename field (`imageName`, default `"inventory.png"`), **Copy JSON** button, and read-only `<textarea>` showing the serialized config.

### Core Data Flow (`Main` class)
1. **`loadIcons()`**:
   - Resets `iconModels` and `loadError`, then `fetch()`es `<selectedFolder>/icons.json` (must be served over HTTP/HTTPS, not `file://`).
   - Guards against stale async responses if the user switches folders mid-fetch.
   - Builds `iconModels` and preloads each `Image` (`<selectedFolder>/<filename>`). When an image finishes loading (and its model is checked), `updateAll()` re-renders the canvas.
2. **`updateAll()`**:
   - Clamps `cols` (`1..8`), filters `checked` models, computes `rows`, `cellSize`, and `canvasHeight`, then calls `renderSpritesheet()` and `generateJson()`.
3. **`renderSpritesheet()`**:
   - Clears the 2D canvas, optionally fills `bgColor`, and draws each checked, loaded image centered within its `(col, row)` grid cell while preserving aspect ratio (`Math.min(cellSize / iw, cellSize / ih)`).
4. **`generateJson()`**:
   - Produces the game configuration JSON for checked items in the active language:
     ```json
     {
       "spritesheet": {
         "image": "inventory.png",
         "rows": 1,
         "cols": 6,
         "items": ["i_gold", "i_shovel"]
       },
       "descriptions": {
         "i_gold": "A small, glittering pile of valuable gold coins.",
         "i_shovel": "A rusty iron shovel featuring a worn wooden shaft."
       }
     }
     ```
