tidy_data
================
Tianqi Li
2024-09-24

This document will show how to tidy data.

``` r
pulse_df = 
  read_sas("data/public_pulse_data.sas7bdat") |>
  janitor::clean_names()
```

``` r
pulse_tidy_df =
  pulse_df |>
  pivot_longer(
    cols = bdi_score_bl:bdi_score_12m,
    names_to = "visit", 
    values_to = "bdi",
    names_prefix = "bdi_score_"
  ) |>
  mutate(
    visit = replace(visit, visit == "bl", "00m")
  ) |>
  relocate(id, visit)
```

Do one more example

``` r
litters_df = 
  read_csv("data/FAS_litters.csv", na = c("NA", "",".")) |>
  janitor::clean_names()
```

    ## Rows: 49 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): Group, Litter Number
    ## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
litters_tidy_df = 
  litters_df |>
    pivot_longer(
    cols = gd0_weight:gd18_weight,
    names_to = "gd_time", 
    values_to = "weight"
    ) |>
  mutate(
    gd_time = case_match(
      gd_time,
      "gd0_weight" ~ 0,
      "gd18_weight" ~ 18
    ))
```

## Pivot_wider

Let’s make up an analysis result table

``` r
analysis_df = 
  tibble(
    group = c("treatment", "treatment", "control", "control"),
    time = c("pre", "post", "pre", "post"),
    mean = c(4, 10, 4.2, 5)
  )
```

Pivot wider for human readability

``` r
analysis_df |>
  pivot_wider(
    names_from = time,
    values_from = mean
  ) |>
  knitr::kable()
```

| group     | pre | post |
|:----------|----:|-----:|
| treatment | 4.0 |   10 |
| control   | 4.2 |    5 |

## Bind tables

``` r
fellowship_ring = 
  read_excel("data/LotR_Words.xlsx", range = "B3:D6") |>
  mutate(movie = "fellowship_rings")

two_towers = 
  readxl::read_excel("data/LotR_Words.xlsx", range = "F3:H6") |>
  mutate(movie = "two_towers")

return_king = 
  readxl::read_excel("data/LotR_Words.xlsx", range = "J3:L6") |>
  mutate(movie = "return_king")

lotr_df = 
  bind_rows(fellowship_ring, two_towers, return_king) |>
  janitor::clean_names() |>
  pivot_longer(
    cols = female:male,
    names_to = "gender", 
    values_to = "words"
  ) |>
  relocate(movie) |>
  mutate(race = str_to_lower(race))
```
