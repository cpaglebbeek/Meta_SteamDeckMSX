# DEPENDENCIES — SteamDeckMSX ecosysteem

## Externe afhankelijkheden

| Naam | Versie | Licentie | Gebruikt door | Impact bij wijzigen |
|------|--------|----------|---------------|---------------------|
| **openMSX** | git HEAD bij fork | GPL-2.0 | Native, Stream_Server | Z80/VDP/PSG-gedrag. Major bump kan game-compat breken. |
| **C-BIOS** | 0.29a+ | BSD-3-Clause | Native, Stream_Server | MSX BIOS-vervanger. AGPL-compatible. |
| **SDL2** | 2.30+ | zlib | Native (via openMSX) | Input + framebuffer. |
| **Qt6** (of GTK4) | 6.7+ | LGPL-3 | Native UI-laag | UI-toolkit. Keuze nog open — beslis v0.0.2. |
| **Sunshine** | 0.23+ | GPL-3.0 | Stream_Server | Streaming-host. CPU-encoding via libx264. |
| **libx264** | 0.164+ | GPL-2.0 | Stream_Server | Video-encoding zonder GPU. |
| **Moonlight-Qt** | 5.0+ | GPL-3.0 | Stream_Client | Streaming-client op Deck. |
| **Flatpak SDK / Freedesktop** | 23.08 | LGPL | Native (build) | Container-image voor Flatpak-build. |
| **systemd** | 255+ | LGPL | Stream_Server | Service-management op HC55. |
| **nginx** | 1.24 | BSD-like | (eventueel) | Alleen als we web-admin-UI voor Stream_Server bouwen. |

## Cross-repo afhankelijkheden

```
Meta_SteamDeckMSX (docs only)
        │
        │ defineert ARCHITECTURE + PRINCIPLES + save-state-formaat
        │
        ├──────► SteamDeckMSX_Native
        │             │
        │             └─► externals/openmsx (git submodule, fork)
        │
        ├──────► SteamDeckMSX_Stream_Server
        │             │
        │             ├─► externals/openmsx (git submodule, dezelfde fork)
        │             └─► /opt/steamdeckmsx/sunshine.conf
        │
        └──────► SteamDeckMSX_Stream_Client
                      │
                      └─► externals/moonlight-qt (config-only)
```

**Synchronisatie-regel:** `SteamDeckMSX_Native` en `SteamDeckMSX_Stream_Server` gebruiken EXACT dezelfde openMSX-fork-commit. Drift = bug.

**Save-state-formaat (API-contract):**
- Magic bytes: `SDMSX01\0`
- Header: openMSX version (string, 32 bytes) + machine-type (`MSX1`/`MSX2`/`MSX2+`/`TurboR`)
- Body: openMSX native savestate (XML)
- Trailer: SHA-256 over header+body

Breaking change = +1.0.0 in alle 3 sub-repo's gelijktijdig.

## Steam Deck OS afhankelijkheden

| Onderdeel | Vereiste versie | Reden |
|-----------|-----------------|-------|
| SteamOS | 3.5+ (Holo) | Flatpak 1.15+ ingebouwd |
| Flatpak runtime | `org.freedesktop.Platform 23.08` | Qt6 + SDL2 beschikbaar |
| Kernel | 6.5+ (HID-bugfixes) | Steam Input-gedrag |
| Mesa | 24.0+ | Voor Vulkan-rendering openMSX (optioneel) |

## HC55 afhankelijkheden (Variant 2 server-side)

| Onderdeel | Versie | Reden |
|-----------|--------|-------|
| Ubuntu | 24.04 | Bestaande HC55-OS |
| Sunshine | latest deb | Niet in Ubuntu repos → eigen `.deb` of Flatpak |
| libx264 | apt repo | `libx264-dev` aanwezig op standaard Ubuntu |
| systemd | 255 | Service-management |
| Nginx | bestaand (gedeeld) | Alleen indien web-admin-UI; voor stream zelf niet nodig |

**HC55 CPU-budget:** Sunshine + openMSX-headless bij 720p60 ≈ 1.5-2 cores. Andere services moeten ≥ 4 cores vrijhouden. Mitigatie: `cpulimit` of `systemd CPUQuota=200%`.

## Impact-matrix (oorzaak/gevolg)

| Wijziging | Native | Stream_Server | Stream_Client | Mitigatie |
|-----------|--------|---------------|---------------|-----------|
| openMSX major bump | ⚠ game-compat | ⚠ game-compat | — | Regression-test-corpus |
| Save-state-formaat | ⚠ break | ⚠ break | — | Synchroon bumpen, migratie-tool |
| Sunshine API-break | — | ⚠ | ⚠ | Moonlight-versie pinnen |
| Steam Input-gedragwissel SteamOS | ⚠ | — | ⚠ | Quarterly retest na SteamOS-update |
| HC55 nginx-config wijziging | — | ⚠ admin-UI (later) | — | SHARED_INFRASTRUCTURE-procedure |
| C-BIOS update | game-compat klein | game-compat klein | — | Changelog-review |

## Niet-afhankelijkheden (expliciet uit scope)

- **Steam SDK** — closed-source, AGPL-conflict
- **Discord SDK** — niet nodig
- **Proton/Wine** — Linux-native, geen Windows-compat-laag
- **CUDA/NVENC** — HC55 heeft geen GPU; CPU-encoding is bewuste keuze
- **WebRTC** — Sunshine doet RTP/UDP zelf, geen browser-target
