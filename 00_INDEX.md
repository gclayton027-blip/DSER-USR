# DSER-USR2 Compiled Reference Set

**Author:** Clayton Gray, Independent Theoretical Research. **Edition:** compiled v2026.

This set consolidates DSER-USR2 material from three sources: the Google Drive `DSER-USR2` folder (theory PDFs, formula maps, code, codemaps), this Claude project's file system (the shared Fundamental Fisycs / DSER Master System codebase), and work product from chats in this project (the Book IV Addendum: prebiotic chemistry, bioreactor, and ocean alkalinity applications).

| File | Contents |
|---|---|
| `01_DSER-USR2_Theory.md/.docx` | Books I–III: axiomatic substrate (master action, MAI algebra, WCP), Universal Static Imprinting (rung hierarchy, Boom Condition, USR2 switching), Cosmological Projection (GR/QFT/Standard Model derivations) |
| `02_DSER-USR2_Formulas.md/.docx` | Complete Canonical Formula Map: all 20 core equations, ~60 derived relations, 10 external validation targets, full symbol dictionary, formula dependency graph |
| `03_DSER-USR2_Applications.md/.docx` | Book IV: the SSDM diagnostic instrument, four-quadrant Phase Space Polarity Map, particle spectrum, Cellular Energetics Simulator, consciousness model — plus the three-system Addendum (prebiotic vent chemistry, plankton-yeast bioreactor, ocean alkalinity enhancement) |
| `04_DSER-USR2_Code_and_Architecture.md/.docx` | The computational implementation: physics-deck mapping, Smart Switch Chip, Bus Driver, K8/Mandalfold renderers, Verification Daemon — with a manifest of every source file and codemap located across both Drive and project |

## How to use this on Pydroid3 / Android

The `.md` files are plain text with light Markdown formatting (`#` headers, `|table|` pipes, `**bold**`) — they open and read fine in any text editor, including Pydroid3's built-in editor, without needing a Markdown renderer. The `.docx` files are for anywhere a formatted Word document is more useful (sharing, printing, reading on desktop).

## Known gaps (see §7 of the Code & Architecture file for the full list)

- `DSER_66D_ARCHITECTURE.md` was located but not cross-checked against the 3-field core — flagged, not resolved.
- The Google Drive DSER-USR2 folder nests below `Actual_Science/MetatronCog.Inc`; this compilation did not exhaustively enumerate every file at that depth.
- Several near-duplicate copies of the same PDFs/docx exist across Drive folders (e.g. multiple `DSER_Canonical_Formula_Map.pdf` copies) — one canonical copy was used per document; duplicates were not diffed against each other for silent edits.
