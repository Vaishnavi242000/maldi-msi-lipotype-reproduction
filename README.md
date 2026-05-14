# MALDI-MSI Lipotype Reproduction

A reproduction of the single-cell lipidomics analytical pipeline described in:

> Maillat et al. (2026). *Visualization of single-cell lipidomes with MALDI-MSI.*
> Methods in Enzymology, Volume 726. https://doi.org/10.1016/bs.mie.2025.12.003

## What this project does

The Maillat et al. protocol combines fluorescence microscopy and MALDI mass spectrometry imaging (MALDI-MSI) to extract per-cell lipid profiles, then clusters cells into "lipotypes" — groups defined by their lipid composition rather than gene expression.

This repository reproduces the analytical half of that pipeline (Sections 6.1–6.7 of the paper) on publicly available data from the La Manno lab's EUCLID tutorial dataset. Specifically, I:

1. Load and process MALDI-MSI data in imzML format
2. Segment cells from fluorescence images using CellPose
3. Register MALDI and fluorescence images
4. Extract per-cell lipid intensity profiles
5. Cluster cells into lipotypes using Scanpy (PCA → UMAP → Leiden)

## Status

🚧 Work in progress. Currently: environment set up and verified.

## Reproducing this work

```bash
conda create -n lipidomics python=3.10 -y
conda activate lipidomics
pip install -r requirements.txt
```

See `data/README.md` for instructions on downloading the input data.
See `notebooks/` for the analysis notebooks, run in numerical order.

## Repository structure

- `notebooks/` — analysis notebooks, run in order (01, 02, ...)
- `src/` — reusable Python functions
- `data/` — input data location (data itself is not committed; see `data/README.md`)
- `results/figures/` — generated figures
- `fiji_scripts/` — ImageJ/Fiji scripts for the manual registration step
- `docs/` — notes on the methods and decisions made

## Acknowledgements

Built on the work of:
- Maillat, Goršek, Barahtjan, D'Angelo and colleagues at EPFL for the original methodology
- The La Manno lab at EPFL for [uMAIA](https://github.com/lamanno-epfl/uMAIA) and [EUCLID](https://github.com/lamanno-epfl/EUCLID)
- The CellPose, Scanpy, and pyimzml maintainers

## License

BSD-3-Clause. See `LICENSE`.
