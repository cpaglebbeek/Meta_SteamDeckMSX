# ARCHITECTURE — SteamDeckMSX ecosysteem

> Twee varianten van dezelfde functionele kern (MSX-emulatie op Steam Deck), met radicaal verschillende deployment-architectuur.

## Functionele kern (beide varianten)

**Doel:** MSX-software (MSX1, MSX2, MSX2+, TurboR) speelbaar maken op een Steam Deck (OLED of LCD), met gamepad-first UI.

**Functionele componenten:**
- **MSX-machine** — Z80/R800 CPU, V9938/V9958 VDP, AY-3-8910 + SCC + MSX-MUSIC + MSX-AUDIO + PCM
- **Software-laden** — cartridges (`.rom` met mapper-detectie: ASCII8/16, Konami, Konami-SCC, MegaROM, etc.), disk (`.dsk`), tape (`.cas`)
- **BIOS** — C-BIOS gebundeld (open source), originele Microsoft BIOS via user-import
- **Save-states** — quicksave/quickload, slot-based
- **Input-mapping** — gamepad → MSX joystick / keyboard-overlay
- **OSD** — pauze, menu, slot-selectie tijdens spel

## Variant 1 — Native (offline)

```
┌────────────────────────────────────────────────────────────┐
│                  Steam Deck (SteamOS, Linux x86_64)        │
│                                                            │
│   Gaming Mode  ─►  Flatpak: nl.icthorse.SteamDeckMSX       │
│                       │                                    │
│                       ▼                                    │
│              ┌──────────────────────┐                      │
│              │  Deck-UI-laag        │  ← Eigen code        │
│              │  (cartridge-browser, │  (Qt6 of GTK4)       │
│              │   OSD, settings,     │                      │
│              │   Steam Input map)   │                      │
│              └──────────┬───────────┘                      │
│                         │                                  │
│                         ▼                                  │
│              ┌──────────────────────┐                      │
│              │  openMSX core (fork) │  ← Upstream + patches│
│              │  Z80, VDP, PSG, …    │  (C++, SDL2)         │
│              └──────────┬───────────┘                      │
│                         │                                  │
│                         ▼                                  │
│              ┌──────────────────────┐                      │
│              │  C-BIOS / user BIOS  │                      │
│              │  ~/.var/app/.../bios │                      │
│              └──────────────────────┘                      │
└────────────────────────────────────────────────────────────┘
```

**Geen netwerkafhankelijkheid.** Alle data lokaal op de Deck.

**Distributie:** Flatpak via Flathub (later) of zijlading via Discover/CLI.

## Variant 2 — Streamservice (HorseCloud55)

```
┌─────────────────────────────────┐         ┌──────────────────────────────┐
│   HorseCloud55 (Hetzner)        │         │   Steam Deck (LAN of WAN)    │
│                                 │         │                              │
│  systemd:                       │         │  Flatpak: Moonlight-Qt       │
│    ─ openmsx-headless.service   │         │      │                       │
│    ─ sunshine.service           │         │      │ HTTPS-pairing 47984  │
│         │                       │         │      │ RTSP 48010           │
│         │  x264 SW-encoding     │         │      │ Video UDP 47998-48000│
│         │  60fps @720p          │         │      │ Audio UDP 48001      │
│         │                       │         │      │ Control TCP 47999    │
│         ▼                       │  Network│      ▼                       │
│    ┌──────────┐    ◄────────────┼────────►│  ┌──────────────┐            │
│    │ Sunshine │   stream + input│         │  │  Moonlight   │            │
│    └────┬─────┘                 │         │  └──────┬───────┘            │
│         │                       │         │         │                    │
│         ▼                       │         │         ▼                    │
│    ┌──────────┐                 │         │  Gaming Mode UI              │
│    │ openMSX  │                 │         │  + Steam Input               │
│    │ headless │                 │         │                              │
│    └──────────┘                 │         │                              │
└─────────────────────────────────┘         └──────────────────────────────┘
```

**Latency-budget (LAN, gemeten typisch):** RTT 15-25 ms + encode 10 ms + decode 10 ms ≈ 50-60 ms totaal. Speelbaar voor MSX-arcade (Bubble Bobble, Knightmare).

**HC55-poorten (gereserveerd in `Meta_Master/SHARED_INFRASTRUCTURE.md`):**

| Poort | Protocol | Doel |
|-------|----------|------|
| 47984 | TCP | Sunshine HTTPS pairing |
| 47989 | TCP | Sunshine HTTP web (optioneel) |
| 47990 | TCP | Sunshine HTTPS web UI |
| 47999 | TCP | Control channel |
| 48010 | TCP | RTSP setup |
| 47998 | UDP | Video stream |
| 47999 | UDP | Video stream (sec) |
| 48000 | UDP | Video stream (sec) |
| 48001 | UDP | Audio stream |
| 48002 | UDP | Microphone (ongebruikt) |

Allen vrij (Hetzner LiveKit-range 50000-60000 niet overlappend).

**Encoding:** HC55 heeft geen GPU → `software-x264` of `software-x265`. CPU-belasting ~1-2 cores bij 720p60. Latency-impact ~10-15 ms vs ~3-5 ms bij NVENC.

**Risico's:**
- CPU-contentie met andere HC55-services tijdens piek (Dashboard, ClaudeWeb, ArchiSaas) — mitigatie: nice/cpulimit + alleen actief tijdens stream-sessie
- WAN-streaming buiten LAN → latency 50-150 ms, mogelijk niet speelbaar voor twitch-games
- Geen multi-user: één Sunshine-pairing = één actieve sessie (kan later naar multi-instance worden uitgebouwd)

## Cross-variant gedeelde elementen

| Element | Variant 1 | Variant 2 |
|---------|-----------|-----------|
| openMSX-fork | Lokaal gebuild, Flatpak | Headless build op HC55 |
| BIOS-handling | User-import via Deck filesystem | User-upload via web UI (later) |
| Cartridge-library | Lokaal `~/.var/app/.../roms` | Server-side `/opt/steamdeckmsx/roms` (gedeeld voor gebruiker) |
| Save-states | Lokaal | Server-side (cloud-saves) |
| Input-mapping | Steam Input op Deck | Moonlight gamepad-passthrough → openMSX |
| Codenaam-thema | MSX-game-helden | MSX-game-helden |
| Licentie | AGPL-3.0 | AGPL-3.0 |

## Data-flow Variant 1

1. User start Flatpak → Deck-UI-laag opent cartridge-browser
2. User selecteert `.rom` → openMSX-core boot met C-BIOS of user-BIOS
3. Steam Input → SDL2-events → openMSX joystick API
4. 60fps render → Deck-display direct (geen netwerk)
5. Quicksave → schrijf naar `~/.var/app/nl.icthorse.SteamDeckMSX/data/states/`

## Data-flow Variant 2

1. User start Moonlight op Deck → pairt met HC55 (eenmalig: PIN-code via web UI 47990)
2. Moonlight start "MSX-stream" (een geconfigureerde Sunshine-app → `openmsx-headless`)
3. Sunshine vangt openMSX framebuffer + audio → x264 encode → UDP stream
4. Deck rendert stream + leest gamepad → Sunshine input-channel → openMSX
5. User stopt → Sunshine stopt openMSX-proces

## Relatie met andere ecosystemen

- **iCt_Horse** — Variant 2 zou later kunnen integreren met `icthorse.nl` (publieke demo / dienst). Nu uit scope.
- **CloudInfra** — HC55-poort-claims komen in `SHARED_INFRASTRUCTURE.md`.
- **ClaudeBug** — Bug-rapportage via standaard ClaudeBug-flow (web HC55:3996 + Android share-target).

## Niet-scope (expliciet)

- Multi-user streaming (één-pairing-tegelijk in v0.1.x)
- Originele Microsoft BIOS in repo (juridisch verboden)
- Windows-build (uit scope; Steam Deck = Linux)
- Web-emulator (uit scope; te traag voor MSX2+/TurboR)
- NDS/GBA/SNES (alleen MSX-familie)
