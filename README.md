# *GDCRNATools* - An R package for downloading, organizing, and integrative analyzing lncRNA, mRNA, and miRNA data in GDC


# Introduction

The [Genomic Data Commons (GDC)](https://portal.gdc.cancer.gov/) maintains standardized genomic, clinical, and biospecimen data from National Cancer Institute (NCI) programs including [The Cancer Genome Atlas (TCGA)](https://tcga-data.nci.nih.gov/) and [Therapeutically Applicable Research To Generate Effective Treatments (TARGET)](https://ocg.cancer.gov/programs/target), It also accepts high quality datasets from non-NCI supported cancer research programs, such as genomic data from the [Foundation Medicine](https://www.foundationmedicine.com/).

`GDCRNATools` is an R package which provides a standard, easy-to-use and comprehensive pipeline for downloading, organizing, and integrative analyzing RNA expression data in the GDC portal with an emphasis on deciphering the lncRNA-mRNA related ceRNA regulatory network in cancer.

Many analyses can be perfomed using `GDCRNATools`, including differential gene expression analysis ([limma](http://bioconductor.org/packages/release/bioc/html/limma.html), [edgeR](http://bioconductor.org/packages/release/bioc/html/edgeR.html), and [DESeq2](http://bioconductor.org/packages/release/bioc/html/DESeq2.html)), univariate survival analysis (CoxPH and KM), competing endogenous RNA network analysis (hypergeometric test, Pearson correlation analysis, regulation similarity analysis, sensitivity Pearson partial  correlation), and functional enrichment analysis(GO, KEGG, DO). Besides some routine visualization methods such as volcano plot, scatter plot, and bubble plot, etc., three simple shiny apps are developed in GDCRNATools allowing users visualize the results on a local webpage.

This user-friendly package allows researchers perform the analysis by simply running a few functions and integrate their own pipelines such as molecular subtype classification, [weighted correlation network analysis (WGCNA)](https://labs.genetics.ucla.edu/horvath/CoexpressionNetwork/Rpackages/WGCNA/), and TF-miRNA co-regulatory network analysis, etc. into the workflow easily.


# Installation
`GDCRNATools` is now under review in Bioconductor. Users can install the package locally.

## On Windows system
* Download the package [GDCRNATools_0.99.0.tar.gz](https://github.com/Jialab-UCR/Jialab-UCR.github.io/blob/master/GDCRNATools_0.99.0.tar.gz)
* Make sure you have [Rtools](https://cran.r-project.org/bin/windows/Rtools/) installed
* ADD R and Rtools to the Path Variable on the Environment Variables panel, including

  c:\program files\Rtools\bin

  c:\program files\Rtools\gcc-4.6.3\bin

  c:\program files\R\R.3.x.x\bin\i386

  c:\program files\R\R.3.x.x\bin\x64 

* Open a command prompt. Type R CMD INSTALL GDCRNATools_0.99.0.tar.gz

## On Linux and Mac systems
Run the following command in R
```R
install.packages('GDCRNATools_0.99.0.tar.gz', repos = NULL, type='source')
```

If `GDCRNATools` cannot be installed due to the lack of dependencies, please run the following code ahead to install those pacakges simutaneously or separately:
```R
source("https://bioconductor.org/biocLite.R")

### install packages simutaneously ###
biocLite(c('limma', 'edgeR', 'DESeq2', 'clusterProfiler', 'DOSE', 'org.Hs.eg.db', 'biomaRt', 'BiocParallel'))
install.packages(c('shiny', 'jsonlite', 'rjson', 'survival', 'survminer', 'ggplot2', 'gplots', 'Hmisc'))

### install packages seperately ###
biocLite('limma')
biocLite('edgeR')
biocLite('DESeq2')
biocLite('clusterProfiler')
biocLite('DOSE')
biocLite('org.Hs.eg.db')
biocLite('biomaRt')
biocLite('BiocParallel')

install.packages('shiny')
install.packages('jsonlite')
install.packages('rjson')
install.packages('survival')
install.packages('survminer')
install.packages('ggplot2')
install.packages('gplots')
install.packages('Hmisc')
```



# Manual
A simply manual of `GDCRNATools` is available below. Users are also highly recommended to download the comprhensive manual in _.html_ format and view on local computer [GDCRNATools Manual](https://github.com/Jialab-UCR/Jialab-UCR.github.io/blob/master/GDCRNATools_manual.html)




## 1 Data download
> Two methods are provided for downloading Gene Expression Quantification (HTSeq-Counts), Isoform Expression Quantification (BCGSC miRNA Profiling), and Clinical (Clinical Supplement) data:

* Manual download  
Step1: Download [GDC Data Transfer Tool](https://gdc.cancer.gov/access-data/gdc-data-transfer-tool) on the GDC website  
Step2: Add data to the GDC cart, then download manifest file and metadata of the cart  
Step3: Download data using `gdcRNADownload()` function by providing the manifest file  

* Automatic download  
Download [GDC Data Transfer Tool](https://gdc.cancer.gov/access-data/gdc-data-transfer-tool), manifest file, and data automatically by specifying the `project.id` and `data.type` in `gdcRNADownload()` function for RNAseq and miRNAs data, and in `gdcClinicalDownload()` function for clinical data

Users can also download data from GDC using the API method developed in [TCGAbiolinks](https://bioconductor.org/packages/release/bioc/html/TCGAbiolinks.html) or using [TCGA-Assembler](http://www.compgenome.org/TCGA-Assembler/)

### 1.1 Manual download

**1.1.1 Installation of GDC Data Transfer Tool gdc-client**

Download [GDC Data Transfer Tool](https://gdc.cancer.gov/access-data/gdc-data-transfer-tool) from the GDC website and unzip the file

**1.1.2 Download manifest file and metadata from GDC Data Portal**

![](https://github.com/Jialab-UCR/Jialab-UCR.github.io/blob/master/figures/download_rna.PRAD.gif)

**1.1.3 Download data**
```{r rnaseq, eval=FALSE, message=FALSE, warning=FALSE}
####### Download RNAseq data #######
gdcRNADownload(manifest  = 'TCGA-PRAD/TCGA-PRAD.RNAseq.gdc_manifest.2017-11-23T14-40-52.txt',
               directory = 'TCGA-PRAD/RNAseq')

####### Download miRNAs data #######
gdcRNADownload(manifest  = 'TCGA-PRAD/TCGA-PRAD.miRNAs.gdc_manifest.2017-11-22T15-36-57.txt',
               directory = 'TCGA-PRAD/miRNAs')

####### Download Clinical data #######
gdcRNADownload(manifest  = 'TCGA-PRAD/TCGA-PRAD.Clinical.gdc_manifest.2017-11-23T14-42-01.txt',
               directory = 'TCGA-PRAD/Clinical')
```



### 1.2 Automatic download
* `gdcRNADownload()` will download HTSeq-Counts data if `data.type='RNAseq'` and download BCGSC miRNA Profiling data if `data.type='miRNAs'`. `project.id` argument is required to be provided.

* `gdcClinicalDownload()` download clinical data in .xml format automatically by simply specifying the `project.id` argument.

#### 1.2.1 Download RNAseq/miRNAs data

```{r manual, eval=FALSE, message=FALSE, warning=FALSE}
####### Download RNAseq data #######
gdcRNADownload(project.id     = 'TCGA-PRAD', 
               data.type      = 'RNAseq', 
               write.manifest = FALSE,
               directory      = 'TCGA-PRAD/RNAseq')

####### Download miRNAs data #######
gdcRNADownload(project.id     = 'TCGA-PRAD', 
               data.type      = 'miRNAs', 
               write.manifest = FALSE,
               directory      = 'TCGA-PRAD/miRNAs')
```


#### 1.2.2 Download clinical data
```{r manual clinical, eval=FALSE, message=FALSE, warning=FALSE}
####### Download clinical data #######
gdcClinicalDownload(project.id     = 'TCGA-PRAD',  
                    write.manifest = FALSE,
                    directory      = 'TCGA-PRAD/Clinical')
```


## 2 Data organization

### 2.1 Parse metadata

Metadata can be parsed by either providing the metadata file that is downloaded in the data download step, or specifying the `project.id` and `data.type` in `gdcParseMetadata()` function to obtain information of data in the manifest file to facilitate data organization and basic clinical information of patients such as age, stage and gender, etc. for data analysis.


#### 2.1.1 Parse metadata by providing the metadata file
```{r parse meta, message=FALSE, warning=FALSE, eval=FALSE}
####### Parse RNAseq metadata #######
metaMatrix.RNA <- gdcParseMetadata(metafile='TCGA-PRAD/TCGA-PRAD.RNAseq.metadata.2017-11-23T17-23-59.json')

####### Parse miRNAs metadata #######
metaMatrix.MIR <- gdcParseMetadata(metafile='TCGA-PRAD/TCGA-PRAD.miRNAs.metadata.2017-11-23T17-33-55.json')
```

#### 2.1.2 Parse metadata by specifying project.id and data.type
```{r parse meta2, message=FALSE, warning=FALSE, eval=TRUE}
####### Parse RNAseq metadata #######
metaMatrix.RNA <- gdcParseMetadata(project.id = 'TCGA-PRAD',
                                   data.type  = 'RNAseq', 
                                   write.meta = FALSE)
```

```{r parse meta3, message=FALSE, warning=FALSE, eval=TRUE}
####### Parse miRNAs metadata #######
metaMatrix.MIR <- gdcParseMetadata(project.id = 'TCGA-PRAD',
                                   data.type  = 'miRNAs', 
                                   write.meta = FALSE)
```




### 2.2 Filter samples

#### 2.2.1 Filter duplicated samples
Only one sample would be kept if the sample had been sequenced more than once by `gdcFilterDuplicate()`.

```{r filter meta, message=FALSE, warning=FALSE, eval=TRUE}
####### Filter duplicated samples in RNAseq metadata #######
metaMatrix.RNA <- gdcFilterDuplicate(metaMatrix.RNA)
```

```{r filter meta2, message=FALSE, warning=FALSE, eval=TRUE}
####### Filter duplicated samples in miRNAs metadata #######
metaMatrix.MIR <- gdcFilterDuplicate(metaMatrix.MIR)
```



#### 2.2.2 Filter non-Primary Tumor and non-Solid Tissue Normal samples
Samples that are neither Primary Tumor (code: 01) nor Solid Tissue Normal (code: 11) would be filtered out by `gdcFilterSampleType()`.

```{r filter meta3, message=FALSE, warning=FALSE, eval=TRUE}
####### Filter non-Primary Tumor and non-Solid Tissue Normal samples in RNAseq metadata #######
metaMatrix.RNA <- gdcFilterSampleType(metaMatrix.RNA)
```

```{r filter meta4, message=FALSE, warning=FALSE, eval=TRUE}
####### Filter non-Primary Tumor and non-Solid Tissue Normal samples in miRNAs metadata #######
metaMatrix.MIR <- gdcFilterSampleType(metaMatrix.MIR)
```



### 2.3 Merge data

* `gdcRNAMerge()` merges raw counts data of RNAseq to a single expression matrix with rows are *Ensembl id* and columns are *samples*. Total read counts for 5p and 3p strands of miRNAs can be processed from isoform quantification files and then merged to a single expression matrix with rows are *miRBase v21 identifiers* and columns are *samples*.

* `gdcClinicalMerge()` merges clinical data to a dataframe with rows are *patient id* and columns are *clinical traits*. If `key.info=TRUE`, only those most commonly used clinical traits will be reported, otherwise, all the clinical information will be reported.


#### 2.3.1 Merge RNAseq/miRNAs data
```{r merge RNAseq, message=FALSE, warning=FALSE, eval=FALSE}
####### Merge RNAseq data #######
rnaMatrix <- gdcRNAMerge(metadata  = metaMatrix.RNA, 
                         path      = 'TCGA-PRAD/RNAseq/', 
                         data.type = 'RNAseq')

####### Merge miRNAs data #######
mirMatrix <- gdcRNAMerge(metadata  = metaMatrix.MIR,
                         path      = 'TCGA-PRAD/miRNAs/',
                         data.type = 'miRNAs')
```

#### 2.3.2 Merge clinical data
```{r merge clinical, message=FALSE, warning=FALSE, eval=FALSE}
####### Merge clinical data #######
clinicalDa <- gdcClinicalMerge(path = 'TCGA-PRAD/Clinical/', key.info = TRUE)
```



### 2.4 TMM normalization and voom transformation
It has repeatedly shown that normalization is a critical way to ensure accurate estimation and detection of differential expression (DE) by removing systematic technical effects that occur in the data[@Robinsonscalingnormalizationmethod2010]. TMM normalization is a simple and effective method for estimating relative RNA production levels from RNA-seq data. Voom is moreover faster and more convenient than existing RNA-seq methods, and converts RNA-seq data into a form that can be analyzed using similar tools as for microarrays[@Lawvoomprecisionweights2014].

By running `gdcVoomNormalization()` function, raw counts data would be normalized by TMM method implemented in [edgeR](http://bioconductor.org/packages/release/bioc/html/edgeR.html)[@RobinsonedgeRBioconductorpackage2010] and further transformed by the voom method provided in [limma](http://bioconductor.org/packages/release/bioc/html/limma.html)[@Ritchielimmapowersdifferential2015a]. Low expression genes (logcpm < 1 in more than half of the samples) will be filtered out by default. All the genes can be kept by setting `filter=TRUE` in the `gdcVoomNormalization()`.



```{r normalization, message=FALSE, warning=FALSE, eval=FALSE}
####### RNAseq data #######
rnaExpr <- gdcVoomNormalization(counts = rnaMatrix, filter = FALSE)

####### miRNAs data #######
mirExpr <- gdcVoomNormalization(counts = mirMatrix, filter = FALSE)
```




## 3. Differential gene expression analysis

***

`gdcDEAnalysis()`, a convenience wrapper, provides three widely used methods [limma](http://bioconductor.org/packages/release/bioc/html/limma.html)[@Ritchielimmapowersdifferential2015a], [edgeR](http://bioconductor.org/packages/release/bioc/html/edgeR.html)[@RobinsonedgeRBioconductorpackage2010], and [DESeq2](http://bioconductor.org/packages/release/bioc/html/DESeq2.html)[@LoveModeratedestimationfold2014] to identify differentially expressed genes (DEGs) or miRNAs between any two groups defined by users. Note that [DESeq2](http://bioconductor.org/packages/release/bioc/html/DESeq2.html)[@LoveModeratedestimationfold2014] maybe slow with a single core. Multiple cores can be specified with the `nCore` argument if [DESeq2](http://bioconductor.org/packages/release/bioc/html/DESeq2.html)[@LoveModeratedestimationfold2014] is in use. Users are encouraged to consult the vignette of each method for more detailed information.


### 3.1 DE analysis
```{r deg, message=FALSE, warning=FALSE, eval=FALSE}
DEGAll <- gdcDEAnalysis(counts     = rnaMatrix, 
                        group      = metaMatrix.RNA$sample_type, 
                        comparison = 'PrimaryTumor-SolidTissueNormal', 
                        method     = 'limma')
```


### 3.2 Report DE genes/miRNAs

All DEGs, DE long non-coding genes, DE protein coding genes and DE miRNAs could be reported separately by setting `geneType` argument in `gdcDEReport()`. Gene symbols and biotypes based on the Ensembl 90 annotation are reported in the output.


```{r data, message=FALSE, warning=FALSE, eval=TRUE}
data(DEGAll)
```

```{r extract, message=FALSE, warning=FALSE, eval=TRUE}
### All DEGs
deALL <- gdcDEReport(deg = DEGAll, gene.type = 'all')
```

```{r extract2, message=FALSE, warning=FALSE, eval=TRUE}
#### DE long-noncoding
deLNC <- gdcDEReport(deg = DEGAll, gene.type = 'long_non_coding')
```

```{r extract3, message=FALSE, warning=FALSE, eval=TRUE}
#### DE protein coding genes
dePC <- gdcDEReport(deg = DEGAll, gene.type = 'protein_coding')
```


### 3.3 DEG visualization

Volcano plot and bar plot are used to visualize DE analysis results in different manners by `gdcVolcanoPlot()` and `gdcBarPlot()` functions, respectively . Hierarchical clustering on the expression matrix of DEGs can be analyzed and plotted by the `gdcHeatmap()` function.

#### 3.3.1 Volcano plot
```{r volcano, fig.align='center', fig.width=5, message=FALSE, warning=FALSE, eval=TRUE}
gdcVolcanoPlot(DEGAll)
```


#### 3.3.2 Barplot
```{r barplot, fig.align='center', fig.height=6, message=FALSE, warning=FALSE, eval=TRUE}
gdcBarPlot(deg = deALL, angle = 45, data.type = 'RNAseq')
```



#### 3.3.3 Heatmap
Heatmap is generated based on the `heatmap.2()` function in [gplots](https://cran.r-project.org/web/packages/gplots/index.html) package.
```{r heatmap, message=FALSE, warning=FALSE, eval=TRUE}
degName = rownames(deALL)
```

```{r heatmap 2, message=FALSE, warning=FALSE, eval=FALSE}
gdcHeatmap(deg.id = degName, metadata = metaMatrix.RNA, rna.expr = rnaExpr)
```

![](vignettes/figures/gdcHeatmap.png)


## 4 Competing endogenous RNAs network analysis

> Three criteria are used to determine the competing endogenous interactions between lncRNA-mRNA pairs: 

* The lncRNA and mRNA must share significant number of miRNAs
* Expression of lncRNA and mRNA must be positively correlated
* Those common miRNAs should play similar roles in regulating the expression of lncRNA and mRNA



### 4.1 Hypergeometric test

Hypergenometric test is performed to test whether a lncRNA and mRNA share many miRNAs significantly.

A newly developed algorithm **[spongeScan](http://spongescan.rc.ufl.edu/)**[@Furio-TarispongeScanwebdetecting2016] is used to predict MREs in lncRNAs acting as ceRNAs. Databases such as **[starBase v2.0](http://starbase.sysu.edu.cn/)**[@ListarBasev2decoding2014], **[miRcode](http://www.mircode.org/)**[@JeggarimiRcodemapputative2012] and **[mirTarBase release 7.0](http://mirtarbase.mbc.nctu.edu.tw/)**[@ChoumiRTarBaseupdate20182017] are used to collect predicted and experimentally validated miRNA-mRNA and/or miRNA-lncRNA interactions. Gene IDs in these databases are updated to the latest Ensembl 90 annotation of human genome and miRNAs names are updated to the new release miRBase 21 identifiers. Users can also provide their own datasets of miRNA-lncRNA and miRNA-mRNA interactions.

> The figure and equation below illustrate how the hypergeometric test works 

![](vignettes/figures/hyper.png)


$$p=1-\sum_{k=0}^m \frac{\binom{K}{k}\binom{N-K}{n-k}}{\binom{N}{n}} $$
here $m$ is the number of shared miRNAs, $N$ is the total number of miRNAs in the database, $n$ is the number of miRNAs targeting the lncRNA, $K$ is the number of miRNAs targeting the protein coding gene.


### 4.2 Pearson correlation analysis

Pearson correlation coefficient is a measure of the strength of a linear association between two variables. As we all know, miRNAs are negative regulators of gene expression. If more common miRNAs are occupied by a lncRNA, less of them will bind to the target mRNA, thus increasing the expression level of mRNA. So expression of the lncRNA and mRNA in a ceRNA pair should be positively correlated.



### 4.3 Regulation pattern analysis

> Two methods are used to measure the regulatory role of miRNAs on the lncRNA and mRNA:

* Regulation similarity

We defined a measurement *regulation similarity score* to check the similarity between miRNAs-lncRNA expression correlation and miRNAs-mRNA expression correlation.

$$Regulation\ similarity\ score  = 1-\frac{1}{M} \sum_{k=1}^M [{\frac{|corr(m_k,l)-corr(m_k,g)|}{|corr(m_k,l)|+|corr(m_k,g)|}}]^M$$

where $M$ is the total number of shared miRNAs, $k$ is the $k$th shared miRNAs, $corr(m_k, l)$ and $corr(m_k, g)$ represents the Pearson correlation between the $k$th miRNA and lncRNA, the $k$th miRNA and mRNA, respectively


* Sensitivity correlation

Sensitivity correlation is defined by Paci et al.[@PaciComputationalanalysisidentifies2014] to measure if the correlation between a lncRNA and mRNA is mediated by a miRNA in the lncRNA-miRNA-mRNA triplet. We take average of all triplets of a lncRNA-mRNA pair and their shared miRNAs as the sensitivity correlation between a selected lncRNA and mRNA.

$$Sensitivity\ correlation  = corr(l,g)-\frac{1}{M}\sum_{k=1}^M {\frac{corr(l,g)-corr(m_k,l)corr(m_k,g)}{\sqrt{1-corr(m_k,l)^2}\sqrt{1-corr(m_k,g)^2}}}$$
where $M$ is the total number of shared miRNAs, $k$ is the $k$th shared miRNAs, $corr(l,g)$, $corr(m_k,l)$ and $corr(m_k, g)$ represents the Pearson correlation between the long non-coding RNA and the protein coding gene, the kth miRNA and lncRNA, the kth miRNA and mRNA, respectively




***
The hypergeometric test of shared miRNAs, expression correlation analysis of lncRNA-mRNA pair, and regulation pattern analysis of shared miRNAs are all implemented in the `gdcCEAnalysis()` function.



```{r ce, message=FALSE, warning=FALSE, eval=FALSE}
ceOutput <- gdcCEAnalysis(lnc         = rownames(deLNC), 
                          pc          = rownames(dePC), 
                          lnc.targets = 'starBase', 
                          pc.targets  = 'starBase', 
                          rna.expr    = rnaExpr, 
                          mir.expr    = mirExpr)
```




### 4.4 ceRNAs visualization
#### 4.4.1 Correlation plot
```{r correlation, fig.align='center', message=FALSE, warning=FALSE, eval=FALSE}
gdcCorPlot(gene1    = 'ENSG00000234456', 
           gene2    = 'ENSG00000105971',
           rna.expr = rnaExpr,
           metadata = metaMatrix.RNA)
```

![](vignettes/figures/gdcCorPlot.png)


#### 4.4.2 Correlation plot on a local webpage by shinyCorplot

Typing and running `gdcCorPlot()` for each pair of lncRNA-mRNA is bothering when multiple pairs are being interested in. `shinyCorPlot()` , a interactive plot function based on `shiny` package, can be easily operated by just clicking the genes in each drop down box (in the GUI window). By running `shinyCorPlot()` function, a local webpage would pop up and correlation plot between a lncRNA and mRNA would be automatically shown.

```{r shiny cor plot, eval=FALSE, echo=TRUE, message=FALSE, warning=FALSE}
shinyCorPlot(gene1    = rownames(deLNC), 
             gene2    = rownames(dePC), 
             rna.expr = rnaExpr, 
             metadata = metaMatrix.RNA)
```

![](vignettes/figures/TCGA-PRAD.shinyCorPlot.gif)



#### 4.4.3 Network visulization in Cytoscape

lncRNA-miRNA-mRNA interactions can be reported by the `gdcExportNetwork()` and visualized in **[Cytoscape](http://www.cytoscape.org/)**.

```{r message=FALSE, warning=FALSE, eval=FALSE}
ceOutput2 <- ceOutput[ceOutput$hyperPValue<0.01 & ceOutput$corPValue<0.01 & ceOutput$regSim != 0,]

edges <- gdcExportNetwork(ceNetwork = ceOutput2, net = 'edges')
nodes <- gdcExportNetwork(ceNetwork = ceOutput2, net = 'nodes')
```



![](figures/network.png)




## 5 Univariate survival analysis

Two methods are provided to perform univariate survival analysis: Cox Proportional-Hazards (CoxPH) model and Kaplan Meier (KM) analysis based on the [survival](https://cran.r-project.org/web/packages/survival/index.html) package. CoxPH model considers expression value as continous variable while KM analysis divides patients into high-expreesion and low-expression groups by a user-defined threshold such as median or mean. `gdcSurvivalAnalysis()` take a list of genes as input and report the hazard ratio, 95% confidence intervals, and test significance of each gene on overall survival.


### 5.1 CoxPH analysis

```{r survival, message=FALSE, warning=FALSE, eval=FALSE}
####### CoxPH analysis #######
survOutput <- gdcSurvivalAnalysis(gene     = rownames(deALL), 
                                  method   = 'coxph', 
                                  rna.expr = rnaExpr, 
                                  metadata = metaMatrix.RNA)
```


### 5.2 KM analysis
```{r survival2, message=FALSE, warning=FALSE, eval=FALSE}
####### KM analysis #######
survOutput <- gdcSurvivalAnalysis(gene     = rownames(deALL), 
                                  method   = 'KM', 
                                  rna.expr = rnaExpr, 
                                  metadata = metaMatrix.RNA, 
                                  sep      = 'median')
```

### 5.3 KM analysis visualization
#### 5.5.1 KM plot
KM survival curves are ploted using the `gdcKMPlot()` function which is based on the R package [survminer](https://cran.r-project.org/web/packages/survminer/index.html).
```{r km plot, fig.align='center', fig.width=7, message=FALSE, warning=FALSE, eval=FALSE}
gdcKMPlot(gene     = 'ENSG00000197275', 
          rna.expr = rnaExpr, 
          metadata = metaMatrix.RNA, 
          sep      = 'median')
```

![](figures/gdcKMPlot.png)

#### 5.3.2 KM plot on a local webpage by shinyKMPlot
The `shinyKMPlot()` function is also a simply `shiny` app which allow users view KM plots of all genes of interests on a local webpackage conveniently.
```{r shiny km plot, eval=FALSE, echo=TRUE, message=FALSE, warning=FALSE, eval=FALSE}
shinyKMPlot(gene = rownames(deALL), rna.expr = rnaExpr, metadata = metaMatrix.RNA)
```

![](figures/TCGA-PRAD.shinyKMPlot.gif)



## 6 Functional enrichment analysis

One of the main uses of the GO is to perform enrichment analysis on gene sets. For example, given a set of genes that are up-regulated under certain conditions, an enrichment analysis will find which GO terms are over-represented (or under-represented) using annotations for that gene set and pathway enrichment can also be applied afterwards.

***

### 6.1 GO, KEGG and DO analyses
`gdcEnrichAnalysis()` can perform Gene ontology (GO), Kyoto Encyclopedia of Genes and Genomes (KEGG) and Disease Ontology (DO) functional enrichment analyses of a list of genes simultaneously. GO and KEGG analyses are based on the R/Bioconductor packages [clusterProfilier](https://bioconductor.org/packages/release/bioc/html/clusterProfiler.html)[@YuclusterProfilerPackageComparing2012] and [DOSE](https://bioconductor.org/packages/release/bioc/html/DOSE.html)[@YuDOSEBioconductorpackage2015]. Redundant GO terms can be removed by specifying `simplify=TRUE` in the `gdcEnrichAnalysis()` function which uses the `simplify()` function in the  [clusterProfilier](https://bioconductor.org/packages/release/bioc/html/clusterProfiler.html)[@YuclusterProfilerPackageComparing2012] package. 

```{r enrichment, message=FALSE, warning=FALSE, eval=FALSE}
enrichOutput <- gdcEnrichAnalysis(gene = rownames(deALL), simplify = TRUE)
```


### 6.2 Enrichment visualization

The output generated by `gdcEnrichAnalysis()` can be used for visualization in the `gdcEnrichPlot()` function by specifying `type`,`category` and `numTerms` arguments. 

```{r enrichment data, message=FALSE, warning=FALSE, eval=TRUE}
data(enrichOutput)
```


#### 6.2.1 GO barplot
```{r go bar, fig.height=8, fig.width=15.5, message=FALSE, warning=FALSE}
gdcEnrichPlot(enrichOutput, type = 'bar', category = 'GO', num.terms = 10)
```


#### 6.2.2 GO bubble plot
```{r go bubble, echo=TRUE, fig.height=8, fig.width=12.5, message=FALSE, warning=FALSE}
gdcEnrichPlot(enrichOutput, type='bubble', category='GO', num.terms = 10)
```

#### 6.2.3 KEGG/DO barplot
```{r kegg bar, fig.height=3.5, fig.width=12.5, message=FALSE, warning=FALSE}
gdcEnrichPlot(enrichment = enrichOutput, 
              type       = 'bar', 
              category   = 'KEGG', 
              bar.color  = 'chocolate1', 
              num.terms  = 20)
```



```{r do bar, fig.height=3, fig.width=10, message=FALSE, warning=FALSE}
gdcEnrichPlot(enrichment = enrichOutput, 
              type       = 'bar', 
              category   = 'DO', 
              bar.color  = 'dodgerblue', 
              num.terms  = 20)
```



#### 6.2.4 KEGG/DO bubble plot
```{r kegg bubble, fig.height=5, fig.width=10.5, message=FALSE, warning=FALSE}
gdcEnrichPlot(enrichOutput, category='KEGG',type = 'bubble', num.terms = 20)
```



```{r do bubble, fig.height=5, fig.width=8.3, message=FALSE, warning=FALSE}
gdcEnrichPlot(enrichOutput, category='DO',type = 'bubble', num.terms = 20)
```


#### 6.2.5 Pathview

Users can visualize a pathway map with `pathview()` function in the [pathview](https://bioconductor.org/packages/release/bioc/html/pathview.html)[@LuoPathviewBioconductorpackage2013] package. It displays related many-genes-to-many-terms on 2-D view, shows by genes on BioCarta & KEGG pathway maps. Gradient colors can be used to indicate if genes are up-regulated or down-regulated.

```{r pathview, message=FALSE, warning=FALSE, eval=TRUE}
deg <- deALL$logFC
names(deg) <- rownames(deALL)
```


```{r pathview2, message=FALSE, warning=FALSE, eval=FALSE}
library(pathview)
hsa04022 <- pathview(gene.data   = deg,
                     pathway.id  = "hsa04022",
                     species     = "hsa",
                     gene.idtype = 'ENSEMBL',
                     limit       = list(gene=max(abs(geneList)), cpd=1))
```


![](vignettes/figures/hsa04022.pathview.png)


#### 6.2.6 View pathway maps on a local webpage by shinyPathview
`shinyPathview()` allows users view and download pathways of interests by simply selecting the pathway terms on a local webpage.

```{r shiny pathview, message=FALSE, warning=FALSE, include=FALSE}
pathways <- as.character(enrichOutput$Terms[enrichOutput$Category=='KEGG'])
```


```{r shiny pathview2, eval=FALSE, echo=TRUE, message=FALSE, warning=FALSE}
shinyPathview(deg, pathways = pathways, directory = 'pathview')
```

![](vignettes/figures/TCGA-PRAD.shinyPathview.gif)


## sessionInfo
```{r sessionInfo}
sessionInfo()
```
