Visualization
================
Fenglin Xie
2025-09-30

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ ggplot2   3.5.2     ✔ tibble    3.3.0
    ## ✔ lubridate 1.9.4     ✔ tidyr     1.3.1
    ## ✔ purrr     1.1.0     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(patchwork)
library(p8105.datasets)
```

## load the weather data

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(aes(colour = name), alpha = 0.5) +
  labs(
    x = "Minimum daily temp",
    y = "Maximum daily temp",
    title = "Temperature scatterplot",
    caption = "Data from NOAA",
    color = "Location"
  )
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

## Scales

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(aes(colour = name), alpha = 0.5) +
  labs(
    x = "Minimum daily temp",
    y = "Maximum daily temp",
    title = "Temperature scatterplot",
    caption = "Data from NOAA",
    color = "Location"
  ) +
  scale_x_continuous(
    breaks = c(-20, 0, 25),
    labels = c("-20C", "0", "25")
  ) +
  scale_y_continuous(
    trans = "sqrt",
    limits = c(10, 30)
  )
```

    ## Warning in transformation$transform(x): NaNs produced

    ## Warning in scale_y_continuous(trans = "sqrt", limits = c(10, 30)): sqrt
    ## transformation introduced infinite values.

    ## Warning: Removed 843 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

限制范围limit另一种

``` r
weather_df |> 
  filter(tmax < 30, tmin > 10) |> 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(aes(colour = name), alpha = 0.5) +
  labs(
    x = "Minimum daily temp",
    y = "Maximum daily temp",
    title = "Temperature scatterplot",
    caption = "Data from NOAA",
    color = "Location"
  ) +
  scale_x_continuous(
    breaks = c(-20, 0, 25),
    labels = c("-20C", "0", "25")
  )
```

![](viz_ii_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

\##挑选颜色很重要，颜色变化不明显不好

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(aes(colour = name), alpha = 0.5) +
  labs(
    x = "Minimum daily temp",
    y = "Maximum daily temp",
    title = "Temperature scatterplot",
    caption = "Data from NOAA",
    color = "Location"
  ) +
  scale_x_continuous(
    breaks = c(-20, 0, 25),
    labels = c("-20C", "0", "25")
  ) +
  viridis::scale_color_viridis(
    name = "Location", 
    discrete = TRUE
  )
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

## Themes

Make my base plot

``` r
ggp_temperature =
weather_df |> 
  ggplot(aes(x = tmin, y = tmax)) +
  geom_point(aes(colour = name), alpha = 0.5) +
  labs(
    x = "Minimum daily temp",
    y = "Maximum daily temp",
    title = "Temperature scatterplot",
    caption = "Data from NOAA",
    color = "Location"
  ) +
  scale_x_continuous(
    breaks = c(-20, 0, 25),
    labels = c("-20C", "0", "25")
  ) +
  viridis::scale_color_viridis(
    name = "Location", 
    discrete = TRUE
  )
```

update my base plot legend.position:图例位置

themes_minimal:忘记之前所有的设计？？（不确定，再听一遍）

``` r
ggp_temperature +
  theme_minimal() +
  theme(legend.position = "bottom")
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

## Adding data in geoms

``` r
central_park_df =
  weather_df |> 
  filter(name == "CentralPark_NY")

molokai_df =
  weather_df |> 
  filter(name == "Molokai_HI")

ggplot(data = molokai_df, aes(x = date, y = tmax, colour = name)) +
  geom_point() +
  geom_line(data = central_park_df)
```

    ## Warning: Removed 1 row containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

## `patchwork`

Make three plots and combine using patchwork

``` r
ggp_tmax_tmin =
  weather_df |> 
  ggplot(aes(x = tmin, y = tmax, colour = name)) +
  geom_point(alpha = 0.5) +
  theme(legend.position = "none")

ggp_prec_density =
  weather_df |> 
  filter(prcp > 0) |> 
  ggplot(aes(x = prcp, fill = name)) +
  geom_density(alpha = 0.5) +
  theme(legend.position = "none")

ggp_temp_season =
  weather_df |> 
  ggplot(aes(x = date, y = tmax, colour = name)) +
  geom_point(alpha = 0.5) +
  geom_smooth(se = FALSE) +
  theme(legend.position = "bottom")

(ggp_tmax_tmin + ggp_prec_density) / ggp_temp_season
```

    ## Warning: Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_smooth()`).
    ## Removed 17 rows containing missing values or values outside the scale range
    ## (`geom_point()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

## Data manipulation

Let’s make temperature violin plots

``` r
weather_df |> 
  mutate(name = fct_relevel(name, c("Monokai_HI", "CentralPark_NY", "Waterhole_WA"))) |> 
  ggplot(aes(x = tmin, y = tmax, fill = name)) +
  geom_violin(alpha = 0.5)
```

    ## Warning: There was 1 warning in `mutate()`.
    ## ℹ In argument: `name = fct_relevel(name, c("Monokai_HI", "CentralPark_NY",
    ##   "Waterhole_WA"))`.
    ## Caused by warning:
    ## ! 1 unknown level in `f`: Monokai_HI

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_ydensity()`).

    ## Warning: `position_dodge()` requires non-overlapping x intervals.

![](viz_ii_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

``` r
weather_df |> 
  mutate(name = fct_reorder(name, tmax)) |>
  ggplot(aes(x = tmin, y = tmax, fill = name)) +
  geom_violin(alpha = 0.5)
```

    ## Warning: There was 1 warning in `mutate()`.
    ## ℹ In argument: `name = fct_reorder(name, tmax)`.
    ## Caused by warning:
    ## ! `fct_reorder()` removing 17 missing values.
    ## ℹ Use `.na_rm = TRUE` to silence this message.
    ## ℹ Use `.na_rm = FALSE` to preserve NAs.

    ## Warning: Removed 17 rows containing non-finite outside the scale range
    ## (`stat_ydensity()`).

    ## Warning: `position_dodge()` requires non-overlapping x intervals.

![](viz_ii_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

What about data tidiness

``` r
pulse_df =
  haven::read_sas("data/public_pulse_data.sas7bdat") |> 
  janitor::clean_names() |>
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    values_to = "bdi"
  ) |> 
  mutate(visit = fct_inorder(visit))

pulse_df |> 
  ggplot(aes(x = visit, y = bdi)) +
  geom_boxplot()
```

    ## Warning: Removed 879 rows containing non-finite outside the scale range
    ## (`stat_boxplot()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

Make a plot for the FAS study

``` r
pups_df =
  read_csv("data/FAS_pups.csv", na = c("NA", ".", ""), skip = 3) |> 
  janitor::clean_names() |> 
  mutate(
    sex = case_match(
      sex,
      1 ~ "male",
      2 ~ "female"
    )
  )
```

    ## Rows: 313 Columns: 6
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): Litter Number
    ## dbl (5): Sex, PD ears, PD eyes, PD pivot, PD walk
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
litters_df =
  read_csv("data/FAS_litters.csv", na = c("NA", ".", "")) |> 
  janitor::clean_names() |> 
  separate(group, into = c("dose", "tx_day"), sep = 3)
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
fas_df = 
  left_join(pups_df, litters_df, by = "litter_number")

fas_df |> 
  select(pd_ears:tx_day) |> 
  pivot_longer(
    pd_ears:pd_walk,
    names_to = "outcome",
    names_prefix = "pd_",
    values_to = "pn_day"
  ) |> 
  ggplot(aes(x = dose, y = pn_day)) +
  geom_violin() +
  facet_grid(tx_day ~ outcome)
```

    ## Warning: Removed 44 rows containing non-finite outside the scale range
    ## (`stat_ydensity()`).

![](viz_ii_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->
