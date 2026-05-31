# CLAUDE.md — Meta_SteamDeckMSX

> Master/orchestratie-repo voor het SteamDeckMSX-ecosysteem (4 repo's). Verwijst naar `Meta_Master/CLAUDE.md` voor alle globale regels (WhatIf, ZSH, sessie-protocol, over-en-uit).

## Rol van deze repo

Deze repo bevat **geen runnable code**. Alleen:
- Ecosysteem-overstijgende architectuur (`ARCHITECTURE.md`)
- Principes (`docs/PRINCIPLES.md`)
- Cross-repo afhankelijkheden (`docs/DEPENDENCIES.md`)
- Wireframes / schermontwerpen (`docs/screens/`)
- Sessie-MD's (`prompts/`)

Wijzigingen aan code-componenten gaan ALTIJD in de sub-repo (`SteamDeckMSX_Native`, `SteamDeckMSX_Stream_Server`, `SteamDeckMSX_Stream_Client`).

## Codenaam-thema: MSX-game-helden

> **Single source of truth voor de hele ecosysteem.** Sub-repos verwijzen hierheen — niet zelf de pool dupliceren (drift-risico).

Elke release krijgt een unieke MSX-titel als codenaam.

### Toegewezen (gebruikte titels)

| Repo | Versie | Codenaam |
|------|--------|----------|
| Meta_SteamDeckMSX | v0.0.1 | Metal Gear |
| SteamDeckMSX_Native | v0.0.1 | Bubble Bobble |
| SteamDeckMSX_Stream_Server | v0.0.1 | Knightmare |
| SteamDeckMSX_Stream_Client | v0.0.1 | Vampire Killer |

### Vrije pool (in willekeurige volgorde)

Nemesis, Aleste, Penguin Adventure, Castlevania, Salamander, Parodius, Snatcher, YS, Xanadu, Treasure of Usas, King's Valley, Twinbee, Gradius, Antarctic Adventure, Hyper Olympic, Goonies, Magical Tree, Pippols, Yie Ar Kung-Fu, Maze of Galious, Vampire, Dragon Slayer, Solid Snake, Quarth, Space Manbow, F1 Spirit, Hinotori, Athletic Land.

### Rotatie-regels

1. Bij toewijzen van een nieuwe codenaam: schuif naar boven uit "Vrije pool", plaats in "Toegewezen"-tabel
2. Geen codenaam mag in meer dan één repo voorkomen
3. Sub-repo CLAUDE.md's verwijzen ALLEEN naar dit bestand voor de pool — geen dubbele administratie

## Feature & Bugfix Protocol (Color-Coded)

**Nieuwe Feature:**
- **Groen** — Documentatie-update zonder architectuurimpact → +0.0.1
- **Oranje** — Architectuurwijziging (nieuwe component, gewijzigde relatie) → +0.1.0
- **Rood** — Conceptuele heroriëntatie (bijv. nieuwe variant) → +1.0.0

**Bugfix** (geldt vooral voor docs hier: incorrecte verwijzing, fout poortnummer, fout claim over latency):
- **Groen** — Tekstcorrectie
- **Geel** — Architectuurdiagram fout (impact: sub-repo's krijgen verkeerde aanname)
- **Rood** — Conceptueel principe blijkt onjuist → re-design

**Root Cause Analysis (verplicht bij elke bugfix):** Functioneel / Technisch / Architectonisch.

## Versioning Mandate

Elke functionele/documentaire wijziging → versie bumpen in `VERSION` (semver + codenaam: `0.0.1-MetalGear`).

## WhatIf Protocol

Zie `Meta_Master/CLAUDE.md` — verplicht vóór elke actie. Hier specifiek: wijziging in `ARCHITECTURE.md` raakt 3 sub-repo's; benoem impact per repo expliciet vóór commit.

## Sub-repo synchronisatie

Bij architectuurwijziging in deze repo: open issue/PR in elk geraakte sub-repo zodat hun `ARCHITECTURE.md` bijgewerkt wordt. Drift tussen master en sub is een **rode bug**.

## Build / Release

Deze repo bouwt niets. "Release" = tag op git met codenaam-versie. Geen artefacten.

## Sessie-MD's

Alle prompt-sessies in `prompts/YYYY-MM-DD_<slug>.md` met frontmatter (`date`, `repo`, `status`, `resume`). Zie `Meta_Master/templates/PROMPT_SESSION_TEMPLATE.md`.
