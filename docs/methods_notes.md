# Methods notes

A running log of decisions and observations as the reproduction proceeds.

## Decisions

- **2026-05-13**: Selected the EUCLID tutorial dataset (La Manno lab) as the starting point because it auto-downloads from Zenodo and provides uMAIA-normalized data + masks, removing several upstream-pipeline blockers.
- **2026-05-13**: Project will reproduce the analytical half of the Maillat 2026 protocol (sections 6.1–6.7) at tissue/pixel level first, then extend to single-cell if time and data allow.
- **2026-05-13**: Tooling: Miniforge conda, uv for pip, Python 3.10. Apple Silicon (M-series) native arm64 stack.
- **2026-05-13**: Skipping uMAIA's molecule-matching module (requires Gurobi license, not available without academic affiliation). Will use METASPACE web interface for annotation instead.

## Open questions

- How does the EUCLID tutorial's pre-normalized data relate to the Maillat paper's per-cell extraction? Tissue vs. cell-culture data may behave differently.
- Will I need to implement my own BigWarp-equivalent registration in Python, or is it skippable for the EUCLID dataset which may already be aligned?

## Things that broke

(nothing yet)