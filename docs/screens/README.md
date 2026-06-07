# Schermontwerpen — SteamDeckMSX

Wireframes en flow-schetsen per scherm. Onderscheid:
- **Native variant** — `native-*.md`
- **Stream variant** — `stream-*.md`
- **Gedeeld** — `shared-*.md`

## v0.0.2-Nemesis (2026-06-07) — ASCII-wireframes Native

ASCII-wireframes voor de 3 belangrijkste Native-schermen. Visuele Excalidraw/Figma-mockups verschoven naar v0.0.3 (zie open vraag onderaan).

### Native — ASCII-wireframes ✅
- [01_library.md](./01_library.md) — CartridgeBrowser (3×2 tile-grid, topbar tabs, hintbar)
- [02_running.md](./02_running.md) — Emulator playback + pauze-overlay (Select-knop)
- [03_savestate.md](./03_savestate.md) — SaveStateOverlay 5×2 slot-grid (X-knop opent)

### Native — gepland v0.0.3+
- `04_settings.md` — Machine-type, BIOS-pad, audio-volume, input-mapping
- `05_addrom.md` — SAF-picker flow + ROM-validatie + machine-detect-modal

### Stream — gepland v0.0.3 (Stream_Client v0.0.3 launcher)
- `stream-01-pairing.md` — Eerste keer: PIN van HC55-web-UI in Moonlight invoeren
- `stream-02-app-list.md` — Moonlight toont "MSX-stream" als app
- `stream-03-in-stream.md` — Sunshine OSD bovenop openMSX render

### Gedeeld — gepland v0.0.3
- `shared-controller-mapping.md` — Steam Input preset overzicht (kruis-referentie naar `Stream_Client/presets/msx-gamepad.vdf`)

## Conventies

- Wireframes in tekst (ASCII-art) + bijschrift met focus-orde + gamepad-mapping-tabel + open vragen
- Steam Deck schermresolutie: **1280×800** (LCD/OLED) — alle UI op dit raster
- Veilige marge: 24 px aan elke kant (overscan-vrij)
- Minimum interactief element: 64×64 px (gamepad-vingervriendelijk)
- Token-koppeling expliciet (verwijst naar `SteamDeckMSX_Native/DESIGN_TOKENS.md`)
- Iedere wireframe heeft sectie "Gamepad-mapping" + "Open vragen → vN.N.N ontwerp"

## Visuele mockup-tooling — open vraag v0.0.3

Beslis tussen Excalidraw (markdown-compatible export, snelle iteratie) en Figma (rijker, exporteerbaar naar Qt Design Studio QML). Voorlopig advies: **Excalidraw** — ASCII-wireframes hier zijn al voldoende voor v0.0.2 (Qt6-besloten, codenamen toegewezen).
