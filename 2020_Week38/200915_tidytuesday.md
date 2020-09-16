Tidy Tuesday: 9/15/2020 - US Spending on Kids
================
Ashlyn Johnson
9/15/2020

## Packages

``` r
library(tidyverse)
library(tidytuesdayR)
library(viridis)
```

## Data

This week’s data comes from the Urban Institute and we will use Joshua
Rosenberg’s package, `tidykids` to access the data. The dataset provides
information on public spending on children from 1997 - 2016. The
`tidykids` package contains a cleaned and tidy version of the data.

``` r
dat <- tidytuesdayR::tt_load(2020, week = 38)
kids <- dat$kids
```

**inf\_adj** is the value adjusted for inflation and
**inf\_adj\_perchild** is the value adjusted for inflation per child.

### Initial Plots to Check Out the Data

``` r
ggplot() + geom_area(data = kids %>% filter(variable == "PK12ed"), aes(x = year, 
    y = inf_adj_perchild, fill = state), show.legend = FALSE) + scale_fill_viridis(discrete = TRUE) + 
    theme_classic() + labs(title = "Public Spending on Elementary & Secondary Schools Per Child by State", 
    subtitle = "in $1000s", xlab = "Year", ylab = "$1000s of Dollars per Child, Adjusted for Inflation") + 
    facet_wrap(~state, ncol = 5)
```

![](200915_tidytuesday_files/figure-gfm/fig1-1.png)<!-- -->
