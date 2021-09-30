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

(no ‘spread()’ akways ‘pivot\_wider()’)

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

## join

``` r
pups_df = 
  read_csv("./data/FAS_pups.csv") %>%
  janitor::clean_names() %>%
  mutate(
    sex = recode(sex, `1` = "male", `2` = "female"), #'1' say that is a variable but not number.
    sex = factor(sex)) 
```

    ## Rows: 313 Columns: 6

    ## -- Column specification --------------------------------------------------------
    ## Delimiter: ","
    ## chr (1): Litter Number
    ## dbl (5): Sex, PD ears, PD eyes, PD pivot, PD walk

    ## 
    ## i Use `spec()` to retrieve the full column specification for this data.
    ## i Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
litters_df = 
  read_csv("./data/FAS_litters.csv") %>%
  janitor::clean_names() %>%
  separate(group, into = c("dose", "day_of_tx"), sep = 3) %>%
  relocate(litter_number) %>%
  mutate(
    wt_gain = gd18_weight - gd0_weight,
    dose = str_to_lower(dose))
```

    ## Rows: 49 Columns: 8

    ## -- Column specification --------------------------------------------------------
    ## Delimiter: ","
    ## chr (2): Group, Litter Number
    ## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...

    ## 
    ## i Use `spec()` to retrieve the full column specification for this data.
    ## i Specify the column types or set `show_col_types = FALSE` to quiet this message.

Let’s join them up.

``` r
fas_df =
  left_join(pups_df, litters_df, by = 'litter_number') %>%
  relocate(litter_number, dose, day_of_tx) #?
```
