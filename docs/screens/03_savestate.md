# Scherm 03 — SaveStateOverlay (5×2 slot-grid)

> ASCII-wireframe v0.0.2-Nemesis. Modaal Popup over Running-scherm, X-knop opent. 10 slots conform MSX-traditie (save 0..9).

## Doel

Snel-toegankelijke save/load tijdens spel. Geen genest menu — één grid, één X-toets om te openen, A om te save/load (afhankelijk van occupied-status van het slot).

## ASCII-wireframe (1280 × 800, modaal)

```
+------------------------------------------------------------------------+
|  [openMSX render-output gedimd met bgOverlay rgba 0.85]                |
|                                                                        |
|      +-------------------------------------------------------+         |
|      |  SAVE STATES — Bubble Bobble                          |         |
|      +-------------------------------------------------------+         |
|      |                                                       |         |
|      |  +-------+  +-------+  +-------+  +-------+  +------+ |         |
|      |  |  0    |  |  1    |  |  2    |  |  3    |  | 4    | |         |
|      |  | bb-1  |  | empty |  | bb-3  |  | empty |  |empty | |         |
|      |  |12:34  |  |       |  |16:02  |  |       |  |      | |         |
|      |  +-------+  +-------+  +-------+  +-------+  +------+ |         |
|      |                                                       |         |
|      |  +-------+  +-------+  +-------+  +-------+  +------+ |         |
|      |  |  5    |  |  6    |  |  7    |  |  8    |  | 9    | |         |
|      |  | bb-5  |  | empty |  | empty |  | bb-8  |  |empty | |         |
|      |  |21:11  |  |       |  |       |  |18:45  |  |      | |         |
|      |  +-------+  +-------+  +-------+  +-------+  +------+ |         |
|      |                                                       |         |
|      +-------------------------------------------------------+         |
|                                                                        |
+------------------------------------------------------------------------+
|  [A] Save/Load   [Y] Wis slot   [B/Esc] Sluit                          |
+------------------------------------------------------------------------+
```

## Slot-tile-design (SaveStateCard.qml)

```
+----------------------+
|     0                |   <- nummer rechts-bovenin, fontSizeDisplay
|                      |
| bb-bubblebobble-1    |   <- rom-stem, fontSizeBody, ellipsize
|                      |
|     2026-06-07 12:34 |   <- timestamp, fontSizeLabel
+----------------------+
```

**Bezet (occupied):**
- `bgElevated` background
- naam + timestamp zichtbaar
- focus = `borderStrong` 4px `accentPrimary`
- A = LOAD, Y = CLEAR

**Leeg (empty):**
- `bgBase` background (subtieler — visueel verschil meteen zichtbaar)
- "empty" placeholder in `fgDisabled`
- focus = idem
- A = SAVE, Y = no-op (toast "Slot is al leeg")

## Componenten

| Component | Source-file | Token-koppeling |
|---|---|---|
| SaveStateOverlay (Popup) | `src/qml/SaveStateOverlay.qml` | `bgOverlay`, modal=true, focus=true |
| SaveStateCard (tile) | `src/qml/SaveStateCard.qml` | conditioneel `bgElevated`/`bgBase`, focus-border `accentPrimary` |
| GridView | inline in SaveStateOverlay | cellWidth 220, cellHeight 140, rows=2, columns=5 |
| Hintbar | onderaan in Popup | `bgElevated`, knop-iconen 32×32 |

## Gamepad-mapping

| Knop | Actie | Icoon-token |
|---|---|---|
| D-pad ↑↓←→ | Navigeer tussen 10 slots (geen wrap) | `iconDpad{Up,Down,Left,Right}` |
| A | Save (als empty) of Load (als occupied) | `iconBtnA` |
| Y | Clear slot (alleen als occupied; confirm via toast) | `iconBtnY` |
| B / Esc | Sluit overlay zonder actie | `iconBtnB` |
| X | Sluit overlay (X is de open-knop — herhaalde druk = sluit) | `iconBtnX` |
| L1 / R1 | (toekomst v0.1.0) vorige/volgende slot-pagina als >10 slots | `iconBumperL1`/`R1` |
| L2 / R2 | (toekomst v0.1.0) wissel tussen "current ROM" en "alle ROMs"-view | `iconTriggerL2`/`R2` |

## Data-flow

```
User press X (Main.qml shortcut)
   │
   └─> SaveStateOverlay.open()
          │
          └─> binds to SaveStateModel (id: saves)
                 │
                 └─> A-press on tile N:
                       ├─> if !occupied(N): saves.saveTo(N, currentRomStem)
                       │      ├─> stage save in model (always)
                       │      └─> if core attached + Running: msxCore.savestate("slot_N_<stem>")
                       │
                       └─> if occupied(N): saves.loadFrom(N)
                              ├─> resolve name from model
                              └─> if core attached: msxCore.loadstate(name)
```

(Volledige "stage save"-rationale: zie `v0.0.6-PenguinAdventure` CHANGELOG-entry in Native.)

## QSettings-persistentie

Per slot:
```ini
[SaveState]
slot_0/occupied=true
slot_0/rom=bubblebobble.rom
slot_0/name=slot_0_bubblebobble
slot_0/timestamp=2026-06-07T12:34:00Z
```

**v0.0.6 limitatie:** geen FS-verify van actuele openMSX-state-file in `~/.var/app/.../states/`. Model trusts QSettings; FS-check verschuift naar v0.0.8 (Flatpak-pad-onbekend-fix).

## Open vragen → v0.1.0 ontwerp

1. Meer dan 10 slots? Pagina-systeem of scroll? L1/R1 = pagina-wisseling.
2. Thumbnails per slot? Vereist openMSX framebuffer-XML-extract + base64 → QImage. Verschoven naar v0.0.8.
3. Naming-conflict bij meerdere SAVE-acties op zelfde slot? Voorstel: overschrijven met confirm-toast.

## Cross-ref

- `SteamDeckMSX_Native/src/qml/SaveStateOverlay.qml`
- `SteamDeckMSX_Native/src/qml/SaveStateCard.qml`
- `SteamDeckMSX_Native/src/model/SaveStateModel.{h,cpp}`
- `SteamDeckMSX_Native/CHANGELOG.md` § v0.0.6-PenguinAdventure ("stage save" pattern)
