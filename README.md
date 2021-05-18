
# sparrow <img src="man/figures/sparrow.png" height="150" align="right"/>

<!-- badges: start -->

[![R build
status](https://github.com/lianos/sparrow/workflows/R-CMD-check/badge.svg)](https://github.com/lianos/sparrow/actions)
![pkgdown](https://github.com/lianos/sparrow/workflows/pkgdown/badge.svg)
[![Project Status:
Active](https://www.repostatus.org/badges/latest/active.svg)](https://www.repostatus.org/#active)
[![Lifecycle:
stable](https://img.shields.io/badge/lifecycle-stable-brightgreen.svg)](https://www.tidyverse.org/lifecycle/#stable)
[![codecov](https://codecov.io/gh/lianos/sparrow/branch/develop/graph/badge.svg)](https://codecov.io/gh/lianos/sparrow)
<!-- badges: end -->

`sparrow` (formerly [multiGSEA](https://github.com/lianos/multiGSEA))
was built to facilitate the use of gene sets in the analysis of high
throughput genomics data (primarily RNA-seq). Analysts can orchestrate
any number of GSEA methods across a specific contrast using the unified
interface provided by the `seas`. A shiny application is provided via
the [sparrow.shiny](https://github.com/lianos/sparrow.shiny) package
that enables the interactive exploration of of GSEA results.

-   The `seas` function is a wrapper that orchestrates the execution of
    any number of user-specified gene set enrichment analyses (GSEA)
    over a particular experimental contrast of interest. This will
    create a `SparrowResult` object which stores the results of each
    GSEA method internally, allowing for easy query and retrieval.

-   A sister [sparrow.shiny](https://github.com/lianos/sparrow.shiny)
    package provides an `explore` function, which is invoked on
    `SparrowResult` objects returned from a call to `seas()`. The shiny
    application facilitates interactive exploration of these GSEA
    results. This application can also be deployed to a shiny server and
    can be initialized by uploading a serialized `SparrowResult` `*.rds`
    file.

Full details that outline the use of this software package is provided
in the [package’s
vignette](https://lianos.github.io/sparrow/articles/sparrow.html),
however a brief description is outlined below.

# Usage

A subset of the RNA-seq data tumor/normal samples in the BRCA indication
from the TCGA are provided in this package. We will use that data to
perform a “camera” and “fry” gene set enrichment analysis of tumor vs
normal samples using the MSigDB hallmark and c2 gene set collections
with `sparrow::seas()`.

``` r
library(sparrow)
library(dplyr)
gdb <- getMSigGeneSetDb(c('H', 'C2'), species = 'human', id.type = "entrez")
vm <- exampleExpressionSet(dataset = 'tumor-vs-normal', do.voom = TRUE)
mg <- seas(vm, gdb, c("camera", "fry"), design = vm$design, contrast = "tumor")
```

We can view the top “camera” results with the smallest pvalues like so:

``` r
results(mg, "camera") %>%
  arrange(pval) %>%
  select(collection, name, padj) %>%
  head
#>   collection                                        name         padj
#> 1         C2      SOTIRIOU_BREAST_CANCER_GRADE_1_VS_3_UP 1.422275e-36
#> 2         C2 ROSTY_CERVICAL_CANCER_PROLIFERATION_CLUSTER 1.422275e-36
#> 3         C2         NAKAYAMA_SOFT_TISSUE_TUMORS_PCA2_DN 1.971141e-23
#> 4         C2              KANG_DOXORUBICIN_RESISTANCE_UP 1.380815e-22
#> 5         C2               CROONQUIST_IL6_DEPRIVATION_DN 2.851619e-22
#> 6         C2                     BENPORATH_PROLIFERATION 8.569928e-22
```

The shift in expression of the genes within the top gene set can be
visualized with the `iplot` function below. This plot produces
interactive graphics, but rasterized versions are saved for use with
this `README` file:

``` r
iplot(mg, 'C2', 'SOTIRIOU_BREAST_CANCER_GRADE_1_VS_3_UP', type = "density")
```

<img src="man/figures/README_iplot_density.png" />

``` r
iplot(mg, 'C2', 'SOTIRIOU_BREAST_CANCER_GRADE_1_VS_3_UP', type = "boxplot")
```

<img src="man/figures/README_iplot_boxplot.png" />

When these plots are rendered in your workspace or an Rmarkdown
document, the user can hover of the genes (dots) to see their name and
differential expression statistics.

For an immersive, interactive way to explore the GSEA results, use the
`sparrow.shiny::explore(mg)` method!

# Installation

The sparrow suite of packages will soon be submitted to bioconductor and
installable via the recommended `BiocManager::install()` mechanism. In
the meantime, you can install the
[sparrow.shiny](https://github.com/lianos/sparrow.shiny) package from
github.

``` r
# install.packages("BiocManager")
BiocManager::install("lianos/sparrow.shiny")
```
