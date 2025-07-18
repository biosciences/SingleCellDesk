# SingleCellDesk

**SingleCellDesk** is a modular and user-friendly R toolkit for downstream analysis of single-cell RNA-seq (scRNA-seq) datasets. It enables fast and customizable exploration of count matrix–level data using standard workflows, including quality control, clustering, trajectory inference, and cell-cell interaction analysis.

## 📄 Project Links
- 📂 [Source Code](https://github.com/biosciences/SingleCellDesk): Explore the full repository
- 🔗 [Live Demo Report](https://biosciences.github.io/SingleCellDesk/SingleCellDesk_Analysis.html): View the interactive HTML output


## 🔍 Features

- Quality control with Seurat
- Clustering and UMAP embedding
- Cell type annotation via marker genes or SingleR
- Pseudotime trajectory analysis using Monocle3
- Ligand-receptor signaling inference with CellChat
- Modular script-based structure suitable for any 10x-style input

# The Cell Type Annotation

This project compares two approaches for annotating cell types in nucleus pulposus (NPC) single-cell RNA-seq data.

---

## 🌐 Global Reference Annotation (SingleR)

We used **SingleR** to assign global cell type identities based on reference datasets such as the Human Primary Cell Atlas. This method helps identify broad cell categories (e.g., MSCs, T cells) based on transcriptional similarity across tissues.

The result is visualized in `umap_clusters_celltype.png`.

---

## 📌 NPC Subtype Annotation via Signature Score Enrichment (AUCell)

To refine cell type annotation beyond global references, we applied **AUCell** to perform signature score enrichment using NPC subtype marker genes reported by Ji Tu *et al.*. This approach quantifies the activation level of each subtype signature per cell. Unlike tsne_clusters_celltype.png, which shows general cell types inferred from reference datasets (e.g., SingleR), tsne_npc_subtypes_auc.png reflects functional NPC subtypes based on marker gene enrichment, offering tissue-specific insights.

The result is visualized in `umap_npc_subtypes_auc.png`.

---

## 🔍 Key Differences Between Annotation Strategies

| Aspect | 🌐 **Global Reference Annotation (SingleR)** | 📌 **NPC Subtype Annotation via Signature Score Enrichment (AUCell)** |
|--------|---------------------------------------------|------------------------------------------------------------------------|
| **Goal** | Assign broad cell type labels using a global reference dataset | Quantify enrichment of known NPC subtype signatures |
| **Reference** | Global references (e.g., Human Primary Cell Atlas, Blueprint) | NPC subtype marker genes from Ji Tu *et al.* |
| **Resolution** | General (e.g., MSCs, T cells, monocytes) | Fine-grained (e.g., HT-CLNP, FibroNPCs, Effector NPCs) |
| **Method** | Correlation with reference expression profiles | AUCell AUC scoring over ranked gene expression |
| **Output** | Discrete cell type labels per cell | Continuous enrichment scores per cell per signature |
| **Best for** | Cross-tissue or pan-cell type comparisons | Tissue-specific functional annotation of IVD cells |

## Modules

### `01_qc_filtering.R`
- Performs **initial quality control and filtering** using Seurat.
- Filters cells based on gene count, UMI count, and mitochondrial gene percentage.
- Generates violin plots and scatter plots to visualize QC thresholds.

### `02_normalization_clustering.R`
- Conducts **data normalization, scaling, and clustering** of cells.
- Identifies variable features, performs PCA and UMAP, and computes neighborhood graph.
- Clusters cells and generates UMAP plots for visual inspection of cluster structure.

### `03_annotation_markers.R`
- Identifies **cluster-specific marker genes** using differential expression.
- Supports manual and automated **cell type annotation** (e.g., using SingleR).
- Exports top markers and generates dot plots and heatmaps for marker expression.

### `04_trajectory_monocle.R`
- Applies **Monocle3 to infer pseudotime trajectories** among specified clusters.
- Constructs principal graph and orders cells along a learned developmental axis.
- Visualizes pseudotime progression and trajectory branches in UMAP space.

### `05_cellchat_interaction.R`
- Runs **CellChat ligand-receptor interaction analysis** on selected NPC subpopulations.
- Computes overexpressed signaling genes and interaction probabilities.
- Generates circle plots and network heatmaps to explore signaling dynamics.

### `06_cytotrace.R`
- Performs **stemness inference** using CytoTRACE.
- Adds CytoTRACE scores to Seurat object and visualizes differentiation potential.

### `07_scenic.R`
- Runs **SCENIC transcription factor network analysis**.
- Outputs regulon activity matrix and plots regulon landscape in 2D embedding.

### `08_gsea.R`
- Conducts **GSEA pathway enrichment** using `fgsea` with Hallmark gene sets.
- Identifies key signaling changes across IVDD severity grades.

### `09_auc_score.R`
- Uses **AUCell** to compute per-cell enrichment of selected gene sets (e.g., SASP).
- Adds AUC scores to Seurat metadata for downstream visualization.

### `10_subset_immune.R`
- Subsets immune-related cell types (e.g., macrophages, T cells, G-MDSCs).
- Re-normalizes, re-clusters, and saves immune subset Seurat object for specialized analysis.

## Prerequisites

All scripts assume a pre-processed `Seurat` object (`seurat_obj`) with:
- Normalized RNA assay (`RNA`)
- Cluster assignments stored in `Idents(seurat_obj)`
For GSE165722 dataset
- IVDD grade annotations where relevant

## Notes

- Scripts can be executed independently or chained as needed.
- Output figures are generated for each module.
- Adjust cluster/marker names according to your dataset annotations.


## 📁 Folder Structure

```
SingleCellDesk/
├── data/                     # Input data (10x count matrix)
├── results/                  # Output Seurat objects, plots, and tables
├── scripts/                  # Modular R scripts for each analysis step
├── paper/                    # JOSS paper and references
├── tests/                    # Unit tests using testthat
├── install.R                 # R dependency installer
├── DESCRIPTION               # Package metadata
├── .github/                  # GitHub Actions CI workflow
└── SingleCellDesk.Rproj      # RStudio project file
```

## 🚀 Getting Started

1. Clone the repository:
```bash
git clone https://github.com/your_username/SingleCellDesk.git
cd SingleCellDesk
```

2. Install R packages:
```r
source("install.R")
```

3. Run the full analysis workflow:
```r
source("scripts/01_qc_filtering.R")
source("scripts/02_normalization_clustering.R")
source("scripts/03_annotation_markers.R")
source("scripts/04_trajectory_monocle.R")
source("scripts/05_cellchat_interaction.R")
source("scripts/06_cytotrace.R")
source("scripts/07_scenic.R")
source("scripts/08_gsea.R")
source("scripts/09_auc_score.R")
source("scripts/10_subset_immune.R")
```

## 🧪 Unit Testing

Run tests to ensure each script performs as expected:
```r
devtools::test()
```

## 📖 Example Dataset

To demonstrate the pipeline, we provide compatibility with GSE165722 (human NP scRNA-seq) and include a mock 10x output folder in `/data`.

## 📥 Loading Your Data

We provide two dedicated loader scripts depending on your data format:

### A. For 10x Genomics Data (e.g., Cell Ranger output folders)

Use:
```r
source("scripts/00_load_all_samples_for_10x_Genomics_data.R")
```

Expected folder layout (one per sample):
```
data/
└── sample1/
    └── filtered_feature_bc_matrix/
        ├── matrix.mtx.gz
        ├── barcodes.tsv.gz
        └── features.tsv.gz
```

### B. For Matrix Format like GSE165722 (TSV + cell name mapping)

Use:
```r
source("scripts/00_load_all_samples_for_GSE165722_matrix_format.R")
```

Expected files per sample:
- `SampleX.counts.tsv.gz`: Count matrix with short column names (e.g. C1, C2, ...)
- `SampleX.cellname.txt.gz`: Two-column mapping file with real cell barcodes

Data source citation:

> J Tu, W Li, S Yang, P Yang, Q Yan, S Wang, K Lai, X Bai, C Wu, W Ding, J Cooper‐White, A Diwan, C Yang, H Yang, J Zou (2021). Single‐Cell Transcriptome Profiling Reveals Multicellular Ecosystem of Nucleus Pulposus during Degeneration Progression. Advanced Science 9(3). Impact Factor: 15.1 (2022)

Output: a list of Seurat objects saved to `results/seurat_list_all_samples.rds`.

## 📄 License

MIT License © Kaitao Lai

## 📬 Citation

If you use SingleCellDesk in your research, please cite the associated JOSS paper (under review). You can also cite the software repository:

> K Lai (2024). *SingleCellDesk: A Modular Toolkit for Downstream Single-Cell RNA-seq Analysis in R*. Journal of Open Source Software (under review). https://github.com/biosciences/SingleCellDesk