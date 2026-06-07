# Scherm 02 — Running (Emulator playback)

> ASCII-wireframe v0.0.2-Nemesis. Doel-raster 1280 × 800, OSD verschijnt op `Steam`-knop of pauzeren.

## Doel

Full-screen openMSX-render. OSD/HUD verschijnt op pauze (Steam-knop simuleert dit niet — apart Select-binding) of bij save-state-overlay (X-knop).

## ASCII-wireframe — actief spelen (geen overlay)

```
+------------------------------------------------------------------------+
|                                                                        |
|                                                                        |
|                                                                        |
|                                                                        |
|                                                                        |
|                  [ openMSX render-output ]                             |
|                  [ 1280 × 800 letterbox / pillarbox ]                  |
|                  [ MSX native = 256×192 / 512×212 ]                    |
|                  [ scale-to-fit 4:3, zwarte randen links/rechts ]      |
|                                                                        |
|                                                                        |
|                                                                        |
|                                                                        |
+------------------------------------------------------------------------+
```

**Tijdens-spel-OSD:** alleen tijdelijk fade-in/out toast bij save/load gebeurtenis (zie `Toast.qml`).

## ASCII-wireframe — pauze-overlay (Select/Menu-knop)

```
+------------------------------------------------------------------------+
|  [SteamDeckMSX]  Bubble Bobble · MSX1                       v0.0.7    |
+------------------------------------------------------------------------+
|                                                                        |
|        +---------------------------------------------------+           |
|        |                                                   |           |
|        |   PAUZE                                           |           |
|        |                                                   |           |
|        |   +------------------+   +------------------+     |           |
|        |   |   [A] Hervat      |   |   [Y] Stop      |     |           |
|        |   +------------------+   +------------------+     |           |
|        |                                                   |           |
|        |   +------------------+   +------------------+     |           |
|        |   |   [X] Saves       |   |   [B] Settings  |     |           |
|        |   +------------------+   +------------------+     |           |
|        |                                                   |           |
|        +---------------------------------------------------+           |
|                                                                        |
+------------------------------------------------------------------------+
|  [A] Hervat    [Y] Stop    [X] Saves    [B] Settings                   |
+------------------------------------------------------------------------+
```

## Componenten

| Component | Token-koppeling | Gedrag |
|---|---|---|
| Render-canvas | full-window; `bgBase` als letterbox-fill | openMSX SDL2 output via Qt6 RHI (open vraag v0.0.8) |
| Pauze-overlay | `bgOverlay` (rgba 0.85), modaal Popup | Verschijnt op Select-knop |
| Quick-knop-tile (256×96) | `bgElevated`, focus → `accentPrimary` 4px border | A-knop op tile = actie |
| Toast (save/load feedback) | bottom-bar, `accentWarm` voor save, `accentInfo` voor load | Auto-dismiss 1.6s (zie `motionSlow`) |

## Gamepad-mapping tijdens Running

| Knop | Actie | Doel-state |
|---|---|---|
| D-pad / linker-stick | openMSX-joystick passthrough | In-game |
| A / B | openMSX-fire-buttons (mapping per cartridge in `msx-gamepad.vdf`) | In-game |
| Select / Menu | Pauze-overlay tonen | OSD |
| X | Save-state overlay (SaveStateOverlay.qml, GridView 5×2) | OSD |
| Y | Niet gebonden tijdens game (overlay-only) | — |
| L1 / R1 | Vorige / volgende save-state-pagina (10 slots verdeeld over 1 pagina nu — toekomstig 30 slots = 3 pagina's) | OSD (SaveStateOverlay) |
| L2 / R2 | Snel-vooruit / slow-motion toggle (openMSX `throttle` / `set fastforward 100` Tcl-cmd) | In-game |
| Steam-knop | Steam-overlay (system) | — |

## Open vragen → v0.0.8 ontwerp

1. **Render-doorgift Qt ↔ openMSX**: `QQuickRhiItem`, externe SDL-window, of openMSX in-process? Beslissing nodig vóór v0.0.8 thumbnails-werk.
2. **Audio-route**: PipeWire direct of via Qt6 `QMediaDevices`? Latentie-budget 60-80 ms eindtotaal.
3. **L2/R2 = snelvooruit OF snelle save-state-pagina-scroll?** Conflict met save-state overlay. Voorstel: L2/R2 = snelvooruit in-game, L1/R1 = save-state-pagina in overlay (geen overlap).

## Cross-ref

- `SteamDeckMSX_Native/src/qml/Main.qml` (state-machine: Idle/Running/Paused)
- `SteamDeckMSX_Native/src/qml/SaveStateOverlay.qml` (X-knop overlay)
- `SteamDeckMSX_Stream_Client/presets/msx-gamepad.vdf` (Steam Input fire-button mapping)
