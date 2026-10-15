Reading_data_from_web
================
Yiman Rui
2026-10-15

``` r
library(tidyverse)
```

    ## Warning: package 'tibble' was built under R version 4.5.2

    ## Warning: package 'dplyr' was built under R version 4.5.2

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.2.1     ✔ readr     2.1.5
    ## ✔ forcats   1.0.1     ✔ stringr   1.6.0
    ## ✔ ggplot2   4.0.0     ✔ tibble    3.3.1
    ## ✔ lubridate 1.9.4     ✔ tidyr     1.3.1
    ## ✔ purrr     1.1.0     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(rvest)
```

    ## 
    ## Attaching package: 'rvest'
    ## 
    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
library(httr)
```

    ## Warning: package 'httr' was built under R version 4.5.2

``` r
url = "https://www.samhsa.gov/data/sites/default/files/NSDUHsaeShortTermCHG2015/NSDUHsaeShortTermCHG2015.htm"
drug_use_html = read_html(url)

drug_use_html
```

    ## {html_document}
    ## <html lang="en">
    ## [1] <head>\n<title>National Survey on Drug Use and Health: Comparison of 2013 ...
    ## [2] <body>\r\n\r\n<noscript>\r\n<p>Your browser's Javascript is off. Hyperlin ...

Pull out table

``` r
marj_df = 
  drug_use_html |>
  html_table() |>
  first() |>
  slice(-1)
```

Starwar!

``` r
swm_html = 
  read_html_live("https://www.imdb.com/list/ls070150896/")
```

Select gadget

``` r
title_vec = swm_html|>
  html_elements(".ipc-title-link-wrapper .ipc-title__text") |>
  html_text()

metascore_vec = 
  swm_html |>
  html_elements(".metacritic-score-box") |>
  html_text()

runtime_vec = 
  swm_html |>
  html_elements(".base .ipc-inline-list__item:nth-child(2)") |>
  html_text()

swm_df = 
  tibble(
    title = title_vec,
    score = metascore_vec,
    runtime = runtime_vec)
```

# Use APIs

``` r
nyc_water = 
  GET("https://data.cityofnewyork.us/api/v3/views/ia2d-e54m/query.csv") |> 
  content("parsed") |>
  select(year:per_capita_gallons_per_person_per_day)
```

    ## Rows: 65 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr  (2): :id, :version
    ## dbl  (4): year, new_york_city_population, nyc_consumption_million_gallons_pe...
    ## dttm (2): :created_at, :updated_at
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

Get a json file

``` r
nyc_water_json = 
  GET("https://data.cityofnewyork.us/api/v3/views/ia2d-e54m/query.json") |>
  content("text") |>
  jsonlite::fromJSON() |>
  as_tibble()
```

Pokemon!

``` r
pokemon = 
  GET("http://pokeapi.co/api/v2/pokemon/1") 

pokemon_data = 
  pokemon |>
  content()

pokemon_data[["name"]]
```

    ## [1] "bulbasaur"

``` r
pokemon_data[["height"]]
```

    ## [1] 7

``` r
pokemon_data[["abilities"]]
```

    ## [[1]]
    ## [[1]]$is_hidden
    ## [1] FALSE
    ## 
    ## [[1]]$slot
    ## [1] 1
    ## 
    ## [[1]]$ability
    ## [[1]]$ability$name
    ## [1] "overgrow"
    ## 
    ## [[1]]$ability$url
    ## [1] "https://pokeapi.co/api/v2/ability/65/"
    ## 
    ## 
    ## 
    ## [[2]]
    ## [[2]]$is_hidden
    ## [1] TRUE
    ## 
    ## [[2]]$slot
    ## [1] 3
    ## 
    ## [[2]]$ability
    ## [[2]]$ability$name
    ## [1] "chlorophyll"
    ## 
    ## [[2]]$ability$url
    ## [1] "https://pokeapi.co/api/v2/ability/34/"
