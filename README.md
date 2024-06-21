## BioTools HELP Document

**BioTools: A Comprehensive Bioinformatics Web Application Based on R Shiny Framework**


### Introduction

[BioTools Web Tool](https://tool.gigaomics.com/BioTools/) is a bioinformatics analysis workflow tool developed based on the R Shiny framework. It currently includes modules for phenotype analysis, single-omics analysis, microbiome functional enrichment analysis, DEGs enrichment analysis, and gene set enrichment analysis (GSEA).

- **Maintainer**: Chengrui Wang
- **Contact Email**: chengrui.wang@gzucm.edu.cn
- **Version**: 2024.05.23


### Analysis Modules

---

#### Phenotype Analysis

*(Input file format: rows are samples, columns are features)*

After uploading the file, ensure to set which column to use as row names (`Column as rowname`), the file delimiter type (Comma, Tab, Space). The check.name parameter checks the variable name (here, the symbols such as `+-: ()` are replaced with `_`, mainly because special symbols need to be avoided where formulas are used. And specify which variables are factor types (`Specify which variables are factor types, one per line`), the normalization method (none, Box-cox normalization using powerTransform package, bestNormalize for optimal method normalization), and provide sample data for download.



The phenotype analysis module allows users to browse data, summarize data using {*skimr*}, specify factor variables, normalize numerical variables using {*powerTransform* and *bestNormalize*} and their correlation heatmaps using {*linkET*}, and perform descriptive statistics and logistic regression analysis using {*autoReg*}.

The specific parts of the phenotype analysis module include:

- **data**: Upload files, specify delimiters, and row names.
  - **Data**: Display the raw data, showing the first 10 rows by default, with options to display 10, 25, 50, or 100 rows.
  - **Data skim**: Provides a quick overview of the data using the R package {*skimr::skim*}, showing means, standard deviations, quartiles, and distribution thumbnails for continuous variables, and counts for factor/categorical variables.
- **Description**: Generates grouped descriptive statistics tables using the {*autoReg*} package's *gaze* function. It employs various methods such as t-test, ANOVA, Kruskal-Wallis, chi-squared, and Fisher's exact tests for group difference testing, allowing users to input custom formulas and select different stratification strategies for continuous/categorical variables. Detailed instructions can be accessed via the see detail link.
- **Logistic regression**: Automatically performs univariate, multivariate, and stepwise logistic regression analysis using the {*autoReg*} package's *autoReg* function. It allows users to input custom formulas and other optional parameters. Results include logistic model summaries, univariate and multivariate regression results, confusion matrices, ROC curve plots, and nomograms. Detailed instructions can be accessed via the see detail link.
- **Correlation**: Generates correlation heatmaps for numerical variables using the {*linkET*} package. It supports methods such as Spearman, Pearson, and Kendall, and allows specifying grouping variables to show group-specific correlations. If the Single-omics module's abundance table is uploaded and the correlative with Single-Omics option is selected, the Correlation plot module will display correlations between phenotypes and omics data, and can perform Mantel tests. It is recommended to filter for differential features to avoid slow performance due to too many variables.

---

#### Abundance Matrix Analysis (Single-omics)

*(Input file format: rows are features, columns are samples)*

After uploading the file, ensure to select the file delimiter type (Comma, Tab, Space). Use the **Mean cutoff** parameter to filter rows based on mean value thresholds, and the **Prevalence cutoff** parameter to filter rows based on non-zero prevalence thresholds. These thresholds help remove low abundance and low prevalence rows. Checking the **Re-scaled** option will rescale each column to relative values summing to 100. The **is.metaphlan_name** option is for cases where row names follow the Metaphlan format (k__|p__|o__|c__|f__|g__|s__|t__), allowing selection of different taxonomy levels. The check.name parameter checks the variable name (here, the symbols such as `+-: ()` are replaced with `_`, mainly because special symbols need to be avoided where formulas are used. Sample data is provided for download.

The **Grouping parameters** sub-module is used for grouped analysis during abundance data analysis. You can upload the corresponding grouping file and select grouping and/or stratification variables in the **Grouping Variable** and **Stratificate Variable** sub-modules. These selections are flexible and can be interchanged to explore your data. Once a grouping variable is selected, you can customize subgroup selections and their order in the **Group rank** section, with similar options for stratification variables. Ensure that the column names of the abundance matrix file and the row names of the phenotype file are sample names and remain consistent.

The **microeco taxa table** sub-module is an optional feature allowing users to provide taxonomy files for different levels of microbial abundance tables, similar to the Metaphlan format. For 16s data analysis, this file is required.

The **ggplot2 parameters** sub-module is a panel for adjusting ggplot2 plotting parameters, currently providing options to adjust the font size of images.

Below are the details of each analysis section in the Abundance Matrix Analysis (Single-omics) module:

- **Raw data**: Upload a single-omics abundance matrix file where each row represents a feature (e.g., gene, bacteria, metabolite) and each column represents a sample. The results displayed are similar to those in the **Phenotype/data** module. The Data skim results are also similar to those in the **Phenotype/Data skim** module.

- **Composition**: Displays the abundance of the top N features (default is top 10) as stacked bar charts, with options to select the aggregation method (mean or median). If grouping information is uploaded, it will also display stacked bar charts by group/stratification.

- **alpha diversity**: Uses the {*vegan*} package to calculate alpha diversity indices (including species richness, Shannon index, Simpson index, Chao index) and displays the results as box plots, violin plots, and dot plots. If relative abundance data is uploaded, the relative abundance values are multiplied by 10,000 and rounded for alpha diversity index calculation. If grouping information is uploaded, it will display the results by group and perform statistical tests for group differences, with options to select the statistical test (wilcox.test or t.test) and display specific p-values (signif_label).

- **Dimension reduction**:
    - **PCA**: Uses the {prcomp} function to perform Principal Component Analysis on the given data matrix and displays the results using the {factoextra} package, including PCA biplots and the contribution of each feature to the first two principal components. Group information can also be displayed if uploaded.
    - **PCoA**: Performs Principal Coordinate Analysis on a distance matrix calculated using {vegan::vegdist} and displays the results using {ape::pcoa} and {vegan::adonis2}, with options to show sample names and ellipse confidence intervals. Pairwise PERMANOVA tests are performed using {pairwiseAdonis::pairwise.adonis}.
    - **CPCoA**: Constrained Principal Coordinates Analysis, which adds constraints to PCoA to explain between-group differences considering custom grouping information. This analysis requires at least three groups and is implemented using the {amplicon:beta_cpcoa} function.
    - **PLS-DA**: Partial Least Squares Discriminant Analysis combines PLS regression and discriminant analysis to identify components that differentiate groups. This analysis uses the {ropls:opls} function and includes PCA and OPLS-DA results and reports.

- **Heatmap**: Uses the {pheatmap} package to draw a clustering heatmap of the abundance matrix. If grouping information is uploaded, it will display column annotations. For Metaphlan format files, it also shows the grouping of microbial levels. Other optional parameters include displaying row/column names, clustering rows/columns, log transformation, centralizing by row/column, distance calculation methods, clustering methods, and selecting specific features to display.

- **diff test**: Differential testing:
    - **Simply Test**: Performs simple group differential testing using t.test/wilcox.test for pairwise tests and kruskal.test for multi-group tests.
    - **fastANCOM**: Uses {fastANCOM} to analyze microbiome data, identifying significant microbial differences between groups, with pre-processing steps to detect structural zeros, remove outliers, and delete rare OTUs or low-depth samples.
    - **Abu boxplot**: Displays the distribution of a specific feature as a boxplot, showing group differences if grouping information is uploaded.

- **DESeq2**: Estimates variance-mean dependence from high-throughput sequencing data and tests for differential expression using a negative binomial model. Input data should be integer counts, with an option to convert to integers (as.Integer). Required parameters include the `design formula` (e.g., `~Group`) and `contrast` for group comparison (e.g., [group variable, treatment group, control group]). The analysis includes differential testing results, volcano plots, and expression abundance of specific features.

- **microeco**: A package for rapid modular analysis of microbiome data, supporting 16s and metagenomic data. It includes:
    - **microeco_abund**: Abundance composition analysis, requiring a taxonomy file or Metaphlan format. Displays the abundance of the top 10 taxa and allows customization of facets and aggregation methods. Refer to `Chapter 4 Composition-based class` in the official documentation.
    - **microeco_diversity**: Species diversity analysis, including alpha and beta diversity. Alpha diversity includes selecting diversity indices and difference testing methods. Beta diversity includes selecting diversity indices, ordination methods, and plot types. Refer to `Chapter 5 Diversity-based class` in the official documentation.
    - **microeco_diff**: Differential abundance analysis, including methods selection for difference testing, p-value adjustment methods, and taxa level selection. Cladogram plots require `taxa_level=all`. Refer to `6.1 trans_diff class` in the official documentation.

- **predictModel**: Uses the Boruta package for feature selection and the caret package for building prediction models. Important parameters include the `formula` for model prediction (e.g., `Group~.`), the default random forest (rf) model, and data preprocessing options to remove low-variance, highly correlated, and collinear variables. Cross-validation parameters include 5-fold cross-validation repeated 5 times, with 75% of samples for training and 25% for testing, adjustable by the user. Suitable for binary classification outcomes, with recommendations for feature selection using Boruta and tuning parameters for model optimization. Results include accuracy and kappa values, feature importance, SHAP values for model interpretability, and confusion matrix and ROC curve for the test set. Independent validation datasets can also be uploaded.

---

#### Microbiome Functional Enrichment Analysis

*(Input format: gene list, one per line)*

The analysis uses the R package [MicrobiomeProfiler](https://bioconductor.org/packages/release/bioc/vignettes/MicrobiomeProfiler/inst/doc/MicrobiomeProfiler.html), a functional enrichment tool for microbiome data based on ClusterProfiler. It provides several enrichment functions:

- **enrichKO**: KO IDs enrichment analysis for microbiome data.
- **enrichModule**: KEGG Module enrichment analysis for microbiome data.
- **enrichCOG**: COG enrichment analysis for microbiome data.
- **enrichHMDB**: HMDB IDs enrichment analysis for microbiome metabolites.
- **enrichMBKEGG**: KEGG IDs enrichment analysis for microbiome metabolites.
- **enrichSMPDB**: smpdb IDs enrichment analysis for microbiome metabolites.
- **enrichMDA**: Microbiome-disease association enrichment analysis.

All analyses support user-provided background gene sets via the `Select Universe Gene Set` parameter, and sample data is provided for quick start.

---

#### DEGs Enrichment Analysis

*(Input format: gene list, one per line)*

The analysis uses the R package [clusterProfiler](https://yulab-smu.top/biomedical-knowledge-mining-book/):

- **enrichGO**: Gene Ontology (GO) enrichment analysis using {*clusterProfiler::enrichGO*} for the given gene list, with results displayed as Dotplot/barplot/treeplot/emapplot/cnetplot. Supported species: ("hsa", "mmu", "rno", "dre").
- **enrichKEGG**: KEGG pathway enrichment analysis using {*clusterProfiler::enrichKEGG*} for the given gene list, with results displayed as Dotplot/barplot/treeplot/emapplot/cnetplot. Supported species: ("hsa", "mmu", "rno", "dre").
- **enrichDO**: Disease Ontology (DO) enrichment analysis using {*DOSE::enrichDO*} for the given gene list, with results displayed as Dotplot/barplot/treeplot/emapplot/cnetplot. Supported species: ("hsa", "mmu").

---

#### Gene Set Enrichment Analysis (GSEA)

*(Input file format: ranked gene list with values, two columns without header, one for gene names, one for values such as FoldChange or other scores)*

- **gseGO**: Gene Ontology (GO) gene set enrichment analysis using {*clusterProfiler::gseGO*} for the given gene list, with results displayed as Dotplot/barplot/treeplot/emapplot/cnetplot. Supported species: ("hsa", "mmu", "rno", "dre").
- **gseKEGG**: KEGG pathway gene set enrichment analysis using {*clusterProfiler::gseKEGG*} for the given gene list, with results displayed as Dotplot/barplot/treeplot/emapplot/cnetplot. Supported species: ("hsa", "mmu", "rno", "dre").
- **gseDO**: Disease Ontology (DO) gene set enrichment analysis using {*DOSE::gseDO*} for the given gene list, with results displayed as Dotplot/barplot/treeplot/emapplot/cnetplot. Supported species: ("hsa", "mmu").
- **GSEA plot**: Uses {*enrichplot::gseaplot2*} to draw GSEA plots, showing the top 5 gene sets in the upper plot and specific gene sets in the lower plot by default.

---

#### Hidden surprise feature

Click the magic wand in the bottom right corner of the [BioTools](https://tool.gigaomics.com/BioTools/) website to discover a hidden surprise feature by the AI assistant 😊