Tidy Tuesday: 10/20/20 - Great American Beer Festival
================
Ashlyn Johnson
10/26/2020

``` r
library(tidyverse)
library(tidytuesdayR) 
library(flextable)
library(officer)
library(webshot2)
```

This week’s Tidy Tuesday data comes from the Great American Beer
Festival (GABF). There are many different categories of beers and within
each category a bronze, silver, or gold medal can be awarded.

### Getting the Data

``` r
tuesdata <- tidytuesdayR::tt_load(2020, week = 43)
```

    ## 
    ##  Downloading file 1 of 1: `beer_awards.csv`

``` r
beer_awards <- tuesdata$beer_awards
```

### Exploration using a table

Being that I live in Georgia currently, I am interested to know how
successful Georgia beers have been in the GABF. First, I’ll use the
`flextable` package to make a table of the winning Georgia beers.

``` r
# cleaning up the dataframe a bit and filtering for GA breweries

ga_beer_awards <- beer_awards %>%
  filter(state == "GA") %>%
  select(-state) %>%
  mutate(medal = factor(medal, levels = c("Bronze", "Silver", "Gold"))) %>%
  arrange(medal, city, brewery, year) %>%
  rename(
    Medal = medal,
    City = city,
    Brewery = brewery,
    Beer = beer_name,
    Category = category,
    Year = year
  )

# getting some useful vectors for specifying rows for conditional formatting

ga_beer_awards_cities <- ga_beer_awards$City %>% unique()
ga_beer_awards_breweries <- ga_beer_awards$Brewery %>% unique()

# making the table 

ga_beer_awards_table <- ga_beer_awards %>%
  as_grouped_data(groups = c("Medal", "City", "Brewery")) %>%
  flextable() %>%
  colformat_char(j = "Year", na_str = "") %>%
  bg(j = "Beer", bg = "skyblue2") %>%
  bg(j = "Category", bg = "skyblue3") %>%
  bg(j = "Year", bg = "skyblue4") %>%
  bg(i = ~ Medal == "Bronze", bg = "#d0700e") %>%
  bg(i = ~ Medal == "Silver", bg = "lightsteelblue4") %>%
  bg(i = ~ Medal == "Gold", bg = "gold") %>%
  bg(i = ~ City %in% ga_beer_awards_cities, bg = "gray60") %>%
  bg(i = ~ Brewery %in% ga_beer_awards_breweries,
     bg = "gray88") %>%
  bold(part = "body") %>%
  fontsize(size = 16, part = "header") %>%
  fontsize(i = ~ Medal %in% c("Bronze", "Silver", "Gold"),
           size = 15) %>%
  fontsize(j = "City", size = 14) %>%
  fontsize(j = "Brewery", size = 13) %>%
  fontsize(j = c("Beer", "Category", "Year"), size = 12) %>% 
  add_header_row(values = "Award Winning Beers in Georgia", colwidths = 6) %>% 
  fontsize(i = 1, size = 20, part = "header") %>% 
  align(align = "center", part = "header") %>% 
  bold(part = "header")

# saving as image

save_as_image(ga_beer_awards_table, path =  "C:/Users/ashly/OneDrive - Emory University/Documents/r_projects_personal/tidytuesday/2020_Week43/ga_beer_awards_table.png")
```

    ## Registered S3 method overwritten by 'webshot':
    ##   method        from    
    ##   print.webshot webshot2

    ## [1] "C:/Users/ashly/OneDrive - Emory University/Documents/r_projects_personal/tidytuesday/2020_Week43/ga_beer_awards_table.png"

![*Data comes from the Great American Beer
Festival.*](C:/Users/ashly/OneDrive%20-%20Emory%20University/Documents/r_projects_personal/tidytuesday/2020_Week43/ga_beer_awards_table.png)
\#\#\# Plotting Award Winning Breweries in Georgia
