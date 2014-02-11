Wood residues
========================================================

Load data and program libraries
-------------------------------
Load libraries Run install.packages(c("ggplot2", "FAOSTAT") if not available.

```r
library(ggplot2)
library(FAOSTAT)
opts_knit$set(root.dir = '../..') # Change to project root directory
```


Load data from FAOSTAT

```r
source("code/func.R")
FAO$itemname(1620)
FAO$elementTable
residues <- FAO$download(1620, 5516, 5616, 5622, 5916, 5922)
```


Load same data from local file

```r
load("rawdata/residues.RDATA")
```


Subset data for Europe

```r
europe <- subset(FAOregionProfile, UNSD_MACRO_REG=="Europe")
rsd <- subset(residues$entity, FAOST_CODE %in% europe$FAOST_CODE)
```


### Wood residues production in Europe (same scale)

```r
ggplot(data=subset(rsd,  Year>1980 )) + 
    aes(x=Year, y=Production) + geom_line() + facet_wrap(~Country) 
```

```
## geom_path: Each group consist of only one observation. Do you need to adjust the group aesthetic?
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5.png) 



### Wood residues production in Europe (varying scale)

```r
# remove NA values otherwise ggplot complains.
rsd$Production[is.na(rsd$Production)] <- 0
ggplot(data=subset(rsd,  Year>1980 )) + 
    aes(x=Year, y=Production) + geom_line() + facet_wrap(~Country, scales = "free_y") 
```

```
## geom_path: Each group consist of only one observation. Do you need to adjust the group aesthetic?
```

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6.png) 

