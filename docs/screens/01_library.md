# Scherm 01 — Library (CartridgeBrowser)

> ASCII-wireframe v0.0.2-Nemesis. Visueel uitwerken in Excalidraw → v0.0.3.
> Doel-raster: **1280 × 800**, safe-zone 24 px, min-element 64 × 64.

## Doel

Bibliotheek van geladen ROMs/cartridges + één "+ ROM toevoegen"-tile. Default-scherm na app-start. D-pad navigeert tussen tiles; A start emulator.

## ASCII-wireframe (landscape, 1280 × 800)

```
+------------------------------------------------------------------------+
|  [SteamDeckMSX]   Library      Streams      Settings        v0.0.7  X  |  <- 64px topbar
|  ====================================                                  |     focus-ring 4px
+------------------------------------------------------------------------+
|                                                                        |
|  +----------------+  +----------------+  +----------------+            |
|  |  [thumb 320x   |  |  [thumb 320x   |  |  [thumb 320x   |            |
|  |   180]         |  |   180]         |  |   180]         |            |
|  |                |  |                |  |                |            |
|  |  Bubble Bobble |  |  Metal Gear    |  |  Nemesis 2     |            |
|  |  MSX1·Konami   |  |  MSX2·Konami   |  |  MSX2·Konami   |            |
|  |  ROM 32KB ASCII|  |  ROM 128KB SCC |  |  ROM 256KB SCC |            |
|  +----------------+  +----------------+  +----------------+            |
|                                                                        |
|  +----------------+  +----------------+  +----------------+            |
|  |  [+]           |  |  [thumb]       |  |  [thumb]       |            |
|  |                |  |                |  |                |            |
|  |  ROM toevoegen |  |  Castlevania   |  |  Salamander    |            |
|  |  · SAF-picker  |  |  MSX2·Konami   |  |  MSX1·Konami   |            |
|  +----------------+  +----------------+  +----------------+            |
|                                                                        |
+------------------------------------------------------------------------+
|  [L1] vorige tab    [R1] volgende tab    [A] starten    [Y] verwijder  |  <- 64px hintbar
+------------------------------------------------------------------------+
```

## Componenten op dit scherm

| Component | Token-koppeling | Gedrag |
|---|---|---|
| Topbar (titel + tabstrip + versie) | `bgElevated`, `space5` padding, `fontSizeLabel` versie | Tab actief = `accentPrimary` 4px onder-border |
| CartridgeCard (tile) | `bgElevated`, `borderSubtle` 1px; focus → `borderStrong` 4px `accentPrimary` | A = start; Y = delete-confirm |
| Thumb-area | 320×180 (16:9), placeholder = `bgBase` met SVG cartridge-icon | Toekomst: openMSX framebuffer-snapshot bij eerste boot |
| AddRomCard | bgElevated, `+` symbool gecentreerd, accentInfo border | A = SAF-picker uit ROMs-folder |
| Hintbar | `bgElevated`, 64 px hoog, knop-iconen 32×32 + label `fontSizeLabel` | Toont actuele context-knoppen |

## Gamepad-mapping (v0.0.7 — na bumper/trigger iconen)

| Knop | Actie | Icoon-token |
|---|---|---|
| D-pad ↑↓←→ | Navigeer tussen tiles | `iconDpad{Up,Down,Left,Right}` |
| A | Start emulator (focus = cartridge) of SAF-picker (focus = AddRomCard) | `iconBtnA` |
| B | Geen actie hier (al op root-scherm; eventueel "Sluit app"-confirm) | `iconBtnB` |
| Y | Delete cartridge (met confirm-toast) | `iconBtnY` |
| X | Save-state overlay (alleen relevant tijdens Running, hier no-op) | `iconBtnX` |
| L1 / R1 | Vorige / volgende tab in topbar (Library ↔ Streams ↔ Settings) | `iconBumperL1` / `iconBumperR1` |
| L2 / R2 | Snelle scroll (paginas tegelijk in lange lijsten) | `iconTriggerL2` / `iconTriggerR2` |
| Steam-knop | Steam overlay (system) | — |

## Layout-grid

- **Tile-rij**: 3 tiles per rij op 1280 px breed.
  - Tile-breedte: `(1280 - 2*24 safeMargin - 2*24 gap) / 3 = 394.6 → 392 px`.
  - Tile-hoogte: thumb 220 + titel/sub ~80 + padding 24 ≈ 324 px.
- **2 rijen visueel zichtbaar**; daarboven scrollt.
- **Focus-pad**: D-pad rechts vanaf laatste tile in rij → wrap niet, blijft hangen (geen surprise-jump).

## Open vragen → v0.0.3 ontwerp

1. Zijn tabs (Library/Streams/Settings) in topbar **OF** in side-rail à la Steam Big Picture?
   - Steam-Deck-conventie zou side-rail kunnen — beoordeel bij Excalidraw-pass.
2. Toon **filter/zoek** rij boven tiles, of pas in v0.1.0 bij >50 ROMs?
3. Hoe groot is "thumb"? 320×180 (16:9) of 256×192 (MSX-aspect)? Beslissing bij eerste framebuffer-snapshot v0.0.8.

## Cross-ref

- `SteamDeckMSX_Native/src/qml/CartridgeBrowser.qml` (huidige impl, geen tabs nog)
- `SteamDeckMSX_Native/src/qml/CartridgeCard.qml`
- `SteamDeckMSX_Native/DESIGN_TOKENS.md` § Iconen v0.0.7
