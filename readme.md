
# mTOR-Independent Compound Screening

Screening workflow and results for identifying TFEB nuclear translocation agonists that do not interfere with mTOR. The project combines ligand-based virtual screening on Deep Drug Discovery (DDD) with large-scale reverse docking against autophagy-lysosome pathway proteins, followed by ranking and selection of top candidates for experimental follow-up.

## Repository Layout
- `data/1_first_stage_compound.csv` — 1,745 candidates from DDD ligand-based screening with similarity scores and reference compounds.
- `data/2_Autophagy_OR_Mitophagy.csv` — 1,023 autophagy/mitophagy-related proteins collected from GeneCards with UniProt IDs and relevance scores.
- `data/3_docking_results.csv` — 1.5M docking scores (Uni-Dock) across the protein panel.
- `data/4_mTOR_independent_docking_top20_mean_energy.csv` — top 20 prioritized mTOR-independent compounds with mean docking energies and SMILES.
- `Receptor Preparation and Structure-based Screeening.ipynb` — notebook for receptor collection, cleaning, pocket prediction, and docking setup.
- `Post-Docking Analysis.ipynb` — notebook for aggregation, filtering, and hit selection.

## Workflow
### Ligand-Based Virtual Screening
- Ligand-based virtual screening (LBVS) was initially performed using the Deep Drug Discovery platform (DDD, https://deepdrugdiscovery.mindrank.ai/).
- Performed on the Deep Drug Discovery platform using 15 known mTOR-independent TFEB nuclear translocation agonists as queries (Supplementary Table 1).
- DDD integrates 1D/2D/3D molecular features with attention-based representations for similarity search.
- Hits were retained if similarity > 0.65 or within the top 500, yielding 1,745 candidates advanced to structure-based screening.

### Receptor Preparation
- Autophagy-lysosome pathway proteins collected from GeneCards; UniProt IDs retrieved from UniProtKB and structures sourced from AlphaFold DB.
- Total of 1,028 proteins collected; 22 lacked usable structures. The remaining 1,006 structures were repaired with PDBFixer (v1.11) to fill missing atoms, add hydrogens, and assign protonation at pH 7.4.

### Structure-Based Screening
- Binding pockets predicted with P2Rank (v2.3). Proteins without pockets or directly tied to mTOR signaling (n=98) were excluded, leaving 908 proteins.
- Docking performed with Uni-Dock (v1.1.1) using Vina scoring and GPU-accelerated Monte Carlo search. Grids centered on the top-ranked P2Rank pocket with a 25 × 25 × 25 Å³ box.

### Post-Docking Analysis
- Produced a 1,745 × 914 cross-docking score matrix.
- Compounds with mTOR binding affinity scores < 8.5 were removed to enforce mTOR independence.
- For each remaining compound, the mean of its top 20 docking scores across 908 proteins was used as the final binding score.
- The top 20 compounds by this metric are listed in `data/4_mTOR_independent_docking_top20_mean_energy.csv` for downstream validation.

## Data Notes
- `1_first_stage_compound.csv`: columns `Source_idx`, `Smiles`, `target_idx`, `similarity_score`, `reference_compound_name`.
- `2_Autophagy_OR_Mitophagy.csv`: columns `Gene Symbol`, `Description`, `Category`, `Uniprot ID`, `Gifts`, `GC Id`, `Relevance score`, `GeneCards Link`.
- `3_docking_results.csv`: columns `Source_idx`, `protein` (UniProt ID), `energy` (Vina/Uni-Dock score).
- `4_mTOR_independent_docking_top20_mean_energy.csv`: columns `Source_idx`, `top20_mean_energy`, `Smiles`.

## Environment and Reproducibility
- Docking engine: [Uni-Dock](https://github.com/dptech-corp/Uni-Dock/tree/main); follow the official installation guide for GPU-enabled docking.
- Receptor processing: PDBFixer (v1.11); pocket prediction: P2Rank (v2.3).
- Analysis and aggregation steps are documented in the included notebooks; run them with a standard scientific Python stack (pandas, numpy, etc.).

## How to Use
1) Inspect ligand and receptor inputs in `data/`.
2) Re-run receptor preparation and docking with `Receptor Preparation and Structure-based Screeening.ipynb` if new targets are added.
3) Recompute hit rankings or apply alternative filters using `Post-Docking Analysis.ipynb`.
4) Prioritized structures and SMILES in `data/4_mTOR_independent_docking_top20_mean_energy.csv` can be forwarded for experimental validation.
