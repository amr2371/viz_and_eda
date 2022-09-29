viz_part_01
================
Allison Randy-Cofie

## Let’s import data

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.2 ──
    ## ✔ ggplot2 3.3.6      ✔ purrr   0.3.4 
    ## ✔ tibble  3.1.8      ✔ dplyr   1.0.10
    ## ✔ tidyr   1.2.1      ✔ stringr 1.4.1 
    ## ✔ readr   2.1.2      ✔ forcats 0.5.2 
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
library(ggridges)
```

This is code from the course website

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USC00519397", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2017-01-01",
    date_max = "2017-12-31") %>%
  mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USC00519397 = "Waikiki_HA",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) %>%
  select(name, id, everything())
```

    ## Registered S3 method overwritten by 'hoardr':
    ##   method           from
    ##   print.cache_info httr

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2022-09-29 10:33:47 (8.401)

    ## file min/max dates: 1869-01-01 / 2022-09-30

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USC00519397.dly

    ## date created (size, mb): 2022-09-29 10:33:50 (1.699)

    ## file min/max dates: 1965-01-01 / 2020-03-31

    ## using cached file: ~/Library/Caches/R/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2022-09-29 10:33:51 (0.95)

    ## file min/max dates: 1999-09-01 / 2022-09-30

Let’s make a scatterplot

``` r
ggplot(weather_df, aes(x=tmin, y=tmax)) +
  geom_point()
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_part_01_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

Let’s make the same scatterplot, but different

``` r
weather_df %>% 
  drop_na() %>% 
  #filter(name == "CentralPark_NY") %>%  this shows only NY
  ggplot(aes(x=tmin, y=tmax)) +
  geom_point()
```

![](viz_part_01_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

Let’s keep making the same plot but different.

``` r
weather_scatterplot = 
  weather_df %>% 
  drop_na() %>% 
  ggplot(aes(x=tmin,y=tmax))

weather_scatterplot +
  geom_point()
```

![](viz_part_01_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

## Let’s fancy this up a bit

``` r
weather_df %>% 
  ggplot(aes(x=tmin,y=tmax,color=name)) +
  geom_point() 
```

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_part_01_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

``` r
 # geom_smooth() applies a line for each location instead of the bottom one
```

``` r
weather_df %>% 
  ggplot(aes(x=tmin,y=tmax,color=name)) +
  geom_point(alpha=0.3) + #makes the points transparent
 geom_smooth(se = FALSE)  #gets rid of error bars
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_part_01_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->

``` r
weather_df %>% 
  ggplot(aes(x=tmin,y=tmax)) +
  geom_point(aes(color=name)) +
  geom_smooth() #runs a line through the middle of the dataset
```

    ## `geom_smooth()` using method = 'gam' and formula 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_part_01_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

Maybe make separate panels.

``` r
weather_df %>% 
  ggplot(aes(x=tmin,y=tmax,color=name)) +
  geom_point(alpha=0.3) + 
  geom_smooth(se = FALSE) +
  facet_grid(. ~ name) #makes different panels without excessive code name~ . will put names on the rows. so it is row ~ column
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 15 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 15 rows containing missing values (geom_point).

![](viz_part_01_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

`tmax` vs `tmin` is boring, let’s spice it up some.

``` r
weather_df %>% 
  ggplot(aes(x=date, y=tmax, size = prcp)) +
  geom_point(alpha=0.3) +
  geom_smooth(se = FALSE)+
  facet_grid(. ~ name)
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](viz_part_01_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

``` r
weather_df %>% 
  ggplot(aes(x=date, y=tmax, color = name)) +
  geom_point(aes(size = prcp), alpha=0.3) +
  geom_smooth(se = FALSE)+
  facet_grid(. ~ name) +
  theme(axis.text.x=element_text(angle = 90, vjust = 9.5, hjust=1)) # rotates the x axis labels
```

    ## `geom_smooth()` using method = 'loess' and formula 'y ~ x'

    ## Warning: Removed 3 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 3 rows containing missing values (geom_point).

![](viz_part_01_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

## Some quick stuff

``` r
weather_df %>% 
 ggplot(aes(x=tmin, y=tmax)) +
  geom_hex()
```

    ## Warning: Removed 15 rows containing non-finite values (stat_binhex).

![](viz_part_01_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

Histograms, densityplots, boxplots and violins,,.

``` r
weather_df %>% 
  ggplot(aes(x=tmin, color=name)) +
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 15 rows containing non-finite values (stat_bin).

![](viz_part_01_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

``` r
weather_df %>% 
  ggplot(aes(x=tmin, fill=name)) +
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 15 rows containing non-finite values (stat_bin).

![](viz_part_01_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

``` r
weather_df %>% 
  ggplot(aes(x=tmin, fill=name)) +
  geom_histogram()+
  facet_grid(. ~ name)
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 15 rows containing non-finite values (stat_bin).

![](viz_part_01_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->

Let’s see more options!

``` r
weather_df %>% 
  ggplot(aes(x=tmax, color=name)) +
  geom_density()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_density).

![](viz_part_01_files/figure-gfm/unnamed-chunk-16-1.png)<!-- -->

``` r
weather_df %>% 
  ggplot(aes(x=tmax, fill=name)) +
  geom_density()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_density).

![](viz_part_01_files/figure-gfm/unnamed-chunk-17-1.png)<!-- -->

``` r
weather_df %>% 
  ggplot(aes(x=tmax, fill=name)) +
  geom_density(alpha=.3)
```

    ## Warning: Removed 3 rows containing non-finite values (stat_density).

![](viz_part_01_files/figure-gfm/unnamed-chunk-18-1.png)<!-- -->

Boxplots?

``` r
weather_df %>% 
  ggplot(aes(y = tmax)) +
  geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

![](viz_part_01_files/figure-gfm/unnamed-chunk-19-1.png)<!-- -->

``` r
weather_df %>% 
  ggplot(aes(x=name, y = tmax)) +
  geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

![](viz_part_01_files/figure-gfm/unnamed-chunk-20-1.png)<!-- -->

``` r
weather_df %>% 
  ggplot(aes(x=name, y = tmax, fill=name)) +
  geom_boxplot()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_boxplot).

![](viz_part_01_files/figure-gfm/unnamed-chunk-21-1.png)<!-- -->

Violin plots come in handy when you have like 20/30 things you are
paneling by

``` r
weather_df %>% 
  ggplot(aes(x=name, y = tmax, fill=name)) +
  geom_violin()
```

    ## Warning: Removed 3 rows containing non-finite values (stat_ydensity).

![](viz_part_01_files/figure-gfm/unnamed-chunk-22-1.png)<!-- -->

OR

``` r
weather_df %>% 
  ggplot(aes(x=tmax,y=name)) +
  geom_density_ridges()
```

    ## Picking joint bandwidth of 1.84

    ## Warning: Removed 3 rows containing non-finite values (stat_density_ridges).

![](viz_part_01_files/figure-gfm/unnamed-chunk-23-1.png)<!-- -->
