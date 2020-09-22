Tidy Tuesday : 9/22/20 - Himalayan Climbing Expeditions
================
Ashlyn Johnson
9/22/2020

## Packages

``` r
library(tidyverse)
library(tidytuesdayR)
library(viridis)
```

## Session Information

## Data

This week’s data comes from The Himalayan Database. This database is
comprised of records of all expeditions in the Nepal Himalaya. These
records come from Elizabeth Hawley as well as other sources. The data
range from 1905 to Spring 2019. The data were cleaned by Alex Cookson.
More information can be found at the [github repository for tidy
tuesday](https://github.com/rfordatascience/tidytuesday/blob/master/data/2020/2020-09-22/readme.md)

``` r
tuesdata <- tidytuesdayR::tt_load(2020, week = 39)
```

    ## --- Compiling #TidyTuesday Information for 2020-09-22 ----

    ## --- There are 3 files available ---

    ## --- Starting Download ---

    ## 
    ##  Downloading file 1 of 3: `peaks.csv`
    ##  Downloading file 2 of 3: `members.csv`
    ##  Downloading file 3 of 3: `expeditions.csv`

    ## --- Download complete ---

``` r
peaks <- tuesdata$peaks
members <-  tuesdata$members
expeditions <- tuesdata$expeditions
```

## Exploration of Data

There are so many questions I can ask from the dataset. And several have
already been looked at in [Alex Cookson’s blog
post](https://www.alexcookson.com/post/analyzing-himalayan-peaks-first-ascents/).

### What is the primary cause of injury in climbers?

Note: there’s an issue here. The number of injuries showing up on the
plot is far less than what shows up when I use the count function.
Unclear what is going on.

``` r
# creating a vector with peak names that is in descending order by the number of injuries at each peak

members_injury_count <- count(members %>% 
        filter(injured == TRUE), 
      vars = peak_name) %>% 
  arrange(desc(n))

# creating a plot that shows the types of injuries at the top 10 peaks for injuries 

ggplot(members %>% 
         filter(injured == TRUE) %>%  # filter for injuries 
         mutate(peak_name = factor(peak_name, levels = members_injury_count$vars, ordered = TRUE)) %>%  # mutate peak_name into a factor
         filter(peak_name == members_injury_count$vars[1:10])) + # filter for only entries that contain the one of the top 10 injury peak names
  geom_bar(aes(x = peak_name, fill = injury_type)) + # will fill the bars in by the type of injury
  scale_fill_viridis(option = "inferno", discrete = TRUE) + 
  coord_flip() + 
  labs( fill = "Injury Type", 
        title = "Types of Injuries Endured During Himalayan Climbing Expeditions",
        subtitle = "Limited to the top 10 peaks with the most injuries.",
        caption = "Source: The Himalayan Database \n Viz: @ashgjoh") + 
  xlab("Peak") + 
  ylab("# of Injuries") +
  theme_classic() + 
  theme(plot.title = element_text(size = 20, face = "bold"), 
        plot.subtitle = element_text(size = 18),
        axis.title.x = element_text(size = 15, face = "bold"), 
        axis.title.y = element_text(size = 15, face = "bold"), 
        axis.text.x = element_text(size = 12),
        axis.text.y = element_text(size = 12), 
        legend.title = element_text(size = 15), 
        legend.text = element_text(size = 12, face = "bold"),
        plot.caption = element_text(size = 14, face = "bold"),
        legend.position = "top")
```

    ## Warning in `==.default`(peak_name, members_injury_count$vars[1:10]): longer
    ## object length is not a multiple of shorter object length

    ## Warning in is.na(e1) | is.na(e2): longer object length is not a multiple of
    ## shorter object length

![](200922_tidytuesday_files/figure-gfm/injury%20figure-1.png)<!-- -->
