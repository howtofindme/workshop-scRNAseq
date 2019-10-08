---
title: "Seurat"
author: "Åsa Björklund  &  Paulo Czarnewski"
date: "Sept 13, 2019"
output:
  html_document:
    self_contained: true
    highlight: tango
    df_print: paged
    toc: yes
    toc_float:
      collapsed: false
      smooth_scroll: true
    toc_depth: 3
    keep_md: yes
  html_notebook:
    self_contained: true
    highlight: tango
    df_print: paged
    toc: yes
    toc_float:
      collapsed: false
      smooth_scroll: true
    toc_depth: 3
    keep_md: yes
---

***
# Data Integration

In this tutorial we will look at different ways of integrating multiple single cell RNA-seq datasets. We will explore two different methods to correct for batch effects across datasets. We will also look at a quantitative measure to assess the quality of the integrated data. Seurat uses the data integration method presented in Comprehensive Integration of Single Cell Data, while Scran and Scanpy use a mutual Nearest neighbour method (MNN). Below you can find a list of the most recent methods for single data integration:

Markdown | Language | Library | Ref
--- | --- | --- | ---
CCA | R | Seurat | [Cell](https://www.sciencedirect.com/science/article/pii/S0092867419305598?via%3Dihub)
MNN | R/Python | Scater/Scanpy | [Nat. Biotech.](https://www.nature.com/articles/nbt.4091)
Conos | R | conos | [Nat. Methods](https://www.nature.com/articles/s41592-019-0466-z?error=cookies_not_supported&code=5680289b-6edb-40ad-9934-415dac4fdb2f)
Scanorama | Python | scanorama | [Nat. Biotech.](https://www.nature.com/articles/s41587-019-0113-3)

Let's first load necessary libraries and the data saved in the previous lab.


```r
suppressMessages(require(Seurat))
```

```
## Warning: package 'Seurat' was built under R version 3.5.2
```

```r
suppressMessages(require(cowplot))
```

```
## Warning: package 'cowplot' was built under R version 3.5.2
```

```
## Warning: package 'ggplot2' was built under R version 3.5.2
```

```r
suppressMessages(require(ggplot2))

alldata <- readRDS("data/3pbmc_qc_dm.rds")
print(names(alldata@reductions))
```

```
## [1] "PCA_on_RNA"  "TSNE_on_RNA" "UMAP_on_RNA"
```

We split the combined object into a list, with each dataset as an element. We perform standard preprocessing (log-normalization), and identify variable features individually for each dataset based on a variance stabilizing transformation ("vst").


```r
alldata.list <- SplitObject(alldata, split.by = "orig.ident")

for (i in 1:length(alldata.list)) {
    alldata.list[[i]] <- NormalizeData(alldata.list[[i]], verbose = FALSE)
    alldata.list[[i]] <- FindVariableFeatures(alldata.list[[i]], selection.method = "vst", nfeatures = 2000,verbose = FALSE)
}
```

We identify anchors using the FindIntegrationAnchors function, which takes a list of Seurat objects as input.


```r
alldata.anchors <- FindIntegrationAnchors(object.list = alldata.list, dims = 1:30)
```

```
## Computing 2000 integration features
```

```
## Scaling features for provided objects
```

```
## Finding all pairwise anchors
```

```
## Running CCA
```

```
## Merging objects
```

```
## Finding neighborhoods
```

```
## Finding anchors
```

```
## 	Found 2185 anchors
```

```
## Filtering anchors
```

```
## 	Retained 1907 anchors
```

```
## Extracting within-dataset neighbors
```

```
## Running CCA
```

```
## Merging objects
```

```
## Finding neighborhoods
```

```
## Finding anchors
```

```
## 	Found 2265 anchors
```

```
## Filtering anchors
```

```
## 	Retained 1981 anchors
```

```
## Extracting within-dataset neighbors
```

```
## Running CCA
```

```
## Merging objects
```

```
## Finding neighborhoods
```

```
## Finding anchors
```

```
## 	Found 3070 anchors
```

```
## Filtering anchors
```

```
## 	Retained 2702 anchors
```

```
## Extracting within-dataset neighbors
```

We then pass these anchors to the IntegrateData function, which returns a Seurat object.


```r
alldata.int <- IntegrateData(anchorset = alldata.anchors, dims = 1:30, new.assay.name = "CCA")
```

```
## Merging dataset 1 into 3
```

```
## Extracting anchors for merged samples
```

```
## Finding integration vectors
```

```
## Finding integration vector weights
```

```
## Integrating data
```

```
## Merging dataset 2 into 3 1
```

```
## Extracting anchors for merged samples
```

```
## Finding integration vectors
```

```
## Finding integration vector weights
```

```
## Integrating data
```

We can observe that a new assay slot is now created under the name `CCA`.


```r
names(alldata.int@assays)
```

```
## [1] "RNA" "CCA"
```

After running IntegrateData, the Seurat object will contain a new Assay with the integrated (or ‘batch-corrected’) expression matrix. Note that the original (uncorrected values) are still stored in the object in the “RNA” assay, so you can switch back and forth. We can then use this new integrated matrix for downstream analysis and visualization. Here we scale the integrated data, run PCA, and visualize the results with UMAP and TSNE. The integrated datasets cluster by cell type, instead of by technology.


```r
#Run Dimensionality reduction on integrated space
alldata.int <- ScaleData(alldata.int, verbose = FALSE,assay = "CCA")
alldata.int <- RunPCA(alldata.int, npcs = 30, verbose = FALSE, assay = "CCA",reduction.name = "PCA_on_CCA")
alldata.int <- RunUMAP(alldata.int, reduction = "PCA_on_CCA", dims = 1:30,reduction.name = "UMAP_on_CCA")
alldata.int <- RunTSNE(alldata.int, reduction = "PCA_on_CCA", dims = 1:30,reduction.name = "TSNE_on_CCA")
```

We can now plot the count and the integrated space reduced dimensions.


```r
plot_grid(ncol = 3,
  DimPlot(alldata, reduction = "PCA_on_RNA", group.by = "orig.ident"),
  DimPlot(alldata, reduction = "TSNE_on_RNA", group.by = "orig.ident"),
  DimPlot(alldata, reduction = "UMAP_on_RNA", group.by = "orig.ident"),
  
  DimPlot(alldata.int, reduction = "PCA_on_CCA", group.by = "orig.ident"),
  DimPlot(alldata.int, reduction = "TSNE_on_CCA", group.by = "orig.ident"),
  DimPlot(alldata.int, reduction = "UMAP_on_CCA", group.by = "orig.ident")
)
```

![](seurat_03_integration_compiled_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

Let's plot some marker genes for different celltypes onto the embedding. Some genes are:

Markers	| Cell Type
--- | ---
CD3E	| T cells
CD3E CD4	| CD4+ T cells
CD3E CD8A	| CD8+ T cells
GNLY, NKG7	| NK cells
MS4A1	| B cells
CD14, LYZ, CST3, MS4A7	| CD14+ Monocytes
FCGR3A, LYZ, CST3, MS4A7	| FCGR3A+  Monocytes
FCER1A, CST3 | DCs


```r
FeaturePlot(alldata.int, reduction = "UMAP_on_CCA",dims = 1:2,features = c("CD3E","CD4","CD8A","NKG7","GNLY","MS4A1","CD14","LYZ","MS4A7","FCGR3A","CST3","FCER1A"),ncol = 4,order = T)
```

![](seurat_03_integration_compiled_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

Finally, lets save the integrated data for further analysis.


```r
saveRDS(alldata,"data/integrated_3pbmc.rds")
```





