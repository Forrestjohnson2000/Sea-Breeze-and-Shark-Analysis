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

We make the assumption that the values 99, 999, and 9999 represent missing data. We replace these with null values, "nan" and recheck the data to see current statistics for each variable that was skewed by the 99/999/9999 values and then impute the missing data with the means. 

![image](https://github.com/Forrestjohnson2000/6162-Seabreeze/blob/main/Images/Data%20Description%20Post%20Nulls.png)

From the histograms, we can see that Wind Speed ("WSPD"), Wind Direction ("WDIR"), and Air Temperature ("ATMP") are not normally distributed. Pressure ("PRES") and Gust ("GST") do appear to be normally distributed.

![image](https://github.com/Forrestjohnson2000/6162-Seabreeze/blob/main/Images/Histograms.png)

### Weather Station Data

For weather station data, we used the METAR data collected from Iowa State University, which contains datasets for multiple years from weather stations across the United States. We focused on two weather stations off the coast of North and South Carolina: Station HXD located at Hilton Head Island in South Carolina and Station SUT located near Wilmington North Carolina.

This data was collected over the last 8 years and includes the columns Weather Station ("station"), the timestamp for when the data was recorded ("valid"), Temperature in Celsius (**"tmpc"**), Dew Point in Celsius (**"dwpc"**), Relative Humidity (%) (**"relh"**), Real Feel Temperature in Fahrenheit (**"feel"**), Wind Direction in degrees from True North (**"drct"**), Wind Speed in mph (**"sped"**), Precipitation in mm (**"p01m"**), Wind Gust in mph (**"gust_mph"**), Sky Level 1 and 2 Cloud Coverage (**"skyc1"** and **"skyc2"**), and lastly Cloud Height Level 1 and 2 (**"skyl1"** and **"skyl2"**).

Data from each of the two weather stations was uploaded separately to gain an understanding of what each column was read in as. A few notable takeaways are that the variable **mslp**, the measurement of sea level pressure, has all null values, and therefore provides no insight to the dataframe. Additionally, some of the other columns such as **gust_mph** and the cloud coverage and height variables seem to have a significantly lower number of non-null values, which we will dig into later.

![image](https://user-images.githubusercontent.com/48931690/142335608-e539073b-9826-4738-a39c-f459f1389885.png)

We convert the **"valid"** column to the datetime format for both datasets, as this is the timestamp of when the data was recorded, and this format will be easier to manipulate.

Because **"p01m"**, **"gust_mph"**, **"skyc2"**, and **"skyl1/skyl2"** have a significant percentage of null values, over about 20%, we remove them from the analysis. This also takes into consideration the fact that precipitation and cloud coverage are not considered to have a large impact on the presence of sea breeze, according to Joe Merchant. On the other hand, wind gust is highly correlated to wind speed, so removing gust_mph will not impact the overall analysis.

Using scatter matrices, we can see how each of the variables interact with one another in both the HXD and SUT weather stations. Both stations have very similar results, which makes sense as they are in a geographically similar location.

![image](https://user-images.githubusercontent.com/48931690/142336870-23d295d7-bfaa-4820-a6c2-e7b52521d706.png)

![image](https://user-images.githubusercontent.com/48931690/142337053-364bca74-4ee5-4ab4-94c5-5e9ce7ea05e0.png)

### Merging Datasets
After cleaning and completing some of the preparation for the buoy and weather station datasets, they will need to be merged into a single dataset for modeling. Merging SUT and HXD datasets was simple as they contained the same variables and measurements, therefore the process only required an appending of one dataset to the other.

Combining the data with the buoy was a little more complicated, but it was managed by inner joining the datasets and merging them on the date and hour of the records. While both SUT and HXD datassets had multiple records per hour, the buoy only had one resulting in repeats in the data.

![image](https://user-images.githubusercontent.com/48931690/142337760-663f194b-2829-4c72-9ef6-7e120faf6ff9.png)

### Calculation of Sea Breeze
The Formula to calculate the Sea Breeze Index (SBI) according to the <a href="https://doi.org/10.1175/1520-0434(2003)018<0614:ASSPAF>2.0.CO;2">Simpson and Walsh</a> is 

![image](https://user-images.githubusercontent.com/48931690/142338267-3720cb5e-0aa7-4e10-bed6-1da18d772ad9.png)
                
Where *U* is the cross-coast component of the synoptic wind with offshore winds taken as positive.The SBI represents the ratio of synoptic wind kinetic energy to thermal gradient potential energy. Values of SBI that are above some critical value (SBIcrit) typically indicate situations in which synoptic airflow blocks sea breezes; values below (SBIcrit) typically indicate conditions conducive to sea breezes.
 
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

This variable is known as **SBI** and a value under 5 is an indication that weather factors are conducive to produce a sea breeze. After evaluating the results, we decided to multiply **SBI** by a factor of 10,000 to adjust our results into a more reasonable representation of the SBI score. We are not sure exactly why this was required, but it gave much clearer results to use for analysis.

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
For shark presence, we used data that was previously collected for Dr. Pamela Thompson’s class. The final dataset, which was used for our models, can be seen below. We added the variable "AttackCat", which indicates whether or not there was a shark attach on any given day in the dataset.

![download](https://user-images.githubusercontent.com/48931690/144726243-d19d909f-e07e-4711-9afd-bd94a5bf0597.png)

After looking at the correlation matrix, we removed the variables that had a > 0.5 correlation. These variables were **"tmpc", "ATMP", "dwpc", "feel", "relh",** and **"GST"**. We created another correlation matrix to virew the remaining variables.

![download](https://user-images.githubusercontent.com/48931690/144726292-944b58e6-ed7b-49fe-ba7b-a94ff5586290.png)

## Data Modeling

We decided to model the data using both unsupervised and supervised techniques using k-means clustering, logistic regression, and a support vector machine (SVM). 

### K-Means Clustering

Our first data model uses k-means clustering, with the goal of uncovering significant clusters that might indicate the presence of a sea breeze.

### Logistic Regression

We also used logistic regression to model the relationships between our independent variables (including **"SBI"**) and our target variable, **"AttackCat"**.

For our model we used the variables in the dataset below including land wind direction, land wind speed, sea wind direction, sea wind speed, pressure, water temperature, and sea breeze indicator to predict our **"AttackCat"** shark attack dependent variable.

![LogData](https://user-images.githubusercontent.com/92108275/144726663-0e7bec02-2116-436c-9ccc-ddfd54925bcb.PNG)

We scaled our data using a standard scaler, then created our logistic regression model and achieved the following results:

![Log_first_cm](https://user-images.githubusercontent.com/92108275/144726792-d1ae06cc-e234-4e5c-bcae-81b7699f4dc2.PNG)

We can see from the above that the model is quite accurate at .97 but it is only accurate at predicting when there is no shark attack, it never correctly predicts a shark attack. THis is because the data is imbalanced; therefore, we need to look into undersampling and/or oversampling our data.

We then used a pipeline which included both undersampling and oversampling to solve our problem and came up with the following results: we had an overall accuracy score of .810 and an AUC score of .787, which we found to be a lot better, considering the model was now correctly predicting 13 of the 19 total shark attacks in the augmented dataset.

![Log_2nd_cm1](https://user-images.githubusercontent.com/92108275/144726976-953fd8ca-b134-4861-982e-04f49666b7b6.PNG)

Additionally, we looked at the coefficients of our model to test and see if our calculated Sea Breeze Indicator was, in fact, making any sort of difference as we hoped. We then found the top 7 features of the dataset by coefficient.

![top7ft](https://user-images.githubusercontent.com/92108275/144727101-daaa86bd-74e1-46a0-8aa7-9a94490b4be2.PNG)

This graph shows that **"SBI"** is the top feature in predicting a shark attack, with a highly negative coefficient which would make sense, as lower SBI leads to a higher chance of sea breeze. This seems to prove that our Sea Breeze Indicator that we created is indeed a strong predictor of shark attacks as we hypothesized.

### Support Vector Machine

We also used a Support Vector Machine (SVM) to model the relationship between our independent variables and **"AttackCat"**. The initial baseline model, based on scaled data, gave the same results as the logistic regression, again due to imbalanced data. We again found that a pipeline of under- and oversampling gave the best result, which gave an accuracy of 0.783 and an ROC AUC score of 0.781.

![image](https://github.com/Forrestjohnson2000/6162-Seabreeze/blob/main/Images/SVM%20Matrix.png)

We can also see that **"SBI"** has the largest absolute value coefficient. As the variables as scaled, this tells us that it may play a significant role in the overall model.

![image](https://github.com/Forrestjohnson2000/6162-Seabreeze/blob/main/Images/SVM%20Coefficients.png)

The permutation importance is the decrease in a model score when a single feature value is randomly shuffled. **"SBI"** has the highest permutation importance, followed by **"PRES"**. This leads us to believe that these may be the most important features in this model.

![image](https://github.com/Forrestjohnson2000/6162-Seabreeze/blob/main/Images/SVM%20Importance.png)

## Conclusion

Throughout this project, we encountered many unexpected issues and setbacks that caused our work to be much more complex than we had initially anticipated. Some of the most significant problems we experienced and resolved include:

Problem: Our initial plan was to find an additional dataset or record of sea breeze occurrences to use as a target variable for our data models. We researched thoroughly for a dataset that included this information, as well as reached out to a domain expert, but we were unable to find a data source.
Solution: We were given a journal article by the domain expert that gave us the framework for creating our own sea breeze occurrence indicator. This required us to do additional data collection and several complicated calculations using our sourced variables. The end result, our Sea Breeze Index (SBI) variable, is still imperfect, due to our lack of domain expertise. However, we were able to move forward with data modeling using this measure.

Problem: Our ultimate goal was to use our sea breeze index to determine whether there is a relationship between the occurrence of a sea breeze and shark presence in coastal waters. For shark presence, we used data that was previously collected for Dr. Pamela Thompson’s class. Due to the format of this dataset, we had difficulty merging it with our own in a way that preserved the relevant and accurate data within each dataset.
Solution: We were able to resolve this problem by grouping the sea breeze dataset on a daily basis, finding the mean values for each day (at the times 14, 15, and 16), before merging the datasets.

Problem: The shark attack data was highly imbalanced. Our baseline models could achieve a 97% accuracy by predicting 0 (no shark attack) every time.
Solution: We implemented a pipeline of under- and oversampling to improve our logistic regression and SVM models.

We found that this project was unlike any of our previous work in data science and machine learning, primarily due to its intensive domain knowledge requirements and complex datasets. **However, based on all of the models we created that are described above, we were able to find results that supported the hypothesis that the presence of a sea breeze is correlated with increased shark presence in an area, represented by the occurrence of a shark attack.**
