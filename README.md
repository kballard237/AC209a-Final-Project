# AC209a Final Project 

##### Kyra Ballard, Kaela Nelson, Lauren Baker

We completed project A: measuring our environment. For this project, we collected data from two sensors placed inside residences. 


## Datasets 

We collected our data using sensors that capture their surrounding features: light (cadmium sulfide photoresistor AFEC), motion (passive infrared detector with 150 cm over 100◦ range; records binary 0 or 1 pulses with 2sec resolution), temperature (-20 to 60 degrees C), humidity (20 to 95% RH with resolution 0.1%), and pressure (300 - 1100 hPa ±1 hPa). We placed Sensor 1 in the foyer of one student’s apartment, and Sensor 2 in the bedroom of another student’s apartment (both students in this group). The differences in these locations are as follows: Sensor 1 is placed in an apartment with multiple residency and building-regulated heat, and Sensor 2 is placed in an apartment with single residency and tenant-regulated heat.

For each sample and corresponding sensor, we specified both the sample and duration rates, as recorded below. Our intuition behind this is that light and motion tend to fluctuate more frequently, so we set their sample rate to collect within smaller windows of time. However, temperature, humidity, and pressure fluctuate less frequently over a small time interval, so we set their sample rate to be higher (note that these environmental variables were recorded via one sensing "head" of the sensor and thus required the same sampling frequency). The duration of our data collections lasted between 24-99 hours (99 hour limit). Details on collection rates are included below.

### Sensor 1 

Location: Apartment 1 foyer

* Light:
    * Sample rate: 5 min
    * Duration Rate: 24-72 hours
* Motion:
    * Sample rate: 1 sec
    * Duration Rate: 24-72 hours
* Temperature/Pressure/Humidity:
    * Sample rate: 10 min 
    * Duration Rate: 24-72 hours  
    * Note: The first three collections used a sample rate of 30 min to have more data points, as the team originally thought that the building-regulated temperature, rather than tenant-regulated temperature, could be an interesting variable to investigate.

### Sensor 2

Location: Apartment 2 bedroom

* Light:
    * Sample rate: 5 min
    * Duration Rate: 99 hours
* Motion:
    * Sample rate: 1 sec
    * Duration Rate: 99 hours
* Temperature/Pressure/Humidity:
    * Sample rate: 30 min
    * Duration Rate: 99 hours

Note: Sensor 2 utilized the maximum duration of recording (99 hours), while Sensor 1 used variable durations due to more frequent collection troubles (needed to perform shorter collections to confirm data was indeed recording). Because there were complications on some of the sample collections, our data has some missing values from some dates in between some collection times. We dealt with this by omitting these sample collections from our data set. We included a separate section with detailed Sensor 1 issues at the end of this report.

## Preliminary Data Analysis (Milestone 3) 

Each sensor stores the data as separate files for each feature in an SD card that we then uploaded and saved on our computers. These files were saved as text files, so we wrote functions that read in these text files and created a dataframe of each feature. In addition, when we collected the data, we recorded each sample’s start date and time. Based on this, we developed a method to create a pandas date time object of the collection start date, and then interpolated more dates to match our collection sample frequency for each feature. Lastly, to handle missing values for this milestone, we omitted any samples that failed to record. Thus, our data set only includes data for times that the sensor actually recorded. We decided not to use interpolation for missing data as the patterns of data are very variable and the duration of missing data is very long (not confident in the accuracy of the interpolated data). 

Initial data visualizations for Milestone 3 indicated that Sensor 1 had the most interesting trend in light, where we could see a pattern of when natural light fills the apartment.

**Insert light vs. time of day**

**Insert light calendar**

After analyzing these two plots, we decided that it would be interesting to look at the average amount of light per hour over the collection sample period. We then overlaid this average light over our plot.

**Insert average light**

As you can see above, we can infer that sunrise occurs around 5 am and sunset occurs around 5pm. It is even more interesting to see that after sunset, we could potentially classify artificial versus natural light, because if light is less than 4095 after sunset, then it would have to be artificial light. This motivated us to adjust our research question to be based on predicting artificial or natural light. Thus, our newly revised project question is: **“Based on indoor environmental features, is the light that the sensor catches artificial or natural?”** In addition, we are interested in features are most indicative of the light is artificial or natural. 

### Decision Tree 

We decided to fit a simple decision tree model to our training data because our outcome variable has 3 categories: light off, natural light, and artificial light, and and it would not make sense to perform a linear or logistic regression. For this model, we created a baseline data set with the following features:

* Light
* Day: day of the month
* Hour: hour of the day (0-23)
* Light_on: 
    * 0 if light is off (determined by if Light = max(Light))
    * 1 if light is on (determined by if Light < max(Light))
* Light_source:
    * 0 if light is on and it is artificial
    * 1 if light is on and it is natural
    * 2 if light is off

From these variables, we have that light, day, hour, and light_on are our predictor variables and light_source is our outcome variable that we wish to predict. To determine the best tree depth, we chose a range of tree depths from 1 to 5 and evaluated the performance and standard deviations for each depth using 5-fold cross-validation. We plotted the estimated cross val mean +/- 2 standard deviations for each depth, along with the train and test scores. 

**Insert accuracy variation graph FOR TRAIN AND TEST**

After analyzing the results from the above plot, we found that a tree depth of 2 returned the highest accuracy score (without overfitting to 100%). We then fit a simple decision tree with a tree depth of 2, based on this plot and get the following results:

* Decision Tree of Depth 2, Accuracy of Training Set: 94.95%
* Decision Tree of Depth 2, Accuracy of Testing Set: 94.47%

These high accuracy scores would be expected in this case because we have a limited number of predictors and the data already seems to have some kind of trend/pattern to it (i.e. change in light over time of day).

### Application of Light Classification Model

Predicting whether light is artifical, natural or off could be useful for the development of a lamp/house lighting that emits a natural pattern of light throughout the day to mirror natural light in a given space and match a user's light needs in that space. Additionally, quanitifying and classifying light may be of interest in the real estaste business, as having rooms with certain durations and intensities of light may be of interest to potential buyers.

### Sensor 1 Issues

Sensor 1 experienced several collection issues since sensor distribution in mid October. Please see a section at the end of this report with detailed descriptions and plots of the issues below. Unfortunately the most recent empty collection/data issues presented themselves right before Thanksgiving break and were not able to be resolved due to staff holiday. Based on all of these data issues from Sensor 1 following the Milestone 3, we decided to proceed with exploring Sensor 2 data for the remainder of this project. Sensor 2 did not experience any data collection trouble for the duration of the project. 


## Further Data Exploration with Sensor 2 

**Insert plot with all data (avg/count per hour) for each day of the week**

**Insert scatter matrix*

*The hourly fluctuation in light and motion visually line up well, while temp/press/hum are close to constant throughout day and across days.*

* We see that there is no light when there is no motion, hinting at some kind of correlation in this case and supporting that motion-sensing lighting may be beneficial (i.e. if Kaela forgets to turn off her lights and her sensor doesn't sense any motion, it will turn the light off and save her electric bill). 

*Consider adding logistic regression here using outcome variable as light on or off and having light and motion as predictors*

## Predicting sleep

We added additional variables to the data set:
* number of problem sets due per day
* number of hours slept during previous night (assigned to "next" day)

*Use interaction of light and motion
*Add polynomial terms


### Linear Regression

### Logistic Regression

### Bagging (cross val) / Random Forest

### Application of Sleep Quality Classification Model


## Details on Sensor 1 Issues

Sensor 1 experienced numerous issues with early collections (i.e. necessity of hard reset or else no data written to SD card) led to several empty collections but these were able to be resolved with help from Evan in the Active Learning Labs. Sensor 1 was able to collect 2 full weeks of consecutive data to prepare for models presented in Milestone 3/the first section of this report. 

Following this streak of good fortune, Sensor 1 had a 72 hour empty collection (not due to lack of hard reset) followed by erractic sensor display and resultant inconsistencies in the subsequent recorded data. Specifically, there was an issue with the light and motion sensing during the collection immediately after the randomly empty collection. The motion data contained values from the light data (i.e. there were instances of 4095 and other values seen in the light data rather than only the expected 0's and 1's) which was peculiar. Lengths of these data were opposite of the expected lengths based on user-input frequency, and while we originally surmised that the user mistakenly input frequencies for light and motion, it seems that there was something going wrong with these sensors based on the data discrepancies. 

**Insert plot**

Similarly, during this and subsequent collections, temperature and humidity had all values recorded as 0, while pressure recorded reasonable values. This is interesting as apartment temparture was indeed above freezing during these collections days and also interesting that these three outcome variables are recorded on the same sensor head. 

**Insert plot**





