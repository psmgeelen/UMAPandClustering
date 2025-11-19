# **UMAP and Clustering: Bias, Instability, and Experimental Analysis**

This repository contains a reproducible experiment that examines how UMAP distortions affect downstream clustering. The notebook runs controlled tests across multiple synthetic datasets, several UMAP configurations, and three clustering algorithms (DBSCAN, OPTICS, HCA).
The goal: show how UMAP can create **self-affirming bias** and how clustering hyperparameters change the severity of the error.

The project uses:

* **uv** for environment management
* **Jupyter Notebook** for running the experiments
* **scikit-learn**, **UMAP**, **matplotlib**, **seaborn**, and **scipy** for analysis and visualization

All produced figures are stored at the project root.


## **Environment Setup (uv)**

Install dependencies:

```bash
uv sync
```

Start a Jupyter session:

```bash
uv run jupyter notebook
```

Open `UMAPandClustering.ipynb` to run all analysis cells.


## **Notebook Overview**

The notebook contains four main experiment blocks. Each block produces visuals or quantitative metrics.

### **1. Dataset Generation + UMAP Distortion Visualization**

The first section:

* builds three high-dimensional datasets
* adds strong structured noise (cluster invasion)
* embeds each dataset with three UMAP configurations
* evaluates DBSCAN on each embedding
* visualizes the results in a 3×3 grid (`ClusterFailures.png`)

This block shows how UMAP **collapses, exaggerates, or reshapes** cluster boundaries depending on `min_dist`. DBSCAN reacts strongly to these distortions, producing very different cluster counts.

### **2. Hyperparameter Grid Search + 3D Error Mapping**

The second section:

* creates one anisotropic dataset with structured noise
* embeds it with multiple UMAP `min_dist` values
* runs DBSCAN, OPTICS, and HCA across wide hyperparameter ranges
* stores the deviation from the true cluster count
* generates a 3D scatter plot (`ClusterFailures_3Dplot.png`)

This experiment maps how clustering error grows or shrinks as both UMAP distance compression and algorithm-level hyperparameters change.

Each algorithm occupies a unique region in error space:

* DBSCAN stabilizes with higher `eps`
* OPTICS destabilizes with higher `max_eps`
* HCA becomes more stable with larger distance thresholds

### **3. Missed-Cluster Distribution Analysis**

The third section aggregates all deviations into a single DataFrame and plots a distribution of errors for each algorithm (`ClusterFailures_missed_clustering.png`).

This part highlights:

* DBSCAN produces the lowest error distribution
* OPTICS shows frequent over-merging
* HCA remains stable but sensitive to threshold selection

### **4. Correlation Analysis Between Hyperparameters and Cluster Error**

The final block calculates Pearson and Spearman correlations per algorithm to quantify whether hyperparameters systematically influence accuracy.

The output correlation table exposes clear trends:

* higher DBSCAN `eps` → fewer spurious clusters
* higher OPTICS `max_eps` → more merging
* higher HCA threshold → more stability

This step ties the geometry distortion from UMAP to measurable clustering behavior.


## **Outputs**

Running the notebook generates:

* `ClusterFailures.png` — UMAP behavior across datasets
* `ClusterFailures_3Dplot.png` — 3D hyperparameter vs. error landscape
* `ClusterFailures_missed_clustering.png` — error distributions


## **Purpose**

The complete experiment demonstrates that:

* UMAP introduces distortions that clustering algorithms interpret as meaningful structure
* hyperparameters can reduce—but never eliminate—these distortions
* using UMAP for clustering creates **systematic, non-obvious bias**

Clustering after UMAP is not inherently wrong, but it is never neutral.
This repository highlights that bias.