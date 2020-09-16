Tidy Tuesday: 9/15/2020 - US Spending on Kids
================
Ashlyn Johnson
9/15/2020

## Packages

``` r
library(tidyverse)
library(tidytuesdayR)
library(viridis)
library(urbnmapr)
library(gganimate)
library(transformr)
library(ggthemes)
library(gifski)
```

## Data

This week’s data comes from the Urban Institute and we will use Joshua
Rosenberg’s package, `tidykids` to access the data. The dataset provides
information on public spending on children from 1997 - 2016. The
`tidykids` package contains a cleaned and tidy version of the data.

``` r
dat <- tidytuesdayR::tt_load(2020, week = 38)
kids <- dat$kids %>% 
  rename(state_name = state)
```

**inf\_adj** is the value adjusted for inflation and
**inf\_adj\_perchild** is the value adjusted for inflation per child.

### Initial Plots to Check Out the Data

``` r
ggplot() +
  geom_area(
    data = kids %>%
      filter(variable == "PK12ed"),
    aes(x = year, y = inf_adj_perchild, fill = state_name),
    show.legend = FALSE
  ) +
  scale_fill_viridis(discrete = TRUE) +
  theme_classic() +
  labs(title = "Public Spending on Elementary & Secondary Schools Per Child by State", subtitle = "in $1000s", xlab = "Year", ylab = "$1000s of Dollars per Child, Adjusted for Inflation") +
  facet_wrap(~state_name, ncol = 5)
```

![](200915_tidytuesday_files/figure-gfm/fig1-1.png)<!-- -->

Looking at this initial plot, I am curious to see this data in a more
concise way. Can I plot this data onto a map of the US?

### US Map Based Plots

This is a static plot for the year 2000.

``` r
states_sf <- get_urbn_map("states", sf = TRUE)


kids_spatial_2000 <- full_join(states_sf,
  kids %>%
    filter(variable == "PK12ed", year == 2000),
  by = "state_name"
)

kids_spatial_2000 %>%
  ggplot() +
  geom_sf(
    mapping = aes(fill = inf_adj_perchild),
    color = "black", size = 0.25
  ) +
  scale_fill_viridis(discrete = FALSE) +
  geom_sf_text(
    data = get_urbn_labels(map = "states", sf = TRUE),
    aes(label = state_abbv), color = "black",
    size = 5
  ) +
  labs(
    fill = "thousand dollars",
    title = "Public Spending on Elementary and Secondary Schools: 2000",
    subtitle = "in thousands of dollars per child, adjusted for inflation",
    caption = "Source: Urban Insitute \n Viz: @ashgjoh"
  ) +
  theme_map() +
  theme(
    plot.title = element_text(size = 20, face = "bold"),
    plot.subtitle = element_text(size = 16),
    plot.caption = element_text(size = 16),
    legend.title = element_text(size = 14, face = "bold"),
    legend.text = element_text(size = 10),
    legend.key.size = unit(20, "pt"),
    legend.position = "top",
    panel.background = element_rect(fill = "gray", colour = "gray"),
    plot.background = element_rect(fill = "gray", colour = "gray"),
    legend.background = element_rect(fill = "gray", color = "gray")
  )
```

![](200915_tidytuesday_files/figure-gfm/fig2-1.png)<!-- -->

This is an animated plots to show all of the years.

``` r
kids_spatial <- full_join(states_sf,
  kids %>%
    filter(variable == "PK12ed"),
  by = "state_name"
)

plot <- kids_spatial %>%
  ggplot() +
  geom_sf(
    mapping = aes(fill = inf_adj_perchild),
    color = "black", size = 0.25
  ) +
  scale_fill_viridis(discrete = FALSE) +
  geom_sf_text(
    data = get_urbn_labels(map = "states", sf = TRUE),
    aes(label = state_abbv), color = "black",
    size = 15
  ) +
  labs(
    fill = "thousand dollars",
    subtitle = "in thousands of dollars per child, adjusted for inflation",
    caption = "Source: Urban Insitute \n Viz: @ashgjoh"
  ) +
  theme_map() +
  theme(
    plot.title = element_text(size = 50, face = "bold"),
    plot.subtitle = element_text(size = 40),
    plot.caption = element_text(size = 40),
    legend.title = element_text(size = 30, face = "bold"),
    legend.text = element_text(size = 25),
    legend.key.size = unit(60, "pt"),
    legend.position = "top",
    panel.background = element_rect(fill = "gray", colour = "gray"),
    plot.background = element_rect(fill = "gray", colour = "gray"),
    legend.background = element_rect(fill = "gray", color = "gray")
  )


plot_animated <- plot +
  ggtitle("Public Spending on Elementary and Secondary Schools: {frame_time}") +
  transition_time(as.integer(year)) +
  ease_aes("linear") +
  enter_fade() +
  exit_fade()


animate(plot_animated, renderer = gifski_renderer(), width = 1000, height = 666, res = 35)
```

![](200915_tidytuesday_files/figure-gfm/animated%20plot-1.gif)<!-- -->

``` r
anim_save("kids_school_spending.gif")
```

One thing that I’m noticing is that DC seems to overall spend far more
money per child than the other states. This vast difference may make it
more difficult to notice differences in spending between the other
states. So for this next plot, I’m going to take DC out of the data.

``` r
kids_spatial_noDC <- kids_spatial %>%
  filter(state_name != "District of Columbia")

plot_noDC <- kids_spatial_noDC %>%
  ggplot() +
  geom_sf(
    mapping = aes(fill = inf_adj_perchild),
    color = "black", size = 0.25
  ) +
  scale_fill_viridis(discrete = FALSE) +
  geom_sf_text(
    data = get_urbn_labels(map = "states", sf = TRUE),
    aes(label = state_abbv), color = "black",
    size = 15
  ) +
  labs(
    fill = "thousand dollars",
    subtitle = "in thousands of dollars per child, adjusted for inflation \n (does not include District of Columbia)",
    caption = "Source: Urban Insitute \n Viz: @ashgjoh"
  ) +
  theme_map() +
  theme(
    plot.title = element_text(size = 50, face = "bold"),
    plot.subtitle = element_text(size = 40),
    plot.caption = element_text(size = 40),
    legend.title = element_text(size = 30, face = "bold"),
    legend.text = element_text(size = 25),
    legend.key.size = unit(60, "pt"),
    legend.position = "top",
    panel.background = element_rect(fill = "gray", colour = "gray"),
    plot.background = element_rect(fill = "gray", colour = "gray"),
    legend.background = element_rect(fill = "gray", color = "gray")
  )


plot_animated_noDC <- plot_noDC +
  ggtitle("Public Spending on Elementary and Secondary Schools: {frame_time}") +
  transition_time(as.integer(year)) +
  ease_aes("linear") +
  enter_fade() +
  exit_fade()

animate(plot_animated_noDC, renderer = gifski_renderer(), width = 1000, height = 666, res = 35)
```

![](200915_tidytuesday_files/figure-gfm/animated%20plot%20without%20DC-1.gif)<!-- -->

``` r
anim_save("kids_school_spending_noDC.gif")
```
