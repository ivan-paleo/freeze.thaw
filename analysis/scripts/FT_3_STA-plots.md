Plots for the the Freeze-thaw project
================
Ivan Calandra
2026-01-29 17:01:46 CET

- [Goal of the script](#goal-of-the-script)
- [Load packages](#load-packages)
- [Read in data](#read-in-data)
- [Format data](#format-data)
  - [Exclude NMP, and combine Specimen and Location in one
    column](#exclude-nmp-and-combine-specimen-and-location-in-one-column)
  - [Create new datasets based on
    NMP_cat](#create-new-datasets-based-on-nmp_cat)
  - [Calculate the mean per specimen per
    cycle](#calculate-the-mean-per-specimen-per-cycle)
  - [Add units to headers for
    plotting](#add-units-to-headers-for-plotting)
- [Line plots for height maps with \<17%
  NMP](#line-plots-for-height-maps-with-17-nmp)
  - [Create list to receive the
    plots](#create-list-to-receive-the-plots)
  - [Plots](#plots)
  - [Save plots](#save-plots)
- [PCA for height maps with \<17% NMP](#pca-for-height-maps-with-17-nmp)
  - [Format data](#format-data-1)
    - [Define function to calculate
      difference](#define-function-to-calculate-difference)
    - [Calculate the difference between after and
      before](#calculate-the-difference-between-after-and-before)
  - [Custom plotting function](#custom-plotting-function)
  - [Run PCA with all parameters](#run-pca-with-all-parameters)
  - [PCA on selected parameters](#pca-on-selected-parameters)
    - [Select surface texture
      parameters](#select-surface-texture-parameters)
    - [PCA](#pca)
    - [Plots](#plots-1)
      - [Screeplot](#screeplot)
      - [Biplots](#biplots)
      - [Combine plots to save them into 1
        file](#combine-plots-to-save-them-into-1-file)
      - [Save plots](#save-plots-1)
- [Line plots for height maps with \<10%
  NMP](#line-plots-for-height-maps-with-10-nmp)
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

# Format data

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

# Line plots for height maps with \<17% NMP

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

# PCA for height maps with \<17% NMP

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
                              pt.size = 3, pt.shape = 19, pt.fill = "white",
                              elli = TRUE, elli.type = "convex", repel.lab = TRUE, 
                              col.variable = "black", main.title){
  
  # Define plotting
  p_out <- fviz_pca_biplot(dat, axes = pc, 
                           geom.ind = geom.pt, col.ind = datpca[[col.pt]], mean.point = mean.pt,
                           palette = col.pal, pointsize = pt.size, pointshape = pt.shape, fill.ind = pt.fill,
                           addEllipses = elli, ellipse.type = elli.type,  
                           repel = repel.lab, col.var = col.variable, title = main.title, legend.title = "")
  
  p_out <- p_out + theme(legend.position = "bottom")

  # Return plotting object
  return(p_out)
}
```

## Run PCA with all parameters

All STA parameters are used here, except Ssw (column 18), which is
almost always 0.  
This PCA is only meant to select the most informative parameters.  
For comments on the code, see section [PCA on selected
parameters](#pca-on-selected-parameters).

``` r
pca_FT_NMP17_all <- prcomp(FT_NMP17_pca_data[c(5:17, 19:38)], scale. = TRUE, center = TRUE)
pca_FT_NMP17_all_scree <- fviz_screeplot(pca_FT_NMP17_all, addlabels = TRUE, ggtheme = theme_classic())
print(pca_FT_NMP17_all_scree)
```

    Warning in geom_bar(stat = "identity", fill = barfill, color = barcolor, :
    Ignoring empty aesthetic: `width`.

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

``` r
grp_PCA <- "Sediment"
pca_FT_NMP17_all_12 <- custom_pca_biplot(pca_FT_NMP17_all, datpca = FT_NMP17_pca_data, pc = c(1, 2), 
                                         col.pt = grp_PCA, main.title = "PC 1&2 - all parameters")
print(pca_FT_NMP17_all_12)
```

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-16-2.png)<!-- -->

``` r
pca_FT_NMP17_all_34 <- custom_pca_biplot(pca_FT_NMP17_all, datpca = FT_NMP17_pca_data, pc = c(3, 4), 
                                         col.pt = grp_PCA, main.title = "PC 3&4 - all parameters")
print(pca_FT_NMP17_all_34)
```

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-16-3.png)<!-- -->

``` r
all_plots_17_all <- list(pca_FT_NMP17_all_scree, pca_FT_NMP17_all_12, pca_FT_NMP17_all_34)  
ggsave(filename = "FT_PCA-plots_NMP17_all-params.pdf", path = dir_plots, plot = all_plots_17_all, 
       width = 190, height = 125, units = "mm")
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
pca_FT_NMP17 <- prcomp(FT_NMP17_pca_data[ , pca_params], scale. = TRUE)
```

### Plots

#### Screeplot

``` r
pca_FT_NMP17_scree <- fviz_screeplot(pca_FT_NMP17, addlabels = TRUE, ggtheme = theme_classic())
print(pca_FT_NMP17_scree)
```

    Warning in geom_bar(stat = "identity", fill = barfill, color = barcolor, :
    Ignoring empty aesthetic: `width`.

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-19-1.png)<!-- -->

#### Biplots

``` r
# Define grouping variable and titles
grp_PCA <- "Sediment"

# Biplot of PC1&2
pca_FT_NMP17_12 <- custom_pca_biplot(pca_FT_NMP17, datpca = FT_NMP17_pca_data, pc = c(1, 2), 
                                     col.pt = grp_PCA, main.title = "PC 1&2 - selected parameters")
print(pca_FT_NMP17_12)
```

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-20-1.png)<!-- -->

``` r
# Biplot of PC3&4
pca_FT_NMP17_34 <- custom_pca_biplot(pca_FT_NMP17, datpca = FT_NMP17_pca_data, pc = c(3, 4), 
                                     col.pt = grp_PCA, main.title = "PC 3&4 - selected parameters")
print(pca_FT_NMP17_34)
```

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-20-2.png)<!-- -->

#### Combine plots to save them into 1 file

``` r
all_plots_17 <- list(pca_FT_NMP17_scree, pca_FT_NMP17_12, pca_FT_NMP17_34)  
```

#### Save plots

``` r
ggsave(filename = "FT_PCA-plots_NMP17.pdf", path = dir_plots, plot = all_plots_17, 
       width = 190, height = 125, units = "mm")
```

    Warning in geom_bar(stat = "identity", fill = barfill, color = barcolor, :
    Ignoring empty aesthetic: `width`.

------------------------------------------------------------------------

# Line plots for height maps with \<10% NMP

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

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-1.png)<!-- -->


    $`Ssk [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-2.png)<!-- -->


    $`Sku [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-3.png)<!-- -->


    $`Sp [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-4.png)<!-- -->


    $`Sv [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-5.png)<!-- -->


    $`Sz [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-6.png)<!-- -->


    $`Sa [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-7.png)<!-- -->


    $`Smr [%]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-8.png)<!-- -->


    $`Smc [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-9.png)<!-- -->


    $`Sxp [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-10.png)<!-- -->


    $`Sal [µm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-11.png)<!-- -->


    $`Str [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-12.png)<!-- -->


    $`Std [°]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-13.png)<!-- -->


    $`Ssw [µm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-14.png)<!-- -->


    $`Sdq [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-15.png)<!-- -->


    $`Sdr [%]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-16.png)<!-- -->


    $`Vm [µm³/µm²]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-17.png)<!-- -->


    $`Vv [µm³/µm²]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-18.png)<!-- -->


    $`Vmp [µm³/µm²]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-19.png)<!-- -->


    $`Vmc [µm³/µm²]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-20.png)<!-- -->


    $`Vvc [µm³/µm²]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-21.png)<!-- -->


    $`Vvv [µm³/µm²]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-22.png)<!-- -->


    $`First.direction [°]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-23.png)<!-- -->


    $`Second.direction [°]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-24.png)<!-- -->


    $`Third.direction [°]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-25.png)<!-- -->


    $`Texture.isotropy [%]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-26.png)<!-- -->


    $`Maximum.depth.of.furrows [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-27.png)<!-- -->


    $`Mean.depth.of.furrows [nm]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-28.png)<!-- -->


    $`Mean.density.of.furrows [cm/cm2]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-29.png)<!-- -->


    $`epLsar [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-30.png)<!-- -->


    $`NewEplsar [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-31.png)<!-- -->


    $`Asfc [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-32.png)<!-- -->


    $`Smfc [µm²]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-33.png)<!-- -->


    $`HAsfc9 [no unit]`

![](FT_3_STA-plots_files/figure-gfm/unnamed-chunk-23-34.png)<!-- -->

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
     [1] lubridate_1.9.4    forcats_1.0.1      stringr_1.6.0      dplyr_1.1.4       
     [5] purrr_1.2.1        readr_2.1.6        tidyr_1.3.2        tibble_3.3.1      
     [9] tidyverse_2.0.0    rmarkdown_2.30     RColorBrewer_1.1-3 R.utils_2.13.0    
    [13] R.oo_1.27.1        R.methodsS3_1.8.2  patchwork_1.3.2    knitr_1.51        
    [17] grateful_0.3.0     factoextra_1.0.7   ggplot2_4.0.1      doBy_4.7.1        

    loaded via a namespace (and not attached):
     [1] tidyselect_1.2.1     timeDate_4052.112    farver_2.1.2        
     [4] S7_0.2.1             fastmap_1.2.0        digest_0.6.39       
     [7] timechange_0.3.0     lifecycle_1.0.5      Deriv_4.2.0         
    [10] magrittr_2.0.4       compiler_4.5.2       rlang_1.1.7         
    [13] sass_0.4.10          tools_4.5.2          yaml_2.3.12         
    [16] ggsignif_0.6.4       labeling_0.4.3       curl_7.0.0          
    [19] TTR_0.24.4           abind_1.4-8          withr_3.0.2         
    [22] nnet_7.3-20          grid_4.5.2           ggpubr_0.6.2        
    [25] xts_0.14.1           colorspace_2.1-2     scales_1.4.0        
    [28] MASS_7.3-65          cli_3.6.5            ragg_1.5.0          
    [31] generics_0.1.4       otel_0.2.0           rstudioapi_0.18.0   
    [34] modelr_0.1.11        tzdb_0.5.0           cachem_1.1.0        
    [37] forecast_9.0.0       parallel_4.5.2       urca_1.3-4          
    [40] vctrs_0.7.1          boot_1.3-32          Matrix_1.7-4        
    [43] carData_3.0-5        jsonlite_2.0.0       car_3.1-3           
    [46] hms_1.1.4            tseries_0.10-59      rstatix_0.7.3       
    [49] ggrepel_0.9.6        Formula_1.2-5        systemfonts_1.3.1   
    [52] jquerylib_0.1.4      quantmod_0.4.28      glue_1.8.0          
    [55] cowplot_1.2.0        stringi_1.8.7        gtable_0.3.6        
    [58] quadprog_1.5-8       lmtest_0.9-40        pillar_1.11.1       
    [61] htmltools_0.5.9      R6_2.6.1             microbenchmark_1.5.0
    [64] textshaping_1.0.4    rprojroot_2.1.1      evaluate_1.0.5      
    [67] lattice_0.22-7       backports_1.5.0      broom_1.0.12        
    [70] fracdiff_1.5-3       bslib_0.10.0         Rcpp_1.1.1          
    [73] nlme_3.1-168         xfun_0.56            zoo_1.8-15          
    [76] pkgconfig_2.0.3     

------------------------------------------------------------------------

# Cite R packages used

| Package | Version | Citation |
|:---|:---|:---|
| base | 4.5.2 | R Core Team (2025) |
| doBy | 4.7.1 | Halekoh and Højsgaard (2025) |
| factoextra | 1.0.7 | Kassambara and Mundt (2020) |
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
