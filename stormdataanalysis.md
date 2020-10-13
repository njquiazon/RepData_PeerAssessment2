---
title: "Storm Data Analysis"
output: 
  html_document:
    keep_md: true
---
##**Synopsis**
This project was created to investigate the Storm Data made available by NOAA, and we are concerned in answering the following questions:

>1. Across the United States, which types of events are most harmful with respect to population health?
>2. Across the United States, which types of events have the greatest economic consequences?

The whole procedure consisted of 3 steps: preprocessing of data, aggregation of the data, and plotting the data to check for trends. The *Dplyr* package was used for aggregation, while the *ggplot2* package was used for plotting.

The data at hand contains quite a big amount of information, so loading and processing it as is will take quite some time. The data was reduced to columns needed, specifically *EVTYPE*, *FATALITIES*, *INJURIES*, *PROPDMG*, *PRPDMGEXP*, *CROPDMG*, and *CROPDMGEXP*. These columns correspond respectively to the event type, fatality and injury counts, estimated property and crop damages. The property and crop damages were stored in two separate columns each, in actual digits and in units, so a simple conversion algorithm was created in order make this data useful.

<conclusion>

##**Procedure**
###**Data Processing**

The procedures in this study used *dplyr* and *ggplot2* packages in *R*. Make sure these packages were already downloaded with the *install.packages()* command to avoid error prompts when rerunning the steps.


```r
#Loading necessary libraries
library(dplyr)
library(ggplot2)
```


```r
#Reading and displaying a summary of the data
data <- read.csv("repdata_data_StormData.csv.bz2")
glimpse(data)
```

```
## Rows: 902,297
## Columns: 37
## $ STATE__    <dbl> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,…
## $ BGN_DATE   <fct> 4/18/1950 0:00:00, 4/18/1950 0:00:00, 2/20/1951 0:00:…
## $ BGN_TIME   <fct> 0130, 0145, 1600, 0900, 1500, 2000, 0100, 0900, 2000,…
## $ TIME_ZONE  <fct> CST, CST, CST, CST, CST, CST, CST, CST, CST, CST, CST…
## $ COUNTY     <dbl> 97, 3, 57, 89, 43, 77, 9, 123, 125, 57, 43, 9, 73, 49…
## $ COUNTYNAME <fct> MOBILE, BALDWIN, FAYETTE, MADISON, CULLMAN, LAUDERDAL…
## $ STATE      <fct> AL, AL, AL, AL, AL, AL, AL, AL, AL, AL, AL, AL, AL, A…
## $ EVTYPE     <fct> TORNADO, TORNADO, TORNADO, TORNADO, TORNADO, TORNADO,…
## $ BGN_RANGE  <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,…
## $ BGN_AZI    <fct> , , , , , , , , , , , , , , , , , , , , , , , , 
## $ BGN_LOCATI <fct> , , , , , , , , , , , , , , , , , , , , , , , , 
## $ END_DATE   <fct> , , , , , , , , , , , , , , , , , , , , , , , , 
## $ END_TIME   <fct> , , , , , , , , , , , , , , , , , , , , , , , , 
## $ COUNTY_END <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,…
## $ COUNTYENDN <lgl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, N…
## $ END_RANGE  <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,…
## $ END_AZI    <fct> , , , , , , , , , , , , , , , , , , , , , , , , 
## $ END_LOCATI <fct> , , , , , , , , , , , , , , , , , , , , , , , , 
## $ LENGTH     <dbl> 14.0, 2.0, 0.1, 0.0, 0.0, 1.5, 1.5, 0.0, 3.3, 2.3, 1.…
## $ WIDTH      <dbl> 100, 150, 123, 100, 150, 177, 33, 33, 100, 100, 400, …
## $ F          <int> 3, 2, 2, 2, 2, 2, 2, 1, 3, 3, 1, 1, 3, 3, 3, 4, 1, 1,…
## $ MAG        <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,…
## $ FATALITIES <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 4, 0, 0,…
## $ INJURIES   <dbl> 15, 0, 2, 2, 2, 6, 1, 0, 14, 0, 3, 3, 26, 12, 6, 50, …
## $ PROPDMG    <dbl> 25.0, 2.5, 25.0, 2.5, 2.5, 2.5, 2.5, 2.5, 25.0, 25.0,…
## $ PROPDMGEXP <fct> K, K, K, K, K, K, K, K, K, K, M, M, K, K, K, K, K, K,…
## $ CROPDMG    <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,…
## $ CROPDMGEXP <fct> , , , , , , , , , , , , , , , , , , , , , , , , 
## $ WFO        <fct> , , , , , , , , , , , , , , , , , , , , , , , , 
## $ STATEOFFIC <fct> , , , , , , , , , , , , , , , , , , , , , , , , 
## $ ZONENAMES  <fct> , , , , , , , , , , , , , , , , , , , , , , , , 
## $ LATITUDE   <dbl> 3040, 3042, 3340, 3458, 3412, 3450, 3405, 3255, 3334,…
## $ LONGITUDE  <dbl> 8812, 8755, 8742, 8626, 8642, 8748, 8631, 8558, 8740,…
## $ LATITUDE_E <dbl> 3051, 0, 0, 0, 0, 0, 0, 0, 3336, 3337, 3402, 3404, 0,…
## $ LONGITUDE_ <dbl> 8806, 0, 0, 0, 0, 0, 0, 0, 8738, 8737, 8644, 8640, 0,…
## $ REMARKS    <fct> , , , , , , , , , , , , , , , , , , , , , , , , 
## $ REFNUM     <dbl> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16…
```

Subsets of the original data were taken in order to lessen the computation time. We are only interested in the columns that will give us the fatalities, injuries, property and crop damage estimates per event type. The data for getting accumulated fatalities, casualty, and property damages across the US were stored in two different data frames, namely *refdata1* and *refdata2*.


```r
#Data frame to store fatalities and injuries
refdata1 <- select(data, c(EVTYPE, FATALITIES, INJURIES))

#Data frame to store property and crop damage estimates
#The ifelse command converts the EXP codes into actual numbers
refdata2 <- data %>% 
     mutate(PROPDMGEXP = 
                 ifelse(PROPDMGEXP == "B", 1000000000,
                 ifelse(PROPDMGEXP == "M", 1000000,
                 ifelse(PROPDMGEXP == "K", 1000, 0)))) %>%
     mutate(PROPDMG = PROPDMG*PROPDMGEXP) %>%
     mutate(CROPDMGEXP = 
                 ifelse(CROPDMGEXP == "B", 1000000000,
                 ifelse(CROPDMGEXP == "M", 1000000,
                 ifelse(CROPDMGEXP == "K", 1000, 0)))) %>%
     mutate(CROPDMG = CROPDMG*CROPDMGEXP) %>%
     select(EVTYPE, PROPDMG, CROPDMG)

#Showing the summaries of the two dataframes
glimpse(refdata1)
```

```
## Rows: 902,297
## Columns: 3
## $ EVTYPE     <fct> TORNADO, TORNADO, TORNADO, TORNADO, TORNADO, TORNADO,…
## $ FATALITIES <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 1, 0, 0, 4, 0, 0,…
## $ INJURIES   <dbl> 15, 0, 2, 2, 2, 6, 1, 0, 14, 0, 3, 3, 26, 12, 6, 50, …
```

```r
glimpse(refdata2)
```

```
## Rows: 902,297
## Columns: 3
## $ EVTYPE  <fct> TORNADO, TORNADO, TORNADO, TORNADO, TORNADO, TORNADO, TO…
## $ PROPDMG <dbl> 25000, 2500, 25000, 2500, 2500, 2500, 2500, 2500, 25000,…
## $ CROPDMG <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,…
```
