Summary statistics on the surface texture parameters for the Freeze-thaw
project
================
Ivan Calandra
2025-11-24 15:42:30 CET

- [Goal of the script](#goal-of-the-script)
- [Load packages](#load-packages)
- [Read in data](#read-in-data)
- [Summary statistics](#summary-statistics)
  - [Create function to compute the statistics at
    once](#create-function-to-compute-the-statistics-at-once)
  - [Compute summary statistics](#compute-summary-statistics)
  - [Save as XLSX](#save-as-xlsx)
- [sessionInfo()](#sessioninfo)
- [Cite R packages used](#cite-r-packages-used)
  - [References](#references)

------------------------------------------------------------------------

# Goal of the script

This script computes standard descriptive statistics for each group.  
The groups are based on:

- Sediment type  
- Freeze-thaw cycles  
- State  
- NMP_cat

It computes the following statistics:

- sample size (n = `length`)  
- smallest value (`min`)  
- largest value (`max`)
- mean  
- median  
- standard deviation (`sd`)

``` r
dir_in <- dir_stats <- "analysis/derived_data"
```

Raw data must be located in “./analysis/derived_data”.  
Summary statistics will be saved in “./analysis/derived_data”.

The knit directory for this script is the project directory.

------------------------------------------------------------------------

# Load packages

``` r
library(doBy)
library(grateful)
library(knitr)
library(R.utils)
library(rmarkdown)
library(tidyverse)
library(writexl)
```

------------------------------------------------------------------------

# Read in data

``` r
FT_file <- list.files(dir_in, pattern = ".*STA.*\\.Rbin$", full.names = TRUE)
FT <- loadObject(FT_file)
```

The following data file has been read:
“analysis/derived_data/FT_STA_formatted-data.Rbin”

Below are its structure and first lines:

``` r
str(FT)
```

    'data.frame':   80 obs. of  41 variables:
     $ Specimen                : chr  "Scra11" "Scra11" "Scra11" "Scra11" ...
     $ Sediment                : chr  "Quincay" "Quincay" "Quincay" "Quincay" ...
     $ Cycles                  : num  600 600 600 600 0 0 0 0 330 330 ...
     $ State                   : Factor w/ 2 levels "before","after": 2 2 2 2 1 1 1 1 2 2 ...
     $ Location                : chr  "loc1" "loc2" "loc3" "loc4" ...
     $ NMP                     : num  6.21 8.99 9.36 7.09 5.59 ...
     $ NMP_cat                 : Ord.factor w/ 3 levels "<10%"<"10-17%"<..: 1 1 1 1 1 1 1 1 2 2 ...
     $ Sq                      : num  510 599 627 536 461 ...
     $ Ssk                     : num  0.208 0.104 -1.349 0.307 0.364 ...
     $ Sku                     : num  3.61 4.28 16.22 3.5 3.44 ...
     $ Sp                      : num  2002 2280 4021 2237 1940 ...
     $ Sv                      : num  1895 2130 4888 1622 1193 ...
     $ Sz                      : num  3898 4410 8910 3859 3134 ...
     $ Sa                      : num  395 444 400 421 362 ...
     $ Smr                     : num  3.359 2.643 0.138 1.933 3.147 ...
     $ Smc                     : num  631 705 594 670 568 ...
     $ Sxp                     : num  959 1299 1119 961 834 ...
     $ Sal                     : num  5.54 6.57 5.66 5.69 6.11 ...
     $ Str                     : num  0.757 0.697 0.611 0.648 0.681 ...
     $ Std                     : num  54.7 74.7 85.5 57.8 86.3 ...
     $ Ssw                     : num  0.425 0.425 0.425 0.425 0.425 ...
     $ Sdq                     : num  0.594 0.319 0.372 0.324 0.318 ...
     $ Sdr                     : num  14.11 4.67 5.96 4.85 4.7 ...
     $ Vm                      : num  0.0322 0.0418 0.0411 0.0331 0.0301 ...
     $ Vv                      : num  0.663 0.747 0.635 0.703 0.598 ...
     $ Vmp                     : num  0.0322 0.0418 0.0411 0.0331 0.0301 ...
     $ Vmc                     : num  0.43 0.452 0.381 0.467 0.401 ...
     $ Vvc                     : num  0.606 0.667 0.547 0.649 0.551 ...
     $ Vvv                     : num  0.0574 0.0803 0.0885 0.054 0.0469 ...
     $ First.direction         : num  45 90 90 45 90 ...
     $ Second.direction        : num  0.0108 179.9952 44.9961 90.0142 135.0262 ...
     $ Third.direction         : num  90 135 116 135 180 ...
     $ Texture.isotropy        : num  82.4 68.8 58.8 71.8 72.2 ...
     $ Maximum.depth.of.furrows: num  3418 4350 9462 3943 3052 ...
     $ Mean.depth.of.furrows   : num  1714 1899 3460 1857 1528 ...
     $ Mean.density.of.furrows : num  6492 6945 7121 6632 6748 ...
     $ epLsar                  : num  0.000586 0.002807 0.001736 0.002383 0.002474 ...
     $ NewEplsar               : num  0.0175 0.0168 0.0179 0.0167 0.0169 ...
     $ Asfc                    : num  49.9 13.8 19.8 13.1 13.1 ...
     $ Smfc                    : num  1.081 1.153 1.495 1.153 0.949 ...
     $ HAsfc9                  : num  0.133 0.444 0.949 0.314 0.177 ...
     - attr(*, "comment")= Named chr [1:35] "%" "nm" "<no unit>" "<no unit>" ...
      ..- attr(*, "names")= chr [1:35] "NMP" "Sq" "Ssk" "Sku" ...

``` r
head(FT)
```

      Specimen Sediment Cycles  State Location      NMP NMP_cat       Sq        Ssk
    1   Scra11  Quincay    600  after     loc1 6.210140    <10% 510.3147  0.2082995
    2   Scra11  Quincay    600  after     loc2 8.988605    <10% 599.4221  0.1038576
    3   Scra11  Quincay    600  after     loc3 9.360195    <10% 626.8703 -1.3491279
    4   Scra11  Quincay    600  after     loc4 7.087054    <10% 535.8293  0.3066295
    5   Scra11  Quincay      0 before     loc1 5.585075    <10% 461.3625  0.3644242
    6   Scra11  Quincay      0 before     loc2 7.824051    <10% 537.4528 -0.3339857
            Sku       Sp       Sv       Sz       Sa       Smr      Smc       Sxp
    1  3.611167 2002.269 1895.237 3897.506 395.2242 3.3585136 630.9984  959.4634
    2  4.277262 2280.247 2129.979 4410.227 444.2419 2.6425792 705.0265 1299.1056
    3 16.215072 4021.251 4888.391 8909.643 399.6521 0.1384577 594.0357 1118.6396
    4  3.504882 2236.933 1622.238 3859.171 420.7068 1.9326553 670.0448  961.0071
    5  3.438282 1940.378 1193.170 3133.549 362.0558 3.1472606 568.2292  834.1125
    6  4.060290 1796.531 2537.250 4333.781 412.1910 6.2930831 674.2111 1111.3785
           Sal       Str      Std       Ssw       Sdq       Sdr         Vm
    1 5.535718 0.7571265 54.74853 0.4249085 0.5937326 14.108213 0.03220712
    2 6.570978 0.6973378 74.74841 0.4249085 0.3186908  4.665525 0.04181877
    3 5.662375 0.6112438 85.50156 0.4249085 0.3722057  5.960471 0.04105984
    4 5.693638 0.6484318 57.75130 0.4249085 0.3241989  4.854970 0.03305849
    5 6.107537 0.6810785 86.25058 0.4249085 0.3179695  4.701684 0.03014862
    6 5.961985 0.6743632 86.99719 0.4249085 0.4060171  7.404031 0.02403996
             Vv        Vmp       Vmc       Vvc        Vvv First.direction
    1 0.6632383 0.03220712 0.4298770 0.6058456 0.05739273        44.99067
    2 0.7468693 0.04181877 0.4518377 0.6665194 0.08034989        89.99559
    3 0.6350726 0.04105984 0.3806123 0.5465987 0.08847389        90.00050
    4 0.7030889 0.03305849 0.4670876 0.6490426 0.05404634        44.98490
    5 0.5983675 0.03014862 0.4005538 0.5514198 0.04694766        89.99058
    6 0.6982755 0.02403996 0.4565223 0.6277425 0.07053295        45.01407
      Second.direction Third.direction Texture.isotropy Maximum.depth.of.furrows
    1       0.01081937        90.00509         82.36371                 3418.379
    2     179.99523860       134.98878         68.76672                 4350.215
    3      44.99608453       116.43945         58.82372                 9461.823
    4      90.01423227       135.00686         71.81574                 3943.282
    5     135.02619380       179.99602         72.18861                 3051.558
    6      89.99811744        63.56398         57.93184                 4003.873
      Mean.depth.of.furrows Mean.density.of.furrows       epLsar  NewEplsar
    1              1714.284                6492.159 0.0005855797 0.01748435
    2              1899.377                6945.496 0.0028073607 0.01681894
    3              3459.689                7121.221 0.0017361474 0.01787320
    4              1857.354                6632.412 0.0023834012 0.01666934
    5              1527.908                6748.034 0.0024736287 0.01686902
    6              1607.176                7054.717 0.0012932325 0.01710849
          Asfc      Smfc    HAsfc9
    1 49.92666 1.0805865 0.1333228
    2 13.83172 1.1530754 0.4437028
    3 19.82069 1.4950314 0.9494297
    4 13.06050 1.1530754 0.3135672
    5 13.10260 0.9489935 0.1773680
    6 26.90327 0.8893344 0.2908531

------------------------------------------------------------------------

# Summary statistics

## Create function to compute the statistics at once

``` r
nminmaxmeanmedsd <- function(x){
    y <- x[!is.na(x)]     # Exclude NAs
    n_test <- length(y)   # Sample size (n)
    min_test <- min(y)    # Minimum
    max_test <- max(y)    # Maximum
    mean_test <- mean(y)  # Mean
    med_test <- median(y) # Median
    sd_test <- sd(y)      # Standard deviation
    out <- c(n_test, min_test, max_test, mean_test, med_test, sd_test) # Concatenate
    names(out) <- c("n", "min", "max", "mean", "median", "sd")         # Name values
    return(out)                                                        # Object to return
}
```

## Compute summary statistics

``` r
# Compute summary statistics based on Sediment
stats_sed <- summaryBy(. ~ Sediment, data = FT, FUN = nminmaxmeanmedsd)
stats_sed[1]
```

         Sediment
    1        Clay
    2 Coarse sand
    3   Fine sand
    4      Gravel
    5     Quincay

``` r
# Compute summary statistics based on Sediment and NMP_cat
stats_sed_NMP <- summaryBy(. ~ Sediment + NMP_cat, data = FT, FUN = nminmaxmeanmedsd)
stats_sed_NMP[1:2]
```

          Sediment NMP_cat
    1         Clay    <10%
    2         Clay  10-17%
    3         Clay    >17%
    4  Coarse sand    <10%
    5  Coarse sand  10-17%
    6    Fine sand    <10%
    7    Fine sand  10-17%
    8    Fine sand    >17%
    9       Gravel    <10%
    10      Gravel  10-17%
    11      Gravel    >17%
    12     Quincay    <10%
    13     Quincay  10-17%
    14     Quincay    >17%

``` r
# Compute summary statistics based on Sediment and Cycles
stats_sed_cy <- summaryBy(. ~ Sediment + Cycles, data = FT, FUN = nminmaxmeanmedsd)
stats_sed_cy[1:2]
```

          Sediment Cycles
    1         Clay      0
    2         Clay    330
    3         Clay    560
    4  Coarse sand      0
    5  Coarse sand    330
    6  Coarse sand    600
    7    Fine sand      0
    8    Fine sand    330
    9    Fine sand    600
    10      Gravel      0
    11      Gravel    330
    12      Gravel    600
    13     Quincay      0
    14     Quincay    330
    15     Quincay    600

``` r
# Compute summary statistics based on Sediment, Cycles and NMP_cat
stats_sed_cy_NMP <- summaryBy(. ~ Sediment + Cycles + NMP_cat, data = FT, FUN = nminmaxmeanmedsd)
stats_sed_cy_NMP[1:3]
```

          Sediment Cycles NMP_cat
    1         Clay      0    <10%
    2         Clay      0  10-17%
    3         Clay      0    >17%
    4         Clay    330    <10%
    5         Clay    330  10-17%
    6         Clay    330    >17%
    7         Clay    560  10-17%
    8  Coarse sand      0    <10%
    9  Coarse sand      0  10-17%
    10 Coarse sand    330  10-17%
    11 Coarse sand    600    <10%
    12   Fine sand      0    <10%
    13   Fine sand      0  10-17%
    14   Fine sand    330  10-17%
    15   Fine sand    600    <10%
    16   Fine sand    600  10-17%
    17   Fine sand    600    >17%
    18      Gravel      0    <10%
    19      Gravel      0  10-17%
    20      Gravel      0    >17%
    21      Gravel    330    <10%
    22      Gravel    330  10-17%
    23      Gravel    330    >17%
    24      Gravel    600    <10%
    25      Gravel    600  10-17%
    26      Gravel    600    >17%
    27     Quincay      0    <10%
    28     Quincay      0  10-17%
    29     Quincay    330    <10%
    30     Quincay    330    >17%
    31     Quincay    600    <10%

``` r
# Compute summary statistics based on Sediment, Cycles and State
stats_sed_cy_st <- summaryBy(. ~ Sediment + Cycles + State, data = FT, FUN = nminmaxmeanmedsd)
stats_sed_cy_st[1:3]
```

          Sediment Cycles  State
    1         Clay      0 before
    2         Clay    330  after
    3         Clay    560  after
    4  Coarse sand      0 before
    5  Coarse sand    330  after
    6  Coarse sand    600  after
    7    Fine sand      0 before
    8    Fine sand    330  after
    9    Fine sand    600  after
    10      Gravel      0 before
    11      Gravel    330  after
    12      Gravel    600  after
    13     Quincay      0 before
    14     Quincay    330  after
    15     Quincay    600  after

``` r
# Compute summary statistics based on Sediment, Cycles, State and NMP_cat
stats_sed_cy_st_NMP <- summaryBy(. ~ Sediment + Cycles + State + NMP_cat, data = FT, FUN = nminmaxmeanmedsd)
stats_sed_cy_st_NMP[1:4]
```

          Sediment Cycles  State NMP_cat
    1         Clay      0 before    <10%
    2         Clay      0 before  10-17%
    3         Clay      0 before    >17%
    4         Clay    330  after    <10%
    5         Clay    330  after  10-17%
    6         Clay    330  after    >17%
    7         Clay    560  after  10-17%
    8  Coarse sand      0 before    <10%
    9  Coarse sand      0 before  10-17%
    10 Coarse sand    330  after  10-17%
    11 Coarse sand    600  after    <10%
    12   Fine sand      0 before    <10%
    13   Fine sand      0 before  10-17%
    14   Fine sand    330  after  10-17%
    15   Fine sand    600  after    <10%
    16   Fine sand    600  after  10-17%
    17   Fine sand    600  after    >17%
    18      Gravel      0 before    <10%
    19      Gravel      0 before  10-17%
    20      Gravel      0 before    >17%
    21      Gravel    330  after    <10%
    22      Gravel    330  after  10-17%
    23      Gravel    330  after    >17%
    24      Gravel    600  after    <10%
    25      Gravel    600  after  10-17%
    26      Gravel    600  after    >17%
    27     Quincay      0 before    <10%
    28     Quincay      0 before  10-17%
    29     Quincay    330  after    <10%
    30     Quincay    330  after    >17%
    31     Quincay    600  after    <10%

## Save as XLSX

``` r
write_xlsx(list("Sediment" = stats_sed, "Sediment+NMP" = stats_sed_NMP, "Sediment+Cycles" = stats_sed_cy, 
                "Sediment+Cycles+NMP" = stats_sed_cy_NMP, "Sediment+Cycles+State" = stats_sed_cy_st,
                "Sediment+Cycles+State+NMP" = stats_sed_cy_st_NMP),
           path = paste0(dir_stats, "/FT_STA-stats.xlsx"))
```

------------------------------------------------------------------------

# sessionInfo()

``` r
sessionInfo()
```

    R version 4.5.1 (2025-06-13 ucrt)
    Platform: x86_64-w64-mingw32/x64
    Running under: Windows 10 x64 (build 19045)

    Matrix products: default
      LAPACK version 3.12.1

    locale:
    [1] LC_COLLATE=English_United Kingdom.utf8 
    [2] LC_CTYPE=English_United Kingdom.utf8   
    [3] LC_MONETARY=English_United Kingdom.utf8
    [4] LC_NUMERIC=C                           
    [5] LC_TIME=English_United Kingdom.utf8    

    time zone: Europe/Berlin
    tzcode source: internal

    attached base packages:
    [1] stats     graphics  grDevices utils     datasets  methods   base     

    other attached packages:
     [1] writexl_1.5.4     lubridate_1.9.4   forcats_1.0.1     stringr_1.6.0    
     [5] dplyr_1.1.4       purrr_1.2.0       readr_2.1.5       tidyr_1.3.1      
     [9] tibble_3.3.0      ggplot2_4.0.0     tidyverse_2.0.0   rmarkdown_2.30   
    [13] R.utils_2.13.0    R.oo_1.27.1       R.methodsS3_1.8.2 knitr_1.50       
    [17] grateful_0.3.0    doBy_4.7.0       

    loaded via a namespace (and not attached):
     [1] sass_0.4.10          generics_0.1.4       stringi_1.8.7       
     [4] lattice_0.22-7       hms_1.1.4            digest_0.6.37       
     [7] magrittr_2.0.4       timechange_0.3.0     evaluate_1.0.5      
    [10] grid_4.5.1           RColorBrewer_1.1-3   fastmap_1.2.0       
    [13] rprojroot_2.1.1      jsonlite_2.0.0       Matrix_1.7-4        
    [16] backports_1.5.0      scales_1.4.0         modelr_0.1.11       
    [19] microbenchmark_1.5.0 jquerylib_0.1.4      cli_3.6.5           
    [22] rlang_1.1.6          cowplot_1.2.0        withr_3.0.2         
    [25] cachem_1.1.0         yaml_2.3.10          tools_4.5.1         
    [28] tzdb_0.5.0           boot_1.3-32          Deriv_4.2.0         
    [31] broom_1.0.10         vctrs_0.6.5          R6_2.6.1            
    [34] lifecycle_1.0.4      MASS_7.3-65          pkgconfig_2.0.3     
    [37] pillar_1.11.1        bslib_0.9.0          gtable_0.3.6        
    [40] glue_1.8.0           xfun_0.54            tidyselect_1.2.1    
    [43] rstudioapi_0.17.1    farver_2.1.2         htmltools_0.5.8.1   
    [46] compiler_4.5.1       S7_0.2.0            

------------------------------------------------------------------------

# Cite R packages used

| Package | Version | Citation |
|:---|:---|:---|
| base | 4.5.1 | R Core Team (2025) |
| doBy | 4.7.0 | Halekoh and Højsgaard (2025) |
| grateful | 0.3.0 | Rodriguez-Sanchez and Jackson (2025) |
| knitr | 1.50 | Xie (2014); Xie (2015); Xie (2025) |
| R.methodsS3 | 1.8.2 | Bengtsson (2003a) |
| R.oo | 1.27.1 | Bengtsson (2003b) |
| R.utils | 2.13.0 | Bengtsson (2025) |
| rmarkdown | 2.30 | Xie, Allaire, and Grolemund (2018); Xie, Dervieux, and Riederer (2020); Allaire et al. (2025) |
| tidyverse | 2.0.0 | Wickham et al. (2019) |
| writexl | 1.5.4 | Ooms (2025) |
| RStudio | 2025.9.0.387 | Posit team (2025) |

## References

<div id="refs" class="references csl-bib-body hanging-indent"
entry-spacing="0">

<div id="ref-rmarkdown2025" class="csl-entry">

Allaire, JJ, Yihui Xie, Christophe Dervieux, Jonathan McPherson, Javier
Luraschi, Kevin Ushey, Aron Atkins, et al. 2025.
*<span class="nocase">rmarkdown</span>: Dynamic Documents for r*.
<https://github.com/rstudio/rmarkdown>.

</div>

<div id="ref-RmethodsS3" class="csl-entry">

Bengtsson, Henrik. 2003a. “The <span class="nocase">R.oo</span>
Package - Object-Oriented Programming with References Using Standard R
Code.” In *Proceedings of the 3rd International Workshop on Distributed
Statistical Computing (DSC 2003)*, edited by Kurt Hornik, Friedrich
Leisch, and Achim Zeileis. Vienna, Austria:
https://www.r-project.org/conferences/DSC-2003/Proceedings/.
<https://www.r-project.org/conferences/DSC-2003/Proceedings/Bengtsson.pdf>.

</div>

<div id="ref-Roo" class="csl-entry">

———. 2003b. “The <span class="nocase">R.oo</span> Package -
Object-Oriented Programming with References Using Standard R Code.” In
*Proceedings of the 3rd International Workshop on Distributed
Statistical Computing (DSC 2003)*, edited by Kurt Hornik, Friedrich
Leisch, and Achim Zeileis. Vienna, Austria:
https://www.r-project.org/conferences/DSC-2003/Proceedings/.
<https://www.r-project.org/conferences/DSC-2003/Proceedings/Bengtsson.pdf>.

</div>

<div id="ref-Rutils" class="csl-entry">

———. 2025. *<span class="nocase">R.utils</span>: Various Programming
Utilities*. <https://doi.org/10.32614/CRAN.package.R.utils>.

</div>

<div id="ref-doBy" class="csl-entry">

Halekoh, Ulrich, and Søren Højsgaard. 2025.
*<span class="nocase">doBy</span>: Groupwise Statistics, LSmeans, Linear
Estimates, Utilities*. <https://doi.org/10.32614/CRAN.package.doBy>.

</div>

<div id="ref-writexl" class="csl-entry">

Ooms, Jeroen. 2025. *<span class="nocase">writexl</span>: Export Data
Frames to Excel “<span class="nocase">xlsx</span>” Format*.
<https://doi.org/10.32614/CRAN.package.writexl>.

</div>

<div id="ref-rstudio" class="csl-entry">

Posit team. 2025. *RStudio: Integrated Development Environment for r*.
Boston, MA: Posit Software, PBC. <http://www.posit.co/>.

</div>

<div id="ref-base" class="csl-entry">

R Core Team. 2025. *R: A Language and Environment for Statistical
Computing*. Vienna, Austria: R Foundation for Statistical Computing.
<https://www.R-project.org/>.

</div>

<div id="ref-grateful" class="csl-entry">

Rodriguez-Sanchez, Francisco, and Connor P. Jackson. 2025.
*<span class="nocase">grateful</span>: Facilitate Citation of R
Packages*. <https://pakillo.github.io/grateful/>.

</div>

<div id="ref-tidyverse" class="csl-entry">

Wickham, Hadley, Mara Averick, Jennifer Bryan, Winston Chang, Lucy
D’Agostino McGowan, Romain François, Garrett Grolemund, et al. 2019.
“Welcome to the <span class="nocase">tidyverse</span>.” *Journal of Open
Source Software* 4 (43): 1686. <https://doi.org/10.21105/joss.01686>.

</div>

<div id="ref-knitr2014" class="csl-entry">

Xie, Yihui. 2014. “<span class="nocase">knitr</span>: A Comprehensive
Tool for Reproducible Research in R.” In *Implementing Reproducible
Computational Research*, edited by Victoria Stodden, Friedrich Leisch,
and Roger D. Peng. Chapman; Hall/CRC.

</div>

<div id="ref-knitr2015" class="csl-entry">

———. 2015. *Dynamic Documents with R and Knitr*. 2nd ed. Boca Raton,
Florida: Chapman; Hall/CRC. <https://yihui.org/knitr/>.

</div>

<div id="ref-knitr2025" class="csl-entry">

———. 2025. *<span class="nocase">knitr</span>: A General-Purpose Package
for Dynamic Report Generation in R*. <https://yihui.org/knitr/>.

</div>

<div id="ref-rmarkdown2018" class="csl-entry">

Xie, Yihui, J. J. Allaire, and Garrett Grolemund. 2018. *R Markdown: The
Definitive Guide*. Boca Raton, Florida: Chapman; Hall/CRC.
<https://bookdown.org/yihui/rmarkdown>.

</div>

<div id="ref-rmarkdown2020" class="csl-entry">

Xie, Yihui, Christophe Dervieux, and Emily Riederer. 2020. *R Markdown
Cookbook*. Boca Raton, Florida: Chapman; Hall/CRC.
<https://bookdown.org/yihui/rmarkdown-cookbook>.

</div>

</div>
