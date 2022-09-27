README
================
Robert Dreyer
2022-09-27

``` r
library(dplyr)
```

    ## 
    ## Attaching package: 'dplyr'

    ## The following objects are masked from 'package:stats':
    ## 
    ##     filter, lag

    ## The following objects are masked from 'package:base':
    ## 
    ##     intersect, setdiff, setequal, union

``` r
#install.packages("nycflights13")
```

``` r
library(nycflights13)
library(ggplot2)
```

\#Q1: How many flights have a missing dep_time? What other variables are
missing? What might these rows represent?

``` r
no_dep <- subset(flights, is.na(flights$dep_time))
count(no_dep)
```

    ## # A tibble: 1 x 1
    ##       n
    ##   <int>
    ## 1  8255

``` r
no_dep
```

    ## # A tibble: 8,255 x 19
    ##     year month   day dep_time sched_dep_time dep_delay arr_time sched_arr_time
    ##    <int> <int> <int>    <int>          <int>     <dbl>    <int>          <int>
    ##  1  2013     1     1       NA           1630        NA       NA           1815
    ##  2  2013     1     1       NA           1935        NA       NA           2240
    ##  3  2013     1     1       NA           1500        NA       NA           1825
    ##  4  2013     1     1       NA            600        NA       NA            901
    ##  5  2013     1     2       NA           1540        NA       NA           1747
    ##  6  2013     1     2       NA           1620        NA       NA           1746
    ##  7  2013     1     2       NA           1355        NA       NA           1459
    ##  8  2013     1     2       NA           1420        NA       NA           1644
    ##  9  2013     1     2       NA           1321        NA       NA           1536
    ## 10  2013     1     2       NA           1545        NA       NA           1910
    ## # … with 8,245 more rows, and 11 more variables: arr_delay <dbl>,
    ## #   carrier <chr>, flight <int>, tailnum <chr>, origin <chr>, dest <chr>,
    ## #   air_time <dbl>, distance <dbl>, hour <dbl>, minute <dbl>, time_hour <dttm>

8255 flights are missing a departure time. dep_delay, arr_time,
arr_delay, and air_time are also missing values in these rows. These
rows might represent cancelled flights because they never departed or
arrived, meaning that the flight never took place.

\#Q2: Currently dep_time and sched_dep_time are convenient to look at,
but hard to compute with because they’re not really continuous numbers.
Convert them to a more convenient representation of number of minutes
since midnight.

``` r
minutes_flights <- flights %>% 
  mutate(flights, 
         dep_minutes = 60*as.integer(substr(as.character(dep_time), 1, nchar(dep_time)-2)) 
         + as.integer(substr(as.character(dep_time), nchar(dep_time)-1, nchar(dep_time))),
         arr_minutes = 60*as.integer(substr(as.character(arr_time), 1, nchar(arr_time)-2)) 
         + as.integer(substr(as.character(dep_time), nchar(arr_time)-1, nchar(arr_time))))
         
select(minutes_flights, dep_time, dep_minutes, arr_time, arr_minutes)
```

    ## # A tibble: 336,776 x 4
    ##    dep_time dep_minutes arr_time arr_minutes
    ##       <int>       <dbl>    <int>       <dbl>
    ##  1      517         317      830         497
    ##  2      533         333      850         513
    ##  3      542         342      923         582
    ##  4      544         344     1004         604
    ##  5      554         354      812         534
    ##  6      554         354      740         474
    ##  7      555         355      913         595
    ##  8      557         357      709         477
    ##  9      557         357      838         537
    ## 10      558         358      753         478
    ## # … with 336,766 more rows

\#Q3:Look at the number of canceled flights per day. Is there a pattern?
Is the proportion of canceled flights related to the average delay? Use
multiple dyplr operations, all on one line, concluding with
ggplot(aes(x= ,y=)) + geom_point()

``` r
flights %>% group_by(month, day) %>%
  summarise(total = n(), cancelled = sum(is.na(dep_time)), avg_delay = mean(dep_delay, na.rm = TRUE)) %>%
  mutate(cancel_prop = cancelled/total) %>%
  ggplot(aes(x = cancel_prop,y = avg_delay)) + geom_point()
```

    ## `summarise()` has grouped output by 'month'. You can override using the `.groups` argument.

![](README_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->
