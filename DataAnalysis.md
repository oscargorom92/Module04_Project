---
title: "Module 4 Worshop 2"
author: "Oscar Gonzalez"
date: "2025-09-26"
output: html_document
---



## End-to-end data alysis in R

The purpose of this exercise is to review available datasets for analysis. I chose to analyze shark captures by year from 2001 to 2025 across six locations in Queensland. The data were obtained as a .csv file from the QFISH website: https://qfish.fisheries.qld.gov.au/. I exported the file to my local folder and began the analysis in Rstudio:

### Install and load all necessary R packages for this analysis

``` r
library(tidyr)
library(dplyr)
library(ggplot2)
```

### Import shark capture data to Rstudio

``` r
sharks <- read.csv("C:/Users/mosac/Desktop/Sharks_QLD.csv", 
                   check.names = FALSE, #Tells R not to fix column names
                   stringsAsFactors = FALSE) #Keep text columns as characters
```

### Tidy data

``` r
sharks_long <- sharks %>%  #Starts a pipeline on the dataframe "sharks" and assign final result to sharks_long
  rename(Location = 1) %>%  #Renames the first column to Location
   pivot_longer(-Location, names_to = "Year", values_to = "Sharks") %>% #Converts table to one row per Location-Year pair
   mutate(   #Ensure correct data types
    Year   = as.integer(Year),
    Sharks = as.numeric(Sharks) 
  ) %>%
  drop_na(Year, Sharks) #Remove rows where Year or Sharks are missing (NA)
```
  
### Visualize and check the new table setup

``` r
head(sharks_long)
```

```
## # A tibble: 6 × 3
##   Location   Year Sharks
##   <chr>     <int>  <dbl>
## 1 Bundaberg  2001     62
## 2 Bundaberg  2002     62
## 3 Bundaberg  2003     50
## 4 Bundaberg  2004     39
## 5 Bundaberg  2005     69
## 6 Bundaberg  2006     49
```

### Build a plot for each Locality

``` r
ggplot(sharks_long, aes(Year, Sharks, color = Location)) +  #Tells ggplot to group by locality with a unique color
  geom_line(linewidth = 0.8) + geom_point(size = 1.4) +    #Add lines and points to each plot
  facet_wrap(~ Location, scales = "free_y") +  #Creates a small panel for each location, each with its own y axis 
  scale_color_viridis_d(option = "C", end = 0.9) +  #Pick viridis color palette
  guides(color = "none") +     #Not showing the color labelling
  labs(x = "Year", y = "Sharks caught") +  #Show labels in axes
  theme_bw()   #Theme black and white
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png)

Shark catches by year (2000–2025) for seven locations in Queensland, Australia. Each facet represents a single location, and colors are unique per location for easy comparison.

### Save the final plot

``` r
ggsave("Sharkrecords_QLD.jpg")
```

```
## Saving 7 x 7 in image
```
