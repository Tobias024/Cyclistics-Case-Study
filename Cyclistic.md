---
title: "Case study - Cyclistic"
output:
  html_document:
    df_print: paged
  html_notebook: default
  word_document: default
---

### Ask:

#### What you will find in this document:

This document shares the steps in the analysis of a data set regarding a company of shareable bikes. The goal is to find the relationship between different memberships in order to answer *"How do annual members and occasional riders differ in their use of Cyclistic bicycles?"*. The idea is to identify those with more profit margins and find out how to increase this type of memberships and with so, the total revenue.

#### Stakeholders:

-   Lily Moreno
-   Cyclistic executive team.\

#### Questions to answer:

-   How do annual memberships and casual riders differ with respect to using Cyclistic bikes?
-   Why would casual cyclists purchase annual memberships from Cyclistic?
-   How can Cyclistic use digital media to influence casual cyclists to become members?

*Business task*: Find the relationships between annual and casual memberships.

### Prepare:

All data sets can be found [here](https://divvy-tripdata.s3.amazonaws.com/index.html). This analysis was based on first source information from last year (2022).

For the purposes of this case study, the data sets are appropriate and will allow to answer the company's questions. The data has been provided by a company under this [license](https://ride.divvybikes.com/data-license-agreement).

\
1. All data sets and libraries were imported. The libraries used were:

```{r}
library(readr)
library(tidyverse)
library(janitor)
library(dplyr)
library(readr)
library(tidyr)
library(data.table)
library(lubridate)

```

\
2. Merged data sets to create a single one:

```{r}
totaltripdata <- rbind(X202112_divvy_tripdata,X202201_divvy_tripdata,	X202202_divvy_tripdata,	X202203_divvy_tripdata,	X202204_divvy_tripdata,	X202205_divvy_tripdata,	X202206_divvy_tripdata,	X202207_divvy_tripdata,	X202208_divvy_tripdata,	X202209_divvy_publictripdata,	X202210_divvy_tripdata,	X202211_divvy_tripdata,	X202212_divvy_tripdata)
ttd <- totaltripdata
```

\

### Process:

#### Used tools:

In order to prepare, organize and clean the data we used R Studio. The language R allows to create summarized tables with relevant information at big scale with which all visualizations were then created. Although R is capable of creating visualization, in this case, most of them were created on *Google sheets* and *tableau* due to aesthetics & efficiency.

\
3. After noticing there were duplicated rows, they were removed by using:

```{r}
unique_ttd <- unique(ttd)
view(unique_ttd)
```

4.We created a table summarizing the type of membership, type of rideable, time of total trips, amount of trips and a ratio of time per trip.

Members_casuals Count + How long it took to complete these trips?

```{r}
Group_ttd <- unique_ttd %>%
  mutate(ride_length = ended_at - started_at) %>% 
  group_by(member_casual, rideable_type) %>%
  summarise( ride_length = sum(ride_length),
             trips_ammount = n())
mutate(Group_ttd ,time_per_trip_ratio = ride_length / trips_ammount)
View(Group_ttd)
```

And saved it as csv using the "write.csv" function.

\
5. Many station names were empty, so we will only be considering the rest.\
By calculating the amount of the different stations, we got a total of over a thousand, despite being mentioned in the original information of having only 692. This means the information regarding this columns was not reliable due to being imprecise. in order to proceed with the analysis, all missing information was filtered.

We decided to continue with the stations Id´s instead by using the following tables:

```{r}
Start_stations <-unique_ttd %>% 
  group_by(start_station_name) %>% 
  summarise(Frequency_start_stations = n())
view(Start_stations)
End_stations <-unique_ttd %>% 
  group_by(end_station_name) %>% 
  summarise(Frequency_end_stations = n())
view(End_stations)

Geo_Start <- unique_ttd[, c("member_casual","rideable_type", "start_lat", "start_lng")]
Sample_geo_start <- Geo_Start[sample(nrow(Geo_Start), 100, replace = FALSE), ]

#Save

Geo_End <- unique_ttd[, c("member_casual","rideable_type", "start_lat", "start_lng")]
Sample_geo_end <- Geo_End[sample(nrow(Geo_End), 100, replace = FALSE), ]

#Save
```

Afterwards we loaded this information on tableau and proceeded to create Maps to have a more clear view of where the most used start and end stations were. You can find it [here](https://public.tableau.com/views/CycliticsStations/Story1)\

6.  To understand the distribution of members & casuals by type of rideable along the year we created a new table using:

```{r}
months_of_year <- unique_ttd %>% 
  mutate(ride_length = ended_at - started_at) %>%
  mutate( Months = month(unique_ttd$started_at)) %>% 
  group_by(member_casual, rideable_type, Months) %>% 
  summarise(ride_id=n(), ride_length = sum(ride_length))
view(months_of_year)
```

And saved it as csv using the "write.csv" function

7.  Now we wanted to understand the most frequent day usage between members and casuals, also by rideable type:

```{r}
days_of_week <- unique_ttd %>% 
  mutate(ride_length = ended_at - started_at) %>%
  mutate( Weekday = weekdays(as.Date(unique_ttd$started_at))) %>% 
  group_by(member_casual, rideable_type, Weekday) %>% 
  summarise(ride_id=n(), ride_length = sum(ride_length))
view(days_of_week)
```

And saved it as csv using the "write.csv" function

Afterwards, we loaded the different tables into google sheets to process as different Pivots. You can access by clicking [Here](https://docs.google.com/spreadsheets/d/1c3dkHICGCMb71rwtfu_XPAY1iNQ_5oJ2OGz6Phsgjn8/edit?usp=sharing)

With the different pivots we created the charts that allowed us to have a clearer understanding of the information

### Analyze:

We find out that:

-   Casuals trips have x2.8 more time per trip than members
-   Members have 59,03% trips.
-   Members most used rideable type are classic bikes while for casuals are electric_bike.
-   Casual members longer trips are with Docked bikes
-   Members do not use docked bikes.
-   Most trips happen during Summer.
-   Members use more bikes from Monday to Fridays while Casuals have an big increase on the weekends
-   When the temperatures increase, electric bikes usage increase as well. Most trips happen \*Casual users start & end point are more disperse than members.

### Share

We proceeded to create a slide to share the information found, the takeaways and proposals as next steps. You can access the slide by clicking [here](https://docs.google.com/presentation/d/1-mk-YMbnPQjdDX4ny4MVpFn2SZq1IcSJYI7K2NT8pcM/edit#slide=id.g1dca03f005f_0_10)
