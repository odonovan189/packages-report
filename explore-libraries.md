My title
================
tdonovan
2020-01-27

``` r
## how jenny might do this in a first exploration
## purposely leaving a few things to change later!
```

Which libraries does R search for packages?

``` r
.libPaths()
```

    ## [1] "C:/RSiteLibrary"                   
    ## [2] "C:/Program Files/R/R-3.6.1/library"

``` r
## let's confirm the second element is, in fact, the default library
.Library
```

    ## [1] "C:/PROGRA~1/R/R-36~1.1/library"

``` r
identical(.Library, .libPaths()[2])
```

    ## [1] FALSE

``` r
## Huh? Maybe this is an symbolic link issue?
library(fs)
identical(path_real(.Library), path_real(.libPaths()[2]))
```

    ## [1] TRUE

Installed packages

``` r
library(tidyverse)
```

    ## Registered S3 methods overwritten by 'ggplot2':
    ##   method         from 
    ##   [.quosures     rlang
    ##   c.quosures     rlang
    ##   print.quosures rlang

    ## Registered S3 method overwritten by 'rvest':
    ##   method            from
    ##   read_xml.response xml2

    ## -- Attaching packages -------------------------------------------------------- tidyverse 1.2.1 --

    ## v ggplot2 3.1.1     v purrr   0.3.2
    ## v tibble  2.1.1     v dplyr   0.8.3
    ## v tidyr   1.0.0     v stringr 1.3.1
    ## v readr   1.3.1     v forcats 0.3.0

    ## -- Conflicts ----------------------------------------------------------- tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
ipt <- installed.packages() %>%
  as_tibble()

head(ipt)
```

    ## # A tibble: 6 x 16
    ##   Package LibPath Version Priority Depends Imports LinkingTo Suggests
    ##   <chr>   <chr>   <chr>   <chr>    <chr>   <chr>   <chr>     <chr>   
    ## 1 abind   C:/RSi~ 1.4-5   <NA>     R (>= ~ method~ <NA>      <NA>    
    ## 2 acepack C:/RSi~ 1.4.1   <NA>     <NA>    <NA>    <NA>      testthat
    ## 3 ade4    C:/RSi~ 1.7-13  <NA>     R (>= ~ graphi~ <NA>      "ade4Tk~
    ## 4 adegen~ C:/RSi~ 2.1.1   <NA>     R (>= ~ "utils~ <NA>      "pegas,~
    ## 5 adehab~ C:/RSi~ 0.4.16  <NA>     "R (>=~ graphi~ <NA>      maptool~
    ## 6 adehab~ C:/RSi~ 0.3.14  <NA>     R (>= ~ graphi~ <NA>      maptool~
    ## # ... with 8 more variables: Enhances <chr>, License <chr>,
    ## #   License_is_FOSS <chr>, License_restricts_use <chr>, OS_type <chr>,
    ## #   MD5sum <chr>, NeedsCompilation <chr>, Built <chr>

``` r
## how many packages?
nrow(ipt)
```

    ## [1] 613

Exploring the packages

``` r
## count some things! inspiration
##   * tabulate by LibPath, Priority, or both
ipt %>%
  count(LibPath, LibPath)
```

    ## # A tibble: 2 x 2
    ##   LibPath                                n
    ##   <chr>                              <int>
    ## 1 C:/Program Files/R/R-3.6.1/library    30
    ## 2 C:/RSiteLibrary                      583

``` r
##   * what proportion need compilation?
ipt %>%
  count(NeedsCompilation) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 3 x 3
    ##   NeedsCompilation     n   prop
    ##   <chr>            <int>  <dbl>
    ## 1 no                 312 0.509 
    ## 2 yes                271 0.442 
    ## 3 <NA>                30 0.0489

``` r
##   * how break down re: version of R they were built on
ipt %>%
  count(Built) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 10 x 3
    ##    Built     n    prop
    ##    <chr> <int>   <dbl>
    ##  1 3.4.1    75 0.122  
    ##  2 3.4.2    31 0.0506 
    ##  3 3.4.3    61 0.0995 
    ##  4 3.4.4   225 0.367  
    ##  5 3.5.0     1 0.00163
    ##  6 3.5.2     2 0.00326
    ##  7 3.5.3    29 0.0473 
    ##  8 3.6.0    83 0.135  
    ##  9 3.6.1    71 0.116  
    ## 10 3.6.2    35 0.0571

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

    ## [1] "translations"

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
    ## 1 FALSE    370 0.604
    ## 2 TRUE     243 0.396
