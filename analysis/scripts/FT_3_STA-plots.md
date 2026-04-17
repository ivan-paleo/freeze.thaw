Plots for the the Freeze-thaw project
================
Ivan Calandra
2026-04-17 10:52:40 CEST

- [Goal of the script](#goal-of-the-script)
- [Load packages](#load-packages)
- [Read in STA data](#read-in-sta-data)
- [Format STA data](#format-sta-data)
  - [Exclude NMP, and combine Specimen and Location in one
    column](#exclude-nmp-and-combine-specimen-and-location-in-one-column)
  - [Create new datasets based on
    NMP_cat](#create-new-datasets-based-on-nmp_cat)
  - [Calculate the mean per specimen per
    cycle](#calculate-the-mean-per-specimen-per-cycle)
  - [Add units to headers for
    plotting](#add-units-to-headers-for-plotting)
- [Line plots of STA for height maps with \<17%
  NMP](#line-plots-of-sta-for-height-maps-with-17-nmp)
  - [Create list to receive the
    plots](#create-list-to-receive-the-plots)
  - [Plots](#plots)
  - [Save plots](#save-plots)
- [PCA for STA with height maps with \<17%
  NMP](#pca-for-sta-with-height-maps-with-17-nmp)
  - [Format data](#format-data)
    - [Define function to calculate
      difference](#define-function-to-calculate-difference)
    - [Calculate the difference between after and
      before](#calculate-the-difference-between-after-and-before)
  - [Custom plotting function](#custom-plotting-function)
  - [Define columns for colors and
    shapes](#define-columns-for-colors-and-shapes)
  - [Run PCA with all parameters](#run-pca-with-all-parameters)
  - [PCA on selected parameters](#pca-on-selected-parameters)
    - [Select surface texture
      parameters](#select-surface-texture-parameters)
    - [PCA](#pca)
    - [Plots](#plots-1)
      - [Scree plot](#scree-plot)
      - [PC1-2](#pc1-2)
      - [PC3-4](#pc3-4)
      - [Combine plots to save them into 1
        file](#combine-plots-to-save-them-into-1-file)
      - [Save plots](#save-plots-1)
- [Combined plots with STA-PCA data and movement
  data](#combined-plots-with-sta-pca-data-and-movement-data)
  - [Read in movement data](#read-in-movement-data)
  - [Extract PC scores on PC1 to PC4](#extract-pc-scores-on-pc1-to-pc4)
  - [Calculate variability of PC scores per specimen and sediment, using
    only used
    surfaces](#calculate-variability-of-pc-scores-per-specimen-and-sediment-using-only-used-surfaces)
  - [Combine data](#combine-data)
  - [Combined plots](#combined-plots)
    - [Save to PDF](#save-to-pdf)
- [Line plots for STA with height maps with \<10%
  NMP](#line-plots-for-sta-with-height-maps-with-10-nmp)
- [sessionInfo()](#sessioninfo)
- [Cite R packages used](#cite-r-packages-used)
  - [References](#references)

------------------------------------------------------------------------

# Goal of the script

The script plots all STA variables for the Freeze-thaw dataset.

``` r
dir_in  <- "analysis/derived_data"
dir_plots <- "analysis/plots"
```

Input Rbin data file must be located in “./analysis/derived_data”.  
Plots will be saved in “./analysis/plots”.

The knit directory for this script is the project directory. This is
important to specify the path for input and output.  
However, the HTML, MD and BIB files resulting from the rendering of this
Rmd file will be located in the same folder as the Rmd file
(i.e. “`./analysis/scripts`”).

------------------------------------------------------------------------

# Load packages

``` r
library(doBy)
library(factoextra)
library(ggarrow)
library(ggplot2)
library(grateful)
library(knitr)
library(patchwork)
library(R.utils)
library(RColorBrewer)
library(rmarkdown)
library(tidyverse)
```

------------------------------------------------------------------------

# Read in STA data

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

    'data.frame':   80 obs. of  42 variables:
     $ Specimen                : chr  "Scra11" "Scra11" "Scra11" "Scra11" ...
     $ Sediment                : chr  "Quincay" "Quincay" "Quincay" "Quincay" ...
     $ Cycles                  : num  600 600 600 600 0 0 0 0 330 330 ...
     $ State                   : Factor w/ 2 levels "before","after": 2 2 2 2 1 1 1 1 2 2 ...
     $ Location                : chr  "loc1" "loc2" "loc3" "loc4" ...
     $ Use                     : chr  "used" "used" "used" "unused" ...
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

      Specimen Sediment Cycles  State Location    Use      NMP NMP_cat       Sq
    1   Scra11  Quincay    600  after     loc1   used 6.210140    <10% 510.3147
    2   Scra11  Quincay    600  after     loc2   used 8.988605    <10% 599.4221
    3   Scra11  Quincay    600  after     loc3   used 9.360195    <10% 626.8703
    4   Scra11  Quincay    600  after     loc4 unused 7.087054    <10% 535.8293
    5   Scra11  Quincay      0 before     loc1   used 5.585075    <10% 461.3625
    6   Scra11  Quincay      0 before     loc2   used 7.824051    <10% 537.4528
             Ssk       Sku       Sp       Sv       Sz       Sa       Smr      Smc
    1  0.2082995  3.611167 2002.269 1895.237 3897.506 395.2242 3.3585136 630.9984
    2  0.1038576  4.277262 2280.247 2129.979 4410.227 444.2419 2.6425792 705.0265
    3 -1.3491279 16.215072 4021.251 4888.391 8909.643 399.6521 0.1384577 594.0357
    4  0.3066295  3.504882 2236.933 1622.238 3859.171 420.7068 1.9326553 670.0448
    5  0.3644242  3.438282 1940.378 1193.170 3133.549 362.0558 3.1472606 568.2292
    6 -0.3339857  4.060290 1796.531 2537.250 4333.781 412.1910 6.2930831 674.2111
            Sxp      Sal       Str      Std       Ssw       Sdq       Sdr
    1  959.4634 5.535718 0.7571265 54.74853 0.4249085 0.5937326 14.108213
    2 1299.1056 6.570978 0.6973378 74.74841 0.4249085 0.3186908  4.665525
    3 1118.6396 5.662375 0.6112438 85.50156 0.4249085 0.3722057  5.960471
    4  961.0071 5.693638 0.6484318 57.75130 0.4249085 0.3241989  4.854970
    5  834.1125 6.107537 0.6810785 86.25058 0.4249085 0.3179695  4.701684
    6 1111.3785 5.961985 0.6743632 86.99719 0.4249085 0.4060171  7.404031
              Vm        Vv        Vmp       Vmc       Vvc        Vvv
    1 0.03220712 0.6632383 0.03220712 0.4298770 0.6058456 0.05739273
    2 0.04181877 0.7468693 0.04181877 0.4518377 0.6665194 0.08034989
    3 0.04105984 0.6350726 0.04105984 0.3806123 0.5465987 0.08847389
    4 0.03305849 0.7030889 0.03305849 0.4670876 0.6490426 0.05404634
    5 0.03014862 0.5983675 0.03014862 0.4005538 0.5514198 0.04694766
    6 0.02403996 0.6982755 0.02403996 0.4565223 0.6277425 0.07053295
      First.direction Second.direction Third.direction Texture.isotropy
    1        44.99067       0.01081937        90.00509         82.36371
    2        89.99559     179.99523860       134.98878         68.76672
    3        90.00050      44.99608453       116.43945         58.82372
    4        44.98490      90.01423227       135.00686         71.81574
    5        89.99058     135.02619380       179.99602         72.18861
    6        45.01407      89.99811744        63.56398         57.93184
      Maximum.depth.of.furrows Mean.depth.of.furrows Mean.density.of.furrows
    1                 3418.379              1714.284                6492.159
    2                 4350.215              1899.377                6945.496
    3                 9461.823              3459.689                7121.221
    4                 3943.282              1857.354                6632.412
    5                 3051.558              1527.908                6748.034
    6                 4003.873              1607.176                7054.717
            epLsar  NewEplsar     Asfc      Smfc    HAsfc9
    1 0.0005855797 0.01748435 49.92666 1.0805865 0.1333228
    2 0.0028073607 0.01681894 13.83172 1.1530754 0.4437028
    3 0.0017361474 0.01787320 19.82069 1.4950314 0.9494297
    4 0.0023834012 0.01666934 13.06050 1.1530754 0.3135672
    5 0.0024736287 0.01686902 13.10260 0.9489935 0.1773680
    6 0.0012932325 0.01710849 26.90327 0.8893344 0.2908531

------------------------------------------------------------------------

# Format STA data

## Exclude NMP, and combine Specimen and Location in one column

``` r
# Exclude NMP
FT <- select(FT, !NMP)  %>% 
  
      # Combine Specimen and Location in one column
      # Necessary to connect points when plotting individual points
      mutate(SpecLoc = paste(Specimen, Location, sep = "-")) %>%
  
      # Combine Specimen and Use in one column
      # Necessary to connect points when plotting mean points
      mutate(SpecUse = paste(Specimen, Use, sep = "-"))
```

## Create new datasets based on NMP_cat

``` r
# Select all rows with NMP_cat ≤ 10% 
FT_NMP10 <- filter(FT, NMP_cat == "<10%")

# Select all rows with NMP_cat ≤ 10% or 10-17%
FT_NMP17 <- filter(FT, NMP_cat %in% c("<10%", "10-17%"))
```

When considering only height maps with 10% NMP or less, 45 height maps
are excluded and 35 are kept for further analysis.  
When considering only height maps with 17% NMP or less, 8 height maps
are excluded and 72 are kept for further analysis.

## Calculate the mean per specimen per cycle

``` r
# Calculate means based on Specimen + Cycles + Use
# Sediment and SpecUse are listed as factor in order to keep these columns
# keep.names = TRUE is important for the matching on names in the plots
FT_NMP10_mean <- summaryBy(.~ Specimen + Sediment + Cycles + Use + SpecUse, 
                           data = FT_NMP10, FUN = mean, keep.names = TRUE)
FT_NMP17_mean <- summaryBy(.~ Specimen + Sediment + Cycles + Use + SpecUse, 
                           data = FT_NMP17, FUN = mean, keep.names = TRUE)
```

## Add units to headers for plotting

This cannot be done before on `FT` because it create problems during the
calculations of the means due to the column names with spaces and
special characters (for units).  
Also, for `FT_NMP10` and `FT_NMP17`, a copy is created here because such
column names are also problematic for the PCA (see below).

``` r
# Get units from comment(FT)
table_units <- comment(FT) %>% 
               data.frame(Parameter = names(.), Unit = ., row.names = NULL) %>% 
  
               # Exclude NMP because it won't be plotted
               filter(Parameter != "NMP") %>% 
  
               # Paste parameter name and unit together in a new column
               mutate(Param_unit = paste0(Parameter, " [", Unit, "]")) 

# Remove > and < symbols
table_units$Param_unit <- gsub(">|<", "", table_units$Param_unit)

# Create copies of FT_NMP10 and FT_NMP17
FT_NMP10_units <- FT_NMP10
FT_NMP17_units <- FT_NMP17

# Adjust column names
colnames(FT_NMP10_units)[colnames(FT_NMP10_units) %in% table_units$Parameter] <- table_units$Param_unit
colnames(FT_NMP17_units)[colnames(FT_NMP17_units) %in% table_units$Parameter] <- table_units$Param_unit
colnames(FT_NMP10_mean)[colnames(FT_NMP10_mean)   %in% table_units$Parameter] <- table_units$Param_unit
colnames(FT_NMP17_mean)[colnames(FT_NMP17_mean)   %in% table_units$Parameter] <- table_units$Param_unit
```

These are the new column names for the plots:

    Sq [nm]
    Ssk [no unit]
    Sku [no unit]
    Sp [nm]
    Sv [nm]
    Sz [nm]
    Sa [nm]
    Smr [%]
    Smc [nm]
    Sxp [nm]
    Sal [µm]
    Str [no unit]
    Std [°]
    Ssw [µm]
    Sdq [no unit]
    Sdr [%]
    Vm [µm³/µm²]
    Vv [µm³/µm²]
    Vmp [µm³/µm²]
    Vmc [µm³/µm²]
    Vvc [µm³/µm²]
    Vvv [µm³/µm²]
    First.direction [°]
    Second.direction [°]
    Third.direction [°]
    Texture.isotropy [%]
    Maximum.depth.of.furrows [nm]
    Mean.depth.of.furrows [nm]
    Mean.density.of.furrows [cm/cm2]
    epLsar [no unit]
    NewEplsar [no unit]
    Asfc [no unit]
    Smfc [µm²]
    HAsfc9 [no unit]

------------------------------------------------------------------------

# Line plots of STA for height maps with \<17% NMP

See section [Line plots for height maps with \<10%
NMP](#line-plots-for-height-maps-with-10-nmp) for line plots for height
maps with \<10% NMP.

## Create list to receive the plots

``` r
p_line_NMP17 <- vector(mode = "list", length = nrow(table_units))
names(p_line_NMP17) <- table_units$Param_unit
```

## Plots

``` r
# Design for patchwork combination of plots (see below)
design_patch <- c(area(1, 1, 2, 3), area(3, 1, 3, 1), area(3, 2, 3, 3))

# Plot for every parameters
for (i in names(p_line_NMP17)) {
  
  # Define y-axis limits based on the range of the y-variable
  # This ensures that both plots have the same y-range
  # Not used currently
  #range_y <- range(FT_NMP17_units[[i]])
  
  # Plot of individual data points
             #  Define aesthetics 
  p_indiv <- ggplot(data = FT_NMP17_units, aes(x = Cycles, y = .data[[i]], color = Sediment, shape = Use)) + 
             
             # Add points
             geom_point(size = 3) + 
     
             # Facet plot by 'Sediment'
             facet_wrap(~ Sediment) +
    
             # Add lines to connect points with identical "SpecLoc"
             geom_line(linewidth = 0.5, aes(group = SpecLoc), show.legend = FALSE) +
    
             # Light theme
             theme_classic() +
     
             # Set2 "may be a problem for some, but not all folks with color vision impairment".
             # However, there is no colorblind safe qualitative set with 5 data classes.
             scale_color_brewer(palette = 'Set2') +
    
             # Set y-limits
             # Not used currently
             #ylim(range_y[1], range_y[2]) +
    
             # Add title to plot
             labs(title = "Individual data points") +
    
             # Position and orientation of legend
             theme(legend.direction = "horizontal", legend.title.position = "top")

  # Plot of means per sample and cycle
  p_mean <- ggplot(data = FT_NMP17_mean, aes(x = Cycles, y = .data[[i]], color = Sediment, shape = Use)) + 
            geom_point(size = 3) + 
            geom_line(linewidth = 0.5, aes(group = SpecUse), show.legend = FALSE) +
            theme_classic() +
            scale_color_brewer(palette = 'Set2') +
            #ylim(range_y[1], range_y[2]) +
            labs(title = "Mean per sample+cycle") +
            theme(legend.direction = "horizontal", legend.title.position = "top")

  # Combine both plots with patchwork
  p_line_NMP17[[i]] <- p_indiv / p_mean + guide_area() + 
                       plot_layout(guides = 'collect', design = design_patch)
}

# Print all plots
print(p_line_NMP17)
```

    $`Sq [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->


    $`Ssk [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-2.png)<!-- -->


    $`Sku [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-3.png)<!-- -->


    $`Sp [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-4.png)<!-- -->


    $`Sv [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-5.png)<!-- -->


    $`Sz [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-6.png)<!-- -->


    $`Sa [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-7.png)<!-- -->


    $`Smr [%]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-8.png)<!-- -->


    $`Smc [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-9.png)<!-- -->


    $`Sxp [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-10.png)<!-- -->


    $`Sal [µm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-11.png)<!-- -->


    $`Str [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-12.png)<!-- -->


    $`Std [°]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-13.png)<!-- -->


    $`Ssw [µm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-14.png)<!-- -->


    $`Sdq [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-15.png)<!-- -->


    $`Sdr [%]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-16.png)<!-- -->


    $`Vm [µm³/µm²]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-17.png)<!-- -->


    $`Vv [µm³/µm²]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-18.png)<!-- -->


    $`Vmp [µm³/µm²]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-19.png)<!-- -->


    $`Vmc [µm³/µm²]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-20.png)<!-- -->


    $`Vvc [µm³/µm²]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-21.png)<!-- -->


    $`Vvv [µm³/µm²]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-22.png)<!-- -->


    $`First.direction [°]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-23.png)<!-- -->


    $`Second.direction [°]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-24.png)<!-- -->


    $`Third.direction [°]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-25.png)<!-- -->


    $`Texture.isotropy [%]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-26.png)<!-- -->


    $`Maximum.depth.of.furrows [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-27.png)<!-- -->


    $`Mean.depth.of.furrows [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-28.png)<!-- -->


    $`Mean.density.of.furrows [cm/cm2]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-29.png)<!-- -->


    $`epLsar [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-30.png)<!-- -->


    $`NewEplsar [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-31.png)<!-- -->


    $`Asfc [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-32.png)<!-- -->


    $`Smfc [µm²]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-33.png)<!-- -->


    $`HAsfc9 [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-11-34.png)<!-- -->

## Save plots

``` r
ggsave(filename = "FT_STA-plots_NMP17.pdf", path = dir_plots, plot = p_line_NMP17, 
       width = 190, height = 200, units = "mm")
```

------------------------------------------------------------------------

# PCA for STA with height maps with \<17% NMP

Note that there are too few data points with only height maps \<10% NMP
to be meaningful for a PCA, so the PCAs are done only with height maps
\< 17% NMP.

## Format data

PCA will be applied to the differences between after and before the
experiments, for all surface texture parameter.  
The method below works only with 2 values per group, which is the case
here.

### Define function to calculate difference

``` r
# Create function to subtract the 1st value from the 2nd value of a vector
# Returns NA if at least one value is NA
fun_minus <- function(x) {
  x[2] - x[1]
}
```

### Calculate the difference between after and before

``` r
                     # Exclude Cycles
FT_NMP17_pca_data <- select(FT_NMP17, !c(Cycles, NMP_cat)) %>% 
  
                     # Arrange to make sure that the difference is calculated with the correct values 
                     # in the correct order
                     arrange(Specimen, Location, State) %>% 
  
                     # Group by Specimen, Sediment and Location (= everything except State)
                     group_by(Specimen, Sediment, Location, Use) %>%
  
                     # Apply function fun_minus to each group
                     summarize(across(where(is.numeric), fun_minus)) %>% 
  
                     # Remove rows with NA
                     na.omit() 
```

## Custom plotting function

The library `factoextra` is not maintained anymore so there are some
warnings due to issues with newer versions of ggplot2.

``` r
custom_pca_biplot <- function(dat, datpca, pc = c(1, 2), geom.pt = "point", col.pt, mean.pt = FALSE, 
                              col.pal = brewer.pal(length(unique(datpca[[col.pt]])), "Set2"), 
                              pt.size = 3, shape.pt, pt.fill = "white", alph = 1,
                              elli = TRUE, elli.type = "convex", repel.lab = TRUE, 
                              col.variable = "black", main.title){
  
  # Define plotting
  p_out <- fviz_pca_ind(dat, axes = pc, 
                           geom.ind = geom.pt, col.ind = datpca[[col.pt]], mean.point = mean.pt,
                           palette = col.pal, pointsize = pt.size, fill.ind = pt.fill, pointshape = 19,
                           addEllipses = elli, ellipse.type = elli.type, alpha = alph,  
                           repel = repel.lab, col.var = col.variable, title = main.title, legend.title = "") +
           theme(legend.position = "bottom", legend.box = "vertical") +
           geom_point(aes(shape = datpca[[shape.pt]], color = datpca[[col.pt]]), size = 3) +
           guides(shape = guide_legend(title = shape.pt),
                  color = guide_legend(title = col.pt),
                  fill = "none")

  # Return plotting object
  return(p_out)
}
```

## Define columns for colors and shapes

``` r
col_PCA <- "Sediment"
shape_PCA <- "Use"
```

## Run PCA with all parameters

All STA parameters are used here, except Ssw (column 18), which is
almost always 0.  
This PCA is only meant to select the most informative parameters.  
For comments on the code, see section [PCA on selected
parameters](#pca-on-selected-parameters).

``` r
pca_FT_NMP17_all <- prcomp(FT_NMP17_pca_data[c(5:17, 19:38)], scale. = TRUE, center = TRUE)

pca_FT_NMP17_all_scree <- fviz_screeplot(pca_FT_NMP17_all, addlabels = TRUE, 
                                         ggtheme = theme_classic(), title = "Scree plot - all parameters")
print(pca_FT_NMP17_all_scree)
```

    Warning in geom_bar(stat = "identity", fill = barfill, color = barcolor, :
    Ignoring empty aesthetic: `width`.

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-17-1.png)<!-- -->

``` r
pca_axes12 <- c(1, 2)
plot_title12_all <- paste("PC", paste(pca_axes12, collapse = "&"), "- all parameters")

pca_FT_NMP17_all_ind12 <- custom_pca_biplot(pca_FT_NMP17_all, datpca = FT_NMP17_pca_data, pc = pca_axes12, 
                                            col.pt = col_PCA, shape.pt = shape_PCA, alph = 0,
                                            main.title = plot_title12_all)
print(pca_FT_NMP17_all_ind12)
```

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-17-2.png)<!-- -->

``` r
pca_FT_NMP17_all_var12 <- fviz_pca_var(pca_FT_NMP17_all, axes = pca_axes12, 
                                       col.var = "black", repel = TRUE, title = plot_title12_all)
print(pca_FT_NMP17_all_var12)
```

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-17-3.png)<!-- -->

``` r
pca_axes34 <- c(3, 4)
plot_title34_all <- paste("PC", paste(pca_axes34, collapse = "&"), "- all parameters")

pca_FT_NMP17_all_ind34 <- custom_pca_biplot(pca_FT_NMP17_all, datpca = FT_NMP17_pca_data, pc = pca_axes34, 
                                            col.pt = col_PCA, shape.pt = shape_PCA, alph = 0,
                                            main.title = plot_title34_all)
print(pca_FT_NMP17_all_ind34)
```

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-17-4.png)<!-- -->

``` r
pca_FT_NMP17_all_var34 <- fviz_pca_var(pca_FT_NMP17_all, axes = pca_axes34, 
                                       col.var = "black", repel = TRUE, title = plot_title34_all)
print(pca_FT_NMP17_all_var34)
```

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-17-5.png)<!-- -->

``` r
all_plots_17_all <- list(pca_FT_NMP17_all_scree, 
                         pca_FT_NMP17_all_ind12, pca_FT_NMP17_all_var12,
                         pca_FT_NMP17_all_ind34, pca_FT_NMP17_all_var34)  
ggsave(filename = "FT_PCA-plots_NMP17_all-params.pdf", path = dir_plots, plot = all_plots_17_all, 
       width = 190, height = 190, units = "mm")
```

    Warning in geom_bar(stat = "identity", fill = barfill, color = barcolor, :
    Ignoring empty aesthetic: `width`.

## PCA on selected parameters

### Select surface texture parameters

The following parameters are selected for the PCA:

``` r
pca_params <- c("Sq", "Ssk", "Sv", "Sxp",
                "Vv", "Vvc", "Vm",
                "Sal", "Str", "epLsar",
                "Mean.density.of.furrows", "Mean.depth.of.furrows", "Maximum.depth.of.furrows",
                "Asfc", "HAsfc9")
cat(paste0(pca_params, "\n"), sep = "")
```

    Sq
    Ssk
    Sv
    Sxp
    Vv
    Vvc
    Vm
    Sal
    Str
    epLsar
    Mean.density.of.furrows
    Mean.depth.of.furrows
    Maximum.depth.of.furrows
    Asfc
    HAsfc9

The selection was based on the [PCA with all
parameters](#run-pca-with-all-parameters), trying to select parameters
that contribute most to the first 4 PCs and trying to avoid parameters
that correspond to the same property of the surface texture (e.g. Sa and
Sq), although some surprisingly provide a different signal (e.g. Str
correlating with PC1 and epLsar with PC2).

### PCA

``` r
pca_FT_NMP17 <- prcomp(FT_NMP17_pca_data[ , pca_params], scale. = TRUE, center = TRUE)
```

### Plots

#### Scree plot

``` r
pca_FT_NMP17_scree <- fviz_screeplot(pca_FT_NMP17, addlabels = TRUE, 
                                     ggtheme = theme_classic(), title = "Scree plot - selected parameters")
print(pca_FT_NMP17_scree)
```

    Warning in geom_bar(stat = "identity", fill = barfill, color = barcolor, :
    Ignoring empty aesthetic: `width`.

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-20-1.png)<!-- -->

#### PC1-2

``` r
# Plot title
plot_title12 <- paste("PC", paste(pca_axes12, collapse = "&"), "- selected parameters")

# Individual points
pca_FT_NMP17_ind12 <- custom_pca_biplot(pca_FT_NMP17, datpca = FT_NMP17_pca_data, pc = pca_axes12, 
                                            col.pt = col_PCA, shape.pt = shape_PCA, alph = 0,
                                            main.title = plot_title12)
print(pca_FT_NMP17_ind12)
```

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-21-1.png)<!-- -->

``` r
# Contributions of variables
pca_FT_NMP17_var12 <- fviz_pca_var(pca_FT_NMP17, axes = pca_axes12, 
                                   col.var = "black", repel = TRUE, title = plot_title12)
print(pca_FT_NMP17_var12)
```

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-21-2.png)<!-- -->

#### PC3-4

``` r
plot_title34 <- paste("PC", paste(pca_axes34, collapse = "&"), "- selected parameters")
pca_FT_NMP17_ind34 <- custom_pca_biplot(pca_FT_NMP17, datpca = FT_NMP17_pca_data, pc = pca_axes34, 
                                            col.pt = col_PCA, shape.pt = shape_PCA, alph = 0,
                                            main.title = plot_title34)
print(pca_FT_NMP17_ind34)
```

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-22-1.png)<!-- -->

``` r
pca_FT_NMP17_var34 <- fviz_pca_var(pca_FT_NMP17, axes = pca_axes34, 
                                   col.var = "black", repel = TRUE, title = plot_title34)
print(pca_FT_NMP17_var34)
```

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-22-2.png)<!-- -->

#### Combine plots to save them into 1 file

``` r
all_plots_17 <- list(pca_FT_NMP17_scree, 
                     pca_FT_NMP17_ind12, pca_FT_NMP17_var12,
                     pca_FT_NMP17_ind34, pca_FT_NMP17_var34)    
```

#### Save plots

``` r
ggsave(filename = "FT_PCA-plots_NMP17.pdf", path = dir_plots, plot = all_plots_17, 
       width = 190, height = 190, units = "mm")
```

    Warning in geom_bar(stat = "identity", fill = barfill, color = barcolor, :
    Ignoring empty aesthetic: `width`.

------------------------------------------------------------------------

# Combined plots with STA-PCA data and movement data

Here, the PCA data for height maps with \<17% NMP with selected
parameters are combined graphically with movement data.

## Read in movement data

``` r
mov_file <- list.files(dir_in, pattern = ".*movement.*\\.Rbin$", full.names = TRUE)
mov <- loadObject(mov_file)
```

The following data file has been read:
“analysis/derived_data/FT_movement_formatted-data.Rbin”

Below are its structure and first lines:

``` r
str(mov)
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
head(mov)
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

## Extract PC scores on PC1 to PC4

``` r
pca_FT_NMP17_scores <- data.frame(FT_NMP17_pca_data[1:4], pca_FT_NMP17$x[, c("PC1", "PC2", "PC3", "PC4")])
```

Below are the structure and first lines of the PC scores:

``` r
str(pca_FT_NMP17_scores)
```

    'data.frame':   34 obs. of  8 variables:
     $ Specimen: chr  "Scra11" "Scra11" "Scra11" "Scra11" ...
     $ Sediment: chr  "Quincay" "Quincay" "Quincay" "Quincay" ...
     $ Location: chr  "loc1" "loc2" "loc3" "loc4" ...
     $ Use     : chr  "used" "used" "used" "unused" ...
     $ PC1     : num  -3.7 -3.24 -4.09 3.67 -1.79 ...
     $ PC2     : num  -1.64 4.03 -5 1.81 -1.69 ...
     $ PC3     : num  -1.308 -0.174 4.135 1.783 0.299 ...
     $ PC4     : num  2.1702 -2.013 0.0584 -0.4686 0.554 ...

``` r
head(pca_FT_NMP17_scores)
```

      Specimen    Sediment Location    Use        PC1         PC2        PC3
    1   Scra11     Quincay     loc1   used -3.7016556 -1.64049159 -1.3081859
    2   Scra11     Quincay     loc2   used -3.2375559  4.02584485 -0.1744678
    3   Scra11     Quincay     loc3   used -4.0896542 -5.00229280  4.1352554
    4   Scra11     Quincay     loc4 unused  3.6696420  1.81370039  1.7825208
    5   Scra12 Coarse sand     loc1   used -1.7929242 -1.69195870  0.2986382
    6   Scra12 Coarse sand     loc2   used  0.4031036  0.09010813  0.5272444
              PC4
    1  2.17021415
    2 -2.01301223
    3  0.05840911
    4 -0.46858267
    5  0.55395403
    6  0.42200060

## Calculate variability of PC scores per specimen and sediment, using only used surfaces

``` r
# keep only used surfaces
pca_FT_NMP17_scores_used <- filter(pca_FT_NMP17_scores, Use == "used")
                          
                          # SD for each PC per specimen and sediment
pca_FT_NMP17_scores_sd <- summaryBy(. ~ Specimen + Sediment, data = pca_FT_NMP17_scores_used, 
                                    FUN = sd) %>% 
  
                          # mean of both SDs per specimen and sediment
                          mutate(mean_sd = rowMeans(.[c("PC1.sd", "PC2.sd", "PC3.sd", "PC4.sd")]))
```

## Combine data

``` r
                # Merge together
pca_mov_data <- merge(pca_FT_NMP17_scores_sd, mov, by = c("Specimen", "Sediment")) %>% 
  
                # Pivot to longer format for facetting
                pivot_longer(c(Average_movement_CONV, Total_abs_movement), 
                             names_to = "Parameter", values_to = "Value")

# Convert to factor for facet labels
pca_mov_data$Parameter <- factor(pca_mov_data$Parameter, 
                                 levels = c("Average_movement_CONV", "Total_abs_movement"), 
                                 labels = c("Average movement [cm]", "Total absolute movement [cm]"))

# Check output
str(pca_mov_data)
```

    tibble [20 × 23] (S3: tbl_df/tbl/data.frame)
     $ Specimen                    : chr [1:20] "Scra11" "Scra11" "Scra12" "Scra12" ...
     $ Sediment                    : chr [1:20] "Quincay" "Quincay" "Coarse sand" "Coarse sand" ...
     $ PC1.sd                      : num [1:20] 0.427 0.427 1.682 1.682 2.302 ...
     $ PC2.sd                      : num [1:20] 4.563 4.563 1.055 1.055 0.642 ...
     $ PC3.sd                      : num [1:20] 2.872 2.872 0.211 0.211 1.554 ...
     $ PC4.sd                      : num [1:20] 2.092 2.092 1.606 1.606 0.347 ...
     $ mean_sd                     : Named num [1:20] 2.49 2.49 1.14 1.14 1.21 ...
      ..- attr(*, "names")= chr [1:20] "1" "1" "2" "2" ...
     $ Freeze.thaw_cycles          : int [1:20] 600 600 330 330 276 276 600 600 330 330 ...
     $ Original_depth_bottom_point1: int [1:20] 5 5 5 5 5 5 5 5 5 5 ...
     $ Original_depth_bottom_point2: int [1:20] 5 5 5 5 5 5 5 5 5 5 ...
     $ Original_depth_top_point1   : num [1:20] 8 8 8 8 9 9 8 8 7 7 ...
     $ Original_depth_top_point2   : num [1:20] 8 8 8 8 9 9 8 8 7 7 ...
     $ Final_depth_top_point1      : num [1:20] 6 6 7.8 7.8 10.5 10.5 9.3 9.3 6 6 ...
     $ Final_depth_top_point2      : num [1:20] 8 8 9.8 9.8 11.5 11.5 11.5 11.5 5.6 5.6 ...
     $ Movement_point1             : num [1:20] -2 -2 -0.2 -0.2 1.5 1.5 1.3 1.3 -1 -1 ...
     $ Movement_point2             : num [1:20] 0 0 1.8 1.8 2.5 2.5 3.5 3.5 -1.4 -1.4 ...
     $ Abs_movement_point1         : num [1:20] 2 2 0.2 0.2 1.5 1.5 1.3 1.3 1 1 ...
     $ Abs_movement_point2         : num [1:20] 0 0 1.8 1.8 2.5 2.5 3.5 3.5 1.4 1.4 ...
     $ Average_movement            : num [1:20] -1 -1 0.8 0.8 2 2 2.4 2.4 -1.2 -1.2 ...
     $ Lithic_rotation             : chr [1:20] "no" "no" "partial" "partial" ...
     $ Lithic_tilting              : chr [1:20] "partial" "partial" "partial" "partial" ...
     $ Parameter                   : Factor w/ 2 levels "Average movement [cm]",..: 1 2 1 2 1 2 1 2 1 2 ...
     $ Value                       : num [1:20] 1 2 -0.8 2 -2 4 -2.4 4.8 1.2 2.4 ...

``` r
head(pca_mov_data)
```

    # A tibble: 6 × 23
      Specimen Sediment    PC1.sd PC2.sd PC3.sd PC4.sd mean_sd Freeze.thaw_cycles
      <chr>    <chr>        <dbl>  <dbl>  <dbl>  <dbl>   <dbl>              <int>
    1 Scra11   Quincay      0.427  4.56   2.87   2.09     2.49                600
    2 Scra11   Quincay      0.427  4.56   2.87   2.09     2.49                600
    3 Scra12   Coarse sand  1.68   1.06   0.211  1.61     1.14                330
    4 Scra12   Coarse sand  1.68   1.06   0.211  1.61     1.14                330
    5 Scra14   Clay         2.30   0.642  1.55   0.347    1.21                276
    6 Scra14   Clay         2.30   0.642  1.55   0.347    1.21                276
    # ℹ 15 more variables: Original_depth_bottom_point1 <int>,
    #   Original_depth_bottom_point2 <int>, Original_depth_top_point1 <dbl>,
    #   Original_depth_top_point2 <dbl>, Final_depth_top_point1 <dbl>,
    #   Final_depth_top_point2 <dbl>, Movement_point1 <dbl>, Movement_point2 <dbl>,
    #   Abs_movement_point1 <dbl>, Abs_movement_point2 <dbl>,
    #   Average_movement <dbl>, Lithic_rotation <chr>, Lithic_tilting <chr>,
    #   Parameter <fct>, Value <dbl>

## Combined plots

``` r
           # Define data and aes for points
pca_mov <- ggplot(data = pca_mov_data, aes(x = Value, y = mean_sd, color = Sediment)) +
  
           # Facetting
           facet_wrap(~ Parameter, scales = "free_x") +
  
           # Add points
           geom_point(size = 2) +
  
           # Add 360° curved arrows for complete rotation
                            # Subset dataset
           geom_arrow_curve(data = pca_mov_data[pca_mov_data$Lithic_rotation == "complete", ],
                            
                            # New aes for beginning and end of arrows
                            aes(x = Value + 0.4, xend = Value + 0.2, 
                                y = mean_sd - 0.1, yend = mean_sd - 0.1), 
                            
                            # Other settings
                            curvature = 4, length_head = 3, linewidth = 0.5, show.legend = FALSE) +
  
           # Add 180° curved arrows for partial rotation
           geom_arrow_curve(data = pca_mov_data[pca_mov_data$Lithic_rotation == "partial", ],
                            aes(x = Value + 0.05, xend = Value + 0.05, 
                                y = mean_sd - 0.1, yend = mean_sd + 0.1), 
                            curvature = 1, length_head = 3, linewidth = 0.5, show.legend = FALSE) +
  
           # Add vertical straight arrows for vertical tilting
           geom_arrow_curve(data = pca_mov_data[pca_mov_data$Lithic_tilting == "vertical", ],
                            aes(x = Value - 0.15, xend = Value - 0.15, 
                                y = mean_sd - 0.1, yend = mean_sd + 0.1), 
                            curvature = 0, length_head = 3, linewidth = 0.5, show.legend = FALSE) +
  
           # Add oblique straight arrows for partial tilting
           geom_arrow_curve(data = pca_mov_data[pca_mov_data$Lithic_tilting == "partial", ],
                            aes(x = Value - 0.05, xend = Value - 0.25, 
                                y = mean_sd - 0.1, yend = mean_sd + 0.1), 
                            curvature = 0, length_head = 3, linewidth = 0.5, show.legend = FALSE) +
  
           # Other settings (see above)
           theme_bw() +
           theme(legend.position = "bottom", legend.box = "vertical") +
           scale_color_brewer(palette = 'Set2') +
  
           # Adjust X and Y labels and add caption
           labs(x = NULL, y = "Mean(sd(PC1)...sd(PC4))", 
                caption = "Curved arrows = lithic rotation, straight arrows = lithic tilting")

# Print plot
print(pca_mov)
```

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-31-1.png)<!-- -->

### Save to PDF

``` r
ggsave(filename = "FT_PCA-mov-plot_NMP17.pdf", path = dir_plots, plot = pca_mov, 
       width = 190, units = "mm")
```

------------------------------------------------------------------------

# Line plots for STA with height maps with \<10% NMP

For comments on the code, see section [Line plots for height maps with
\<17% NMP](#line-plots-for-height-maps-with-17-nmp).

``` r
p_line_NMP10 <- vector(mode = "list", length = nrow(table_units))
names(p_line_NMP10) <- table_units$Param_unit
design_patch <- c(area(1, 1, 2, 3), area(3, 1, 3, 1), area(3, 2, 3, 3))

for (i in names(p_line_NMP10)) {
  p_indiv <- ggplot(data = FT_NMP10_units, aes(x = Cycles, y = .data[[i]], color = Sediment, shape = Use)) + 
             geom_point(size = 3) + 
             facet_wrap(~ Sediment) +
             geom_line(linewidth = 0.5, aes(group = SpecLoc), show.legend = FALSE) +
             theme_classic() +
             scale_color_brewer(palette = 'Set2') +
             labs(title = "Individual data points") +
             theme(legend.direction = "horizontal", legend.title.position = "top")
  p_mean <- ggplot(data = FT_NMP10_mean, aes(x = Cycles, y = .data[[i]], color = Sediment, shape = Use)) + 
            geom_point(size = 3) + 
            geom_line(linewidth = 0.5, aes(group = SpecUse), show.legend = FALSE) +
            theme_classic() +
            scale_color_brewer(palette = 'Set2') +
            labs(title = "Mean per sample+ cycle") +
            theme(legend.direction = "horizontal", legend.title.position = "top")
  p_line_NMP10[[i]] <- p_indiv / p_mean + guide_area() + 
                       plot_layout(guides = 'collect', design = design_patch)
}

print(p_line_NMP10)
```

    $`Sq [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-1.png)<!-- -->


    $`Ssk [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-2.png)<!-- -->


    $`Sku [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-3.png)<!-- -->


    $`Sp [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-4.png)<!-- -->


    $`Sv [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-5.png)<!-- -->


    $`Sz [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-6.png)<!-- -->


    $`Sa [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-7.png)<!-- -->


    $`Smr [%]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-8.png)<!-- -->


    $`Smc [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-9.png)<!-- -->


    $`Sxp [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-10.png)<!-- -->


    $`Sal [µm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-11.png)<!-- -->


    $`Str [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-12.png)<!-- -->


    $`Std [°]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-13.png)<!-- -->


    $`Ssw [µm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-14.png)<!-- -->


    $`Sdq [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-15.png)<!-- -->


    $`Sdr [%]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-16.png)<!-- -->


    $`Vm [µm³/µm²]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-17.png)<!-- -->


    $`Vv [µm³/µm²]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-18.png)<!-- -->


    $`Vmp [µm³/µm²]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-19.png)<!-- -->


    $`Vmc [µm³/µm²]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-20.png)<!-- -->


    $`Vvc [µm³/µm²]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-21.png)<!-- -->


    $`Vvv [µm³/µm²]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-22.png)<!-- -->


    $`First.direction [°]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-23.png)<!-- -->


    $`Second.direction [°]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-24.png)<!-- -->


    $`Third.direction [°]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-25.png)<!-- -->


    $`Texture.isotropy [%]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-26.png)<!-- -->


    $`Maximum.depth.of.furrows [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-27.png)<!-- -->


    $`Mean.depth.of.furrows [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-28.png)<!-- -->


    $`Mean.density.of.furrows [cm/cm2]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-29.png)<!-- -->


    $`epLsar [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-30.png)<!-- -->


    $`NewEplsar [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-31.png)<!-- -->


    $`Asfc [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-32.png)<!-- -->


    $`Smfc [µm²]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-33.png)<!-- -->


    $`HAsfc9 [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-33-34.png)<!-- -->

``` r
ggsave(filename = "FT_STA-plots_NMP10.pdf", path = dir_plots, plot = p_line_NMP10, 
       width = 190, height = 200, units = "mm")
```

------------------------------------------------------------------------

# sessionInfo()

``` r
sessionInfo()
```

    R version 4.5.2 (2025-10-31 ucrt)
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
     [1] lubridate_1.9.4    forcats_1.0.1      stringr_1.6.0      dplyr_1.1.4       
     [5] purrr_1.2.1        readr_2.1.6        tidyr_1.3.2        tibble_3.3.1      
     [9] tidyverse_2.0.0    rmarkdown_2.30     RColorBrewer_1.1-3 R.utils_2.13.0    
    [13] R.oo_1.27.1        R.methodsS3_1.8.2  patchwork_1.3.2    knitr_1.51        
    [17] grateful_0.3.0     ggarrow_0.1.1      factoextra_1.0.7   ggplot2_4.0.1     
    [21] doBy_4.7.1        

    loaded via a namespace (and not attached):
     [1] tidyselect_1.2.1     timeDate_4052.112    farver_2.1.2        
     [4] S7_0.2.1             fastmap_1.2.0        digest_0.6.39       
     [7] timechange_0.4.0     lifecycle_1.0.5      Deriv_4.2.0         
    [10] magrittr_2.0.4       compiler_4.5.2       rlang_1.1.7         
    [13] sass_0.4.10          tools_4.5.2          utf8_1.2.6          
    [16] yaml_2.3.12          ggsignif_0.6.4       labeling_0.4.3      
    [19] curl_7.0.0           TTR_0.24.4           abind_1.4-8         
    [22] withr_3.0.2          polyclip_1.10-7      nnet_7.3-20         
    [25] grid_4.5.2           ggpubr_0.6.2         xts_0.14.1          
    [28] colorspace_2.1-2     scales_1.4.0         MASS_7.3-65         
    [31] cli_3.6.5            ragg_1.5.0           generics_0.1.4      
    [34] otel_0.2.0           rstudioapi_0.18.0    modelr_0.1.11       
    [37] tzdb_0.5.0           cachem_1.1.0         forecast_9.0.0      
    [40] parallel_4.5.2       urca_1.3-4           vctrs_0.7.1         
    [43] boot_1.3-32          Matrix_1.7-4         carData_3.0-6       
    [46] jsonlite_2.0.0       car_3.1-3            hms_1.1.4           
    [49] tseries_0.10-59      rstatix_0.7.3        ggrepel_0.9.6       
    [52] Formula_1.2-5        systemfonts_1.3.1    jquerylib_0.1.4     
    [55] quantmod_0.4.28      glue_1.8.0           cowplot_1.2.0       
    [58] stringi_1.8.7        gtable_0.3.6         quadprog_1.5-8      
    [61] lmtest_0.9-40        pillar_1.11.1        htmltools_0.5.9     
    [64] R6_2.6.1             microbenchmark_1.5.0 textshaping_1.0.4   
    [67] rprojroot_2.1.1      evaluate_1.0.5       lattice_0.22-7      
    [70] backports_1.5.0      broom_1.0.12         fracdiff_1.5-3      
    [73] bslib_0.10.0         Rcpp_1.1.1           nlme_3.1-168        
    [76] xfun_0.56            zoo_1.8-15           pkgconfig_2.0.3     

------------------------------------------------------------------------

# Cite R packages used

| Package | Version | Citation |
|:---|:---|:---|
| base | 4.5.2 | R Core Team (2025) |
| doBy | 4.7.1 | Halekoh and Højsgaard (2025) |
| factoextra | 1.0.7 | Kassambara and Mundt (2020) |
| ggarrow | 0.1.1 | van den Brand (2025) |
| grateful | 0.3.0 | Rodriguez-Sanchez and Jackson (2025) |
| knitr | 1.51 | Xie (2014); Xie (2015); Xie (2025) |
| patchwork | 1.3.2 | Pedersen (2025) |
| R.methodsS3 | 1.8.2 | Bengtsson (2003a) |
| R.oo | 1.27.1 | Bengtsson (2003b) |
| R.utils | 2.13.0 | Bengtsson (2025) |
| RColorBrewer | 1.1.3 | Neuwirth (2022) |
| rmarkdown | 2.30 | Xie, Allaire, and Grolemund (2018); Xie, Dervieux, and Riederer (2020); Allaire et al. (2025) |
| tidyverse | 2.0.0 | Wickham et al. (2019) |
| RStudio | 2026.1.0.392 | Posit team (2026) |

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

<div id="ref-factoextra" class="csl-entry">

Kassambara, Alboukadel, and Fabian Mundt. 2020.
*<span class="nocase">factoextra</span>: Extract and Visualize the
Results of Multivariate Data Analyses*.
<https://doi.org/10.32614/CRAN.package.factoextra>.

</div>

<div id="ref-RColorBrewer" class="csl-entry">

Neuwirth, Erich. 2022. *RColorBrewer: ColorBrewer Palettes*.
<https://doi.org/10.32614/CRAN.package.RColorBrewer>.

</div>

<div id="ref-patchwork" class="csl-entry">

Pedersen, Thomas Lin. 2025. *<span class="nocase">patchwork</span>: The
Composer of Plots*. <https://doi.org/10.32614/CRAN.package.patchwork>.

</div>

<div id="ref-rstudio" class="csl-entry">

Posit team. 2026. *RStudio: Integrated Development Environment for r*.
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

<div id="ref-ggarrow" class="csl-entry">

van den Brand, Teun. 2025. *<span class="nocase">ggarrow</span>: Arrows
for “<span class="nocase">ggplot2</span>”*.
<https://doi.org/10.32614/CRAN.package.ggarrow>.

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
