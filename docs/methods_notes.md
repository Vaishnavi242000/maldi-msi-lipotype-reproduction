# Methods notes

A running log of decisions and observations as the reproduction proceeds.

## Decisions
- **2026-05-13**: Created a separate `EUCLID_ENV` conda env for EUCLID work, kept main `lipidomics` env clean. EUCLID brings ~100 extra packages (scanpy, squidpy, spatialdata, geopandas stack); mixing them with our existing env risked version conflicts on numpy/pandas/scipy/matplotlib. EUCLID env follows the upstream README install instructions verbatim. Notebooks that use EUCLID will select the `Python (EUCLID_ENV)` kernel; everything else stays on `lipidomics`.
- **2026-05-13**: imzML smoke test passes. pyimzml 1.5.5 reads `Example_Continuous` (3×3 px, 8,399 m/z points/pixel) without issue. Both a single spectrum and an ion image render cleanly. MSI pipeline viable on this hardware.
- **2026-05-13**: Smoke test passes. CellPose 4 (`cpsam` model) segments `skimage.data.coins()` cleanly — 24/24 objects. Pipeline works end-to-end on Apple Silicon (CPU mode). GPU/MPS deferred until needed.
- **2026-05-13**: Selected the EUCLID tutorial dataset (La Manno lab) as the starting point because it auto-downloads from Zenodo and provides uMAIA-normalized data + masks, removing several upstream-pipeline blockers.
- **2026-05-13**: Project will reproduce the analytical half of the Maillat 2026 protocol (sections 6.1–6.7) at tissue/pixel level first, then extend to single-cell if time and data allow.
- **2026-05-13**: Tooling: Miniforge conda, uv for pip, Python 3.10. Apple Silicon (M-series) native arm64 stack.
- **2026-05-13**: Skipping uMAIA's molecule-matching module (requires Gurobi license, not available without academic affiliation). Will use METASPACE web interface for annotation instead.

## Open questions

- How does the EUCLID tutorial's pre-normalized data relate to the Maillat paper's per-cell extraction? Tissue vs. cell-culture data may behave differently.
- Will I need to implement my own BigWarp-equivalent registration in Python, or is it skippable for the EUCLID dataset which may already be aligned?

## Things that broke

### 2026-05-13 — CellPose API change

- **What broke**: `models.Cellpose(model_type='cyto2')` throws `AttributeError: module 'cellpose.models' has no attribute 'Cellpose'`
- **Why**: CellPose 4.x removed the `Cellpose` class. Now you must use `CellposeModel`. The `channels` parameter is also gone (Cellpose-SAM is channel-invariant).
- **Fix**: `models.CellposeModel(gpu=False)` then `model.eval(img, diameter=None)`. No channels arg.
- **Implication for the reproduction**: Paper uses cyto2 (CellPose 3.x); we have cpsam (CellPose 4.x). Different underlying network. To revisit when working with real data.

### 2026-05-13 — `data.cells3d()` needs pooch

- **What broke**: `skimage.data.cells3d()` requires `pooch` to download the data on first use, which isn't a default dependency of scikit-image
- **Workaround**: For the smoke test we switched to `data.coins()`, which ships with scikit-image directly. No download needed.
- **For later**: If we want fluorescence test data, `pip install pooch` first, then restart the kernel (Jupyter doesn't see newly-installed packages until restart).
- ### 2026-05-13 — EUCLID install fails with setuptools 82+

- **What broke**: After `pip install euclid-msi==0.0.6`, `import euclid_msi` fails with `ModuleNotFoundError: No module named 'pkg_resources'`. Traceback chain: euclid_msi → squidpy → spatialdata → xarray_schema → tries `from pkg_resources import DistributionNotFound, get_distribution`.
- **Why**: setuptools 81+ no longer bundles `pkg_resources` by default. The `xarray_schema` package (a transitive dep) still uses the legacy import.
- **Fix**: `pip install "setuptools<81"` — pins setuptools to 80.x where `pkg_resources` is still available.
- **Note**: setuptools itself warns about this and suggests the pin. Slated for removal 2025-11-30, so future EUCLID releases may fix it upstream. Watch for `xarray_schema` updating its imports.
- **Bonus**: First `import euclid_msi` after the fix triggers auto-download of ~265 MB of reference databases (`structures.sdf` 254 MB, `HMDB_complete.csv` 10 MB, `lipidclasscolors.h5ad` 1 MB). Stored somewhere under `~/.euclid_msi/` or similar (haven't checked the exact path yet).