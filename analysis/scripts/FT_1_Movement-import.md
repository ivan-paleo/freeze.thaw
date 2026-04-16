Import movement dataset for the Freeze-thaw project
================
Ivan Calandra
2026-04-16 15:00:17 CEST

- [Goal of the script](#goal-of-the-script)
- [Load packages](#load-packages)
- [Read and format data](#read-and-format-data)
  - [Read in CSV file](#read-in-csv-file)
  - [Calculate new columns](#calculate-new-columns)
  - [Extract sediment info from column
    Specimen](#extract-sediment-info-from-column-specimen)
  - [Keep only SampleID in the column
    Specimen](#keep-only-sampleid-in-the-column-specimen)
  - [Re-order columns and add unit as
    comment](#re-order-columns-and-add-unit-as-comment)
  - [Check the result](#check-the-result)
- [Save data](#save-data)
  - [As XLSX](#as-xlsx)
  - [As Rbin](#as-rbin)
- [sessionInfo()](#sessioninfo)
- [Cite R packages used](#cite-r-packages-used)
  - [References](#references)

------------------------------------------------------------------------

# Goal of the script

This script formats the movement data. The script will:

1.  Read in the original file\
2.  Format the data\
3.  Write XLSX file and save R object ready for further analysis in R

``` r
dir_in  <- "analysis/raw_data"
dir_out <- "analysis/derived_data"
```

Raw data must be located in “./analysis/raw_data”.\
Formatted data will be saved in “./analysis/derived_data”.

The knit directory for this script is the project directory. This is
important to specify the path for input and output.\
However, the HTML, MD and BIB files resulting from the rendering of this
Rmd file will be located in the same folder as the Rmd file
(i.e. “`./analysis/scripts`”).

------------------------------------------------------------------------

# Load packages

``` r
library(grateful)
library(knitr)
library(R.utils)
library(rmarkdown)
library(tidyverse)
library(writexl)
```

------------------------------------------------------------------------

# Read and format data

## Read in CSV file

``` r
mov_file <- list.files(dir_in, pattern = ".*movement.*\\.csv$", full.names = TRUE) 
mov <- read.csv(mov_file, header = TRUE, fileEncoding = 'latin1')
```

The following raw data file has been read:
“analysis/raw_data/FT_movement_data.csv”

## Calculate new columns

Create new columns based on combinations of existing columns.\
All values are in cm, except for columns `Freeze-thaw_cycles` and
`NO_tube`.

``` r
mov_all <- mutate(mov,
                  Original_depth_top_point1 = Tube_height - Original_depth_bottom_point1,
                  Original_depth_top_point2 = Tube_height - Original_depth_bottom_point2,
                  Movement_point1 = Final_depth_top_point1 - Original_depth_top_point1,
                  Movement_point2 = Final_depth_top_point2 - Original_depth_top_point2,
                  Abs_movement_point1 = abs(Movement_point1),
                  Abs_movement_point2 = abs(Movement_point2),
                  Average_movement = (Movement_point1 + Movement_point2)/2,
                  Average_movement_CONV = -Average_movement,
                  Total_abs_movement = Abs_movement_point1 + Abs_movement_point2) 
```

## Extract sediment info from column Specimen

``` r
                    # Split column at every "_"
mov_all$Sediment <- strsplit(mov_all$Specimen, "_") %>% 
  
                    # Keep second value
                    sapply(., "[[", 2) %>% 
  
                    # Replace "fs" and "cs"
                    gsub("fs", "Fine sand", .) %>% 
                    gsub("cs", "Coarse sand", .) %>% 
  
                    # Capitalize first word
                    str_to_sentence()
```

## Keep only SampleID in the column Specimen

``` r
mov_all$Specimen <- strsplit(mov_all$Specimen, "_") %>% 
                    sapply(., "[[", 1)
```

## Re-order columns and add unit as comment

``` r
mov_final <- select(mov_all,
                    Specimen, Sediment, Freeze.thaw_cycles, 
                    Original_depth_bottom_point1, Original_depth_bottom_point2,
                    Original_depth_top_point1, Original_depth_top_point2,
                    Final_depth_top_point1, Final_depth_top_point2,
                    Movement_point1:Total_abs_movement,
                    Lithic_rotation, Lithic_tilting)
comment(mov_final) <- "cm"
```

Type `comment(mov_final)` to check the unit of the values.

## Check the result

``` r
str(mov_final)
```

    'data.frame':   10 obs. of  18 variables:
     $ Specimen                    : chr  "Scra21" "Scra14" "Scra25" "Scra11" ...
     $ Sediment                    : chr  "Clay" "Clay" "Quincay" "Quincay" ...
     $ Freeze.thaw_cycles          : int  330 276 330 600 330 600 330 600 330 600
     $ Original_depth_bottom_point1: int  5 5 5 5 5 5 5 5 5 5
     $ Original_depth_bottom_point2: int  5 5 5 5 5 5 5 5 5 5
     $ Original_depth_top_point1   : num  7 9 7 8 7 8 8 8 8 7.5
     $ Original_depth_top_point2   : num  7 9 7 8 7 8 8 8 8 7.5
     $ Final_depth_top_point1      : num  5.2 10.5 0.5 6 6 9.3 7.8 6 3 5
     $ Final_depth_top_point2      : num  9.9 11.5 6 8 5.6 11.5 9.8 8.5 4.3 10
     $ Movement_point1             : num  -1.8 1.5 -6.5 -2 -1 1.3 -0.2 -2 -5 -2.5
     $ Movement_point2             : num  2.9 2.5 -1 0 -1.4 3.5 1.8 0.5 -3.7 2.5
     $ Abs_movement_point1         : num  1.8 1.5 6.5 2 1 1.3 0.2 2 5 2.5
     $ Abs_movement_point2         : num  2.9 2.5 1 0 1.4 3.5 1.8 0.5 3.7 2.5
     $ Average_movement            : num  0.55 2 -3.75 -1 -1.2 2.4 0.8 -0.75 -4.35 0
     $ Average_movement_CONV       : num  -0.55 -2 3.75 1 1.2 -2.4 -0.8 0.75 4.35 0
     $ Total_abs_movement          : num  4.7 4 7.5 2 2.4 4.8 2 2.5 8.7 5
     $ Lithic_rotation             : chr  "partial" "complete" "partial" "no" ...
     $ Lithic_tilting              : chr  "vertical" "partial" "vertical" "partial" ...
     - attr(*, "comment")= chr "cm"

``` r
head(mov_final)
```

      Specimen  Sediment Freeze.thaw_cycles Original_depth_bottom_point1
    1   Scra21      Clay                330                            5
    2   Scra14      Clay                276                            5
    3   Scra25   Quincay                330                            5
    4   Scra11   Quincay                600                            5
    5   Scra17 Fine sand                330                            5
    6   Scra16 Fine sand                600                            5
      Original_depth_bottom_point2 Original_depth_top_point1
    1                            5                         7
    2                            5                         9
    3                            5                         7
    4                            5                         8
    5                            5                         7
    6                            5                         8
      Original_depth_top_point2 Final_depth_top_point1 Final_depth_top_point2
    1                         7                    5.2                    9.9
    2                         9                   10.5                   11.5
    3                         7                    0.5                    6.0
    4                         8                    6.0                    8.0
    5                         7                    6.0                    5.6
    6                         8                    9.3                   11.5
      Movement_point1 Movement_point2 Abs_movement_point1 Abs_movement_point2
    1            -1.8             2.9                 1.8                 2.9
    2             1.5             2.5                 1.5                 2.5
    3            -6.5            -1.0                 6.5                 1.0
    4            -2.0             0.0                 2.0                 0.0
    5            -1.0            -1.4                 1.0                 1.4
    6             1.3             3.5                 1.3                 3.5
      Average_movement Average_movement_CONV Total_abs_movement Lithic_rotation
    1             0.55                 -0.55                4.7         partial
    2             2.00                 -2.00                4.0        complete
    3            -3.75                  3.75                7.5         partial
    4            -1.00                  1.00                2.0              no
    5            -1.20                  1.20                2.4              no
    6             2.40                 -2.40                4.8              no
      Lithic_tilting
    1       vertical
    2        partial
    3       vertical
    4        partial
    5        partial
    6        partial

------------------------------------------------------------------------

# Save data

## As XLSX

``` r
write_xlsx(list("data" = mov_final, "unit" = data.frame(Unit = paste("All values are in", comment(mov_final)))), 
           path = paste0(dir_out, "/FT_movement_formatted-data.xlsx"))
```

## As Rbin

``` r
saveObject(mov_final, file = paste0(dir_out, "/FT_movement_formatted-data.Rbin"))
```

Rbin files (e.g. `FT_movement_formatted-data.Rbin`) can be easily read
into an R object (e.g. `rbin_data`) using the following code:

``` r
library(R.utils)
rbin_data <- loadObject("FT_movement_formatted-data")
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
    [1] stats     graphics  grDevices utils     datasets  methods   base     

    other attached packages:
     [1] writexl_1.5.4     lubridate_1.9.5   forcats_1.0.1     stringr_1.6.0    
     [5] dplyr_1.2.1       purrr_1.2.2       readr_2.2.0       tidyr_1.3.2      
     [9] tibble_3.3.1      ggplot2_4.0.2     tidyverse_2.0.0   rmarkdown_2.31   
    [13] R.utils_2.13.0    R.oo_1.27.1       R.methodsS3_1.8.2 knitr_1.51       
    [17] grateful_0.3.0   

    loaded via a namespace (and not attached):
     [1] gtable_0.3.6       jsonlite_2.0.0     compiler_4.5.3     tidyselect_1.2.1  
     [5] jquerylib_0.1.4    scales_1.4.0       yaml_2.3.12        fastmap_1.2.0     
     [9] R6_2.6.1           generics_0.1.4     rprojroot_2.1.1    tzdb_0.5.0        
    [13] bslib_0.10.0       pillar_1.11.1      RColorBrewer_1.1-3 rlang_1.2.0       
    [17] stringi_1.8.7      cachem_1.1.0       xfun_0.57          sass_0.4.10       
    [21] S7_0.2.1           otel_0.2.0         timechange_0.4.0   cli_3.6.6         
    [25] withr_3.0.2        magrittr_2.0.5     digest_0.6.39      grid_4.5.3        
    [29] rstudioapi_0.18.0  hms_1.1.4          lifecycle_1.0.5    vctrs_0.7.3       
    [33] evaluate_1.0.5     glue_1.8.0         farver_2.1.2       tools_4.5.3       
    [37] pkgconfig_2.0.3    htmltools_0.5.9   

------------------------------------------------------------------------

# Cite R packages used

| Package | Version | Citation |
|:---|:---|:---|
| base | 4.5.3 | R Core Team (2026) |
| grateful | 0.3.0 | Rodriguez-Sanchez and Jackson (2025) |
| knitr | 1.51 | Xie (2014); Xie (2015); Xie (2025) |
| R.methodsS3 | 1.8.2 | Bengtsson (2003a) |
| R.oo | 1.27.1 | Bengtsson (2003b) |
| R.utils | 2.13.0 | Bengtsson (2025) |
| rmarkdown | 2.31 | Xie et al. (2018); Xie et al. (2020); Allaire et al. (2026) |
| tidyverse | 2.0.0 | Wickham et al. (2019) |
| writexl | 1.5.4 | Ooms (2025) |
| RStudio | 2026.1.2.418 | Posit team (2026) |

## References

<div id="refs" class="references csl-bib-body hanging-indent">

<div id="ref-rmarkdown2026" class="csl-entry">

Allaire, JJ, Yihui Xie, Christophe Dervieux, et al. 2026.
*<span class="nocase">rmarkdown</span>: Dynamic Documents for r*.
<https://github.com/rstudio/rmarkdown>.

</div>

<div id="ref-RmethodsS3" class="csl-entry">

Bengtsson, Henrik. 2003a. “The <span class="nocase">R.oo</span>
Package - Object-Oriented Programming with References Using Standard R
Code.” In *Proceedings of the 3rd International Workshop on Distributed
Statistical Computing (DSC 2003)*, edited by Kurt Hornik, Friedrich
Leisch, and Achim Zeileis.
Https://www.r-project.org/conferences/DSC-2003/Proceedings/.
<https://www.r-project.org/conferences/DSC-2003/Proceedings/Bengtsson.pdf>.

</div>

<div id="ref-Roo" class="csl-entry">

Bengtsson, Henrik. 2003b. “The <span class="nocase">R.oo</span>
Package - Object-Oriented Programming with References Using Standard R
Code.” In *Proceedings of the 3rd International Workshop on Distributed
Statistical Computing (DSC 2003)*, edited by Kurt Hornik, Friedrich
Leisch, and Achim Zeileis.
Https://www.r-project.org/conferences/DSC-2003/Proceedings/.
<https://www.r-project.org/conferences/DSC-2003/Proceedings/Bengtsson.pdf>.

</div>

<div id="ref-Rutils" class="csl-entry">

Bengtsson, Henrik. 2025. *<span class="nocase">R.utils</span>: Various
Programming Utilities*. <https://doi.org/10.32614/CRAN.package.R.utils>.

</div>

<div id="ref-writexl" class="csl-entry">

Ooms, Jeroen. 2025. *<span class="nocase">writexl</span>: Export Data
Frames to Excel “<span class="nocase">xlsx</span>” Format*.
<https://doi.org/10.32614/CRAN.package.writexl>.

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

<div id="ref-tidyverse" class="csl-entry">

Wickham, Hadley, Mara Averick, Jennifer Bryan, et al. 2019. “Welcome to
the <span class="nocase">tidyverse</span>.” *Journal of Open Source
Software* 4 (43): 1686. <https://doi.org/10.21105/joss.01686>.

</div>

<div id="ref-knitr2014" class="csl-entry">

Xie, Yihui. 2014. “<span class="nocase">knitr</span>: A Comprehensive
Tool for Reproducible Research in R.” In *Implementing Reproducible
Computational Research*, edited by Victoria Stodden, Friedrich Leisch,
and Roger D. Peng. Chapman; Hall/CRC.

</div>

<div id="ref-knitr2015" class="csl-entry">

Xie, Yihui. 2015. *Dynamic Documents with R and Knitr*. 2nd ed. Chapman;
Hall/CRC. <https://yihui.org/knitr/>.

</div>

<div id="ref-knitr2025" class="csl-entry">

Xie, Yihui. 2025. *<span class="nocase">knitr</span>: A General-Purpose
Package for Dynamic Report Generation in R*. <https://yihui.org/knitr/>.

</div>

<div id="ref-rmarkdown2018" class="csl-entry">

Xie, Yihui, J. J. Allaire, and Garrett Grolemund. 2018. *R Markdown: The
Definitive Guide*. Chapman; Hall/CRC. <https://yihui.org/rmarkdown/>.

</div>

<div id="ref-rmarkdown2020" class="csl-entry">

Xie, Yihui, Christophe Dervieux, and Emily Riederer. 2020. *R Markdown
Cookbook*. Chapman; Hall/CRC. <https://yihui.org/rmarkdown-cookbook>.

</div>

</div>
