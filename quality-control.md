Quality Control Project
================
309657009 邱泓儒

``` r
library(qcc)
setwd("C:/Users/ray98/Desktop/project/class/qc")
dt <- read.csv("water_potability.csv")
dt <- dt[1:2000,]
dt <- dt[sample(2000),]
```

## Data cleaning

``` r
# use the Turbidity variable
tur <- dt$Turbidity[1:2000]

# check if there is NA
which(is.na(dt$Turbidity))
```

    ## integer(0)

``` r
# check the assumptions for control chart
hist(tur, breaks = 20, main = "Histogram of Turbidity", xlab = "Turbidity")
```

![](quality-control_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

``` r
shapiro.test(tur)
```

    ## 
    ##  Shapiro-Wilk normality test
    ## 
    ## data:  tur
    ## W = 0.99949, p-value = 0.9036

``` r
par(mfrow=c(1,3))
plot(dt$Hardness[1:2000], dt$Turbidity[1:2000], pch = 20, cex = 0.5, ylab = "Turbidity", xlab = "Hardness")
plot(dt$Solids[1:2000], dt$Turbidity[1:2000], pch = 20, cex = 0.5, ylab = "Turbidity", xlab = "Solids")
plot(dt$ph[1:2000], dt$Turbidity[1:2000], pch = 20, cex = 0.5, ylab = "Turbidity", xlab = "ph")
```

![](quality-control_files/figure-gfm/unnamed-chunk-2-2.png)<!-- -->

## X bar-S chart

``` r
tur <- matrix(dt$Turbidity[1:2000], ncol = 5)
par(mfrow=c(1,1))
obj <- qcc(tur[1:50,], type="xbar", newdata=tur[51:400,], std.dev = "UWAVE-SD")
```

![](quality-control_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

``` r
obj <- qcc(tur[1:50,], type="S", newdata=tur[51:400,])
```

![](quality-control_files/figure-gfm/unnamed-chunk-3-2.png)<!-- -->

## X bar-R chart

``` r
obj <- qcc(tur[1:50,], type="xbar", newdata=tur[51:400,], std.dev = "UWAVE-R")
```

![](quality-control_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

``` r
obj <- qcc(tur[1:50,], type="R", newdata=tur[51:400,])
```

![](quality-control_files/figure-gfm/unnamed-chunk-4-2.png)<!-- -->

## p chart

``` r
# use the variable Potability to construct p chart
pot <- matrix(dt$Potability[1:2000], ncol = 20)
pot <- as.data.frame(pot)
pot$defect <- 20-rowSums(pot)
pot$size <- 20
with(pot, qcc(pot$defect[1:20], pot$size[1:20], type = "p", newdata=pot$defect[21:100], newsizes=pot$size[21:100]))
```

![](quality-control_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

    ## List of 15
    ##  $ call        : language qcc(data = pot$defect[1:20], type = "p", sizes = pot$size[1:20], newdata = pot$defect[21:100],      newsizes = pot$size[21:100])
    ##  $ type        : chr "p"
    ##  $ data.name   : chr "pot$defect[1:20]"
    ##  $ data        : num [1:20, 1] 14 14 12 13 8 13 17 12 11 15 ...
    ##   ..- attr(*, "dimnames")=List of 2
    ##  $ statistics  : Named num [1:20] 0.7 0.7 0.6 0.65 0.4 0.65 0.85 0.6 0.55 0.75 ...
    ##   ..- attr(*, "names")= chr [1:20] "1" "2" "3" "4" ...
    ##  $ sizes       : num [1:20] 20 20 20 20 20 20 20 20 20 20 ...
    ##  $ center      : num 0.608
    ##  $ std.dev     : num 0.488
    ##  $ newstats    : Named num [1:80] 0.6 0.7 0.7 0.6 0.6 0.85 0.8 0.4 0.65 0.4 ...
    ##   ..- attr(*, "names")= chr [1:80] "21" "22" "23" "24" ...
    ##  $ newdata     : num [1:80, 1] 12 14 14 12 12 17 16 8 13 8 ...
    ##  $ newsizes    : num [1:80] 20 20 20 20 20 20 20 20 20 20 ...
    ##  $ newdata.name: chr "pot$defect[21:100]"
    ##  $ nsigmas     : num 3
    ##  $ limits      : num [1:100, 1:2] 0.28 0.28 0.28 0.28 0.28 ...
    ##   ..- attr(*, "dimnames")=List of 2
    ##  $ violations  :List of 2
    ##  - attr(*, "class")= chr "qcc"

## CUSUM chart

``` r
q <- cusum(tur[1:50,], newdata=tur[51:400,], se.shift = 1)
```

![](quality-control_files/figure-gfm/unnamed-chunk-6-1.png)<!-- -->

## EWMA chart

``` r
q <- ewma(tur[1:50,], lambda=0.2, nsigmas=3, newdata=tur[51:400,]) 
```

![](quality-control_files/figure-gfm/unnamed-chunk-7-1.png)<!-- -->
