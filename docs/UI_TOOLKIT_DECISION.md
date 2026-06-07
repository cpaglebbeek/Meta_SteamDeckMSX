# UI Toolkit Decision — Qt6 (lock-in)

> Status: **BESLOTEN v0.0.2-Nemesis (2026-06-07)**.
> Eerder open vraag in `SteamDeckMSX_Native/ARCHITECTURE.md` (open vraag #1).
> Geldt voor variant 1 (Native) — variant 2 client (Stream_Client) leunt op upstream Moonlight-Qt en is hiermee impliciet aligned.

## Samenvatting

**Qt6 / Qt Quick (QML)** wordt de UI-toolkit voor `SteamDeckMSX_Native`. GTK4 valt af.

Reden in één zin: Native v0.0.3..v0.0.6 zijn al gebouwd in Qt6/QML (12 QML-files, Tokens-singleton, `qt_add_qml_module`, 17/17 tests groen) — een switch naar GTK4 is per `CLAUDE.md` kleurcode-protocol een **rode wijziging (+1.0.0)** en is op dit punt niet meer kosten-baten-positief.

## Optie-vergelijking (volledigheidshalve)

| Aspect | Qt6 / QML | GTK4 |
|---|---|---|
| **Steam Deck runtime (Flathub)** | `org.kde.Platform` 6.x — beschikbaar, bewezen voor Plasma / Moonlight-Qt | `org.gnome.Platform` 4x — beschikbaar, bewezen voor GTK4-apps |
| **Bestaande code in Native (v0.0.6)** | **12 QML-files, Tokens-singleton, modulaire RESOURCES via `qt_add_qml_module`, tests draaien al offscreen** | nul regels |
| **Gamepad-input (Steam Input passthrough)** | `Keys`-attached + `Shortcut` werken; SDL2-gamepad via `QGamepad` is verlaten, maar Steam Input genereert KB-events die QML opvangt | GTK4 `EventControllerKey` + Steam Input KB-events idem werken |
| **GPU-render-stack** | RHI (Vulkan/OpenGL/Metal) abstractie, OOM-prio sinds Qt 6.5; SDL-canvas via `QQuickRhiItem` mogelijk voor openMSX-render-doorgift | GTK4 GL-renderer (GSK); embedded SDL via `GtkGLArea` mogelijk maar bewerkelijker |
| **License compatibiliteit (AGPL-3.0 app)** | LGPL-3.0 / GPL-3.0 dual — dynamisch linken vrij; AGPL-3.0 als app-licentie OK | LGPL-2.1+ — AGPL-3.0 OK |
| **Designer-tooling** | Qt Design Studio, Figma-export naar QML — productief voor wireframe → screen mapping | Cambalache / Glade-opvolger — minder rijp |
| **Documentatie (NL community)** | groter Qt-publiek; KDE-Plasma docs | GNOME-docs, kleinere overlap met gaming-UI |
| **MSX-emulator-context** | openMSX zelf gebruikt SDL2 (geen UI-toolkit); UI is dus volledig vrij | idem |
| **Theming naar MSX-CRT-revival** (DESIGN_TOKENS) | QML-theming via Tokens-singleton al ingebouwd — `currentColor` SVG-render werkt | GTK CSS-theming werkt, maar CSS-vars iets minder direct dan QML-property-binding |
| **Footprint Flatpak** | ~80 MB extra (KDE-Platform overlap) | ~70 MB extra (GNOME-Platform overlap) — verwaarloosbaar verschil |
| **Switch-kosten v0.0.7** | nul (we blijven) | **rood** — 100% rewrite UI-laag, 17 tests opnieuw schrijven, Tokens-laag opnieuw bouwen, ~3-5 sessies werk |

## Beslissende argumenten

1. **Sunk cost is niet alleen sunk** — Tokens-singleton, SaveStateModel-binding, `qt_add_qml_module` RESOURCES-flow en 17/17 tests zijn productieve assets, geen vervangbare boilerplate.
2. **MSX-CRT-revival theming** zit verweven in QML-property-bindings (Tokens.colorBgBase enz.) — niet 1-op-1 over te zetten naar GTK4-CSS zonder semantisch verlies.
3. **Steam Deck Plasma-toekomst** — Valve heeft Plasma-Bigscreen niet aangekondigd voor Gaming Mode, maar de richting binnen SteamOS 3.x volgt KDE-stack. Qt-runtime is daarmee niet weg-te-investeren.
4. **Geen blokkerend GTK4-voordeel** — geen feature die we nodig hebben (libadwaita-styling? niet relevant voor on-couch gaming-UI).
5. **Kleurcode-protocol** — switch = rood, en we hebben geen rood-rechtvaardigende driver (geen architectuur-fout, geen license-conflict, geen capability-gap).

## Geweigerde alternatieven

- **Geen UI (alleen openMSX upstream)** — verwerpt het bestaansrecht van het project (Steam Deck-eerste UI is de hele scope).
- **Web-UI in WebView** — extra runtime, slechter Gamepad-IME-pad, AGPL-positie minder schoon (chromium-fork-overhead).
- **Toolkit-loze SDL2 UI bouwen** — herontwikkelen wat Qt al kan; 6 maanden verlies.

## Wat dit besluit niet vastlegt

- **Qt-versie-pinnen** — Qt 6.11 (huidige Mac dev-versie) is de huidige floor; Flathub `org.kde.Platform//6.7+` is target. Update-policy = open vraag v0.0.3.
- **QML-module-URI** — blijft `SteamDeckMSX` (geen ecosysteem-prefix).
- **openMSX-render-doorgift** — open vraag v0.0.8 (`QQuickRhiItem` vs externe SDL-window). Niet door dit besluit bepaald.

## Sub-repo impact

| Repo | Wijziging na dit besluit |
|---|---|
| `SteamDeckMSX_Native` | `ARCHITECTURE.md` open vraag #1 verplaatsen naar "BESLOTEN — zie Meta `docs/UI_TOOLKIT_DECISION.md`" |
| `SteamDeckMSX_Stream_Server` | geen — server-side gebruikt geen UI-toolkit |
| `SteamDeckMSX_Stream_Client` | geen — leunt op upstream Moonlight-Qt (al Qt6) — bekrachtigt impliciet de keuze |
| `Meta_SteamDeckMSX` | dit document is de bron van waarheid |

## Heroverweging-trigger

Heroverweging staat open als één van deze gebeurtenissen optreedt:
- Valve kondigt aan dat Gaming Mode op GTK-stack switcht (~10 jaar horizon onwaarschijnlijk).
- Qt6 licentie-model verandert in AGPL-onverenigbare richting.
- KDE-Platform Flathub-runtime droogvalt (huidige update-cadans is gezond).

In alle andere gevallen: **niet heroverwegen vóór v0.5.0**.
