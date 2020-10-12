Tidy Tuesday: 10/6/20 - NCAA Women’s Basketball Tournament
================
Ashlyn Johnson
10/12/2020

``` r
library(tidyverse)
library(tidytuesdayR)
library(knitr)
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
    ##  [1] knitr_1.29         tidytuesdayR_1.0.1 forcats_0.5.0      stringr_1.4.0     
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
    ## [25] curl_4.3         fansi_0.4.1      broom_0.7.0      Rcpp_1.0.5      
    ## [29] scales_1.1.1     backports_1.1.9  jsonlite_1.7.1   fs_1.5.0        
    ## [33] hms_0.5.3        digest_0.6.25    stringi_1.5.3    grid_4.0.2      
    ## [37] cli_2.0.2        tools_4.0.2      magrittr_1.5     crayon_1.3.4    
    ## [41] pkgconfig_2.0.3  ellipsis_0.3.1   xml2_1.3.2       reprex_0.3.0    
    ## [45] lubridate_1.7.9  assertthat_0.2.1 rmarkdown_2.3    httr_1.4.2      
    ## [49] rstudioapi_0.11  R6_2.4.1         compiler_4.0.2

### Importing the data

The data this week is about Women’s NCAA basketball tournaments. So by
nature, each entry is a team that made it to the tournament that year.

``` r
tuesdata <- tidytuesdayR::tt_load(2020, week = 41)
```

    ## --- Compiling #TidyTuesday Information for 2020-10-06 ----

    ## --- There is 1 file available ---

    ## --- Starting Download ---

    ## 
    ##  Downloading file 1 of 1: `tournament.csv`

    ## --- Download complete ---

``` r
tournament <- tuesdata$tournament
```

### Narrowing the data down

I’m not quite sure what I want to do with these data yet. But I do know
that I want to focus on ‘Tobacco Road’ schools, so North Carolina, NC
State, Duke, and Wake Forest. However, just a quick look at the data
shows me that Wake Forest only made it into the tournament once. So
instead, I’ll focus on ‘Triangle Teams’.

``` r
triangle_tournament <- tournament %>%
  filter(school == "North Carolina" | school == "NC State" | school == "Duke")
```

### The Plot\!

I’ve decided to make a graph with percentage of wins by year for each
team. These wins include conference and tournament wins. Unfortunately,
if the team didn’t make the tournament that year, then there is no
information how how many conference wins they had.

``` r
ggplot(data = triangle_tournament, aes(x = year, y = full_percent)) +
  geom_point(aes(color = school), size = 2) +
  geom_line(aes(color = school), size = 1) +
  scale_color_manual(values = c("#00009c", "#CC0000", "#7BAFD4")) + # these hex codes are specific for each school's colors
  scale_y_continuous(breaks = c(0, 10, 20, 30, 40, 50, 60, 70, 80, 90, 100),
                     limits = c(0, 100)) +
  scale_x_continuous(
    breaks = c(1982, 1985, 1990, 1995, 2000, 2005, 2010, 2015, 2018),
    limits = c(1982, 2018)
  ) +
  theme_classic() +
  labs(
    title = "Triangle Basketball",
    subtitle = "% of wins (regular + tournament) for teams that made the NCAA tournament that year",
    y = "% of total wins",
    x = "Tournament Year"
  ) +
  theme(
    plot.subtitle = element_text(size = 14,
                                 colour = "black"),
    plot.caption = element_text(
      size = 11,
      face = "bold",
      colour = "black"
    ),
    axis.line = element_line(linetype = "solid"),
    axis.ticks = element_line(colour = "black"),
    panel.grid.major = element_line(linetype = "blank"),
    panel.grid.minor = element_line(linetype = "blank"),
    axis.title = element_text(size = 14,
                              face = "bold"),
    axis.text = element_text(size = 12,
                             colour = "black"),
    plot.title = element_text(size = 15,
                              face = "bold"),
  ) +
  labs(caption = "data: FiveThirtyEight \n viz: @ashgjoh") +
  theme(
    legend.text = element_text(size = 10,
                               face = "bold"),
    legend.position = "bottom",
    legend.direction = "horizontal"
  ) +
  theme(legend.title = element_text(face = "bold")) +
  labs(colour = "School") +
  theme(
    panel.background = element_rect(fill = "gray95"),
    plot.background = element_rect(fill = "gray95"),
    legend.key = element_rect(fill = "gray95"),
    legend.background = element_rect(fill = "gray95")
  ) 
```

![](201006_tidytuesday_files/figure-gfm/initial%20plot-1.png)<!-- --> An
initial look at this plot shows that the Duke team had a very low % of
total wins in 2005. This seemed really unusual because all of the teams
in this dataset made the NCAA tournament. It’s possible that they won
the ACC tournament that year which could be why they were able to make
‘the Big Dance’ with such a low percentage of total wins. But either
way, it warranted looking at the data a little closer.

If I look at the entry for the Duke team in the year of 2005, I get
this:

``` r
triangle_tournament %>% 
  filter(school == "Duke", year == 2005) %>% 
  kable()
```

| year | school | seed | conference     | conf\_w | conf\_l | conf\_percent | conf\_place | reg\_w | reg\_l | reg\_percent | how\_qual | x1st\_game\_at\_home | tourney\_w | tourney\_l | tourney\_finish | full\_w | full\_l | full\_percent |
| ---: | :----- | ---: | :------------- | ------: | ------: | ------------: | :---------- | -----: | -----: | -----------: | :-------- | :------------------- | ---------: | ---------: | :-------------- | ------: | ------: | ------------: |
| 2005 | Duke   |    2 | Atlantic Coast |      12 |       2 |          85.7 | T1st        |     28 |      4 |         87.5 | at-large  | N                    |          3 |          1 | RF              |      31 |      53 |          36.9 |

Looking at this entry, I can see an error. The total losses, which is
calculated by adding regular season losses and tournament losses, is
listed as 53. However, Duke only had 4 regular season losses and 1
tournament loss that year, so the number should be 5.

So let’s recalculate the `full_percent` value for the 2005 Duke Team.

``` r
duke_2005_total_wins <- triangle_tournament %>%
  filter(school == "Duke", year == "2005") %>%
  select(reg_w, tourney_w) %>%
  sum()

duke_2005_total_losses <- triangle_tournament %>%
  filter(school == "Duke", year == "2005") %>%
  select(reg_l, tourney_l) %>%
  sum()

duke_2005_full_percent <-
  duke_2005_total_wins / sum(duke_2005_total_losses, duke_2005_total_wins)

paste0(
  "The percentage of total wins for the Duke 2005 team is ",
  round(duke_2005_full_percent * 100, digits = 1),
  "%"
)
```

    ## [1] "The percentage of total wins for the Duke 2005 team is 86.1%"

So, now I’m a little afraid that there might be errors other places. So,
to get around that, I will recalculate the `full_w`, `full_l`, and
`full_percent` columns and then replot my graph.

``` r
triangle_tournament_2 <- triangle_tournament %>%
  mutate(
    full_w = reg_w + tourney_w,
    full_l = reg_l + tourney_l,
    full_percent = round((full_w / (full_w + full_l)) * 100, digits = 1)
  ) 
```

So now I’ll remake the plot.

``` r
ggplot(data = triangle_tournament_2, aes(x = year, y = full_percent)) +
  geom_point(aes(color = school), size = 2) +
  geom_line(aes(color = school), size = 1) +
  scale_color_manual(values = c("#00009c", "#CC0000", "#7BAFD4")) + # these hex codes are specific for each school's colors
  scale_y_continuous(breaks = c(50, 60, 70, 80, 90, 100),
                     limits = c(50, 100)) +
  scale_x_continuous(
    breaks = c(1983, 1988, 1993, 1998, 2003, 2008, 2013, 2018),
    limits = c(1982, 2018)
  ) +
  theme_classic() +
  labs(
    title = "Triangle Basketball",
    subtitle = "% of wins (regular + tournament) for teams that made the NCAA tournament that year",
    y = "% of total wins",
    x = "Tournament Year"
  ) +
  theme(
    plot.subtitle = element_text(size = 14,
                                 colour = "black"),
    plot.caption = element_text(
      size = 11,
      face = "bold",
      colour = "black"
    ),
    axis.line = element_line(linetype = "solid"),
    axis.ticks = element_line(colour = "black"),
    panel.grid.major = element_line(linetype = "blank"),
    panel.grid.minor = element_line(linetype = "blank"),
    axis.title = element_text(size = 14,
                              face = "bold"),
    axis.text = element_text(size = 12,
                             colour = "black"),
    plot.title = element_text(size = 15,
                              face = "bold"),
  ) +
  labs(caption = "data: FiveThirtyEight \n viz: @ashgjoh") +
  theme(
    legend.text = element_text(size = 10,
                               face = "bold"),
    legend.position = "bottom",
    legend.direction = "horizontal"
  ) +
  theme(legend.title = element_text(face = "bold")) +
  labs(colour = "School") +
  theme(
    panel.background = element_rect(fill = "gray95"),
    plot.background = element_rect(fill = "gray95"),
    legend.key = element_rect(fill = "gray95"),
    legend.background = element_rect(fill = "gray95")
  ) 
```

![](201006_tidytuesday_files/figure-gfm/final%20plot-1.png)<!-- -->
