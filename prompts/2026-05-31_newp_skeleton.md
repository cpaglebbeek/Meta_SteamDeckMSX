---
date: 2026-05-31
repo: Meta_SteamDeckMSX
status: open
resume: "verder met Meta_SteamDeckMSX — wireframes screens/, beslis Qt6 vs GTK4 (zie open vraag Native ARCHITECTURE)"
---

# Sessie 2026-05-31 — newp Meta_SteamDeckMSX (ecosysteem-master)

**Agent:** Claude Opus 4.7 (1M context)
**Repo:** Meta_SteamDeckMSX (`cpaglebbeek/Meta_SteamDeckMSX`)
**Branche:** main
**Cross-repo werk:** SteamDeckMSX_Native, SteamDeckMSX_Stream_Server, SteamDeckMSX_Stream_Client, Meta_Master
**Eindstand commits:** (initial commit, hash volgt)

---

## Opdracht (samengevat)

Gebruiker startte newp "native Steamdeck MSX Emulator". Na WhatIf-iteraties (5 vragen) zijn deze besluiten genomen: forken openMSX, hardware Steam Deck aanwezig, publiek + AGPL-3.0, Meta-ecosysteem-structuur (Optie Y, 4 repo's), Sunshine/Moonlight streaming-stack (Optie A). Deze sessie zet skeleton op voor het ecosysteem.

---

## Prompts en acties — chronologisch

### Prompt 1 — "newp 'native Steamdeck MSX Emulator'"
**Actie:** /verifyrules + Meta_Master pull + WhatIf v1 (5 open vragen).
**Keuze:** Vragen één voor één conform `feedback_questions_one_by_one`.

### Prompt 2 — "forken. ik heb steamdeck hardware. ik wil 2 branches uitwerken: 1) native op deck, offline, 2) streamservice vanaf horsecloud"
**Actie:** WhatIf v2 met scope-uitbreiding (variant 2 = HC55 streaming). 3 streaming-stack-opties (Sunshine/Moonlight, WebRTC, xpra) en 3 repo-structuur-opties (X branches, Y meta-ecosysteem, Z monorepo) gepresenteerd.
**Keuze:** Advies Optie Y + Optie A.

### Prompt 3 — "publiek, AGPL, Y, A"
**Actie:** SHARED_INFRASTRUCTURE.md gelezen, poort-conflict-check Sunshine 47984-48010 → conflict-vrij. Finaal plan + 11-stap skeleton-actieplan gepresenteerd. Akkoord gevraagd.

### Prompt 4 — "ja"
**Actie:** Skeleton-bouw gestart. 4 repo-dirs aangemaakt. Per repo: README, LICENSE (via gh later), CLAUDE.md, VERSION, ARCHITECTURE.md, CHANGELOG.md, .gitignore. Meta-repo extra: docs/PRINCIPLES.md (8 principes P-SDM-01 t/m 08), docs/DEPENDENCIES.md (cross-repo + HC55), docs/screens/ (wireframe-plan).
**Keuze:** Codenamen toegewezen: Meta=Metal Gear, Native=Bubble Bobble, Stream_Server=Knightmare, Stream_Client=Vampire Killer.

---

## Belangrijke keuzes deze sessie

| Keuze | Reden |
|---|---|
| Optie Y (Meta-ecosysteem, 4 repo's) | Conform bestaande Meta_PhotoVerify/Meta_MindBodyNJoy patroon; schone scheiding tussen variant 1 en 2 |
| Sunshine + Moonlight (A) | Beproefd voor gaming, lage latency, Steam Deck heeft Moonlight in Flathub |
| AGPL-3.0 | Streaming-variant op HC55 = SaaS-achtig; AGPL borgt source-availability |
| C-BIOS only (P-SDM-05) | Microsoft BIOS distributie illegaal; juridisch-veilig + AGPL-compatible |
| Software x264 encoding | HC55 = CPU-only, geen GPU; ~1.5-2 cores piek = acceptabel |
| Qt6 als voorlopige UI-toolkit | Definitieve keuze v0.0.2 (Native ARCHITECTURE open vraag #1) |

---

## Open eindjes na deze sessie

**Klaar voor verzending / publicatie:**
- Skeleton-commits (alle 4 repos) — gepushed na akkoord

**Wacht op afhankelijkheden:**
- Beslissing Qt6 vs GTK4 (v0.0.2)
- Beslissing openMSX-API-koppeling: embedded library vs subprocess/IPC (v0.0.2)
- Wireframes visueel uitwerken (v0.0.2) — tool-keuze Excalidraw/Figma/Penpot
- HC55 deploy van Sunshine (v0.0.2 Stream_Server)
- SHARED_INFRA_PATCH mergen in `Meta_Master/SHARED_INFRASTRUCTURE.md` bij eerste deploy

**Onafhankelijk werk:**
- openMSX-fork aanmaken op cpaglebbeek/openMSX (git mirror van upstream) — v0.0.2

---

## Verbinding met andere sessies

| Sessie | Locatie | Verbinding |
|---|---|---|
| 2026-05-31 newp skeleton Native | SteamDeckMSX_Native/prompts/ | zusterskeleton, dezelfde besluiten |
| 2026-05-31 newp skeleton Stream_Server | SteamDeckMSX_Stream_Server/prompts/ | idem |
| 2026-05-31 newp skeleton Stream_Client | SteamDeckMSX_Stream_Client/prompts/ | idem |
