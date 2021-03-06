
# unravel

<!-- badges: start -->

[![Lifecycle:
experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://www.tidyverse.org/lifecycle/#experimental)
[![Travis build
status](https://travis-ci.org/gvdr/unravel.svg?branch=master)](https://travis-ci.org/gvdr/unravel)
<!-- badges: end -->

Hairballs, hairbalss everywhere. Most complex networks are plot in an
(arguably) awe-inspiring, questionably informative way, that we should
call “a ball of yarn” (Guðmundsdóttir B.
[Unravel](https://invidio.us/watch?v=WgBbJKiRxmc), in Homogenic. El
Cortijo, Málaga, Spain. 1997 Sept 22). A lot of color (that don’t mean
anything), a lot of node-links-overlap mess (that is hard to read into),
and a lot of force-directed graph layout algorithms that nobody could
ever explain me why should they be used a part that “hey, they are
cool\!”.

The result of this is that the graphic parameters of a network
representation are almost never meaningful. You don’t know what you are
looking at.

The same is true also for food webs, that is the domain of my research.
I know food webs (well, a little at least), I’ve been working on food
webs for the last 8 years. Yet, if you show me a food web plot I would
not “see” anything.

Here comes unravel\!

<img src="man/figures/README-logo.png" height="80">

The radical philosophy of unravel dictates that:

**all graphic elements** (e.g., size, colour, placement) **should mean
something**

This means, for example, that the vertical and horizontal position of a
node in the plot **must** correspond to something. This, requires domain
knowledge (and that’s the reason why I started with food webs, but I
hope somebody else will help me with other kind of networks).

This also means that if something does not mean anything, it **must
not** be used. So, if you can assign a vertical / horizontal position to
your nodes based on something you know about the nodes, think twice: do
you *reallY* want to plot the network as a hairball? What information
will the reader get from your plot? Isn’t there a better way of plotting
it?

## Installation

unravel is not yet on CRAN. You can install it from GitHub with ease:

``` r
remotes::install_github("gvdr/unravel")
```

A word about GitHub. GitHub has systematically banned people coming from
or living in certain countries: that sucks, and I’m looking for a better
place where to transfer the package.

## Example

The main high level plotting function produces a specialised, domain
aware, and opinionated version of a `ggraph` plot. That means that (1)
it makes decision and (2) if you don’t make decision, `unravel` will
make decision for you.

``` r
library(unravel)
library(dplyr)
#> 
#> Attaching package: 'dplyr'
#> The following objects are masked from 'package:stats':
#> 
#>     filter, lag
#> The following objects are masked from 'package:base':
#> 
#>     intersect, setdiff, setequal, union
## basic example code

data("TuesdayLakes_1984")

TuesdayLakes_1984 %>%
  plot_foodweb()
```

<img src="man/figures/README-example-1.png" width="100%" />

Additional graphical elements can be addedd using the `ggraph` (and
`ggplot2`) API.

``` r
library(viridis)
#> Loading required package: viridisLite
library(ggraph)
#> Loading required package: ggplot2


TuesdayLakes_1984 %>%
  plot_foodweb() +
  ggtitle("Tuesday Lake 1984") +
  scale_colour_viridis()
```

<img src="man/figures/README-unnamed-chunk-2-1.png" width="100%" />

The underlying, more flexible, function is `ggfoodweb()`. It requires
both a foodweb and a layout (that is, a matrix with two columns,
specifying the x and y position of the specis in the plot).
`ggfoodweb()` does not compute the layer for you, nor provides colour
and size for the nodes. You can use `tl_oi_layer()` to compute a layout,
and `fw_node_{size,colour}()` to define size and colour.

In the following example, we change size and colour with two different
centralities, and place the species on the x axis following the order in
which they are written in the network (this is usually a very BAD
choice).

``` r
default_layer <- tl_oi_layer(TuesdayLakes_1984, dodge = 0.04)

# we change the first column (horizontal position) to the row number of species
default_layer[,1] <- 1:nrow(default_layer) 


TuesdayLakes_1984 %>%
  fw_node_colour(tidygraph::centrality_eigen) %>%
  fw_node_size(tidygraph::centrality_authority) %>%
  ggfoodweb(default_layer) +
  # with ggfoodweb() we need to define the axis and guides labels by hand
  scale_x_continuous(name = "Omnivory Index") +
  scale_y_continuous(name = "Trophic Level") +
  guides(size = guide_legend(title="Authority"),
         colour = guide_colourbar(title="Eigen Centr")) +
  ggtitle("Tuesday Lake 1984") +
  scale_colour_viridis()
```

<img src="man/figures/README-cars-1.png" width="100%" />

### Code of Conduct and Contribution

Please note that the ‘unravel’ project is released with a [Contributor
Code of Conduct](CODE_OF_CONDUCT.md). By contributing to this project,
you agree to abide by its terms.
