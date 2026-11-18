# RPG Inventory Icons & Spritesheet Configurator

A lightweight, browser-based configurator for assembling 2D inventory spritesheets and multi-language item inspection configs for role-playing games (RPGs), powered by AI-generated medieval/fantasy item icons.

### 🚀 [Live Demo — Open Spritesheet Configurator](https://katzlbt.github.io/rpg-inventory-icons/www/spritesheet.html)

---

## ✨ Features & Capabilities

### 🎨 Interactive Spritesheet Canvas
- **Real-time Canvas Preview**: Live-rendered HTML5 canvas (`1024px` width, dynamic height scaled to row count) displaying selected inventory items on a checkerboard transparency grid.
- **Configurable Grid Layout**: Adjust columns dynamically from `1` to `8` (default `4`); rows and cell dimensions calculate automatically based on the number of selected items.
- **Aspect-Ratio Preserving Scaling**: Automatically centers and fits each icon cleanly into its grid cell.
- **Background Modes**: Switch seamlessly between a **Transparent** background or a custom **Solid Color** picker (`#000000` default) to match your game UI.
- **Multiple Export Formats**: Download the generated spritesheet directly from the browser as lossless **PNG** (`image/png`) or compressed **WebP** (`image/webp`).

### 🗡️ Icon Catalog & Selection
- **Curated RPG Asset Library**: Includes AI-generated medieval/fantasy items across multiple categories:
  - **Weapons & Gear**: Swords (rusty, polished, frost, fire), axes, maces, wooden & mithril bows, standard & flame arrows, shovels.
  - **Potions**: Vibrant alchemical vials (yellow, red, blue, green, orange, purple) with rich, humorous lore descriptions.
  - **Keys**: Rusty, polished, black metal, silver, golden, ornate filigree, wooden, and bone keys.
  - **Light & Valuables**: Torches (new, burning, burnt, brass), gold coins, and miscellaneous items.
- **Interactive Selection List**: Browse item thumbnails, internal item IDs (derived from filenames, e.g., `i_sword_fire`), and localized inspection text. Click any row to toggle inclusion, or use **Select All** / **Deselect All** for bulk changes.
- **Graceful Missing-Asset Handling**: Automatically detects and skips missing or failed image files without breaking the spritesheet layout or JSON generation.

### 🌐 Multi-Language Inspection & JSON Config Generator
- **Dynamic Language Switching**: Automatically discovers all language keys present in `www/icons/icons.json` (currently `en` and `de`) and updates item descriptions and output JSON on the fly.
- **Ready-to-Use Game Config Output**: Generates a structured `Inventory.config` JSON containing:
  - `spritesheet`: Target image filename (customizable via input, defaults to `inventory.png`), `rows`, `cols`, and ordered `items` ID array matching the grid cells.
  - `descriptions`: Key-value map of selected item IDs to their localized inspection texts for the active language.
- **One-Click Copy**: Copy the generated JSON configuration directly to your clipboard.

---

## 📋 Example Generated JSON

```json
{
  "spritesheet": {
    "image": "inventory.png",
    "rows": 1,
    "cols": 4,
    "items": [
      "i_gold",
      "i_sword_ice",
      "i_potion_red",
      "i_key_golden"
    ]
  },
  "descriptions": {
    "i_gold": "A small, glittering pile of valuable gold coins.",
    "i_sword_ice": "A magical frost sword with a translucent icy blue crystal blade, glowing frost runes, and cold mist radiating from the edge.",
    "i_potion_red": "A thick crimson syrup that gently boils whenever you break eye contact with it. It smells of cinnamon and bad decisions, and while it heals wounds instantly, your inner monologue will temporarily be broadcast via a ghostly tuba.",
    "i_key_golden": "Heavy for its size, the key gleams with a warm, opulent luster across its sun-motif bow, braided shaft, and symmetrical, crown-like bit. Though it looks fit for a royal treasury, the faint hum vibrating through the metal hints at a forgotten vault where something far more dangerous than wealth is kept."
  }
}
```

---

## 🛠️ Adding Custom Icons & Translations

1. Place your icon image (e.g., `i_shield_oak.png`) inside [`www/icons/`](www/icons/).
2. Add an entry to [`www/icons/icons.json`](www/icons/icons.json) with translations for each supported language code:
   ```json
   "i_shield_oak.png": {
     "en": "A sturdy oaken shield reinforced with iron bands.",
     "de": "Ein robuster Eichenschild, verstärkt mit Eisenbändern."
   }
   ```
   *(Any new language code added to `icons.json` will automatically appear in the configurator's **Language** dropdown.)*

---

## 💻 Local Usage

Because the configurator loads [`www/icons/icons.json`](www/icons/icons.json) via `fetch()` and exports canvas image data, serve the repository over a local HTTP server rather than opening the HTML file via `file://`:

```bash
# Run from the repository root
python3 -m http.server 8000
```

Then open `http://localhost:8000/www/spritesheet.html` in your browser, or use the hosted [GitHub Pages Demo](https://katzlbt.github.io/rpg-inventory-icons/www/spritesheet.html).

---

## 🤖 AI Asset Generation & Tech Notes

- **Icon Generation**: Icons and decriptions were generated locally via Google Nano Banana 2.1, Google Gemini 4 Hydrogen and ComfyUI / Qwen-image-2.1 using reference-image workflows to get transparency.
- **Roadmap / TODO**: Add generalized scripts to automate generating icons and multi-language item descriptions using AI.
- **Implementation**: Built as a self-contained single-page application in [`www/spritesheet.html`](www/spritesheet.html) using AngularJS (`angular.min.js`) and Basscss—implemented by Gemini 4 Carbon in 54 seconds (after 2 hours of specification).

## 📄 License

Licensed under the [MIT License](LICENSE).
