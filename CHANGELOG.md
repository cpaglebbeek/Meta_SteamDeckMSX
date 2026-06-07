# CHANGELOG — Meta_SteamDeckMSX

## v0.0.2-Nemesis (2026-06-07) — Qt6 decision + ASCII-wireframes (oranje)

### UI Toolkit Decision — Qt6 lock-in
- `docs/UI_TOOLKIT_DECISION.md` (~120 regels) — open vraag #1 uit Native ARCHITECTURE
  gesloten: **Qt6/QML lock-in**, GTK4 valt af.
- Reden in één regel: Native v0.0.3..v0.0.6 zit al volledig in Qt6/QML (12 QML-files,
  Tokens-singleton, 17 tests groen). Switch = rood (+1.0.0), geen rood-rechtvaardigende
  driver, ~3-5 sessies switch-kosten.
- Heroverweging-trigger expliciet gedocumenteerd (Valve GTK-switch / Qt6 license /
  KDE Platform droogval).
- Sub-repo impact: Native ARCHITECTURE.md open vraag #1 verplaatst naar "BESLOTEN".

### ASCII-wireframes — 3 Native-schermen
- `docs/screens/01_library.md` — CartridgeBrowser 3×2 tiles + topbar tabs + hintbar
- `docs/screens/02_running.md` — Full-screen render + pauze-overlay (Select-knop) + toast
- `docs/screens/03_savestate.md` — SaveStateOverlay 5×2 slot-grid + slot-tile-design
- `docs/screens/README.md` herschreven: nieuwe planning (gepland v0.0.3 settings/addrom/stream-screens)
- Iedere wireframe heeft Gamepad-mapping-tabel + Open-vragen-sectie + Cross-refs naar Native QML-files

### Gamepad-mapping — incl. nieuwe bumper/trigger iconen (Native v0.0.7)
- Library: L1/R1 = tab-wisseling, L2/R2 = snelle scroll
- Running: L2/R2 = snel-vooruit / slow-motion toggle (openMSX Tcl-cmd)
- SaveState: L1/R1 = pagina-wisseling (v0.1.0), L2/R2 = view-toggle (v0.1.0)

### Codenaam-pool — toewijzingen rechtgezet
- Toegewezen-tabel uitgebreid met retroactieve entries: Native v0.0.4 Aleste,
  v0.0.5 Solid Snake, v0.0.6 Penguin Adventure (waren wel gebruikt maar niet
  in master-tabel geregistreerd → drift-risico verholpen).
- Nieuwe toewijzingen v0.0.2/v0.0.7:
  - Meta v0.0.2 = **Nemesis** (Konami MSX1 1985 shoot-em-up — "richting bepaald")
  - Native v0.0.7 = **Castlevania** (Konami MSX2 1986 "Akumajou Dracula")
  - Stream_Server v0.0.2 = **Parodius** (Konami MSX2 1988 — vervolg van Gradius — past bij streaming-server-rol; oorspronkelijk Aleste gepland maar conflict met Native v0.0.4)
  - Stream_Client v0.0.2 = **Salamander** (Konami MSX 1986 vervolg op Gradius — koppeling Client↔Server-paar)
- Pool-verkleining: 7 namen verschoven uit pool naar toegewezen.

### Mockup-tooling — voorlopig advies
- Excalidraw (markdown-export, snelle iteratie) voor v0.0.3 visuele wireframes.
- Definitieve keuze v0.0.3.

### Wat NIET in deze release
- Geen visuele mockups (alleen ASCII) — verschuift naar v0.0.3 met tooling-keuze.
- Geen Stream-variant screens (pairing/app-list/in-stream) — verschuift naar v0.0.3 samen met Stream_Client v0.0.3 launcher.
- Geen Settings/AddRom wireframes — verschuift naar v0.0.3.

### Kleurcode: ORANJE (+0.1.0)
Architectuur-besluit (UI-toolkit lock-in) + nieuwe documentatie-componenten
(3 screen-MD's + decision-doc). Raakt 3 sub-repo's via cross-refs (Native
ARCHITECTURE open vraag #1 verplaatst).

## v0.0.1-MetalGear (2026-05-31) — Skeleton + sanitycheck-correcties

### Initial skeleton (newp)
- README, LICENSE (AGPL-3.0), CLAUDE.md, ARCHITECTURE.md
- `docs/PRINCIPLES.md` — 8 principes P-SDM-01..08 (Upstream first / Gamepad-first / Cycle-accurate / Stream-fallback-mode / Geen MS-BIOS / Steam Input passthrough / AGPL-vriendelijke deps / Steam-Deck-only)
- `docs/DEPENDENCIES.md` — cross-repo deps + HC55-dependencies + impact-matrix
- `docs/screens/README.md` — schermontwerp-plan + Steam Deck conventies (1280×800, 24px marge, 64px min-element)
- `prompts/2026-05-31_newp_skeleton.md` met frontmatter

### Post-skeleton corrections (sanitycheck P1)
- CHANGELOG.md aangemaakt (parity met sub-repos)
- Codenaam-vrije-pool ge-centraliseerd in CLAUDE.md (sub-repos krijgen 1-regel verwijzing)
