# Multi-Cohort Transcriptomic Stratification and Target Safety Profiling in Renal Cell Carcinoma (RCC)

## Executive Summary
This translational bioinformatics pipeline characterizes intermediate molecular phenotypes in Renal Cell Carcinoma (RCC) and executes cross-cohort target safety filtering for preclinical drug discovery frameworks. By integrating raw patient tumor RNA-seq matrices from **The Cancer Genome Atlas (TCGA)** with healthy baseline tissue metrics from the **Genotype-Tissue Expression (GTEx) V10 dataset**, I successfully isolated a high-risk mixed KIRC-KIRP patient cohort and established an automated toxicological screening window for candidate biomarkers.

---

## Scientific Validation & Figure Replication

A core objective of this study was to evaluate the biological robustness of the 174-gene expression signature developed by *Büttner et al. (2022) Genome Medicine*. By transitioning the unsupervised clustering pipeline into a dual-cohort target framework, I successfully replicated the spatial dimensionality reduction boundaries separating distinct renal cell carcinoma lineages.

### Figure Comparison: Transcriptomic Subtype Separation

| Original Publication Layout (Büttner et al.) | Replicated Pipeline Output (This Study) |
| :---: | :---: |
| ![Büttner Fig 2A](reports/publication_fig2a.png) | ![Replicated PCA](reports/my_replicated_pca.png) |


### Comparative Analysis & Insights

* **Lineage Discrimination:** In complete alignment with the source publication, Principal Component 1 (PC1) cleanly partitions tumors originating from distal cell types (Chromophobe RCC / KICH) from those arising from proximal tubule structures (Clear Cell / KIRC and Papillary / KIRP).
* **The Hidden Cluster Shift (Cluster 3 Isolation):** While the stable cell lineages group exactly as published, my unsupervised complete-linkage hierarchical clustering (`hclust`) isolated an additional, intermediate population (**Cluster 3**). This subset exhibits an ambiguous, mixed KIRC-KIRP expression phenotype that correlates clinically with aggressive progression and poor survival windows.

---

## Analytical Workflow & Methodology

```text
[Raw Count Matrices] ──> [Inverse Log2 Transformation] ──> [VST Homoscedasticity]
                                                                  │
[Target Safety Screen] <── [GTEx Matrix Stream-Parsing] <── [DESeq2 GLM Contrasts]

Data Ingestion & Back-Transformation: Restored log2-normalized count matrices to raw integer vectors using an arithmetic inverse transformation to satisfy negative binomial distribution models in DESeq2.

Dimensionality Reduction & Unsupervised Clustering: Utilized Variance-Stabilizing Transformations (VST) alongside complete-linkage agglomerative clustering (hclust) across the 174 target features to map patient distances.

Differential Expression Modeling: Constructed rigorous generalized linear models (GLMs) contrasting Mixed_Phenotype (Cluster 3) vs. Stable_Subtype populations using Wald tests, filtering for high-confidence drivers ($p_{adj} < 0.01$, $\log_2\text{FC} > 1.5$).

Cross-Cohort Normal Tissue Screening: Programmatically parsed compressed GTEx V10 data using data.table::fread() and R.utils to evaluate median and maximum expression ranges across normal vital organ architectures, ensuring a clear therapeutic window.

Repository Architecture
The repository follows layout separating execution logic from final deliverables:
├── scripts/          # Production-grade Quarto (.qmd) computational pipeline
├── reports/          # Standalone, reproducible compiled HTML executive reports and figures
├── .gitignore        # Data-integrity file path exclusion filters
└── README.md         # Documentation

Computational Stack & Dependencies
Language: R (v4.x)
Core Bioinformatic Frameworks: DESeq2, EnhancedVolcano, ComplexHeatmap
Data Engineering Utilities: data.table, dplyr, tidyr, R.utils