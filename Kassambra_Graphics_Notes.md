# Kassambra_Graphics_Notes
Georg Maubach  
19 Februar 2017  




```r
# install.packages(pkgs = "ggplot2", dependencies = TRUE, type = "source")
library(ggplot2)
# install.packages(pkgs = "dplyr", dependencies = TRUE, type = "source")
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

# Introduction to ggplot2

## Definition of plots

**Plot = data + Aesthetics + Geometry**

- data: a data frame  
- Aesthetics:
    1. indicate the x and y variables,  
    2. control color, size, shape of points, etc.  
- Geometry: defines the type of graphic, e. g. histogram, box plot, etc.

Geometry is defined in geom_*(). Geoms are called layers cause the can occur multiple times and put on top of each other.

## Data format and preparation

- **Data must be a data frame, containing all information to make a ggplot graphic.**  
- **Data should be tidy, i. e. columns should be variables, rows should be observations.**  


```r
# mtcars
## Load data
data(mtcars)
df <- mtcars[ , c("mpg", "cyl", "wt")]

## Convert dyl to a factor variable
df$cyl <- as.factor(df$cyl)

## Print a sample of data
head(mtcars)
```

```
##                    mpg cyl disp  hp drat    wt  qsec vs am gear carb
## Mazda RX4         21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
## Mazda RX4 Wag     21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
## Datsun 710        22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
## Hornet 4 Drive    21.4   6  258 110 3.08 3.215 19.44  1  0    3    1
## Hornet Sportabout 18.7   8  360 175 3.15 3.440 17.02  0  0    3    2
## Valiant           18.1   6  225 105 2.76 3.460 20.22  1  0    3    1
```

```r
#----------------------------------------------------------
library(dplyr)

set.seed(1234)
wdata <- data.frame(
  sex = factor(rep(c("F", "M"), each = 200)),
  weight = c(rnorm(200, 55), rnorm(200, 58)))

head(wdata)
```

```
##   sex   weight
## 1   F 53.79293
## 2   F 55.27743
## 3   F 56.08444
## 4   F 52.65430
## 5   F 55.42912
## 6   F 55.50606
```

```r
mu <- wdata %>%
  group_by(sex) %>%
  summarise(grp.mean = mean(weight))

head(mu)
```

```
## # A tibble: 2 × 2
##      sex grp.mean
##   <fctr>    <dbl>
## 1      F 54.94224
## 2      M 58.07325
```

## ggplot Basics
### ggplot basic example

```r
# Basic scatter plot
ggplot(data = mtcars, aes(x = wt, y = mpg)) +
  geom_point()
```

![](Kassambra_Graphics_Notes_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

```r
# Change the font size and shape
ggplot(data = mtcars, aes(x = wt, y = mpg)) +
   geom_point(size = 1.5, shape = 18)
```

![](Kassambra_Graphics_Notes_files/figure-html/unnamed-chunk-3-2.png)<!-- -->

### aes_string()
aes_string() generates aesthetics from a string. This is particularly useful when writing functions that create plots cause strings can be used to define aesthetic mappings. Otherwise it would be needed to use a substitute to generate a call to aes().


```r
ggplot(data = mtcars, aes_string(x = "wt", y = "mpg")) +
   geom_point(size = 1.5, shape = 18)
```

![](Kassambra_Graphics_Notes_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

```r
ggpoints <- function(data, x_name, y_name)
{
  p <- ggplot(data = data, aes_string(x_name, y_name)) +
    geom_point(color = "red") +
    geom_smooth()
  
  return(p)
}

ggpoints(data = mtcars, x_name = "wt", y_name = "mpg")
```

```
## geom_smooth: method="auto" and size of largest group is <1000, so using loess. Use 'method = x' to change the smoothing method.
```

![](Kassambra_Graphics_Notes_files/figure-html/unnamed-chunk-4-2.png)<!-- -->

### Visualise transformations of original dataset
Some plots visualise a transformation on the original data set. In this case, an alternative way to build a layer is to use stat_*() functions.


```r
set.seed(1234)
wdata = data.frame(
  sex = factor(rep(c("F", "M"), each = 200)),
  weight = c(rnorm(200, 55), rnorm(200, 58)))

print("Use geometry function")
```

```
## [1] "Use geometry function"
```

```r
ggplot(wdata, aes(x = weight)) + geom_density()
```

![](Kassambra_Graphics_Notes_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

```r
print("Use stat function")
```

```
## [1] "Use stat function"
```

```r
ggplot(wdata, aes(x = weight)) + stat_density()
```

![](Kassambra_Graphics_Notes_files/figure-html/unnamed-chunk-5-2.png)<!-- -->

### Layers using geoms
Each plot consists of one or more layers. Layers are defined using geom_*().

#### Using the same data and same aesthetic mapping in one plot

```r
ggplot(data = mtcars, aes(x = wt, y = mpg)) +
  geom_point() +  # to draw points
  geom_line()     # to draw a line
```

![](Kassambra_Graphics_Notes_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

#### Using different data and mappings for different layers

```r
ggplot(data = mtcars, aes(x = wt, y = mpg)) +
  geom_point() +  # to draw points from whole data set
  geom_line(data = head(mtcars), color = "red")  # to draw a line from a subset of data
```

![](Kassambra_Graphics_Notes_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

### Calculations in aes()

```r
ggplot(data = mtcars, aes(x = log2(wt), y = log2(mpg))) +
  geom_point()
```

![](Kassambra_Graphics_Notes_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

### Saving plots
- Plots can be stored as a variable and printed using print().  
- last_plot(): returns last plot modified  
- ggsave("plot.png", width = 5, height = 5): save the last plot to the current working directory  

#### Saving directly from screen

```r
ggplot(mtcars, aes(wt, mpg)) + geom_point()
```

![](Kassambra_Graphics_Notes_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

```r
ggsave("myplot1.pdf")  # to PDF
```

```
## Saving 7 x 5 in image
```

```r
ggsave("myplot1.png")  # to PNG
```

```
## Saving 7 x 5 in image
```

#### Saveing graphic objects

```r
pdf("myplot2.pdf")
myplot <- ggplot(mtcars, aes(wt, mpg)) + geom_point()
print(myplot)
dev.off()
```

```
## png 
##   2
```

```r
png("myplot2.png")
myplot <- ggplot(mtcars, aes(wt, mpg)) + geom_point()
print(myplot)
dev.off()
```

```
## png 
##   2
```

#
