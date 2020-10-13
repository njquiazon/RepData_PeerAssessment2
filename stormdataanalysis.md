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

The whole procedure consisted of the following steps: preprocessing of data, aggregation of the data, and plotting the data to check for trends. The *dplyr* package was used for cleaning and aggregation, while the *ggplot2* package was used for visualization.

The data at hand contains quite a big amount of information, so loading and processing it as is will take quite some time. To resolve this, the data was reduced to include only the columns that were needed, specifically *EVTYPE*, *FATALITIES*, *INJURIES*, *PROPDMG*, *PRPDMGEXP*, *CROPDMG*, and *CROPDMGEXP*, corresponding respectively to the event type, fatality and injury counts, estimated property and crop damages. The property and crop damages were stored in two separate columns each, in actual digits and in units, so a simple conversion algorithm was created in order make this data useful. Then, empty rows were removed, and event types were regrouped in order to catch misspelled event types that were falsely listed separately.

<conclusion>

##**Data Processing**

The procedures in this study used *dplyr* and *ggplot2* packages in *R*. Make sure these packages were already downloaded with the *install.packages()* command to avoid error prompts when rerunning the steps.


```r
#Loading necessary libraries
library(dplyr)
library(ggplot2)
```


```r
#Reading and displaying a summary of the data
data <- read.csv("repdata_data_StormData.csv.bz2")
```

A subset of the original data was taken in order to lessen the computation time. We are only interested in the columns that will give us the fatalities, injuries, property and crop damage estimates per event type. The data for getting accumulated fatalities, casualty, and property damages across the US were stored a data frame *refdata*.


```r
#Data frame to store event type, fatalities, injuries, crop and property damage estimates
#The ifelse command converts the EXP codes into actual numbers
refdata <- data %>% 
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
     select(EVTYPE, FATALITIES, INJURIES, PROPDMG, CROPDMG)
```

*refdata* was further cleaned by removing rows that give no additional value (rows where all columns are zero), and renaming misspelled *EVTYPE* inputs. Because there were various identifiers given to the same event types, the cleaning process also involved grouping the events into umbrella names that captured such events into one identifier. Following the *Storm Data Event Table*, table 2.1.1 on page 6 of the [NWS Storm Data Documentation](https://d396qusza40orc.cloudfront.net/repdata%2Fpeer2_doc%2Fpd01016005curr.pdf), *EVTYPE* variable was reduced to a factor with 48 levels. The levels and their corresponding values in the *refdata* table were:

<Put 48 levels here>


```r
#Removing rows with no damage, fatality, or casualty value (zero rows)
refdata <- filter_at(refdata, vars(2:5), any_vars(. != 0))

#Renaming data accordingly to eradicate misspellings and lapses in the recorded data
#refdata$EVTYPE[grepl("SURF|HIGH TIDE|BEACH", refdata$EVTYPE, ignore.case = TRUE)] <- "HIGH TIDE"   


#Storing the EVTYPE as factor      
#refdata$EVTYPE<-factor(refdata$EVTYPE)

#Showing the summary of the data frame
#glimpse(refdata)
```

##**Results**


```r
#Reducing the table to retain unique events data

#Plotting the result
#ggplot(refdata1, aes(fill=condition, y=value, x=specie))
#      + geom_bar(position="stack", stat="identity")
```
