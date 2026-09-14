# KI Malta — Base Folder Map (for Karl)

Source folder: `Kaizen Institute Ltd. (KIAG)\KI Malta - Documents` (OneDrive, 1.3GB, 1,084 files)

This is becoming the base for the shared GitHub + Supabase + Claude Code workspace agreed in the 11 Sep Dev Days meeting. Before wiring it in, read this once so we don't build the workspace on top of duplicates or stale data.

## Top level

- `Eva_Training/` — 26MB, 216 files
- `General/` — 1.2GB, 868 files, numbered subfolders (00 to 100)
- `KPIs/` — empty, root level, ignore

## Live and reusable now

- `General/00. Management/AI resources/` — the actual Kaizen.AI sales and delivery kit: How We Work decks, data/security annexes, funding overviews, case studies, training courses. Each item already exists as PDF, PPTX and HTML side by side. Reuse this rather than rebuilding positioning material.
- `General/00. Management/Database/Malta_Clients_FINAL_CLEAN.csv` — **this is the client list to treat as truth.**
- `General/03. Office Resources/Malta Database and Action Plan/` — `Business Developement Action Plan.xlsx`, `Malta Database.xlsx`.
- `General/06 KI MALTA KPI's/` — Sales, Income, Delivery trackers. Last real update 2024-2025, nothing recent, needs a refresh before anyone relies on it.
- `General/02. Events/Executive Breakfast 2026/Kaizen in AI - 30th October/` — the live event, and already has its own working scripts inside (`md_to_pdf.py`, `build_flyer.py`, `build_decision_tree.py`, `xlsx_to_pdf.ps1`). Worth looking at before writing new tooling from scratch, this pattern already works.

## Duplicated, needs a decision before ingest

- `Eva_Training/EVA_AI_Course_V1/` (7.1MB) and `Eva_Training/Eva_Training_VF/` (19MB, plus a further `To Share` copy inside it) are the same AI course three times over: same ~30-file Skill Library, same 16 participant cards, in each tree. Pick one as canonical before this goes into the shared repo.
- `General/00. Management/Database/OLD/` holds four older client-list versions (`2026 Database.xlsx`, `Data_MC_Clean (1).xlsx`, `Full Database 2026.xlsx`, `WhosWho DB.xlsx`) that predate `Malta_Clients_FINAL_CLEAN.csv`. Do not merge these back in without checking dates against the FINAL_CLEAN file.
- `General/02. Events/Database/` (`Database - September 2023.xlsx`, `MT_Strat to Action event_11052021.xlsx`) is a separate, older events-only database, not the client list. Don't confuse it with the one above.

## Archive, exclude from the base entirely

- `General/98. Archive- COVID-19/`
- `General/99. Archive- Working Folders/`
- `General/99. Apartment/`
- `General/100. Colleges 2025/`
- `General/03. Office Resources/99.Archive/`

## File mix (whole folder)

283 pptx, 192 pdf, 185 md (almost all inside the Eva_Training duplicates above), 113 xlsx, 87 png, 71 jpg, 51 docx, plus smaller counts of mp4, txt, html, zip.
