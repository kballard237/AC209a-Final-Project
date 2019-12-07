# AC209a Final Project 

##### Kyra Ballard, Kaela Nelson, Lauren Baker

We completed project A: measuring our environment. For this project, we collected data from two sensors placed inside residences. 

## Datasets 

We collected our data using sensors that capture their surrounding features: light (cadmium sulfide photoresistor AFEC), temperature (-20 to 60 degrees C), motion (passive infrared detector with 150 cm over 100◦ range; records binary 0 or 1 pulses with 2sec resolution), humidity (20 to 95% RH with resolution 0.1%), and pressure (300 - 1100 hPa ±1 hPa). We placed sensor 1 in the foyer of one student’s apartment, and sensor 2 in the bedroom of another student’s apartment (both students in this group). The differences in these locations are as follows: sensor 1 is placed in an apartment with multiple residency and unregulated heat, and sensor 2 is placed in an apartment with single residency and regulated heat.

For each sample and corresponding sensor, we specified both the sample and duration rates, as recorded below. Our intuition behind this is that light and motion tend to fluctuate more frequently, so we set their sample rate to collect within smaller windows of time. However temperature, humidity, and pressure fluctuate less frequently over a small time interval, so we set their sample rate to be higher. Our duration rates lasted between 24-99 hours (99 hour limit). Details on collection rates are included below.

### Sensor 1 

Location: Apartment 1 foyer

* Light:
    * Sample rate: 5 min
    * Duration Rate: 24-72 hours
* Motion:
    * Sample rate: 1 sec
    * Duration Rate: 24-72 hours
* Pressure:
    * Sample rate: 10 min (changed from 30 min after first few collections to have more data points)
    * Duration Rate: 24-72 hours
* Temperature:
    * Sample rate: 10 min (changed from 30 min after first few collections to have more data points)
    * Duration Rate: 24-72 hours
* Humidity:
    * Sample rate: 10 min (changed from 30 min after first few collections to have more data points)
    * Duration Rate: 24-72 hours

### Sensor 2

Location: Apartment 2 bedroom

* Light:
    * Sample rate: 5 min
    * Duration Rate: 99 hours
* Motion:
    * Sample rate: 1 sec
    * Duration Rate: 99 hours
* Pressure:
    * Sample rate: 30 min
    * Duration Rate: 99 hours
* Temperature:
    * Sample rate: 30 min 
    * Duration Rate: 99 hours
* Humidity:
    * Sample rate: 30 min 
    * Duration Rate: 99 hours

Note: Because there were complications on some of the sample collections (i.e. SD card resetting issues so that new data was recorded), our data collection has some missing values from some dates in between some collection times. We dealt with this by omitting these sample collections from our data set.

### Exploratory Data Analysis 

Each sensor stores the data as separate files for each feature in an SD card that we then uploaded and saved on our computers. These files were saved as text files, so we wrote functions that read in these text files and created a dataframe of each feature. In addition, when we collected the data, we recorded each sample’s start date and time. Based off of this, we wrote a method to create a pandas date time object of the collection start date, and then interpolated more dates to match our collection sample frequency for each feature. Lastly, to handle missing values for this milestone, we omitted any samples that failed to record. Thus, our data set only includes data for times that the sensor actually recorded. In the future, we plan to adjust how to handle this missing data by using the most recent measurements to fill in the missing values, given that the missing time period is no longer than 5 hours. This is because we feel that interpolating for longer than this will give inaccurate results.

After handling this data, we then visualized each feature in a few different ways (hourly, weekly, and daily). We found that out of all the features, sensor 1 had the most interesting trend in light, where we could see a pattern of when natural light fills the apartment. We see this below.

**Insert light vs. time of day**

**Insert light calendar**

After analyzing these two plots, we decided that it would be interesting to look at the average amount of light per hour over the collection sample period. We then overlaid this average light over our plot.

**Insert average light**

As you can see above, we can infer that sunrise occurs around 5 am and sunset occurs around 5pm. It is even more interesting to see that after sunset, we could potentially classify artificial versus natural light, because if light is less than 4095 after sunset, then it would have to be artificial light. This motivated us to adjust our research question to be based on predicting artificial or natural light. Thus, our newly revised project question is: **“Based on indoor environmental features, is the light that the sensor catches artificial or natural?”** In addition, we are interested in features are most indicative of the light is artificial or natural. 

### Baseline Model: Decision Tree

We decided to fit a simple decision tree model to our training data because our outcome variable has 3 categories: light off, natural light, and artificial light. For this model, we created a baseline data set with the following features:

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

From these variables, we have that light, day, hour, and light_on are our predictor variables and light_source is our outcome variable that we wish to predict. To determine the best tree depth, we chose a range of tree depths from 1 to 5 and evaluated the performance and standard deviations for each depth using 5-fold cross-validation. We plotted the estimated mean +/- 2 standard deviations for each depth. 

**Insert accuracy variation graph**

After analyzing the results from the above plot, we found that a tree depth of 2 returned the highest accuracy score (without overfitting to 100%). We then fit a simple decision tree with a tree depth of 2, based on this plot and get the following results:

* Decision Tree of Depth 2, Accuracy of Training Set: 94.95%
* Decision Tree of Depth 2, Accuracy of Testing Set: 94.47%

These high accuracy scores would be expected in this case because we have a limited number of predictors and the data already seems to have some kind of trend/pattern to it (i.e. change in light over time of day).

### Extension 

For our final project result, our goal is to extend our project potentially in the following ways:

* Determine which of our existing predictors are most important for predicting our outcome.
    * Note: all of these will be aggregated based on hour: motion (count frequency per hour), temperature, humidity, light, and pressure (average). We can choose different frequency to aggregate on based off of the results of the baseline model.
* Implement more complex models (i.e. Bagging Random Forest, and XGBoost)
* Include more feature engineered predictor variables (i.e. recorded indoor temperature, outdoor temperature)
* Incorporate sensor 2’s data as test data for our models.
