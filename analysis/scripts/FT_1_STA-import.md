Import dataset from the surface texture analysis for the Freeze-thaw
project
================
Ivan Calandra
2025-11-24 15:38:37 CET

- [Goal of the script](#goal-of-the-script)
- [Load packages](#load-packages)
- [Read and format data](#read-and-format-data)
  - [Read in CSV file](#read-in-csv-file)
  - [Select relevant columns and
    rows](#select-relevant-columns-and-rows)
  - [Identify results using frame
    numbers](#identify-results-using-frame-numbers)
  - [Add headers](#add-headers)
  - [Extract units](#extract-units)
  - [Split column ‘Name’](#split-column-name)
  - [Add columns for sediment type and freeze-thaw
    cycles](#add-columns-for-sediment-type-and-freeze-thaw-cycles)
  - [Convert variables](#convert-variables)
  - [Set Cycles to 0 for state
    before](#set-cycles-to-0-for-state-before)
  - [Add column for NMP categories](#add-column-for-nmp-categories)
  - [Re-order columns and add units as
    comment](#re-order-columns-and-add-units-as-comment)
  - [Check the result](#check-the-result)
- [Save data](#save-data)
  - [As XLSX](#as-xlsx)
  - [As Rbin](#as-rbin)
- [sessionInfo()](#sessioninfo)
- [Cite R packages used](#cite-r-packages-used)
  - [References](#references)

------------------------------------------------------------------------

# Goal of the script

This script formats the output of the resulting files from applying
surface texture analysis to a sample of experimental lithics. The script
will:

1.  Read in the original files  
2.  Format the data  
3.  Write XLSX file and save R objects ready for further analysis in R

``` r
dir_in  <- "analysis/raw_data"
dir_out <- "analysis/derived_data"
```

Raw data must be located in “./analysis/raw_data”.  
Formatted data will be saved in “./analysis/derived_data”.

The knit directory for this script is the project directory.

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
FT_file <- list.files(dir_in, pattern = ".*STA.*\\.csv$", full.names = TRUE) 
FT <- read.csv(FT_file, header = FALSE, na.strings = "*****", fileEncoding = 'WINDOWS-1252')
```

The following raw data file has been read:
“analysis/raw_data/FT_STA_raw-data_2025-11-24.csv”

## Select relevant columns and rows

``` r
FT_keep_col  <- c(4, 19:53)                    # Define columns to keep
FT_keep_rows <- which(FT[[1]] != "#")          # Define rows to keep
FT_keep      <- FT[FT_keep_rows, FT_keep_col]  # Subset rows and columns
```

## Identify results using frame numbers

``` r
frames <- as.numeric(unlist(FT[1, FT_keep_col]))
ID <- which(frames %in% c(2, 8))
ISO <- which(frames == 10)
diriso <- which(frames %in% 11:12)
SSFA <- which(frames %in% 14:15)
furrow <- which(frames == 13)
```

## Add headers

``` r
# Get headers from 2nd row
colnames(FT_keep) <- FT[2, FT_keep_col] %>% 
  
                     # Convert to valid names
                     make.names() %>% 
  
                     # Delete repeated periods
                     gsub("\\.+", "\\.", x = .) %>% 
  
                     # Delete periods at the end of the names
                     gsub("\\.$", "", x = .)
  
# Keep parameter name before the first period for ISO
colnames(FT_keep)[ISO] <- strsplit(names(FT_keep)[ISO], ".", fixed = TRUE) %>% 
                          sapply(`[[`, 1)

# Keep parameter name after the last period for SSFA
colnames(FT_keep)[SSFA] <- gsub("^([A-Za-z0-9]+\\.)+", "", colnames(FT_keep)[SSFA])

# Edit header non-measured point ratios
colnames(FT_keep)[ID][2] <- "NMP"

# Check results
str(FT_keep)
```

    'data.frame':   80 obs. of  36 variables:
     $ Name                    : chr  "Scra11_LSM_20x-0.7_LSM_after_mold_ventral_loc1_mean_extracted" "Scra11_LSM_20x-0.7_LSM_after_mold_ventral_loc2_mean_extracted" "Scra11_LSM_20x-0.7_LSM_after_mold_ventral_loc3_mean_extracted" "Scra11_LSM_20x-0.7_LSM_after_mold_ventral_loc4_mean_extracted" ...
     $ NMP                     : chr  "6.210140306" "8.988605008" "9.360194971" "7.087053571" ...
     $ Sq                      : chr  "510.3146761" "599.4221317" "626.8702986" "535.8292596" ...
     $ Ssk                     : chr  "0.2082995257" "0.103857641" "-1.349127918" "0.3066294796" ...
     $ Sku                     : chr  "3.611167169" "4.277262096" "16.21507197" "3.504881576" ...
     $ Sp                      : chr  "2002.268525" "2280.247346" "4021.251284" "2236.933193" ...
     $ Sv                      : chr  "1895.23744" "2129.979177" "4888.391305" "1622.238282" ...
     $ Sz                      : chr  "3897.505965" "4410.226522" "8909.642589" "3859.171475" ...
     $ Sa                      : chr  "395.2242358" "444.2419269" "399.6521136" "420.7068176" ...
     $ Smr                     : chr  "3.358513583" "2.64257917" "0.1384576616" "1.932655279" ...
     $ Smc                     : chr  "630.9983711" "705.02651" "594.0356977" "670.0448426" ...
     $ Sxp                     : chr  "959.4634056" "1299.105579" "1118.639648" "961.0071208" ...
     $ Sal                     : chr  "5.535717812" "6.570978128" "5.662374686" "5.69363755" ...
     $ Str                     : chr  "0.7571264937" "0.6973377801" "0.6112437598" "0.6484317613" ...
     $ Std                     : chr  "54.74853125" "74.74840725" "85.50156405" "57.75130496" ...
     $ Ssw                     : chr  "0.4249085041" "0.4249085041" "0.4249085041" "0.4249085041" ...
     $ Sdq                     : chr  "0.59373265" "0.3186908037" "0.372205708" "0.324198925" ...
     $ Sdr                     : chr  "14.10821254" "4.665524789" "5.960471374" "4.854969956" ...
     $ Vm                      : chr  "0.03220712242" "0.04181877389" "0.04105984277" "0.03305849431" ...
     $ Vv                      : chr  "0.6632382845" "0.7468693034" "0.6350725639" "0.7030889261" ...
     $ Vmp                     : chr  "0.03220712242" "0.04181877389" "0.04105984277" "0.03305849431" ...
     $ Vmc                     : chr  "0.4298770045" "0.4518376615" "0.3806122545" "0.4670876133" ...
     $ Vvc                     : chr  "0.6058455505" "0.6665194179" "0.5465986711" "0.6490425864" ...
     $ Vvv                     : chr  "0.05739273397" "0.08034988548" "0.08847389275" "0.05404633971" ...
     $ First.direction         : chr  "44.99066565" "89.99558778" "90.00049955" "44.98490159" ...
     $ Second.direction        : chr  "0.01081937014" "179.9952386" "44.99608453" "90.01423227" ...
     $ Third.direction         : chr  "90.00509231" "134.9887841" "116.4394462" "135.0068606" ...
     $ Texture.isotropy        : chr  "82.36371353" "68.76672171" "58.82371774" "71.81573825" ...
     $ Maximum.depth.of.furrows: chr  "3418.37855" "4350.215412" "9461.823474" "3943.281765" ...
     $ Mean.depth.of.furrows   : chr  "1714.283928" "1899.376555" "3459.688943" "1857.354041" ...
     $ Mean.density.of.furrows : chr  "6492.158778" "6945.496465" "7121.220991" "6632.411999" ...
     $ epLsar                  : chr  "0.0005855797274" "0.002807360738" "0.0017361474" "0.00238340124" ...
     $ NewEplsar               : chr  "0.01748434864" "0.01681894117" "0.01787319695" "0.01666934487" ...
     $ Asfc                    : chr  "49.92665862" "13.83171789" "19.82068691" "13.0605013" ...
     $ Smfc                    : chr  "1.080586533" "1.15307539" "1.49503139" "1.15307539" ...
     $ HAsfc9                  : chr  "0.1333228463" "0.4437028233" "0.9494296848" "0.3135671868" ...

``` r
head(FT_keep)
```

                                                                Name         NMP
    4  Scra11_LSM_20x-0.7_LSM_after_mold_ventral_loc1_mean_extracted 6.210140306
    5  Scra11_LSM_20x-0.7_LSM_after_mold_ventral_loc2_mean_extracted 8.988605008
    6  Scra11_LSM_20x-0.7_LSM_after_mold_ventral_loc3_mean_extracted 9.360194971
    7  Scra11_LSM_20x-0.7_LSM_after_mold_ventral_loc4_mean_extracted 7.087053571
    8 Scra11_LSM_20x-0.7_LSM_before_mold_ventral_loc1_mean_extracted 5.585075229
    9 Scra11_LSM_20x-0.7_LSM_before_mold_ventral_loc2_mean_extracted 7.824051177
               Sq           Ssk         Sku          Sp          Sv          Sz
    4 510.3146761  0.2082995257 3.611167169 2002.268525  1895.23744 3897.505965
    5 599.4221317   0.103857641 4.277262096 2280.247346 2129.979177 4410.226522
    6 626.8702986  -1.349127918 16.21507197 4021.251284 4888.391305 8909.642589
    7 535.8292596  0.3066294796 3.504881576 2236.933193 1622.238282 3859.171475
    8 461.3624847  0.3644241958 3.438281754 1940.378093 1193.170473 3133.548566
    9 537.4528483 -0.3339857246 4.060290192 1796.530701 2537.250497 4333.781197
               Sa          Smr         Smc         Sxp         Sal          Str
    4 395.2242358  3.358513583 630.9983711 959.4634056 5.535717812 0.7571264937
    5 444.2419269   2.64257917   705.02651 1299.105579 6.570978128 0.6973377801
    6 399.6521136 0.1384576616 594.0356977 1118.639648 5.662374686 0.6112437598
    7 420.7068176  1.932655279 670.0448426 961.0071208  5.69363755 0.6484317613
    8 362.0557726  3.147260593 568.2291887 834.1125407  6.10753735 0.6810785363
    9 412.1910261    6.2930831 674.2111302 1111.378536 5.961985451 0.6743631927
              Std          Ssw          Sdq         Sdr            Vm           Vv
    4 54.74853125 0.4249085041   0.59373265 14.10821254 0.03220712242 0.6632382845
    5 74.74840725 0.4249085041 0.3186908037 4.665524789 0.04181877389 0.7468693034
    6 85.50156405 0.4249085041  0.372205708 5.960471374 0.04105984277 0.6350725639
    7 57.75130496 0.4249085041  0.324198925 4.854969956 0.03305849431 0.7030889261
    8 86.25057711 0.4249085041 0.3179694623 4.701684148 0.03014862059 0.5983674765
    9 86.99719416 0.4249085041 0.4060170515 7.404031275 0.02403996445 0.6982754828
                Vmp          Vmc          Vvc           Vvv First.direction
    4 0.03220712242 0.4298770045 0.6058455505 0.05739273397     44.99066565
    5 0.04181877389 0.4518376615 0.6665194179 0.08034988548     89.99558778
    6 0.04105984277 0.3806122545 0.5465986711 0.08847389275     90.00049955
    7 0.03305849431 0.4670876133 0.6490425864 0.05404633971     44.98490159
    8 0.03014862059 0.4005538481 0.5514198143 0.04694766218     89.99058392
    9 0.02403996445 0.4565222931 0.6277425315 0.07053295125     45.01407039
      Second.direction Third.direction Texture.isotropy Maximum.depth.of.furrows
    4    0.01081937014     90.00509231      82.36371353               3418.37855
    5      179.9952386     134.9887841      68.76672171              4350.215412
    6      44.99608453     116.4394462      58.82371774              9461.823474
    7      90.01423227     135.0068606      71.81573825              3943.281765
    8      135.0261938     179.9960214      72.18860888                3051.5584
    9      89.99811744     63.56397522      57.93184387              4003.873071
      Mean.depth.of.furrows Mean.density.of.furrows          epLsar     NewEplsar
    4           1714.283928             6492.158778 0.0005855797274 0.01748434864
    5           1899.376555             6945.496465  0.002807360738 0.01681894117
    6           3459.688943             7121.220991    0.0017361474 0.01787319695
    7           1857.354041             6632.411999   0.00238340124 0.01666934487
    8            1527.90806             6748.033798  0.002473628671 0.01686902362
    9           1607.176415             7054.717255  0.001293232514 0.01710848802
             Asfc         Smfc       HAsfc9
    4 49.92665862  1.080586533 0.1333228463
    5 13.83171789   1.15307539 0.4437028233
    6 19.82068691   1.49503139 0.9494296848
    7  13.0605013   1.15307539 0.3135671868
    8 13.10260134 0.9489935195 0.1773680407
    9 26.90326537 0.8893344066 0.2908531468

## Extract units

``` r
# Filter out rows which contains units
n_units <- filter(FT, V4 == "<no unit>") %>% 
          
           # Keep only unique/distinct rows of units
           distinct() %>% 
  
           # Number of unique/distinct rows of units
           nrow()

if (n_units != 1) {
  stop("The different datasets have different units")
} else {
  # Extract unit line from 3rd row for considered columns
  FT_units <- unlist(FT[3, FT_keep_col[-1]])

  # Get names associated to the units
  names(FT_units) <- colnames(FT_keep)[-1]

  # Combine into a data.frame for export
  units_table <- data.frame(variable = names(FT_units), units = FT_units)
  row.names(units_table) <- NULL
}

# Check results
units_table
```

                       variable     units
    1                       NMP         %
    2                        Sq        nm
    3                       Ssk <no unit>
    4                       Sku <no unit>
    5                        Sp        nm
    6                        Sv        nm
    7                        Sz        nm
    8                        Sa        nm
    9                       Smr         %
    10                      Smc        nm
    11                      Sxp        nm
    12                      Sal        µm
    13                      Str <no unit>
    14                      Std         °
    15                      Ssw        µm
    16                      Sdq <no unit>
    17                      Sdr         %
    18                       Vm   µm³/µm²
    19                       Vv   µm³/µm²
    20                      Vmp   µm³/µm²
    21                      Vmc   µm³/µm²
    22                      Vvc   µm³/µm²
    23                      Vvv   µm³/µm²
    24          First.direction         °
    25         Second.direction         °
    26          Third.direction         °
    27         Texture.isotropy         %
    28 Maximum.depth.of.furrows        nm
    29    Mean.depth.of.furrows        nm
    30  Mean.density.of.furrows    cm/cm2
    31                   epLsar <no unit>
    32                NewEplsar <no unit>
    33                     Asfc <no unit>
    34                     Smfc       µm²
    35                   HAsfc9 <no unit>

## Split column ‘Name’

``` r
FT_keep[c("Specimen", "State", "Location")] <- FT_keep$Name %>% 
  
                                               # Split by "_" into a matrix with 10 cols
                                               str_split_fixed("_", n = 10) %>% 
  
                                               # Keep only cols 1, 5 and 8
                                               .[, c(1, 5, 8)]
```

## Add columns for sediment type and freeze-thaw cycles

``` r
# Load CSV file with information on samples
FT_samples_file <- list.files(dir_in, pattern = ".*samples.*\\.csv$", full.names = TRUE)
FT_samples <- read.csv(FT_samples_file)

# Merge the data.frames by chert type and chert tool
FT_keep_sed_cy <- merge(FT_keep, FT_samples, by = "Specimen")
```

The file containing the info on samples is:
“analysis/raw_data/FT_samples.csv”

Scra14 was planned for 600 cycles but the tube opened before the end of
the experiment. Hence, only 560 cycles were conducted for that sample.

## Convert variables

``` r
# Convert parameter variables to numeric
FT_keep_sed_cy <- type_convert(FT_keep_sed_cy)

# Convert state to factor and re-order it (1 = before, 2 = after)
FT_keep_sed_cy$State <- factor(FT_keep_sed_cy$State, levels = c("before", "after"))
```

## Set Cycles to 0 for state before

``` r
# Replace
FT_keep_sed_cy[FT_keep_sed_cy[["State"]] == "before", "Cycles"] <- 0

# Check
FT_keep_sed_cy[c("Specimen", "State", "Cycles")]
```

       Specimen  State Cycles
    1    Scra11  after    600
    2    Scra11  after    600
    3    Scra11  after    600
    4    Scra11  after    600
    5    Scra11 before      0
    6    Scra11 before      0
    7    Scra11 before      0
    8    Scra11 before      0
    9    Scra12  after    330
    10   Scra12  after    330
    11   Scra12  after    330
    12   Scra12  after    330
    13   Scra12 before      0
    14   Scra12 before      0
    15   Scra12 before      0
    16   Scra12 before      0
    17   Scra14  after    560
    18   Scra14  after    560
    19   Scra14  after    560
    20   Scra14  after    560
    21   Scra14 before      0
    22   Scra14 before      0
    23   Scra14 before      0
    24   Scra14 before      0
    25   Scra16  after    600
    26   Scra16  after    600
    27   Scra16  after    600
    28   Scra16  after    600
    29   Scra16 before      0
    30   Scra16 before      0
    31   Scra16 before      0
    32   Scra16 before      0
    33   Scra17  after    330
    34   Scra17  after    330
    35   Scra17  after    330
    36   Scra17  after    330
    37   Scra17 before      0
    38   Scra17 before      0
    39   Scra17 before      0
    40   Scra17 before      0
    41   Scra21  after    330
    42   Scra21  after    330
    43   Scra21  after    330
    44   Scra21  after    330
    45   Scra21 before      0
    46   Scra21 before      0
    47   Scra21 before      0
    48   Scra21 before      0
    49   Scra25  after    330
    50   Scra25  after    330
    51   Scra25  after    330
    52   Scra25  after    330
    53   Scra25 before      0
    54   Scra25 before      0
    55   Scra25 before      0
    56   Scra25 before      0
    57   Scra26  after    600
    58   Scra26  after    600
    59   Scra26  after    600
    60   Scra26  after    600
    61   Scra26 before      0
    62   Scra26 before      0
    63   Scra26 before      0
    64   Scra26 before      0
    65    Scra7  after    600
    66    Scra7  after    600
    67    Scra7  after    600
    68    Scra7  after    600
    69    Scra7 before      0
    70    Scra7 before      0
    71    Scra7 before      0
    72    Scra7 before      0
    73    Scra8  after    330
    74    Scra8  after    330
    75    Scra8  after    330
    76    Scra8  after    330
    77    Scra8 before      0
    78    Scra8 before      0
    79    Scra8 before      0
    80    Scra8 before      0

## Add column for NMP categories

Here we define 3 ranges of non-measured points (NMP):  
- ≤ 10% NMP: “\<10%”  
- \> 10% and ≤ 17% NMP: “10-17%”  
- \> 17% NMP: “\>17%”

``` r
# Create new column and fill it
FT_keep_sed_cy[FT_keep_sed_cy$NMP <= 10                           , "NMP_cat"] <- "<10%"
FT_keep_sed_cy[FT_keep_sed_cy$NMP >  10 & FT_keep_sed_cy$NMP <= 17, "NMP_cat"] <- "10-17%"
FT_keep_sed_cy[FT_keep_sed_cy$NMP >  17                           , "NMP_cat"] <- ">17%"

# Convert to ordered factor
FT_keep_sed_cy[["NMP_cat"]] <- factor(FT_keep_sed_cy[["NMP_cat"]], 
                                      levels = c("<10%", "10-17%", ">17%"), ordered = TRUE)
```

The number of height maps in each NMP category is:

``` r
table(FT_keep_sed_cy["NMP_cat"])
```

    NMP_cat
      <10% 10-17%   >17% 
        35     37      8 

## Re-order columns and add units as comment

``` r
FT_final <- select(FT_keep_sed_cy, Specimen, Sediment, Cycles, State, Location, NMP, NMP_cat, Sq:HAsfc9)
comment(FT_final) <- FT_units
```

Type `comment(FT_final)` to check the units of the parameters.

## Check the result

``` r
str(FT_final)
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
head(FT_final)
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

# Save data

## As XLSX

``` r
write_xlsx(list("data" = FT_final, "units" = units_table), 
           path = paste0(dir_out, "/FT_STA_formatted-data.xlsx"))
```

## As Rbin

``` r
saveObject(FT_final, file = paste0(dir_out, "/FT_STA_formatted-data.Rbin"))
```

Rbin files (e.g. `FT_STA_formatted-data.Rbin`) can be easily read into
an R object (e.g. `rbin_data`) using the following code:

``` r
library(R.utils)
rbin_data <- loadObject("FT_STA_formatted-data.Rbin")
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
    [17] grateful_0.3.0   

    loaded via a namespace (and not attached):
     [1] gtable_0.3.6       jsonlite_2.0.0     crayon_1.5.3       compiler_4.5.1    
     [5] tidyselect_1.2.1   jquerylib_0.1.4    scales_1.4.0       yaml_2.3.10       
     [9] fastmap_1.2.0      R6_2.6.1           generics_0.1.4     rprojroot_2.1.1   
    [13] tzdb_0.5.0         bslib_0.9.0        pillar_1.11.1      RColorBrewer_1.1-3
    [17] rlang_1.1.6        stringi_1.8.7      cachem_1.1.0       xfun_0.54         
    [21] sass_0.4.10        S7_0.2.0           timechange_0.3.0   cli_3.6.5         
    [25] withr_3.0.2        magrittr_2.0.4     digest_0.6.37      grid_4.5.1        
    [29] rstudioapi_0.17.1  hms_1.1.4          lifecycle_1.0.4    vctrs_0.6.5       
    [33] evaluate_1.0.5     glue_1.8.0         farver_2.1.2       tools_4.5.1       
    [37] pkgconfig_2.0.3    htmltools_0.5.8.1 

------------------------------------------------------------------------

# Cite R packages used

| Package | Version | Citation |
|:---|:---|:---|
| base | 4.5.1 | R Core Team (2025) |
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
