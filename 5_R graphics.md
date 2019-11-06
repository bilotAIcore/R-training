<p align="middle"> <img src="redstudio.png"/> </p>

---

<b>Author</b>:  
<i>Lasse Ruokolainen, Bilot Consulting Oy</i>

<br>

# (5) Graphics

One great application of **R** is graphics. With some skill, one can 
generate whatever visualisation they please. Of course, this takes 
quite some practice and experimentation. Still, it is possible to 
make nice plots even without years of experience onder ones belt. 
There are basically two main approaches for making graphics: 
base-**R** and `ggplot` (the [`tidyverse`](https://www.tidyverse.org/)).

---

<br>

# Base-**R** graphics
## *Plot data.frame*
When thinking about what to visualise, plotting your entire data (given 
that it is in the form of a data.frame) can be usefull, unless the data 
is large and contains many variables.

```{r, fig.width=4, fig.height=4}
# Load data on New York Air Quality Measurements:
data(airquality)

# Plot data.frame:
plot(airquality,panel = panel.smooth,cex = 0.2)
```

<br>

## *Single plots*
Make a scatter plot with two variables only:

```{r}
# Plot tree hight against tree age:
plot(x = airquality$Temp,y = airquality$Wind,
     xlab='Temperature (°F)',
     ylab='Wind speed (mph)',
     pch=21,bg='gray80',
     main='Scatterplot')
```

Plot a frequency distribution (i.e., a histogram):

```{r}
hist(rpois(1000,10),15,main='Histogram',xlab='Value',col='wheat')
```

Make a boxplot of chick weight as a function of feed:
```{r}
data(chickwts)

# Specify custom colors:
cols = c("#999999", "#E69F00", "#56B4E9", 
         "#009E73", "#F0E442", "#0072B2")

# Draw plot:
boxplot(weight~feed,data = chickwts,
        ylab = 'Chick weight',xlab = 'Feed',
        col = cols)
```

- **Do it yourself!** Make a horizontal barplot of the weight of chicks 
fed on horsebean, ordered along increasing weight. Also, make the bars of 
different color and adjust the spacing between columns to 0.5.

```{r}
# your code comes here:
```

<br>

## *Combining different plots to same figure*
Some times it might be desirable to present several plots side by side. 
While there are several approaches for achieving this in base **R**, 
we will here use the simples alternative. This is done by altering 
figure parameters:

```{r}
# Plot two figures beside each other:
par(mfrow = c(1,2)) # change figure parameters

# left plot:
plot(airquality$Solar.R,airquality$Ozone,
     xlab='Solar radiation',ylab='Ozone (ppb)',
     pch=21,bg='dodgerblue',main='Scatterplot')

# right plot:
hist(airquality$Temp,
     main='Histogram',xlab='Temperature (°F)',col='wheat')
```

---

<br>

# Visualising with ggplot
Especially if your not into elaborate programming of graphical layout, 
a good option is to turn to `ggplot`. With `ggplot`, one can make fairly 
good looking graphs with minimal effort.

```{r, warning=F, message=FALSE}
# Load ggplot:
require(ggplot2)

# Get example data:
data(Orange)

# Plot:
p = ggplot(Orange, aes(age,circumference)) + 
      geom_jitter()
p
```

So far so good. However, the plot does not look too impressive.
As we have saveed the plot to object `p`, we can continue adding new 
layers to that object, just like in a graphical editor:

```{r,message=F,warning=FALSE}
p + geom_smooth() + theme_minimal()
```

This looks much better already.

We could also add some eye-candy according to a grouping factor in the data:

```{r,message=FALSE}
p = ggplot(Orange, aes(age,circumference,col=Tree)) + 
      geom_jitter() + 
      geom_smooth(se=F) +
      theme_minimal() +
      xlab('Tree age (days)') + 
      ylab('Circumfenrence at chest hight (mm)')
p
```

- **Do it yourself!** Make a horizontal boxplot of chick weight according to 
feed in ggplot. Make sore that the boxes are filled with different color 
depending on feed type.

```{r}
# your code comes here:
```

# Using Plotly
While `ggplot` is great, it generates static graphs, just like base-**R**. 
If one desires to add interactive components to figures, one can use either 
[`ggviz`](https://ggvis.rstudio.com/) (an implementation of an interactive 
grammar of graphics, taking the best parts of `ggplot`, combining them with 
the reactive framework of `shiny` and drawing web graphics using `vega`) or 
[`plotly`](https://plotly-r.com/)

The easiest way to use `plotly` is to convert an exaisting `ggplot` 
visualisation to `plotly`:

```{r,message=FALSE}
library(plotly)

ggplotly(p)
```

Building `plotly` graphs from scratch:
```{r,warning=FALSE}
p2 = plot_ly(mtcars, x = ~disp, y = ~mpg)
p2 = add_markers(p2, color = ~factor(cyl), size = ~gear)
p2
```