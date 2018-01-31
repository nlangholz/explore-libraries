01\_explore-libraries\_jenny.R
================
nlangholz
Wed Jan 31 14:23:40 2018

``` r
## how jenny might do this in a first exploration
## purposely leaving a few things to change later!
```

Which libraries does R search for packages?

``` r
.libPaths()
```

    ## [1] "/Library/Frameworks/R.framework/Versions/3.4/Resources/library"

``` r
## let's confirm the second element is, in fact, the default library
.Library
```

    ## [1] "/Library/Frameworks/R.framework/Resources/library"

``` r
library(fs)
path_real(.Library)
```

    ## /Library/Frameworks/R.framework/Versions/3.4/Resources/library

Installed packages

``` r
library(tidyverse)
```

    ## ── Attaching packages ───────────────────────────────────────────────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 2.2.1     ✔ purrr   0.2.4
    ## ✔ tibble  1.4.1     ✔ dplyr   0.7.4
    ## ✔ tidyr   0.7.2     ✔ stringr 1.2.0
    ## ✔ readr   1.1.1     ✔ forcats 0.2.0

    ## ── Conflicts ──────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
ipt <- installed.packages() %>%
  as_tibble()

## how many packages?
nrow(ipt)
```

    ## [1] 152

Exploring the packages

``` r
## count some things! inspiration
##   * tabulate by LibPath, Priority, or both
ipt %>%
  count(LibPath, Priority)
```

    ## # A tibble: 3 x 3
    ##   LibPath                                                 Priority       n
    ##   <chr>                                                   <chr>      <int>
    ## 1 /Library/Frameworks/R.framework/Versions/3.4/Resources… base          14
    ## 2 /Library/Frameworks/R.framework/Versions/3.4/Resources… recommend…    15
    ## 3 /Library/Frameworks/R.framework/Versions/3.4/Resources… <NA>         123

``` r
##   * what proportion need compilation?
ipt %>%
  count(NeedsCompilation) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 3 x 3
    ##   NeedsCompilation     n   prop
    ##   <chr>            <int>  <dbl>
    ## 1 no                  64 0.421 
    ## 2 yes                 82 0.539 
    ## 3 <NA>                 6 0.0395

``` r
##   * how break down re: version of R they were built on
ipt %>%
  count(Built) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 4 x 3
    ##   Built     n   prop
    ##   <chr> <int>  <dbl>
    ## 1 3.4.0    56 0.368 
    ## 2 3.4.1    15 0.0987
    ## 3 3.4.2    21 0.138 
    ## 4 3.4.3    60 0.395

Reflections

``` r
## reflect on ^^ and make a few notes to yourself; inspiration
##   * does the number of base + recommended packages make sense to you?
##   * how does the result of .libPaths() relate to the result of .Library?
```

Going further

``` r
## if you have time to do more ...

## is every package in .Library either base or recommended?
all_default_pkgs <- list.files(.Library)
all_br_pkgs <- ipt %>%
  filter(Priority %in% c("base", "recommended")) %>%
  pull(Package)
setdiff(all_default_pkgs, all_br_pkgs)
```

    ##   [1] "assertthat"   "backports"    "base64enc"    "BH"          
    ##   [5] "bindr"        "bindrcpp"     "bitops"       "broom"       
    ##   [9] "callr"        "caret"        "caTools"      "cellranger"  
    ##  [13] "cli"          "clipr"        "clisymbols"   "colorspace"  
    ##  [17] "crayon"       "curl"         "CVST"         "data.table"  
    ##  [21] "DBI"          "dbplyr"       "ddalpha"      "DEoptimR"    
    ##  [25] "desc"         "devtools"     "dichromat"    "digest"      
    ##  [29] "dimRed"       "dplyr"        "DRR"          "e1071"       
    ##  [33] "enc"          "evaluate"     "feather"      "forcats"     
    ##  [37] "foreach"      "fs"           "ggplot2"      "gh"          
    ##  [41] "git2r"        "glue"         "gower"        "gtable"      
    ##  [45] "haven"        "highr"        "hms"          "htmltools"   
    ##  [49] "htmlwidgets"  "httr"         "igraph"       "ini"         
    ##  [53] "ipred"        "iterators"    "jsonlite"     "kernlab"     
    ##  [57] "knitr"        "labeling"     "lava"         "lazyeval"    
    ##  [61] "lme4"         "lubridate"    "magrittr"     "markdown"    
    ##  [65] "memoise"      "mime"         "minqa"        "mnormt"      
    ##  [69] "ModelMetrics" "modelr"       "munsell"      "networkD3"   
    ##  [73] "nloptr"       "numDeriv"     "openssl"      "pillar"      
    ##  [77] "pkgconfig"    "plogr"        "plyr"         "prodlim"     
    ##  [81] "psych"        "purrr"        "R6"           "RColorBrewer"
    ##  [85] "Rcpp"         "RcppEigen"    "RcppRoll"     "RCurl"       
    ##  [89] "readr"        "readxl"       "recipes"      "rematch"     
    ##  [93] "rematch2"     "reprex"       "reshape2"     "rlang"       
    ##  [97] "rmarkdown"    "robustbase"   "RPostgreSQL"  "rprojroot"   
    ## [101] "Rspotify"     "rstudioapi"   "rvest"        "scales"      
    ## [105] "selectr"      "sfsmisc"      "SQUAREM"      "stringdist"  
    ## [109] "stringi"      "stringr"      "styler"       "tibble"      
    ## [113] "tidyr"        "tidyselect"   "tidyverse"    "timeDate"    
    ## [117] "translations" "usethis"      "utf8"         "viridisLite" 
    ## [121] "whisker"      "withr"        "xml2"         "yaml"

``` r
## study package naming style (all lower case, contains '.', etc

## use `fields` argument to installed.packages() to get more info and use it!
ipt2 <- installed.packages(fields = "URL") %>%
  as_tibble()
ipt2 %>%
  mutate(github = grepl("github", URL)) %>%
  count(github) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 2 x 3
    ##   github     n  prop
    ##   <lgl>  <int> <dbl>
    ## 1 F         75 0.493
    ## 2 T         77 0.507

``` r
sessionInfo()
```

    ## R version 3.4.3 (2017-11-30)
    ## Platform: x86_64-apple-darwin15.6.0 (64-bit)
    ## Running under: macOS High Sierra 10.13.1
    ## 
    ## Matrix products: default
    ## BLAS: /Library/Frameworks/R.framework/Versions/3.4/Resources/lib/libRblas.0.dylib
    ## LAPACK: /Library/Frameworks/R.framework/Versions/3.4/Resources/lib/libRlapack.dylib
    ## 
    ## locale:
    ## [1] en_US.UTF-8/en_US.UTF-8/en_US.UTF-8/C/en_US.UTF-8/en_US.UTF-8
    ## 
    ## attached base packages:
    ## [1] stats     graphics  grDevices utils     datasets  methods   base     
    ## 
    ## other attached packages:
    ##  [1] bindrcpp_0.2    forcats_0.2.0   stringr_1.2.0   dplyr_0.7.4    
    ##  [5] purrr_0.2.4     readr_1.1.1     tidyr_0.7.2     tibble_1.4.1   
    ##  [9] ggplot2_2.2.1   tidyverse_1.2.1 fs_1.1.0       
    ## 
    ## loaded via a namespace (and not attached):
    ##  [1] Rcpp_0.12.14     cellranger_1.1.0 compiler_3.4.3   pillar_1.1.0    
    ##  [5] plyr_1.8.4       bindr_0.1        tools_3.4.3      digest_0.6.13   
    ##  [9] lubridate_1.7.1  jsonlite_1.5     evaluate_0.10.1  nlme_3.1-131    
    ## [13] gtable_0.2.0     lattice_0.20-35  pkgconfig_2.0.1  rlang_0.1.6     
    ## [17] psych_1.7.8      cli_1.0.0        rstudioapi_0.7   yaml_2.1.16     
    ## [21] parallel_3.4.3   haven_1.1.0      xml2_1.1.1       httr_1.3.1      
    ## [25] knitr_1.18       hms_0.4.0        rprojroot_1.3-2  grid_3.4.3      
    ## [29] glue_1.2.0       R6_2.2.2         readxl_1.0.0     foreign_0.8-69  
    ## [33] rmarkdown_1.8    modelr_0.1.1     reshape2_1.4.3   magrittr_1.5    
    ## [37] scales_0.5.0     backports_1.1.2  htmltools_0.3.6  rvest_0.3.2     
    ## [41] assertthat_0.2.0 mnormt_1.5-5     colorspace_1.3-2 utf8_1.1.3      
    ## [45] stringi_1.1.6    lazyeval_0.2.1   munsell_0.4.3    broom_0.4.3     
    ## [49] crayon_1.3.4
