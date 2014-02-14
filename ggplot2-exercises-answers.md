# An introduction to the ggplot2 package

Exercises written by Sean C. Anderson <sean "at" seananderson.ca>.

For a UW FISH 544 self-study workshop in February 2014.

This is an R Markdown document. Install the knitr package to work with it.
See <http://www.rstudio.com/ide/docs/authoring/using_markdown> for more details.

Once knitr is installed, you can "knit" the document by clicking the "knit" button in RStudio, or running `knitr::knit2html("ggplot2-exercises.Rmd")` in an R console.







First, load ggplot2:


```S
library(ggplot2)
```


## Loading the data

We're going to work with morphological data from Galapagos finches, which is available from BIRDD: Beagle Investigation Return with Darwinian Data at <http://bioquest.org/birdd/morph.php>. It is originally from Sato et al. 2000 Mol. Biol. Evol. <http://mbe.oxfordjournals.org/content/18/3/299.full>.

I've taken the data and cleaned it up a bit for this exercise. I've removed some columns and made the column names lower case. I've also removed all but one island. You can do that with this code:


```S
morph <- read.csv("Morph_for_Sato.csv")
names(morph) <- tolower(names(morph)) # make columns names lowercase
morph <- subset(morph, islandid == "Flor_Chrl") # take only one island
morph <- morph[,c("taxonorig", "sex", "wingl", "beakh", "ubeakl")] # only keep these columns
names(morph)[1] <- "taxon"
morph <- data.frame(na.omit(morph)) # remove all rows with any NAs to make this simple
morph$taxon <- factor(morph$taxon) # remove extra remaining factor levels
morph$sex <- factor(morph$sex) # remove extra remaining factor levels
row.names(morph) <- NULL # tidy up the row names
```


Take a look at the data. There are columns for taxon, sex, wing length, beak height, and upper beak length:


```S
head(morph)
str(morph)
```


## Part 1: geoms

First, read the included notes from the introduction to the end of the section on geoms.
Then, open the ggplot2 web documentation <http://docs.ggplot2.org/> and keep it open to refer back to it through these exercises.

First, let's experiment with some geoms using the `morph` dataset. I'll start by setting up a basic scatterplot of beak height vs. wing length:


```S
ggplot(morph, aes(wingl, beakh)) + geom_point(alpha = 0.4)
```

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6.png) 


Because there's lots of overplotting, I've set the alpha (opacity) value to 40%. Alternatively, we could have added some jittering. We'll do that below.

Experiment with ggplot2 geoms. Try applying at least 3 different geoms to the `morph` dataset. For example, try showing the distribution of wing length with `geom_histogram()` and `geom_density()`. You could also try showing the distribution of wing length for male and female birds by using `geom_violin()`. Remember to consult <http://docs.ggplot2.org/>.


```S
ggplot(morph, aes(wingl)) + geom_histogram(binwidth=1)
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-71.png) 

```S
ggplot(morph, aes(wingl)) + geom_density()
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-72.png) 

```S
ggplot(morph, aes(sex, wingl)) + geom_violin()
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-73.png) 

```S
ggplot(morph, aes(taxon, wingl)) + geom_violin() + coord_flip() # coord_flip() rotates 90 degrees
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-74.png) 

```S
ggplot(morph, aes(taxon, wingl)) + geom_boxplot() + coord_flip()
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-75.png) 


## Part 2: aesthetics

Start by reading the section on aesthetics in the included notes.

Let's play with mapping some of our data to aesthetics. I'll start with one example. I'm going to map the male/female value to a colour in our scatterplot of wing length and beak height. This time I'll use jittering instead of transparency to deal with overplotting:


```S
ggplot(morph, aes(wingl, beakh)) +
  geom_point(aes(colour = sex),
    position = position_jitter(width = 0.3, height = 0))
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8.png) 


Explore the `morph` dataset yourself by applying some aesthetics. You can see all the available aesthetics for a give geom by looking at the documentation. Either see the website or, for example, `?geom_point`

Some suggestions:

- try the same scatterplot but show upper beak length (`ubeakl`) with size
- try the same scatterplot but show the taxon with colour
- try the same scatterplot but show the sex with a different shape
- combine all these: colour for taxon, shape for sex, and size for upper beak length

Yes, this last version is a bit silly, but it illustrates how quickly you can explore multiple dimensions with ggplot2.


```S
ggplot(morph, aes(wingl, beakh)) +
  geom_point(aes(size = ubeakl), alpha = 0.4)
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9.png) 



```S
ggplot(morph, aes(wingl, beakh)) +
  geom_point(aes(colour = taxon),
    position = position_jitter(width = 0.3, height = 0))
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10.png) 



```S
ggplot(morph, aes(wingl, beakh)) +
  geom_point(aes(shape = sex),
    position = position_jitter(width = 0.3, height = 0))
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11.png) 

```S
ggplot(morph, aes(wingl, beakh)) +
  geom_point(aes(shape = sex, size = ubeakl, colour = taxon),
    alpha = 0.4)
```

![plot of chunk unnamed-chunk-12](figure/unnamed-chunk-12.png) 


## Part 3: facets (small multiples)

Read the notes section on small multiples.

Try a scatterplot of beak height against wing length with a different panel for each taxon. Use `facet_wrap`:


```S
ggplot(morph, aes(wingl, beakh)) +
  geom_point(alpha = 0.4) + facet_wrap(~taxon)
```

![plot of chunk unnamed-chunk-13](figure/unnamed-chunk-13.png) 


In some cases, it's useful to let the x or y axes have different scales for each panel. Try giving each panel a different axis here using `scales = "free"` in your call to `facet_wrap()`:


```S
ggplot(morph, aes(wingl, beakh)) +
  geom_point(alpha = 0.4) +
  facet_wrap(~taxon, scales = "free")
```

![plot of chunk unnamed-chunk-14](figure/unnamed-chunk-14.png) 


Now try using `facet_grid` to explore the same scatterplot for each combination of sex and taxa. (Remove the `scales = "free"` code for simplicity.)


```S
ggplot(morph, aes(wingl, beakh)) +
  geom_point(alpha = 0.4) + facet_grid(sex~taxon)
```

![plot of chunk unnamed-chunk-15](figure/unnamed-chunk-15.png) 


As another example, let's look at the distribution of wing length by sex with different panels for each taxa. Use a boxplot or violin plot to show the distributions.


```S
ggplot(morph, aes(sex, wingl)) + geom_violin() +
  facet_wrap(~taxon)
```

![plot of chunk unnamed-chunk-16](figure/unnamed-chunk-16.png) 


## Part 4: customizing ggplot2

So far we've used the default settings. Let's try applying a theme and adjust some of the annotation of our plots.

Read the notes from the section on themes until the end.

Let's start by applying the black and white theme, `theme_bw()`. Try adding that on to the end of a plot showing the distribution of wing length by sex using `geom_violin()`. Save your plot to an object named `p` and `print()` it to show it. We'll re-use this plot in the next step.


```S
p <- ggplot(morph, aes(sex, wingl)) + geom_violin() + theme_bw()
print(p)
```

![plot of chunk unnamed-chunk-17](figure/unnamed-chunk-17.png) 


Let's go one step further and remove all grid lines. See the included notes and the help for `?theme`. Hint: setting an argument to `element_blank()` will remove it. Add these elements to the object `p` and print `p` again. Hint: see the note "Exploiting the object-oriented nature of ggplot2" in the "Random tips" section of the notes.


```S
p <- p + theme(panel.grid.major = element_blank(),
  panel.grid.minor = element_blank())
print(p)
```

![plot of chunk unnamed-chunk-18](figure/unnamed-chunk-18.png) 


And now let's set the x and y axis labels ourselves. Name them something more appropriate.


```S
p <- p + xlab("Sex") + ylab("Wing length")
print(p)
```

![plot of chunk unnamed-chunk-19](figure/unnamed-chunk-19.png) 


Use the function `ggsave()` to save your plot to a PDF file.


```S
ggsave(p, file = "wingl-violin.pdf", width = 6, height = 6)
```


## More examples

### Dot and line plots

ggplot2 can be useful for quickly making dot and line plots. For example, this is useful for coefficient plots. To illustrate how to make this style of plot, let's make a shows a dot for the median and line segment for the quantiles. First, we'll calculate these values. Run the following code:


```S
# install.packages("dplyr")
library(dplyr)
morph_quant <- as.data.frame(summarise(group_by(morph, taxon),
  l = quantile(wingl, 0.25)[[1]],
  m = median(wingl),
  u = quantile(wingl, 0.75)[[1]]))
# order taxa factor levels by the median for plotting:
morph_quant <- transform(morph_quant,
  taxon = reorder(taxon, m, function(x) x)) # see ?reorder
```


Now make the plot. The final plot should have the taxa listed down the y-axis and the wing length value on the x axis. Use the geom `geom_pointrange()`. Because this geom only works for vertical line segments, you'll need to rotate the whole plot by adding `+ coord_flip()`. So, `ymax` and `ymin` refer to the maximum and minimum line segment values and `x` to the taxa, even though they will appear rotated in the end.


```S
ggplot(morph_quant, aes(x = taxon, y = m, ymin = l, ymax = u)) +
  geom_pointrange() + coord_flip() +
  ylab("Wing length") + xlab("")
```

![plot of chunk unnamed-chunk-22](figure/unnamed-chunk-22.png) 


### Adding model fits to the data

ggplot2 can add model fits to the data to help visualize patterns. For example, it can quickly add linear regression lines, GLMs, GAMs, and loess curves. Let's add loess curves to scatter plots of beak height and wing length with a panel for male and female. See `?stat_smooth`


```S
ggplot(morph, aes(wingl, beakh)) +
  geom_point(alpha = 0.4) + facet_wrap(~sex) +
  stat_smooth(method = "loess")
```

![plot of chunk unnamed-chunk-23](figure/unnamed-chunk-23.png) 


You can add any model fit by specifying the function and the formula (see the examples in `?stat_smooth`). Alternatively, you can fit your own model, predict from it, and add the data with `geom_ribbon()`.