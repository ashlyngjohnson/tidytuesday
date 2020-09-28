Tidy Tuesday : 9/22/20 - Himalayan Climbing Expeditions
================
Ashlyn Johnson
9/22/2020

## Packages

``` r
library(tidyverse)
library(tidytuesdayR)
library(RColorBrewer)
```

## Session Information

``` r
sessionInfo()
```

    ## R version 4.0.2 (2020-06-22)
    ## Platform: x86_64-w64-mingw32/x64 (64-bit)
    ## Running under: Windows 10 x64 (build 19042)
    ## 
    ## Matrix products: default
    ## 
    ## locale:
    ## [1] LC_COLLATE=English_United States.1252 
    ## [2] LC_CTYPE=English_United States.1252   
    ## [3] LC_MONETARY=English_United States.1252
    ## [4] LC_NUMERIC=C                          
    ## [5] LC_TIME=English_United States.1252    
    ## 
    ## attached base packages:
    ## [1] stats     graphics  grDevices utils     datasets  methods   base     
    ## 
    ## other attached packages:
    ##  [1] RColorBrewer_1.1-2 tidytuesdayR_1.0.1 forcats_0.5.0      stringr_1.4.0     
    ##  [5] dplyr_1.0.2        purrr_0.3.4        readr_1.3.1        tidyr_1.1.2       
    ##  [9] tibble_3.0.3       ggplot2_3.3.2      tidyverse_1.3.0   
    ## 
    ## loaded via a namespace (and not attached):
    ##  [1] tidyselect_1.1.0 xfun_0.17        haven_2.3.1      colorspace_1.4-1
    ##  [5] vctrs_0.3.4      generics_0.0.2   htmltools_0.5.0  usethis_1.6.1   
    ##  [9] yaml_2.2.1       blob_1.2.1       rlang_0.4.7      pillar_1.4.6    
    ## [13] glue_1.4.2       withr_2.2.0      DBI_1.1.0        dbplyr_1.4.4    
    ## [17] modelr_0.1.8     readxl_1.3.1     lifecycle_0.2.0  munsell_0.5.0   
    ## [21] gtable_0.3.0     cellranger_1.1.0 rvest_0.3.6      evaluate_0.14   
    ## [25] knitr_1.29       curl_4.3         fansi_0.4.1      broom_0.7.0     
    ## [29] Rcpp_1.0.5       scales_1.1.1     backports_1.1.9  jsonlite_1.7.1  
    ## [33] fs_1.5.0         hms_0.5.3        digest_0.6.25    stringi_1.5.3   
    ## [37] grid_4.0.2       cli_2.0.2        tools_4.0.2      magrittr_1.5    
    ## [41] crayon_1.3.4     pkgconfig_2.0.3  ellipsis_0.3.1   xml2_1.3.2      
    ## [45] reprex_0.3.0     lubridate_1.7.9  assertthat_0.2.1 rmarkdown_2.3   
    ## [49] httr_1.4.2       rstudioapi_0.11  R6_2.4.1         compiler_4.0.2

## Data

This week’s data comes from The Himalayan Database. This database is
comprised of records of all expeditions in the Nepal Himalaya. These
records come from Elizabeth Hawley as well as other sources. The data
range from 1905 to Spring 2019. The data were cleaned by Alex Cookson.
More information can be found at the [github repository for tidy
tuesday](https://github.com/rfordatascience/tidytuesday/blob/master/data/2020/2020-09-22/readme.md)

``` r
tuesdata <- tidytuesdayR::tt_load(2020, week = 39)
## --- Compiling #TidyTuesday Information for 2020-09-22 ----
## --- There are 3 files available ---
## --- Starting Download ---
## 
##  Downloading file 1 of 3: `peaks.csv`
##  Downloading file 2 of 3: `members.csv`
##  Downloading file 3 of 3: `expeditions.csv`
## --- Download complete ---
members <- tuesdata$members
```

There are so many questions I can ask from the dataset. And several have
already been looked at in [Alex Cookson’s blog
post](https://www.alexcookson.com/post/analyzing-himalayan-peaks-first-ascents/).

### What are the most common injuries in climbers?

``` r
# creating a dataframe with peak names that is sorted by the number of injuries at each peak

members_injury_count <- count(members %>%
  filter(injured == TRUE),
peak_name
) %>%
  arrange(n)

# creating a dataframe that shows the types of injuries at the top 10 peaks for injuries

members_injuries <- members %>%
  filter(injured == TRUE) %>% # filter for injuries
  mutate(peak_name = factor(peak_name, levels = members_injury_count$peak_name, ordered = TRUE)) %>% # change peak_name variable into a factor
  filter(peak_name %in% tail(members_injury_count$peak_name, 10)) %>% # filter for top 10 peaks with the most recorded injuries
  select(c(peak_name, injured, injury_type)) %>% # pulling only the columns that I'll need for my visualization
  drop_na() # removing the one na value because not useful for the question we're asking

# making the graph!

ggplot(members_injuries) +
  geom_bar(aes(
    x = peak_name,
    fill = injury_type
  )) + # will fill the bars in by the type of injury
  scale_fill_brewer(palette = "Set3") +
  coord_flip() +
  labs(
    fill = "Injury Type",
    title = "Types of Injuries Endured During Himalayan Climbing Expeditions",
    subtitle = "Limited to the top 10 peaks with the most injuries.",
    caption = "Source: The Himalayan Database \n Viz: @ashgjoh"
  ) +
  xlab("Peak") +
  ylab("# of Injuries") +
  theme_classic() +
  theme(
    plot.title = element_text(size = 30, face = "bold"),
    plot.subtitle = element_text(size = 20),
    axis.title.x = element_text(size = 25, face = "bold"),
    axis.title.y = element_text(size = 25, face = "bold"),
    axis.text.x = element_text(size = 16, face = "bold"),
    axis.text.y = element_text(size = 16, face = "bold"),
    legend.title = element_text(size = 20, face = "bold"),
    legend.text = element_text(size = 18, face = "bold"),
    plot.caption = element_text(size = 17, face = "bold"),
    legend.position = c(0.8, 0.4),
    legend.key.size = unit(30, "pt")
  )
```

![](200922_tidytuesday_files/figure-gfm/injury%20figure-1.png)<!-- -->
