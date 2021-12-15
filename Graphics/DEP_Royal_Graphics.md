Graphics From DEP Royal Transect
================
Curtis C. Bohlen, Casco Bay Estuary Partnership.
06/16/2021

-   [Introduction](#introduction)
-   [Folder References](#folder-references)
-   [Load Data](#load-data)
-   [Summary of Metadata](#summary-of-metadata)
    -   [QA/QC Samples](#qaqc-samples)
    -   [Censoring Flags](#censoring-flags)
    -   [Units](#units)
-   [Make Data Subsets](#make-data-subsets)
-   [Royal River Transects](#royal-river-transects)
-   [Royal River Transect](#royal-river-transect)
    -   [Approximate upstream to downstream
        distances](#approximate-upstream-to-downstream-distances)
    -   [How often was each site
        sampled?](#how-often-was-each-site-sampled)
    -   [Maximum Depths, 2017](#maximum-depths-2017)
    -   [May](#may)
        -   [Temperature](#temperature)
        -   [Salinity](#salinity)
        -   [Dissolved Oxygen](#dissolved-oxygen)
    -   [September](#september)
        -   [Temperature](#temperature-1)
        -   [Salinity](#salinity-1)
        -   [Dissolved Oxygen, September](#dissolved-oxygen-september)

<img
    src="https://www.cascobayestuary.org/wp-content/uploads/2014/04/logo_sm.jpg"
    style="position:absolute;top:10px;right:50px;" />

# Introduction

Sonde “downcast” data generates a huge volume of data, which can be
difficult to analyze or summarize. Consequently, the data is often
presented largely in graphical form. Formal statistical analyses are of
less interest, as we expect variation by depth, time, and location.

Here we focus on producing graphical summaries of the DEP sonde downcast
data from a transect from near the head of tide to the mouth of the
Royal River estuary.

We make use of a small graphics package we produced, `tdggraph`, that
encapsulates logic needed to generate the necessary graphics.

\#Load libraries

``` r
#library(readxl)
library(tidyverse)
#> Warning: package 'tidyverse' was built under R version 4.0.5
#> -- Attaching packages --------------------------------------- tidyverse 1.3.1 --
#> v ggplot2 3.3.5     v purrr   0.3.4
#> v tibble  3.1.6     v dplyr   1.0.7
#> v tidyr   1.1.4     v stringr 1.4.0
#> v readr   2.1.0     v forcats 0.5.1
#> Warning: package 'ggplot2' was built under R version 4.0.5
#> Warning: package 'tidyr' was built under R version 4.0.5
#> Warning: package 'dplyr' was built under R version 4.0.5
#> Warning: package 'forcats' was built under R version 4.0.5
#> -- Conflicts ------------------------------------------ tidyverse_conflicts() --
#> x dplyr::filter() masks stats::filter()
#> x dplyr::lag()    masks stats::lag()

library(CBEPgraphics)
load_cbep_fonts()
theme_set(theme_cbep())

library(tdggraph)    # CBEP package for time-depth "profile" graphics
```

# Folder References

``` r
sibfldnm <- 'Data'
parent <- dirname(getwd())
sibling <- paste(parent,sibfldnm, sep = '/')

dir.create(file.path(getwd(), 'figures'), showWarnings = FALSE)
```

# Load Data

``` r
sonde_data <- read_csv(file.path(sibling, 'dep_sonde_data.csv')) %>%
  mutate(yearf = factor(year)) %>%
  mutate(month = factor(month,levels = month.abb))
#> Rows: 2679 Columns: 16
#> -- Column specification --------------------------------------------------------
#> Delimiter: ","
#> chr   (3): site_name, site, month
#> dbl  (10): year, hour, depth, temp, salinity, ph, pctsat, do, chl_a_sonde, t...
#> lgl   (1): turbidity_cens
#> date  (1): dt
#> time  (1): time
#> 
#> i Use `spec()` to retrieve the full column specification for this data.
#> i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

# Summary of Metadata

## QA/QC Samples

We conducted no analysis of QA/QC samples, and simply deleted then from
the data to avoid confusion.

## Censoring Flags

While preparing our working data, we separated raw observations from
text annotations, including data quality flags. In the sonde-related
data, we only had to contend with (1) left censoring of turbidity data ,
and (2) data quality flags on all chlorophyll data.

Since all sonde-related chlorophyll data was flagged as of questionable
accuracy (with “J” flags), it does us no good to track that information
during analysis. We confirmed with DEP staff that the “J” flags reflect
the fact that these are sonde estimates of chlorophyll, based on
observed florescence, which were never recalibarated based on laboratory
samples.

We also had a few “U&lt;” (undetected) flags in the Turbidity data. We
separated out a `TRUE` / `FALSE` flag to indicated censored values, with
the name ’turbidity\_cens\`.

## Units

Our derived data files lack any indication of units. Units were
documented in the source Excel files. We summarize relevant information
here.

| Variable Name | Meaning                                         | Units                       |
|---------------|-------------------------------------------------|-----------------------------|
| site\_name    | DEP “Site ID”                                   |                             |
| site          | DEP “Sample Point ID” without depth designation |                             |
| dt            | Date of sample collection                       | dd/mm/yyyy format           |
| month         | Month, derived from date                        | Three letter codes          |
| year          | Year, derived from date                         |                             |
| time          | time of sample                                  | 24 hour clock, hh:mm format |
| hour          | hour, derived from time                         |                             |
| depth         | Sample Depth                                    | Meters                      |
| temp          | Water Temperature                               | DEG C                       |
| salinity      | Salinity                                        | PSU (roughly, PPTH )        |
| ph            | pH                                              | NBS pH scale                |
| pctsat        | Dissolved Oxygen Saturation                     | %                           |
| do            | Dissolved Oxygen                                | MG/L                        |
| turbidity     | Turbidity                                       | NTU                         |
| chl\_a\_sonde | Chlorophyll A, measured with a sonde            | UG/L                        |

# Make Data Subsets

# Royal River Transects

We filter to sites in the Royal River and Cousins River only. We order
sites in upstream to downstream sequence along each river. Royal River
sites RR-19 and RR-20 are close together, and quite near CR-44. Any of
these three sites could be considered the “most marine” station in these
series, but we use RR-20 as the common “most marine” station, as it
occurs at the confluence of the two rivers.

``` r
rr_transect_sites  <- sonde_data %>%
  filter(grepl('RR', site) |grepl('CR', site)) %>%
  select(site, site_name) %>%
  unique() %>%
  pull(site)
cr_transect <- rr_transect_sites[c(1,4,2,3,9)]
rr_transect <- rr_transect_sites[c(5:9)]
rm(rr_transect_sites)

rr_data <- sonde_data %>%
  filter(site %in% rr_transect | site %in% cr_transect)
#rm(cr_transect, rr_transect)
```

# Royal River Transect

## Approximate upstream to downstream distances

We estimated distances between sampling in GIS locations so the graphics
reflect relative locatiosn of sampling points up and down the estuary.

``` r
rr_dist_lookup <- tibble( site = c('RR-01', 'RR-06', 
                                   'RR-13', 'RR-19', 'RR-20'),
                          dist = c(0, 885, 2043, 3089, 3248))
```

## How often was each site sampled?

We make an assumption here that sampling on one day is related.

``` r
tmp <- rr_data %>%
  filter(site %in% rr_transect) %>%
  group_by(site, year) %>%
  summarize(was_sampled = sum(! is.na(depth)) > 1,
            .groups = 'drop')
xtabs(~ year + site, addNA = TRUE, data = tmp)
#>       site
#> year   RR-01 RR-06 RR-13 RR-19 RR-20
#>   2016     0     1     1     1     0
#>   2017     1     1     1     1     1
```

So, DEP sampled some sites, including the most upstream and the most
downstream only in 2017. Since our purpose here is largely to
demonstrate ideas for displaying these data, we focus principally on
2017.

We filter the Royal Rive data to 2017, and order sites in upstream to
downstream order. We also add in the `dist` values that roughly indicate
distance from the most upstream sampling location. Note that RR-19 and
RR-20 are quite close. RR20 is shallower, as it sits on the edge of the
bar at the confluence of the Royal and Cousins rivers.

``` r
rr_trans <- rr_data %>%
  filter(site %in% rr_transect) %>%
  left_join(rr_dist_lookup, by = 'site') %>%
  rename(dates = dt) %>%
  filter(year == 2017) %>%
  mutate(site = factor(site, 
                       levels = c('RR-01', 'RR-06', 'RR-13',
                                  'RR-19', 'RR-20')),
         sitenum = as.numeric(site))   # Needed for aleternate plotting
```

## Maximum Depths, 2017

``` r
rr_data %>%
  filter(year == 2017) %>%
  group_by(site) %>%
  summarize(maxdepth = max(depth, na.rm = TRUE))
#> # A tibble: 9 x 2
#>   site    maxdepth
#>   <chr>      <dbl>
#> 1 CR-31       5.02
#> 2 CR-44       1.84
#> 3 CR00        1.49
#> 4 CRTRIB0     2.49
#> 5 RR-01       5.00
#> 6 RR-06       5.5 
#> 7 RR-13       5.51
#> 8 RR-19       5.07
#> 9 RR-20       2.5
```

## May

``` r
tmp1 <- rr_trans %>%
  filter(year == 2017) %>%
  filter(dates == min(dates))
```

``` r
ptlines(tmp1, temp, depth, site)
```

<img src="DEP_Royal_Graphics_files/figure-gfm/unnamed-chunk-9-1.png" style="display: block; margin: auto;" />

### Temperature

``` r
ptsmooth(tmp1, .x  = dist,  .y  = depth,  .val = temp, 
         .res_x = 50, .res_y = .1,
         y_grow_grid = FALSE, y_with_zero = FALSE) +
  scale_fill_distiller(palette = 7, direction = 2, 
                      limits = c(7, 22),
                      na.value = 'gray95'
                                  ) +
  theme_cbep(base_size = 12) +
  theme(legend.position = 'bottom',
        axis.text.x = element_text(angle = 90)) +
  guides(fill = guide_colorbar(title = expression(Temperature ~ ( degree * C)), 
                               title.position = 'top',
                               barheight = unit(0.2, 'cm'))) +

  geom_point(mapping = aes(dist, depth), data= tmp1,
             shape = 21, fill = NA, color = 'gray70', size = 2) +
  scale_x_continuous(breaks = rr_dist_lookup$dist, 
                     labels = rr_dist_lookup$site,
                     limits = c(0, 3250)) +
  ylim(6,0) +
  
  xlab('') +
  ylab('Depth (m)') +
  ggtitle('May')
#> Scale for 'y' is already present. Adding another scale for 'y', which will
#> replace the existing scale.
```

<img src="DEP_Royal_Graphics_files/figure-gfm/unnamed-chunk-10-1.png" style="display: block; margin: auto;" />

``` r
ggsave('figures/royal_may_temp.pdf', device = cairo_pdf, width = 3, height = 3)
```

### Salinity

``` r
ptsmooth(tmp1, .x  = dist,  .y  = depth,  .val = salinity, 
          .res_x = 50, .res_y = .1,
         y_grow_grid = FALSE, y_with_zero = FALSE) +
  scale_fill_distiller(palette = 3, direction = 2, 
                      limits = c(0, 32),
                      na.value = 'gray95'
                                  ) +
  theme_cbep(base_size = 12) +
  theme(legend.position = 'bottom') +
  guides(fill = guide_colorbar(title = 'Salinity (PSU)', 
                               title.position = 'top',
                               barheight = unit(0.2, 'cm'))) +

  geom_point(mapping = aes(dist, depth), data= tmp1,
             shape = 21, fill = NA, color = 'gray70', size = 3) +
   scale_x_continuous(breaks = rr_dist_lookup$dist, 
                     labels = rr_dist_lookup$site,
                     limits = c(0, 3250)) +
  ylim(6,0) +

  xlab('') +
  ylab('Depth (m)') +
  ggtitle('May')
#> Scale for 'y' is already present. Adding another scale for 'y', which will
#> replace the existing scale.
```

<img src="DEP_Royal_Graphics_files/figure-gfm/unnamed-chunk-11-1.png" style="display: block; margin: auto;" />

``` r
ggsave('figures/royal_may_salinity.pdf', device = cairo_pdf, width = 3, height = 3)
```

### Dissolved Oxygen

``` r
ptsmooth(tmp1, .x  = dist,  .y  = depth,  .val = do, 
          .res_x = 50, .res_y = .1,
         y_grow_grid = FALSE, y_with_zero = FALSE) +
  scale_fill_distiller(palette = 4, direction = 2, 
                        limits = c(6, 11),
                        na.value = 'gray95',
                                  ) +
  theme_cbep(base_size = 12) +
  theme(legend.position = 'bottom') +
  guides(fill = guide_colorbar(title = 'Dissolved Oxygen (mg/l)', 
                               title.position = 'top',
                               barheight = unit(0.2, 'cm'))) +
  
  geom_point(mapping = aes(dist, depth), data= tmp1,
             shape = 21, fill = NA, color = 'gray70', size = 3) +
  scale_x_continuous(breaks = rr_dist_lookup$dist, 
                     labels = rr_dist_lookup$site,
                     limits = c(0, 3250)) +
  ylim(6,0) +

  xlab('') +
  ylab('Depth (m)') +
  ggtitle('May')
#> Scale for 'y' is already present. Adding another scale for 'y', which will
#> replace the existing scale.
```

<img src="DEP_Royal_Graphics_files/figure-gfm/unnamed-chunk-12-1.png" style="display: block; margin: auto;" />

``` r
ggsave('figures/royal_may_oxygen.pdf', device = cairo_pdf, width = 3, height = 3)
```

## September

``` r
tmp2 <- rr_trans %>%
  filter(year == 2017) %>%
  filter(month == 'Sep')
```

### Temperature

``` r
ptsmooth(tmp2, .x  = dist,  .y  = depth,  .val = temp, 
          .res_x = 50, .res_y = .1,
         y_grow_grid = FALSE, y_with_zero = FALSE) +
  
  scale_fill_distiller(palette = 7, direction = 2, 
                        limits = c(7, 22),
                        na.value = 'gray95',
                                  ) +
  theme_cbep(base_size = 12) +
  theme(legend.position = 'bottom') +
  guides(fill = guide_colorbar(title = 'Temperature (C)', 
                               title.position = 'top',
                               barheight = unit(0.2, 'cm'))) +
  
  geom_point(mapping = aes(dist, depth), data= tmp2,
             shape = 21, fill = NA, color = 'gray70', size = 3) +
  scale_x_continuous(breaks = rr_dist_lookup$dist, 
                     labels = rr_dist_lookup$site,
                     limits = c(0, 3250)) +
  ylim(6,0) +

  xlab('') +
  ylab('Depth (m)') +
  ggtitle('September')
#> Scale for 'y' is already present. Adding another scale for 'y', which will
#> replace the existing scale.
```

<img src="DEP_Royal_Graphics_files/figure-gfm/unnamed-chunk-14-1.png" style="display: block; margin: auto;" />

``` r
ggsave('figures/royal_sept_temp.pdf', device = cairo_pdf, width = 3, height = 3)
```

### Salinity

``` r
ptsmooth(tmp2, .x  = dist,  .y  = depth,  .val = salinity, 
          .res_x = 50, .res_y = .1,
         y_grow_grid = FALSE, y_with_zero = FALSE) +
  scale_fill_distiller(palette = 3, direction = 2, 
                      limits = c(0, 32),
                      na.value = 'gray95'
                                  ) +
  theme_cbep(base_size = 12) +
  theme(legend.position = 'bottom') +
  guides(fill = guide_colorbar(title = 'Salinity (PSU)', 
                               title.position = 'top',
                               barheight = unit(0.2, 'cm'))) +

  geom_point(mapping = aes(dist, depth), data= tmp2,
             shape = 21, fill = NA, color = 'gray70', size = 3) +
  scale_x_continuous(breaks = rr_dist_lookup$dist, 
                     labels = rr_dist_lookup$site,
                     limits = c(0, 3250)) +
  ylim(6,0) +

  xlab('') +
  ylab('Depth (m)') +
  ggtitle('September')
#> Scale for 'y' is already present. Adding another scale for 'y', which will
#> replace the existing scale.
```

<img src="DEP_Royal_Graphics_files/figure-gfm/unnamed-chunk-15-1.png" style="display: block; margin: auto;" />

``` r
ggsave('figures/royal_sept_salinity.pdf', device = cairo_pdf, width = 3, height = 3)
```

### Dissolved Oxygen, September

``` r
ptsmooth(tmp2, .x  = dist,  .y  = depth,  .val = do, 
          .res_x = 50, .res_y = .1,
         y_grow_grid = FALSE, y_with_zero = FALSE) +
  scale_fill_distiller(palette = 4, direction = 2, 
                        limits = c(6, 11),
                        na.value = 'gray95',
                                  ) +
  theme_cbep(base_size = 12) +
  theme(legend.position = 'bottom') +
  guides(fill = guide_colorbar(title = 'Dissolved Oxygen (mg/l)', 
                               title.position = 'top',
                               barheight = unit(0.2, 'cm'))) +
  
  geom_point(mapping = aes(dist, depth), data= tmp2,
             shape = 21, fill = NA, color = 'gray70', size = 3) +
  scale_x_continuous(breaks = rr_dist_lookup$dist, 
                     labels = rr_dist_lookup$site,
                     limits = c(0, 3250)) +
  ylim(6,0) +

  xlab('') +
  ylab('Depth (m)') +
  ggtitle('September')
#> Scale for 'y' is already present. Adding another scale for 'y', which will
#> replace the existing scale.
```

<img src="DEP_Royal_Graphics_files/figure-gfm/unnamed-chunk-16-1.png" style="display: block; margin: auto;" />

``` r
ggsave('figures/royal_sept_oxygen.pdf', device = cairo_pdf, width = 3, height = 3)
```
