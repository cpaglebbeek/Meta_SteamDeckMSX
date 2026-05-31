# Meta_SteamDeckMSX

Master/orchestratie-repo voor het **SteamDeckMSX** ecosysteem: een native MSX-emulator-ervaring op de Valve Steam Deck, in twee varianten:

1. **Native** — offline emulator-fork (openMSX) als Flatpak, draait volledig op de Deck.
2. **Streamservice** — emulator draait op `horsecloud55.ddns.net` (Hetzner) en streamt video+audio naar de Deck via Sunshine/Moonlight; gamepad-input retour.

Deze repo bevat de gedeelde architectuur, principes, screens en cross-repo afhankelijkheden. De daadwerkelijke code zit in de drie zusterrepo's.

## Ecosysteem

| Repo | Rol | Codename v0.0.1 |
|------|-----|------------------|
| **Meta_SteamDeckMSX** | Master/docs (deze repo) | Metal Gear |
| [SteamDeckMSX_Native](https://github.com/cpaglebbeek/SteamDeckMSX_Native) | Variant 1 — Flatpak op Deck | Bubble Bobble |
| [SteamDeckMSX_Stream_Server](https://github.com/cpaglebbeek/SteamDeckMSX_Stream_Server) | Variant 2 — Sunshine + openMSX op HC55 | Knightmare |
| [SteamDeckMSX_Stream_Client](https://github.com/cpaglebbeek/SteamDeckMSX_Stream_Client) | Variant 2 — Moonlight-config voor Deck | Vampire Killer |

## Codenaam-thema

MSX-game-helden — klassieke MSX/MSX2-titels (Konami, Compile, Taito, Falcom). Elke release krijgt een unieke titel (bijv. *Metal Gear*, *Knightmare*, *Bubble Bobble*, *Vampire Killer*, *Nemesis*, *Aleste*, *Penguin Adventure*, *Castlevania*, *Salamander*, *Parodius*, *Snatcher*, *YS*, *Xanadu*).

## Status

- **Fase:** v0.0.1 skeleton (newp 2026-05-31)
- **Hardware:** Steam Deck OLED beschikbaar bij Christian
- **Upstream:** openMSX (MIT) — fork-aanpak, geen from-scratch Z80/VDP

## Licentie

AGPL-3.0 — zie [LICENSE](LICENSE). Reden voor AGPL boven MIT: streaming-variant draait potentieel als SaaS-achtige dienst op HC55; AGPL borgt dat modificaties van publieke streaming-instances source-available blijven.

## Documentatie

- [ARCHITECTURE.md](ARCHITECTURE.md) — componenten + relaties + data-flow (3-componenten in 2 varianten)
- [docs/PRINCIPLES.md](docs/PRINCIPLES.md) — ontwerpprincipes
- [docs/DEPENDENCIES.md](docs/DEPENDENCIES.md) — externe afhankelijkheden + impact-matrix
- [docs/screens/](docs/screens/) — wireframes per scherm
- [CLAUDE.md](CLAUDE.md) — werkprotocol voor AI-agenten op deze repo
