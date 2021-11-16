# Sea Breeze Analysis Project
<hr>
Jacob Brand, Rachael Dewey, Forrest Johnson, Rohith Bakkannagari, and Joshua Allard

## Introduction
This site contains our project for DSBA 6162 Knowledge Discovery in Databases class at UNC Charlotte, Fall 2021, taught by Dr. Pamela Thompson. 

### Introduction to Problem/Opportunity: 
In his presentation on research into shark presence and attacks, Joe Merchant asserted that there is a relationship between the occurrence of sea breezes and shark presence at the shoreline. However, there is a lack of available data on when sea breezes have occurred. Based on the existing Carolina coast weather data that has been collected for the past ten years, we aim to create a model that helps to predict the conditions that are most viable for creating a sea breeze utilizing two sets of data from onshore weather stations and data from an offshore weather buoy.

Some features we plan to look into include, but are not limited to:
- Wind speed,
- Wind direction,
- Seawater and land temperature,
- Alpha vector (Wind measurement), and
- Cloud cover

**Formation of a Sea Breeze**

<figure>
    <img alt="Formation of sea breeze" height="300px" src="https://www.weather.gov/images/jetstream/ocean/seabreeze.jpg">
</figure>

**Research Question:**
Using buoy and coastal weather station data from within the last ten years, do various coastal weather phenomena such as sea and land temperature and turbidity accurately predict the presence of a sea breeze on the Carolina coast?

**Data Resources:**
- Weather Station Data: https://mesonet.agron.iastate.edu/request/download.phtml?network=SC_ASOS
    -  For weather station data we used the METAR data collected from Iowa State University which contained datasets for multiple years from weather stations across the United             States. We focused on two weather stations off of North and South Carolina. **Station HXD** located at Hilton Head Island in South Carolina and **Station SUT** located             near Wilmington North Carolina.
- Weather Buoy Data:  https://www.ndbc.noaa.gov/station_page.php?station=41033
    -  For the buoy data we went to the National Data Buoy Center (NDBC) and collected data over the past 8 years from a buoy off of South Carolina's coastline. 

## Data Collection, Preprocessing, and Exploratory Data Analysis

### Buoy Data

The first data we collected for the calculation of Sea Breeze was from Station 41033 - Fripp Nearshore, SC. We have called it "Buoy Data," as it was collected from an offshore buoy. 

This data was suggested to us by meteorologist Joe Merchant because it includes important air and water temperature data, which the difference between which is a significant indicator of a sea breeze. The buoy data was collected from the last 10 years and includes the columns Year (**"YY"**), Month (**"MM"**), Day (**"DD"**), Minute (**"mm"**), Hour (**"hh"**), Wind Direction (**"WDIR"**), Wind Speed (**"WSPD"**), Gust (**"GST"**), Air Pressure (**"PRES"**), Air Temperature (**"ATMP"**), and Water Temperature (**"WTMP"**). Upon reading the original csv, we parsed the Year, Month, and Day columns to a single (**"date"**) column. We also dropped the Minute (**"mm"**) column.

This displays the head and tail of our initial dataset.

![image](https://user-images.githubusercontent.com/92127317/141392400-a0ba339e-9dc8-4191-b8f2-e3ad3c9aa27c.png)

Next, we can examine the summary of each variable.

![image](https://github.com/Forrestjohnson2000/6162-Seabreeze/blob/main/Images/Buoy%20Data%20Description.jpg)

We make the assumption that the values 99, 999, and 9999 represent missing data. We replace these with null values, "nan" and recheck the data to see current statistics for each variable that was skewed by the 99/999/9999 values.

![image](https://github.com/Forrestjohnson2000/6162-Seabreeze/blob/main/Images/Data%20Description%20Post%20Nulls.png)

We impute the missing data with the means using SimpleImputer. Now we can visualize the Buoy Data.

![image](https://github.com/Forrestjohnson2000/6162-Seabreeze/blob/main/Images/Histograms.png)

From the histograms, we can see that Wind Speed ("WSPD"), Wind Direction ("WDIR"), and Air Temperature ("ATMP") are not normally distributed. Pressure ("PRES") and Gust ("GST") do appear to be normally distributed.

![image](https://github.com/Forrestjohnson2000/6162-Seabreeze/blob/main/Images/Scatter%20Plots.png)

We also create a scatter matrix of all variables. Based on this, there does not appear to be many strong correlations between variables except for Gust ("GST") and Wind Speed ("WSPD"). This makes intuitive sense, as high wind speeds create gusts, so these variables are positively correlated.

![image](https://github.com/Forrestjohnson2000/6162-Seabreeze/blob/main/Images/Correlation%20Matrix.png)

The above figure is a correlation heatmap. What we take from this is that windspeed and gust are highly correlated at .84, air temp and water temp are fairly correlated at .64, and month and air temp are somewhat positively correlated at .32.

### Weather Station Data


### Merging Datasets

### Calculation of Sea Breeze
<img alt="Sea breeze conducive score formula" height="45px" src="https://journals.ametsoc.org/view/journals/wefo/18/4/images/i1520-0434-18-4-614-e1.gif" align="left" vspace="0px">

where SBI is the sea breeze index, U is ___ and ΔT is difference in temperature between the air temperature and water temperature.

## Data Modeling

### K-Means Clustering

### Logistic Regression

### Support Vector Machine

