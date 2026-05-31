# Principes — SteamDeckMSX

Ontwerpprincipes die voor alle 4 repo's gelden. In Dragon1-structuur: **statement → rationale → mechanisme → implicaties**.

---

## P-SDM-01 — Upstream first

**Statement:** Alle wijzigingen aan emulatie-logica gaan eerst naar **openMSX upstream** als PR. Pas als upstream afwijst of niet reageert binnen 30 dagen, houden we een private patch in `SteamDeckMSX_Native/patches/`.

**Rationale:** openMSX is een volwassen project met 20+ jaar geschiedenis. Forks die uit elkaar groeien sterven binnen 2 jaar. Door upstream-first blijft onze Deck-laag dun en onderhoudbaar.

**Mechanisme:** Per commit aan `externals/openmsx/` evalueren: "kan dit naar upstream?". Zo ja → PR + wachten. Zo nee → patch + comment waarom upstream het niet wil (link naar PR-discussie).

**Implicaties:**
- Deck-specifieke UI/Steam-Input-zaken komen in **onze laag**, niet in openMSX
- Geen openMSX-API-breakers; alles backwards-compatible

---

## P-SDM-02 — Gamepad-first UI

**Statement:** De hele UI moet bedienbaar zijn met **alleen de Deck-gamepad**. Toetsenbord/muis = nice-to-have, nooit vereist.

**Rationale:** Gaming Mode op Steam Deck heeft geen virtueel toetsenbord-default. Apps die op tekstinvoer leunen falen UX-test.

**Mechanisme:** Elk scherm krijgt focus-navigatie (D-pad + A/B). Tekstinvoer via on-screen-keyboard (Steam's overlay of eigen). Cartridge-search via filter-letters + scroll.

**Implicaties:**
- Geen modale dialogen die alleen muis ondersteunen
- Geen menus met meer dan ~7 items per niveau (D-pad-vermoeidheid)

---

## P-SDM-03 — Cycle-accurate emulatie

**Statement:** We accepteren geen "fast-mode" shortcuts die game-timing breken. openMSX is cycle-accurate; we breken die garantie niet.

**Rationale:** Veel MSX-games (Konami's SCC-titels) zijn timing-gevoelig. Demoscene-software is dat zelfs extreem. Een spelersbase die specifiek MSX wil emuleren accepteert geen "ongeveer goed".

**Mechanisme:** Performance-bottlenecks lossen we op via betere CPU-utilisatie (multi-threading per subsysteem), niet via timing-shortcuts.

**Implicaties:**
- Geen `--turbo`-flag in de UI
- Streaming-variant moet 60fps halen op HC55 CPU; als dat niet lukt, verlagen we resolutie, niet timing

---

## P-SDM-04 — Stream-fallback-mode

**Statement:** Native en Stream zijn **geen** wederzijds exclusief. De Deck-app (variant 1) detecteert of HC55 bereikbaar is en biedt dan ook een "Stream" knop aan; vice versa kan de stream-client de native installatie aanbevelen als die er is.

**Rationale:** Voor de gebruiker is het één ecosysteem. Twee aparte apps met verschillende save-state-locaties is verwarrend.

**Mechanisme:** Beide apps lezen/schrijven save-states naar een gestandaardiseerd schema (zie `docs/DEPENDENCIES.md` → "save-state-formaat"). Synchronisatie tussen lokaal en HC55 via simpele rsync-stub (v0.2.x).

**Implicaties:**
- Save-state-formaat is een **API-contract** tussen beide varianten; breaking change → +1.0.0
- Vereist account-koppeling in v0.2.x (uit scope nu)

---

## P-SDM-05 — Geen Microsoft-BIOS in repo

**Statement:** De repo's bevatten **uitsluitend C-BIOS** (open source). Originele MSX BIOS-files importeert de gebruiker zelf.

**Rationale:** Microsoft's MSX BIOS is auteursrechtelijk beschermd. Distributie = illegaal in NL, US, JP. AGPL-licentie zou hierdoor besmet raken (license-conflict).

**Mechanisme:** `SteamDeckMSX_Native` bundelt C-BIOS in Flatpak; UI heeft "Import BIOS" knop voor user-supplied files (opgeslagen in `~/.var/app/.../data/bios/`). `SteamDeckMSX_Stream_Server` heeft `/opt/steamdeckmsx/bios/` met C-BIOS + admin-only upload-pad voor user-BIOS (single-user assumption v0.1.x).

**Implicaties:**
- Compatibility-matrix in README: welke games werken op C-BIOS, welke vereisen MSX2-BIOS. Houden we bij.
- Geen automatische BIOS-downloads (anti-piraterij). Wel link naar legale bronnen (bv. backup van eigen MSX-machine).

---

## P-SDM-06 — Steam Input passthrough

**Statement:** Steam Input is de **enige** input-pipeline. Geen directe `/dev/input/eventX` reads, geen SDL2 raw joystick voor Deck-gebruikers.

**Rationale:** Op Steam Deck rebindt Steam Input gamepad-events. Apps die rond Steam Input gaan, krijgen onverwacht gedrag bij user-binding-wissels.

**Mechanisme:** openMSX joystick → SDL2 → Steam Input wrapper. Voor variant 2: Moonlight stuurt al via Steam Input ge-encodeerde events naar Sunshine.

**Implicaties:**
- Custom-binding-UI in onze app is geen vervanger voor Steam Input-config — alleen aanvulling (preset-import).

---

## P-SDM-07 — AGPL-vriendelijke afhankelijkheden

**Statement:** Geen GPL-incompatibele dependencies. Geen closed-source libs. C-BIOS is BSD-compatible → ok.

**Rationale:** AGPL-3.0 is strikt over linkbare libs. Steam SDK is closed-source → mag NIET in onze proces-space. Steam Input gebruiken we via OS-niveau-mechanismen (gamepad-emulatie), niet via Steam SDK-linkage.

**Mechanisme:** Bij elke nieuwe dependency → license-check vóór PR-merge. Lijst bijhouden in `docs/DEPENDENCIES.md`.

**Implicaties:**
- Mogelijk geen Steam Achievement-integratie (vereist Steam SDK). Acceptabel: MSX-games hebben geen achievements.
- Flatpak-runtime keuze beperkt tot Freedesktop / KDE / GNOME runtimes (alle compatible).

---

## P-SDM-08 — Niet-doel: andere platforms

**Statement:** Steam Deck (Linux x86_64) is de **enige** doelplatform. Geen Windows-build, geen Android, geen iOS, geen Switch.

**Rationale:** Scope-creep is dood. openMSX heeft al builds voor Mac/Win/Linux/Android — wie die wil, gebruikt upstream.

**Mechanisme:** CMake-presets hebben alleen `steamdeck-release` en `steamdeck-debug`. Cross-builds → PR-wijzing.

**Implicaties:**
- Andere handhelds (ROG Ally, Legion Go) kunnen werken (Linux-modus) maar zijn niet getest; geen support-belofte.
