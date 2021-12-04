# Sea Breeze Analysis Project

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
    <img alt="Formation of a Sea Breeze" height="300px" src="https://www.weather.gov/images/jetstream/ocean/seabreeze.jpg">
</figure>

**Research Question:**
Using buoy and coastal weather station data from within the last ten years, do various coastal weather phenomena such as sea and land temperature and turbidity accurately predict the presence of a sea breeze on the Carolina coast?

**Data Resources:**
- Weather Station Data: https://mesonet.agron.iastate.edu/request/download.phtml?network=SC_ASOS
    -  For weather station data we used the METAR data collected from Iowa State University which contained datasets for multiple years from weather stations across the United             States. We focused on two weather stations off of North and South Carolina. **Station HXD** located at Hilton Head Island in South Carolina and **Station SUT** located             near Wilmington North Carolina.
- Weather Buoy Data:  https://www.ndbc.noaa.gov/station_page.php?station=41033
    -  For the buoy data we went to the National Data Buoy Center (NDBC) and collected data over the past 8 years from a buoy off of South Carolina's coastline. 
-  Shark Presence Data: https://github.com/profunccdata/Knowledge_Discovery_In_Databases
    -  For the shark presence data we utilized the data made available to us by the professor of this course, Dr. Pamela Thompson, which was previously collected and cleaned by             herself and several of her students in previous courses.

## Group Plan

![image](https://github.com/Forrestjohnson2000/6162-Seabreeze/blob/main/Images/Group%207%20Plan%20Final.png)

## Data Collection, Preprocessing, and Exploratory Data Analysis

### Buoy Data

The first data we collected for the calculation of Sea Breeze was from Station 41033 - Fripp Nearshore, SC. We have called it "Buoy Data," as it was collected from an offshore buoy. 

This data was suggested to us by meteorologist Joe Merchant because it includes important air and water temperature data, the difference between which is a significant indicator of a sea breeze. The buoy data was collected from the last 10 years and includes the columns Year (**"YY"**), Month (**"MM"**), Day (**"DD"**), Minute (**"mm"**), Hour (**"hh"**), Wind Direction (**"WDIR"**), Wind Speed (**"WSPD"**), Gust (**"GST"**), Air Pressure (**"PRES"**), Air Temperature (**"ATMP"**), and Water Temperature (**"WTMP"**). Upon reading the original csv, we parsed the Year, Month, and Day columns to a single (**"date"**) column. We also dropped the Minute (**"mm"**) column.

This displays the head and tail of our initial dataset.

![image](https://user-images.githubusercontent.com/92127317/141392400-a0ba339e-9dc8-4191-b8f2-e3ad3c9aa27c.png)

We make the assumption that the values 99, 999, and 9999 represent missing data. We replace these with null values, "nan" and recheck the data to see current statistics for each variable that was skewed by the 99/999/9999 values and then impute the missing data with the means. 

![image](https://github.com/Forrestjohnson2000/6162-Seabreeze/blob/main/Images/Data%20Description%20Post%20Nulls.png)

From the histograms, we can see that Wind Speed ("WSPD"), Wind Direction ("WDIR"), and Air Temperature ("ATMP") are not normally distributed. Pressure ("PRES") and Gust ("GST") do appear to be normally distributed.

![image](https://github.com/Forrestjohnson2000/6162-Seabreeze/blob/main/Images/Histograms.png)

The below figure is a correlation heatmap. What we take from this is that windspeed and gust are highly correlated at .84, air temp and water temp are fairly correlated at .64, and month and air temp are somewhat positively correlated at .32.

![image](https://github.com/Forrestjohnson2000/6162-Seabreeze/blob/main/Images/Correlation%20Matrix.png)

### Weather Station Data

For weather station data we used the METAR data collected from Iowa State University which contained datasets for multiple years from weather stations across the United States. We focused on two weather stations off of North and South Carolina, Station HXD located at Hilton Head Island in South Carolina and Station SUT located near Wilmington North Carolina.

This data was collected over the last 8 years and includes the columns Weather Station ("station"), the timestamp for when the data was recorded ("valid"), Temperature in Celsius ("tmpc"), Dew Point in Celsius ("dwpc"), Relative Humidity (%) ("relh"), Real Feel Temperature in Farenheit ("feel"), Wind Direction in degrees from True North ("drct"), Wind Speed in mph ("sped"), Precipitation in mm ("p01m"), Wind Gust in mph ("gust_mph"), Sky Level 1 and 2 Cloud Coverage ("skyc1" and "skyc2"), and lastly Cloud Height Level 1 and 2 ("skyl1" and "skyl2").

We collected our weather station data from two different weather stations. Uploading both datasets separately as their own dataframes.

To gain an understanding of what each column was read in as. A few notable takeaways are that the variable **mslp**, the measurement of sea level pressure, has all null values, and therefore provides no insight to the dataframe. Additionally, some of the other columns such as **gust_mph** and the cloud coverage and height variables seem to have a significantly lower number of non-null values, which we will dig into later.

![image](https://user-images.githubusercontent.com/48931690/142335608-e539073b-9826-4738-a39c-f459f1389885.png)

We convert the **"valid"** column to the datetime format for both datasets as this is the timestamp of when the data was recorded and this format will be easier to manipulate.

Because p01m, gust_mph, skyc2, and skyl1/skyl2 have a significant percentage of null values, over about 20%, we remove them from the analysis. This also takes into consideration the fact that precipitation and cloud coverage are not considered to have a large impact on the presence of sea breeze, according to Joe Merchant. On the other hand, wind gust is highly correlated to wind speed, so removing gust_mph will not impact the overall analysis.

Using scatter matricies we can see how each of the variables interact with one another in both the HXD and SUT weateher stations. Both stations have very similar results which makes sense as they are in a geographically similar location.

![image](https://user-images.githubusercontent.com/48931690/142336870-23d295d7-bfaa-4820-a6c2-e7b52521d706.png)

![image](https://user-images.githubusercontent.com/48931690/142337053-364bca74-4ee5-4ab4-94c5-5e9ce7ea05e0.png)

To get a more simplified exploration of relationships we explored the correlation between varaibles using an *sns heatmap* which makes the correlation between variables very visiblly apparent.

![image](https://user-images.githubusercontent.com/48931690/142336909-f1e3f048-96c1-4a22-b40a-1e313b0141ae.png)

From the above correlation heatmap of the SUT station data, we can see that:
1.   The variables **"dwpc"** has a high correlation with **"tmpc"** at 0.91 and with **"feel"** at **"0.9"**. The variables **"feel"** and **"tmpc"** are extremely highly correlated at 0.99.
2.   The variables **"relh"** and **"dwpc"** are moderately correlated at 0.47. The variables **"drct"** and **"sped"** are moderately correlated at 0.51.

The above observations are mostly inuitive, as they represent weather phenomena and/or measurements that are known to have a caclulable relationship. However, we may be surprised to learn that wind direction **"drct"** and speed **"sped"** are moderately correlated.

![image](https://user-images.githubusercontent.com/48931690/142336948-4a59ad1b-deee-4dca-b28f-add962813d82.png)

From the above correlation heatmap of the HXD station data, we can see that:
1.   The variables **"dwpc"** has a high correlation with **"tmpc"** at 0.88 and with **"feel"** at **"0.86"**. The variables **"feel"** and **"tmpc"** are extremely highly correlated at 0.98.
2.   The variables **"relh"** and **"dwpc"** are moderately correlated at 0.44. The variables **"drct"** and **"sped"** are moderately correlated at 0.4.

The above observations are very similar to those gained from the previous SUT station data correlation heatmap. However, we can also note that in general, the correlations between variables for this HXD dataset are slightly lower.



### Merging Datasets
After cleaning and completing some of the preparation for the buoy and weather station datasets, they will need to be merged into a single dataset for modeling. Merging SUT and HXD datasets were simple as the contained the same variables and measurements, therefore the process only required an appending of one dataset to the other.

Combining the data with the buoy was a little more complicated, but it was managed by inner joining the datasets and merging them on the date and hour of the records. While both SUT and HXD datassets had multiple records per hour, the buot only had one resulting in repeats in the data.

![image](https://user-images.githubusercontent.com/48931690/142337760-663f194b-2829-4c72-9ef6-7e120faf6ff9.png)




### Calculation of Sea Breeze
The Formula to calculate the Sea Breeze Index (SBI) according to the Simpson and Walsh is 

![image](https://user-images.githubusercontent.com/48931690/142338267-3720cb5e-0aa7-4e10-bed6-1da18d772ad9.png)
                
 Where *U* is the cross-coast component of the synoptic wind with offshore winds taken as positive.The SBI represents the ratio of synoptic wind kinetic energy to thermal gradient potential energy.Values of SBI that are above some critical value (SBIcrit) typically indicate situations in which synoptic airflow blocks sea breezes; values below (SBIcrit) typically indicate conditions conducive to sea breezes.
 
            ΔT = Tair − Tsea 
is the difference in temperature between the ocean surface and the overland air. After taking reference from the Bernouli’s equation and modifications and calculations, equation can be modified as 

![image](https://user-images.githubusercontent.com/48931690/142338306-004281f2-a8b0-49e1-813b-1933c6d2e784.png)

Where *h* represents the thickness of the air mass that exchanges heat with the surface

**Formula to calculate Air Density:**
Calculate the saturation vapor pressure at given temperature T using the formula
 
          Svp = 6.1078 x 10^[7.5*T /(T + 237.3)],

where *T* is measured in degrees Celsius. Saturation vapor pressure is the vapor pressure at 100% relative humidity.
Find the actual vapor pressure, multiplying the saturation vapor pressure by the relative humidity: 

            Actual(pv) = Svp x RelH.
Subtract the vapor pressure from the total air pressure to find the pressure of dry air: 

                   pd = p - pv
 The formula to calculate the air density is given by: 
 
       ρ = (pd / (Rd x T)) + (pv / (Rv x T))
        where 
              pd is the pressure of dry air in Pa
              pv is the water vapor pressure in Pa
              T is the air temperature in Kelvins

*Rd* is the specific gas constant for dry air equal to 287.058 J/(kg·K), and
*Rv* is the specific gas constant for water vapor equal to 461.495 J/(kg·K)

After substituting the values in the equation we get 

        ρ = (pd / (287.058 T)) + (pv / (461.49 T))

This variable is known as **SBI** and a value under 5 is an indication that weather factors are conducive to produce a sea breeze. After evaluating the results we decided to multiply **SBI** by a factor of 10,000 to adjust our results into a more reasonable representation of the SBI score. We are not sure exactly why this was required but it gave much clearer results to use for analysis.

Here are our results based on our calculations and the constants given:

```
Saturated_vapor_pressure = 6.1078 * (10**((7.5*(data.tmpc))/(data.tmpc + 273.15)))
vapor_pressure = Saturated_vapor_pressure * (data.relh)
dry_pressure = data.PRES - vapor_pressure

air_density = dry_pressure/(287.058*(data.tmpc+273.15)) + vapor_pressure/(461.495*(data.tmpc+273.15))

SBI = 19.6*air_density/(data.ATMP+273.15)
SBI = SBI*10000
```

![image](https://github.com/Forrestjohnson2000/6162-Seabreeze/blob/main/Images/SBI%20Summary.png)

### Shark Data



## Data Modeling

We decided to model the data using both unsupervised and supervised techniques using k-means clustering, logistic regression, and a support vector machine (SVM). 

### K-Means Clustering

Our first data model uses k-means clustering, with the goal of uncovering significant clusters that might indicate the presence of a sea breeze.

### Logistic Regression

We also used logistic regression to model the relationships between our independent variables (including "SBI") and our target variable, "AttackCat".

### Support Vector Machine



## Conclusion


