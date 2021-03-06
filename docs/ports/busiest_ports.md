World's busiest 50 ports
========================================================

Data downloaded from [List of world's busiest container ports](http://en.wikipedia.org/wiki/List_of_world%27s_busiest_container_ports) - 
[original source](http://www.joc.com/port-news/joc-top-50-world-container-ports_20130815.html).



```r
library(reshape2)
library(plyr)
library(ggplot2)
setwd("../..")
ports <- read.csv("rawdata/world_busiest_50_ports_container_traffic.csv")
names(ports) <- tolower(names(ports))


#reshape table
ports <- melt(ports, id=c("rank", "port","country"))
ports$year <- as.numeric(substring(ports$variable,2))

# Aggregate data by country
# remove 2012 as data is missing for most countries
ports_country <- ddply(ports[ports$year<2012,], .(country,year), 
                      summarise, value=sum(value,na.rm=TRUE))
ports_country <- arrange(ports_country, year, -value)
country_rank <- ports_country$country[ports_country$year==2011]
ports$country <- ordered(ports$country, levels=country_rank)
ports_country$country <- ordered(ports_country$country, 
                                 levels=country_rank)
```



## Traffic by port, by year.

```r
# color by country
ggplot(data=ports, aes(x=year, y=value, color=country)) +
    geom_point() +
    geom_point(data=subset(ports,
                           country %in% c("China", "Singapore", "United States")), aes(shape=country, size=5)) +
     scale_shape_manual(values = c(3,2,7)) +
    ylab("Transport volume in thousand twenty-foot equivalent units")
```

```
## Warning: Removed 59 rows containing missing values (geom_point).
## Warning: Removed 20 rows containing missing values (geom_point).
```

![plot of chunk unnamed-chunk-1](figure/unnamed-chunk-1.png) 



## Traffic in those 50 ports by country, by year

```r
ggplot(data=ports_country, aes(x=year, y=value, color=country)) +
    geom_line() +
    ylab("Transport volume in thousand twenty-foot equivalent units")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2.png) 

Consider using this summary function directly on ports, in the plot
```
stat_summary(fun.y="sum", geom="point") 
``` 
Inspired by [this genetic blog](http://pathogenomics.bham.ac.uk/blog/2013/04/sequencing-instruments-by-number/)


```r
# color by country
ggplot(data=ports, aes(x=year, y=value, color=country)) +
    stat_summary(fun.y="sum", geom="line") +
    ylab("Transport volume in thousand twenty-foot equivalent units")
```

```
## Warning: Removed 59 rows containing missing values (stat_summary).
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3.png) 




# Paul comments
Todo change port to a vector according to port ranking
Todo change country to a vector according to Country ranking
```
ports$country <- factor(ports$country,labels=ports$country, ordered=TRUE)
```
