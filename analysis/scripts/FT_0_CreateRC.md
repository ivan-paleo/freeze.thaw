Create Research Compendium - Freeze-Thaw project
================
Ivan Calandra
2026-04-22 08:20:35 CEST

- [Goal of the script](#goal-of-the-script)
- [Prerequisites](#prerequisites)
- [Preparations](#preparations)
- [Create the research compendium](#create-the-research-compendium)
  - [Load packages](#load-packages)
  - [Create compendium](#create-compendium)
  - [Create README.qmd file](#create-readmeqmd-file)
  - [Create a folders](#create-a-folders)
  - [Delete file ‘NAMESPACE’ and folder
    ‘.binder’](#delete-file-namespace-and-folder-binder)
- [Before running the analyses](#before-running-the-analyses)
- [After running the analyses](#after-running-the-analyses)
  - [DESCRIPTION](#description)
  - [renv](#renv)
- [sessionInfo()](#sessioninfo)
- [Cite R packages used](#cite-r-packages-used)
  - [References](#references)

------------------------------------------------------------------------

# Goal of the script

Create and set up a research compendium for the paper on the Freeze-Thaw
project using the R package `rrtools`.\
For details on rrtools, see Ben Marwick’s [GitHub
repository](https://github.com/benmarwick/rrtools).

Note that this script is there only to show the steps taken to create
the research compendium and is not part of the analysis per se. For this
reason, most of the code is not evaluated
(`knitr::opts_chunk$set(eval=FALSE)`).

The knit directory for this script is the project directory.

------------------------------------------------------------------------

# Prerequisites

This script requires that you have a GitHub account and that you have
connected RStudio, Git and GitHub. For details on how to do it, check
[Happy Git](https://happygitwithr.com/).

------------------------------------------------------------------------

# Preparations

Before running this script, the first step is to [create a repository on
GitHub and to download it to
RStudio](https://happygitwithr.com/new-github-first.html). In this case,
the repository is called “freeze.thaw”.\
Finally, open the RStudio project created.

------------------------------------------------------------------------

# Create the research compendium

## Load packages

``` r
library(grateful)
library(renv)
library(rrtools)
library(usethis)
```

## Create compendium

``` r
rrtools::use_compendium(getwd())
```

A new project has opened in a new session.\
Edit the fields “Title”, “Author” and “Description” in the `DESCRIPTION`
file.

## Create README.qmd file

``` r
rrtools::use_readme_qmd()
```

Edit the `README.qmd` file as needed.\
Make sure you render it to create the `README.md` file.

## Create a folders

Create a folder ‘analysis’ and subfolders to contain raw data, derived
data, plots, statistics and scripts. Also create a folder for the Python
analysis:

``` r
dir.create("analysis", showWarnings = FALSE)
dir.create("analysis/raw_data", showWarnings = FALSE)
dir.create("analysis/derived_data", showWarnings = FALSE)
dir.create("analysis/plots", showWarnings = FALSE)
dir.create("analysis/scripts", showWarnings = FALSE)
dir.create("analysis/stats", showWarnings = FALSE)
```

Note that the folders cannot be pushed to GitHub as long as they are
empty.

## Delete file ‘NAMESPACE’ and folder ‘.binder’

``` r
file.remove("NAMESPACE")
unlink(".binder", recursive = TRUE)
```

------------------------------------------------------------------------

# Before running the analyses

After the creation of this research compendium, I have moved the raw,
input data files to `"~/analysis/raw_data"` (as read-only files) and the
R scripts to `"~/analysis/scripts"`.

------------------------------------------------------------------------

# After running the analyses

## DESCRIPTION

Run this command to add the dependencies to the DESCRIPTION file.

``` r
rrtools::add_dependencies_to_description()
```

## renv

Save the state of the project library using the `renv` package.

``` r
renv::init()
```

------------------------------------------------------------------------

# sessionInfo()

``` r
sessionInfo()
```

    R version 4.5.3 (2026-03-11 ucrt)
    Platform: x86_64-w64-mingw32/x64
    Running under: Windows 11 x64 (build 26200)

    Matrix products: default
      LAPACK version 3.12.1

    locale:
    [1] LC_COLLATE=English_United States.utf8 
    [2] LC_CTYPE=English_United States.utf8   
    [3] LC_MONETARY=English_United States.utf8
    [4] LC_NUMERIC=C                          
    [5] LC_TIME=English_United States.utf8    

    time zone: Europe/Berlin
    tzcode source: internal

    attached base packages:
    [1] stats     graphics  grDevices datasets  utils     methods   base     

    other attached packages:
    [1] usethis_3.2.1  rrtools_0.1.6  renv_1.2.1     grateful_0.3.0

    loaded via a namespace (and not attached):
     [1] vctrs_0.7.3       crayon_1.5.3      cli_3.6.6         knitr_1.51       
     [5] clisymbols_1.2.0  rlang_1.2.0       xfun_0.57         otel_0.2.0       
     [9] purrr_1.2.2       pkgload_1.5.1     jsonlite_2.0.0    glue_1.8.0       
    [13] git2r_0.36.2      rprojroot_2.1.1   htmltools_0.5.9   pkgbuild_1.4.8   
    [17] sass_0.4.10       rmarkdown_2.31    evaluate_1.0.5    jquerylib_0.1.4  
    [21] ellipsis_0.3.3    fastmap_1.2.0     yaml_2.3.12       lifecycle_1.0.5  
    [25] memoise_2.0.1     compiler_4.5.3    sessioninfo_1.2.3 fs_2.0.1         
    [29] here_1.0.2        rstudioapi_0.18.0 digest_0.6.39     R6_2.6.1         
    [33] magrittr_2.0.5    bslib_0.10.0      tools_4.5.3       devtools_2.5.0   
    [37] cachem_1.1.0     

------------------------------------------------------------------------

# Cite R packages used

| Package  | Version      | Citation                             |
|:---------|:-------------|:-------------------------------------|
| base     | 4.5.3        | R Core Team (2026)                   |
| grateful | 0.3.0        | Rodriguez-Sanchez and Jackson (2025) |
| renv     | 1.2.1        | Ushey and Wickham (2026)             |
| rrtools  | 0.1.6        | Marwick (2019)                       |
| usethis  | 3.2.1        | Wickham et al. (2025)                |
| RStudio  | 2026.1.2.418 | Posit team (2026)                    |

## References

<div id="refs" class="references csl-bib-body hanging-indent">

<div id="ref-rrtools" class="csl-entry">

Marwick, Ben. 2019. *<span class="nocase">rrtools</span>: Creates a
Reproducible Research Compendium*.
<https://github.com/benmarwick/rrtools>.

</div>

<div id="ref-rstudio" class="csl-entry">

Posit team. 2026. *RStudio: Integrated Development Environment for r*.
Posit Software, PBC. <http://www.posit.co/>.

</div>

<div id="ref-base" class="csl-entry">

R Core Team. 2026. *R: A Language and Environment for Statistical
Computing*. R Foundation for Statistical Computing.
<https://www.R-project.org/>.

</div>

<div id="ref-grateful" class="csl-entry">

Rodriguez-Sanchez, Francisco, and Connor P. Jackson. 2025.
*<span class="nocase">grateful</span>: Facilitate Citation of R
Packages*. <https://pakillo.github.io/grateful/>.

</div>

<div id="ref-renv" class="csl-entry">

Ushey, Kevin, and Hadley Wickham. 2026.
*<span class="nocase">renv</span>: Project Environments*.
<https://doi.org/10.32614/CRAN.package.renv>.

</div>

<div id="ref-usethis" class="csl-entry">

Wickham, Hadley, Jennifer Bryan, Malcolm Barrett, and Andy Teucher.
2025. *<span class="nocase">usethis</span>: Automate Package and Project
Setup*. <https://doi.org/10.32614/CRAN.package.usethis>.

</div>

</div>
