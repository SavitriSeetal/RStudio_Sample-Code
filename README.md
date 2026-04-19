---
Title: "GES668: Maryland Protected Lands - Land Preservation Foundation Easements"
Author: "Savitri Seetal.Mantripragada"
Format: revealjs
date-modified: 2023-12-13
---

## Overview

::: r-fit-text
-   The Maryland Agricultural Land Preservation Foundation is one of the earliest foundations established in America by conserving more land that we have over any state in our country, it has risen to become a leader in farmland preservation. The primary purpose is to preserve sufficient agricultural land to maintain a viable local base of food and fiber production for the present and future citizens of Maryland. For eligible agricultural land throughout the State, the programme purchases perpetual agricultural conservation easements. This is to say, the programme pays landowners for maintaining what they've done over a number of years.Agricultural conservation easement in perpetuity held by MALPF and administered through the Maryland Department of Agriculture.The aim of this project is to make use of spatial data from the Maryland Agricultural Land Preservation Foundation M.A.L.F. in order to investigate and analyse protected farmlands under MALPF's management. A valuable source of information is the GIS data for MALPF easements which gives an insight into the spatial distribution of protected areas.Created by the General Assembly in 1977, MALPF purchases agricultural preservation easements that forever restrict development on prime farmland and woodland and has permanently preserved land in each of Maryland's 23 counties.
:::

## Project goals

::: r-fit-text
-   My Initial Goal for the project was to work in a broader scope, in the whole United States but I narrowed down my scope of work to Maryland to understand integration of the dataset and identify the Protected Areas Database. I planned to do a time series plot in a detailed overview of changes in the number of easements during each year. In order to identify the trends, patterns and variations in the distribution of conservation areas, each line on the map corresponds to the date when an easement was established. I had assessed the distribution of protected areas at a county level and identified regions in which preserved agricultural land is more concentrated and represented those protected land as polygons in an interactive web map. As on working with the project, I explored new resources about the various availability of data sets and new functions which were spatial regression analysis using the packages "spdep" for spatial dependencies. I was successful in creating a time series plot which is a comprehensive view of how the number of easements has changed over time. The date on which an easement was established is the corresponding line of the map to be used for identification of trends, patterns and variations between conservation areas.Further, I wanted to give users access to MALPF easements in a geographic manner, develop an interactive web project which has a multisectoral objectives and benefits beyond the immediate analysis of MALPF easements, which seek to have a positive impact on policy, civic awareness, stakeholder involvement and ethical data practice.
:::

## Data sources

::: r-fit-text
-   \[Maryland Protected Lands & Agricultural Land Preservation Foundation Easements\] (https://data.imap.maryland.gov/datasets/maryland::maryland-protected-lands-md-agricultural-land-preservation-foundation-easements/about)\*\*

The Maryland Agricultural Land Preservation Foundation (MALPF), housed within the Maryland Department of Agriculture (MDA), protects agricultural lands through the use of perpetual easements. The data was created by the Maryland General Assembly in 1977 which is governed by the Agricultural Article, Sections 2-501 through 2-519 of the Annotated Code of Maryland. This is achieved by the voluntary application of landowners to sell an easement on their land through a competitive Statewide application procedure.

MALPF easement boundaries are estimated based on property descriptions provided by MDA, aligned to MD i Map parcel polygons and supplemented with Md Property View data to facilitate use with vectorized parcel data. This GIS dataset is intended for general guidance and use only and is not designed as a substitute for legal survey. The coordinates displayed do not represent legal parcel corners and/or boundaries and they cannot be used for establishment of or in lieu of legal land survey boundaries.

```{r}
#| label: extraction of geojson
#| echo: true
library(sf)
library(tidyverse)
library(leaflet)
library(ggplot2)
library(dplyr)
geojson_file_path <- here::here("files/Maryland_Protected_Lands_-_MD_Agricultural_Land_Preservation_Foundation_Easements.geojson")
malpf_easements <- st_read(geojson_file_path)
```
:::

## Data sources

::: r-fit-text
-   \[Maryland National Register Historic Places\](https://data.imap.maryland.gov/datasets/maryland::maryland-national-register-historic-places-national-register-of-historic-places/about)

The Data was created by National Register of Historic Places recognizes districts, buildings, structures, objects, and sites for their significance in American history, archaeology, architecture, engineering, or culture, and identifies them as worthy of preservation. The National Register is a program of the U. S. Department of the Interior, National Park Service, and is administered at the State level by the Maryland Historical Trust. This Data set is a MD iMap service layer with 1596 features and 18 fields.

```{r}
#| label: extraction of shapefile
#| echo: true
geojson_file_path <- here::here("C:/SSM/GES 668/Project/Historic_Places/Maryland_National_Register_Historic_Places.geojson")
historic_places <- st_read(geojson_file_path)

head(historic_places)
```
:::

## Approach and Methods for working with data

::: r-fit-text
Exploratory Data Analysis:

1)  Ensure that the data is clean and there are no missing values.
2)  Append the relevant columns to a suitable type of data.

#Bar Plot Visualization 1) A bar plot is visualized to show the number of protected lands in each county based on the malpf_easements dataset.

```{r}
ggplot(malpf_easements, aes(y = County)) +
  geom_bar() +
  labs(title = "Number of Protected Lands by County")
```
:::

## Bar Plot Visualization

::: r-fit-text
2)  To illustrate the annual trends in the acquisition of easements by the Maryland Agricultural Land Preservation Foundation with bars representing the number of easements acquired each year, this creates a bar plot.

```{r}
# Create a new column for the year
malpf_easements$Year <- lubridate::year(malpf_easements$Establishment_Date)

# Plot annual easement acquisition trends with spacing between bars and rotated labels
ggplot(malpf_easements, aes(x = Year)) +
  geom_bar(color = "blue", fill = "lightblue", stat = "count", width = 0.7) +
  labs(title = "Annual Analysis of MALPF Easements",
       x = "Year",
       y = "Number of Easements") +
  scale_x_continuous(breaks = unique(malpf_easements$Year), labels = unique(malpf_easements$Year)) +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))  # Adjust the angle for better readability
```
:::

## Bar Plot Visualization

::: r-fit-text
-   A bar chart showing the total area of the two subsets of the Maryland Agricultural Land Preservation Foundation's MALPF easements, "1977-2000" and "2001-2023", respectively.

```{r}
library(dplyr)

# Assuming Malpf_easements$Establishment_Date is a Date column
malpf_easements <- malpf_easements %>%
  mutate(Year = lubridate::year(Establishment_Date))

# Create subsets based on the establishment year
malpf_subset_1977_2000 <- malpf_easements %>%
  filter(Year >= 1977, Year <= 2000)

malpf_subset_2001_2023 <- malpf_easements %>%
  filter(Year >= 2001, Year <= 2023)

#head(malpf_subset_1977_2000)
#head(malpf_subset_2001_2023)
```

```{r}
library(ggplot2)
library(sf)

# Assuming malpf_1977_2000_landcover and malpf_2001_2023_landcover are sf objects

# Calculate total acres for each subset
total_acres_1977_2000 <- sum(malpf_subset_1977_2000$Acres)
total_acres_2001_2023 <- sum(malpf_subset_2001_2023$Acres)
# Create a data frame for visualization
acres_data <- data.frame(
  Period = c("1977-2000", "2001-2023"),
  Total_Acres = c(total_acres_1977_2000, total_acres_2001_2023)
)
# Bar plot to visualize acres in each subset
ggplot(acres_data, aes(x = Period, y = Total_Acres, fill = Period)) +
  geom_bar(stat = "identity", position = "dodge", color = "black") +
  labs(title = "Total Acres in MALPF Easements",
       x = "Period",
       y = "Total Acres") +
  theme_minimal()
```
:::

## Temporal Analysis

::: r-fit-text
1)  Analyze the distribution of MALPF easements over time.
2)  Identify trends and patterns in the establishment of easements.

```{r}
malpf_easements$Establishment_Date <- as.Date(malpf_easements$Establishment_Date)

# Time Series Plot
ggplot(malpf_easements, aes(x = Establishment_Date)) +
  geom_freqpoly(binwidth = 365, color = "blue") + 
  labs(title = "Temporal Analysis of MALPF Easements",
       x = "Establishment Date",
       y = "Number of Easements") +
  theme_minimal()
```
:::

## Interactive Web Map Visualization

::: r-fit-text
1)  In order to give users geographical access to the MALPF easement, a Interactive web map was created with Pop ups with details about County, Acres and Unique Id on each easement highlighted as blue polygons on a base map,

```{r}
   library(leaflet)
 # Create a simple leaflet map
leaflet(malpf_easements) %>%
  addTiles() %>%
  addPolygons(
    fillColor = "blue",
    fillOpacity = 0.5,
    color = "white",
    weight = 1,
    popup = ~paste("Unique_ID: ", Unique_ID, "<br>",
                   "Acres: ", Acres, "<br>",
                   "County: ", County)
  )
```
:::

## Resources

#Packages

library(sf)

library(tidyverse)

library(leaflet)

library(ggplot2)

library(dplyr)

## Challenges in working with data

::: r-fit-text
-   Data Source & Limitation:

The scope of some analyses was hindered by the lack of certain attributes in the malpf data and the historical land cover data, that's where I performed Exploratory data analysis to clean the data and remove all the missing values.

-   Spatial Clustering Analysis:

Initially I wanted to work on performing a cluster analysis using the Density Based Spatial Clustering of Applications with NOISE(DBSCAN) algorithms to identify spatial patterns in easement distribution, also to identify areas with high concentrations, visualize clusters of protected areas.

```{r}
# Load required libraries
#library(sf)
#library(dbscan)

#Assuming malpf_easements is an sf object with geometry column 'geometry'
#Extracting the numeric coordinates for clustering
#coordinates <- st_coordinates(malpf_easements)

# Perform spatial clustering using DBSCAN
#dbscan_clusters <- dbscan::dbscan(coordinates[, c("X", "Y")], eps = 0.1, minPts = 5)

# Add the cluster labels to the original dataset
#malpf_easements$Cluster <- dbscan_clusters$cluster

# number of cluster labels matches the number of rows in the original dataset
#if (length(malpf_easements$Cluster) != nrow(malpf_easements)) {stop}
# Plotting clustered data
#plot(malpf_easements, col = malpf_easements$Cluster, main = "Spatial Clustering of Protected Lands")
```

#("Error: Number of cluster labels does not match the number of rows in the dataset.") #Error in\<-.data.frame, i, value = c(1L, 1L, 1L, 1L, 1L, 1L, : replacement has 392105 rows, data has 2527
:::

## Completed Analysis

::: r-fit-text
1)  Integration of the dataset with the Protected Areas Database of the Maryland.

2)  County-wise Visualization of Protected Lands. Created visualizations depicting the number of protected lands in each county based on the MALPF_EASEMENT dataset.

3)  Visual overview of the trends in (MALPF) Maryland Agricultural Land Preservation Foundation easement acquisitions.

4)  Land Use Land Cover Change Analysis in the period (1977 - 2000) & (2001 - 2023).

5)  Conducted Temporal Analysis and analyzed the distribution of MALPF easements over time.

6)  Interactive Web Map Visualization to access MALPF easements from a geographical perspective.
:::

## Output Files

::: r-fit-text
#Project Code 1) Create subsets of the malpf_easements dataset based on the establishment year (1977-2000) and (2001-2023). ![](images/222.png){fig-align="center"}
:::

## Output Files

#Protected Lands in Each County ![](Images/protected%20_lands%20each%20_county.png){width="707"}

## Total Acres in MALPF Easements

![](Images/Total_Acres.png){fig-align="center"}

## Temporal Analysis of MALPF Easements

![](Images/temporal_analysis.png){fig-align="center" width="549"}

## Annual trends of MALPF (Maryland Agricultural Land Preservation Foundation) easement acquisitions

![](Images/Annual%20_trends%20_of%20MALPF.png){fig-align="center" width="418"}

## A spatial Map Plotting of the malpf_data dataset, polygons from the malpf_data dataset are filled based on their "Acres" values

![](Images/spatial%20plot%20map.png){fig-align="center" width="669"}

## Limitations & Future Works

::: r-fit-text
#Limitations\
1) Clustering algorithms assume homogeneity within clusters, Further Spatial Clustering analysis might over simplify the spatial patterns.

2)  The number of data sets is high, determine the strategies for handling and showing only a small proportion of them at first.

-   Future Work

    1)  In order to analyse changes in land use over time, a comparison of historical data on area covered and current protected areas shall be made. The efficientness of the MALPF programme for protecting farm land can be seen in this way.

    2)  Identify how the protection of agricultural land in rural areas has a positive impact on their economies. The creation of jobs, agriculture's productivity and the associated benefits could also form part of this.

    3)  Will be working on he clustering analysis, with more ground knowledge about clustering algorithms. :::
:::
