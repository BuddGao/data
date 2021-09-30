Tidy\_data
================

I’m an R Markdown document!

``` r
library(tidyverse)
```

    ## -- Attaching packages --------------------------------------- tidyverse 1.3.1 --

    ## v ggplot2 3.3.5     v purrr   0.3.4
    ## v tibble  3.1.4     v dplyr   1.0.7
    ## v tidyr   1.1.3     v stringr 1.4.0
    ## v readr   2.0.1     v forcats 0.5.1

    ## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
library(readxl)
library(haven)
```

## pivot longer

``` r
pulse_df = 
  haven::read_sas("./data/public_pulse_data.sas7bdat") %>%
  janitor::clean_names()
```

try to pivot

``` r
pulse_tidy = 
  pulse_df %>%
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = 'visit',
    names_prefix = 'bdi_score_',
    values_to = 'bdi'
  ) %>%
  mutate(
    visit = replace(visit, visit == "bl", '00m')
  )
```

\#\#pivot\_wider

``` r
analysis_df =
  tibble(
    group = c('treatment', 'treatment','control','control'),
    time = c('a','b','c','d'),
    group_mean = c(4,8,3,6) 
    #kniter::kable()?
    )
```

## bind\_rows

import LotR movie

``` r
fellowship_df =
  read_excel('data/LotR_Words.xlsx', range = 'B3:D6') %>%
  mutate(movie = ' fellowship_rings')

two_towers_df =
  read_excel('data/LotR_Words.xlsx', range = 'F3:H6') %>%
  mutate(movie = ' two_towers')#?

return_king_df =
  read_excel('data/LotR_Words.xlsx', range = 'J3:L6') %>%
  mutate(movie = ' return_king')

lotr_df =
  bind_rows(fellowship_df, two_towers_df, return_king_df) %>%
  janitor::clean_names() %>%
  pivot_longer(
    female:male,
    names_to = "sex", 
    values_to = "words"
  ) %>%
#  mutate(race = str_to_lower(race))  
  relocate(movie)
```

(never use rbind(), always use ‘bind\_rows()’.)
