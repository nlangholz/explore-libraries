01\_explore-libraries\_jenny.R
================
nlangholz
Wed Jan 31 14:03:59 2018

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
