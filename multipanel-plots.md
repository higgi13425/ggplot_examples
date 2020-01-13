Multipanel\_with\_Tables
================

### reference source

<http://www.sthda.com/english/articles/24-ggpubr-publication-ready-plots/81-ggplot2-easy-way-to-mix-multiple-graphs-on-the-same-page/>

## Create example plots

``` r
# Box plot (bp)
bxp <- ggboxplot(ToothGrowth, x = "dose", y = "len",
                 color = "dose", palette = "jco")
bxp
```

![](multipanel-plots_files/figure-gfm/unnamed-chunk-1-1.png)<!-- -->

``` r
# Dot plot (dp)
dp <- ggdotplot(ToothGrowth, x = "dose", y = "len",
                 color = "dose", palette = "jco", binwidth = 1)
dp
```

![](multipanel-plots_files/figure-gfm/unnamed-chunk-1-2.png)<!-- -->

``` r
bp <- ggbarplot(ToothGrowth,x = "len", y = "dose",
                 color = "dose", palette = "jco")

# Scatter plots (sp)
sp <- ggscatter(mtcars, x = "wt", y = "mpg",
                add = "reg.line",               # Add regression line
                conf.int = TRUE,                palette = "jco"
                )+
  stat_cor(aes(color = cyl), label.x = 3)       # Add correlation coefficient
sp
```

![](multipanel-plots_files/figure-gfm/unnamed-chunk-1-3.png)<!-- -->

## Survival example plots

``` r
# Fit survival curves
fit <- survfit( Surv(time, status) ~ adhere, data = colon )

# Plot survival curves
ggsurv <- ggsurvplot(fit, data = colon, 
                     palette = "jco",                              # jco palette
                     pval = TRUE, pval.coord = c(500, 0.4),        # Add p-value
                     risk.table = TRUE                            # Add risk table
                     )
names(ggsurv)
```

    ## [1] "plot"           "table"          "data.survplot"  "data.survtable"

## Arrange survival plot with table

``` r
ggarrange(ggsurv$plot, ggsurv$table, heights = c(2, 0.7),
          ncol = 1, nrow = 2, align = "v")
```

![](multipanel-plots_files/figure-gfm/cars-1.png)<!-- -->

## Arrange with Table

You can also arrange plots with tables, for example:

![](multipanel-plots_files/figure-gfm/pressure-1.png)<!-- -->

## Table inside of plot

``` r
density.p + annotation_custom(ggplotGrob(stable.p),
                              xmin = 5.5, ymin = 0.7,
                              xmax = 8)
```

![](multipanel-plots_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

## add boxplot to a ggplot

``` r
# Scatter plot colored by groups ("Species")
#::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
sp <- ggscatter(iris, x = "Sepal.Length", y = "Sepal.Width",
                color = "Species", palette = "jco",
                size = 3, alpha = 0.6)
# Create box plots of x/y variables
#::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
# Box plot of the x variable
xbp <- ggboxplot(iris$Sepal.Length, width = 0.3, fill = "lightgray") +
  rotate() +
  theme_transparent()
# Box plot of the y variable
ybp <- ggboxplot(iris$Sepal.Width, width = 0.3, fill = "lightgray") +
  theme_transparent()
# Create the external graphical objects
# called a "grob" in Grid terminology
xbp_grob <- ggplotGrob(xbp)
ybp_grob <- ggplotGrob(ybp)
# Place box plots inside the scatter plot
#::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
xmin <- min(iris$Sepal.Length); xmax <- max(iris$Sepal.Length)
ymin <- min(iris$Sepal.Width); ymax <- max(iris$Sepal.Width)
yoffset <- (1/15)*ymax; xoffset <- (1/15)*xmax
# Insert xbp_grob inside the scatter plot
sp + annotation_custom(grob = xbp_grob, xmin = xmin, xmax = xmax, 
                       ymin = ymin-yoffset, ymax = ymin+yoffset) +
  # Insert ybp_grob inside the scatter plot
  annotation_custom(grob = ybp_grob,
                       xmin = xmin-xoffset, xmax = xmin+xoffset, 
                       ymin = ymin, ymax = ymax)
```

![](multipanel-plots_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

## Background image in a ggplot

``` r
mypngfile <- download.file("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e4/France_Flag_Map.svg/612px-France_Flag_Map.svg.png", 
                           destfile = "france.png", mode = 'wb') 
img <- png::readPNG('france.png') 
ggplot(iris, aes(x = Sepal.Length, y = Sepal.Width)) +
  background_image(img)+
  geom_point(aes(color = Species), alpha = 0.6, size = 5)+
  color_palette("jco")+
  theme(legend.position = "top")
```

![](multipanel-plots_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

## Nested layout

``` r
p1 <- ggarrange(sp, bxp, ncol = 1, nrow = 2)
p2 <- ggarrange(density.p, stable.p, text.p, 
                ncol = 1, nrow = 3,
                heights = c(1, 0.5, 0.3))
ggarrange(p1, p2, ncol = 2, nrow = 1)
```

![](multipanel-plots_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->
