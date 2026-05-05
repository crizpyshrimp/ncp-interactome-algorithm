# Data Upload Guide

Practical, project-specific guidance for getting the nucleosome–protein interaction (NCP) data into this repository in a usable, reproducible state.

## Recommended upload order

Do these in sequence. Each step unlocks the next: training labels define what we're predicting; sequences define the protein universe; XL-MS adds spatial restraints; FoldX and structures add energetics; AF3 fills in predicted complex geometry.

### 1. Training tables → `data_raw/training/`

Start here — without labels we can't define the task.

- One CSV/TSV per study or curated set, e.g. `binders_2025_curated.csv`.
- Required columns (suggested): `protein_id`, `uniprot`, `histone_target` (e.g. H3, H2A.Z), `binding_label` (0/1 or affinity), `assay`, `source_doi`, `notes`.
- Add a small `README.md` alongside it describing provenance.

### 2. Protein FASTA files → `data_raw/sequences/`

- One FASTA per protein family or one combined FASTA (`ncp_partners.fasta`).
- Include histone variants and PTM-annotated sequences where relevant.
- FASTA headers should include UniProt accessions so they can be joined to the training tables.

### 3. XL-MS crosslink CSVs → `data_raw/xlms/`

- One CSV per dataset / publication, e.g. `xlms_smith2024.csv`.
- Suggested columns: `protein_a`, `residue_a`, `protein_b`, `residue_b`, `score`, `crosslinker`, `source`.
- Keep raw mass-spec files (mzML, RAW) **out** of the repo — link in an index file instead.

### 4. FoldX summary outputs → `data_raw/foldx/`

- Commit *summary* tables (per-mutation ΔΔG, interaction energies), not raw repair logs.
- One CSV per scan or per system, e.g. `foldx_h3_tail_scan.csv`.
- Note the FoldX version and PDB input IDs in a sibling `README.md`.

### 5. Representative PDB / mmCIF structures → `data_raw/structures/`

- Commit a small, curated set of representative structures (e.g. canonical NCP, key complexes).
- For larger sets, keep them on external storage and commit only an index CSV listing PDB IDs and download URLs.
- Use original PDB filenames (e.g. `1kx5.pdb`) when possible.

### 6. AF3 indexes / links → `data_raw/af3/`

- Do **not** commit AF3 model archives directly — they are large.
- Commit an `af3_index.csv` listing: `complex_id`, `partners`, `model_path` (external URL or storage key), `pTM`, `ipTM`, `ranking_score`, `date_run`.
- Optionally commit a handful of small per-prediction summary JSONs for quick inspection.

## File-naming conventions

- Lower-case, snake-case filenames.
- Include a date or version suffix when a file is expected to be replaced: `training_v2_2026-05.csv`.
- Pair every non-trivial data file with a tiny `README.md` (or comments at the top of the CSV) describing source, columns, and any caveats.

## What goes in `data_processed/`

Files in `data_processed/` should be **reproducible from `data_raw/`** by running code in `src/` or notebooks in `notebooks/`. Treat `data_raw/` as immutable; treat `data_processed/` as a cache.

## Large-file rules of thumb

| Size                | Where it goes                                                           |
| ------------------- | ----------------------------------------------------------------------- |
| < 10 MB             | Commit directly                                                         |
| 10–100 MB           | Commit via Git LFS if it really needs to be versioned with the code     |
| > 100 MB            | External storage (S3 / Zenodo / shared drive); commit an index file     |
| MD trajectories     | Always external                                                         |
| Raw mass-spec files | Always external                                                         |
| Full AF3 archives   | Always external                                                         |

## Sanity checklist before committing data

- [ ] No patient-identifiable or otherwise restricted data.
- [ ] No API keys, credentials, or `.env` files.
- [ ] Filenames follow the snake-case convention.
- [ ] A short note in `README.md` (or a sibling file) explains provenance.
- [ ] Files >100 MB are linked, not committed.
