# ncp-interactome-algorithm

Algorithmic framework for predicting nucleosome–protein (NCP) interactions by integrating multiple complementary data modalities.

## Purpose

This project develops a predictive algorithm for nucleosome–protein interactions. It brings together:

- **Sequence data** — protein FASTAs, histone variants, PTM annotations
- **Structural data** — PDB/mmCIF files of nucleosomes and binding partners
- **FoldX** — mutational and binding-energy estimates from structural models
- **XL-MS** — cross-linking mass spectrometry restraints identifying spatial proximities
- **AlphaFold 3 (AF3)** — predicted complex structures and per-residue confidence scores
- **Experimental measurements** — affinities, binding assays, and curated training labels

The goal is to fuse these signals into a single model that predicts whether (and where) a candidate protein binds the nucleosome core particle, and to rank candidates for downstream experimental validation.

## Directory Structure

```
.
├── data_raw/            # Raw inputs (do NOT modify after upload)
│   ├── training/        # Curated training tables (CSV/TSV) — labels + features
│   ├── sequences/       # FASTA files (proteins, histones, variants)
│   ├── structures/      # Experimental PDB/mmCIF (nucleosomes, complexes)
│   ├── foldx/           # FoldX outputs (energies, mutation scans)
│   ├── xlms/            # Cross-linking MS data (CSV of crosslink pairs)
│   └── af3/             # AlphaFold 3 predictions (or pointers/links)
├── data_processed/      # Cleaned, merged, feature-engineered tables
├── configs/             # YAML/JSON configuration for runs and experiments
├── notebooks/           # Jupyter notebooks for exploration and figures
├── src/                 # Python source: data loaders, features, models, eval
├── results/             # Model outputs, predictions, metrics, plots
└── docs/                # Project documentation and guides
```

## Where to upload what

| Type of file                                        | Destination               |
| --------------------------------------------------- | ------------------------- |
| Training tables, labels, curated experimental data  | `data_raw/training/`      |
| Protein/histone FASTA files                         | `data_raw/sequences/`     |
| Experimental PDB / mmCIF structures                 | `data_raw/structures/`    |
| FoldX summary outputs (per-mutation energies, etc.) | `data_raw/foldx/`         |
| XL-MS crosslink CSVs                                | `data_raw/xlms/`          |
| AF3 prediction indexes, summaries, or small models  | `data_raw/af3/`           |
| Cleaned/merged/feature tables                       | `data_processed/`         |
| Run configurations                                  | `configs/`                |
| Exploration / figures                               | `notebooks/`              |
| Python modules                                      | `src/`                    |
| Predictions, metrics, plots                         | `results/`                |
| Guides, protocols, write-ups                        | `docs/`                   |

See `docs/data_upload_guide.md` for the recommended upload order and file-naming conventions.

## Large-file guidance

GitHub is a poor home for very large binary outputs (multi-GB AF3 predictions, MD trajectories, bulk FoldX archives). Use the right tool for each file size:

- **Commit directly to the repo** — Python source, configs, notebooks, small tables (< ~10 MB), representative example PDBs, READMEs, and other text artifacts.
- **Use [Git LFS](https://git-lfs.com/)** — for files in roughly the 10–100 MB range that are still essential to version with the code (e.g., a curated reference PDB set, medium-size processed tables).
- **Keep on external storage** — for very large outputs (multi-GB AF3 prediction sets, full MD trajectories, raw mass-spec files, FoldX scans across thousands of mutants). Use S3 / institutional storage / Zenodo / a shared drive, and commit only an *index* file (CSV or JSON) under `data_raw/af3/` (etc.) that lists the artifact names, checksums, and download URLs.

When in doubt: if a file is over ~100 MB or binary and frequently regenerated, do not commit it — link to it instead.

## Getting started

1. Clone the repo.
2. Drop your initial data files into the appropriate `data_raw/` subdirectories (see `docs/data_upload_guide.md`).
3. Add Python modules under `src/` and notebooks under `notebooks/`.
4. Track configs under `configs/` so runs are reproducible.
