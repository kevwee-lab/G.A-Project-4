# ![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png) WNV Classification Modelling

## Problem Statement

In view of the recent epidemic in Windy City of Chicago state affecting the state population, we aim to build a classifier model to make predictions on the possibility of West Nile Virus occurence on various locations of interest, which could be used to aid the deployment of pesticides in the fight for public health and safety.The model would be build using collected data related to mosquito population from the surveillance and control system setup by Deparment of Public Health.
In addition, a cost-benefit analysis would be conducted on the cost benefits for the use of pesticides as a response in managing the epidemic. 

## Executive Summary

A high-level overview of the report is EDA analysis, data merging, feature engineering, modelling, cost-benefit analysis and finally the evaluation and conclusions.   

Some features were dropped along the way (traps and spraying data for example) and some new features were created. From background research, relative humidity is considered as an indicator, and can be calculated from the weather data provided.
The time dimension was controlled by using lags on related subset of features. There is also a clear relation observed between the lagged temperature and the presence of WNV along week number (refer to report EDA for further details).

After the modelling was done, the models were evaluated based on ROC-AUC (to get as accurate predicted possibilities as possible) and recall score. The recall score is very important as we want to reduce the number of false negatives as far as possible (predicting no WNV when WNV is present). The cost is too high since lives are at stake! Models fitted on SMOTED data have far better recall scores than the rest, which were probably affected heavily by the class imbalance. Although the accuracy scores were also lower, minimising false negatives is of greater importance. Ultimately, the best model selected is the GradientBoost model which was trained on SMOTE-d data.

To determine the cost effectiveness of spraying, below factors were considered:  
Direct costs: These would mainly include the cost of procuring chemicals required for spraying.   
Indirect costs: These would be productivity/economic costs incurred from people seeking short/long term treatment for the west nile virus.   

## Data Dictionary

Spray data: GIS data of spraying efforts in 2011 and 2013

|Feature|Python data Type|Description|
|---|---|---|
|**Date**|*String*|Date of spray|
|**Time**|*String*|Time of spray|
|**Latitude**|*float*|Latitude of spray location|
|**Longitude**|*float*|Longitude of spray location|



Weather data: Weather data from 2007 to 2014

|Feature|Python data Type|Description|
|---|---|---|
|**Station**|*Integer*|Station ID|
|**Date**|*String*|Date of the weather data|
|**Tmax**|*Integer*|Max temperature in Fahrenheit|
|**Tmin**|*Integer*|Min temperature in Fahrenheit|
|**Tavg**|*Integer*|Average temperature in Fahrenheit|
|**Depart**|*Integer*|Temperature departure from normal|
|**DewPoint**|*Integer*|Average Dew Point in Fahrenheit|
|**WetBulb**|*Integer*|Average Wet Bulb temperature in Fahrenheit|
|**Heat**|*Integer*|Absolute temperature difference of average temperature (Tavg) from base 65 deg Fahrenheit for Tavg >=65 (season begins with July)|
|**Cool**|*Integer*|Absolute temperature difference of average temperature from base 65 deg Fahrenheit for Tavg <=65|
|**Sunrise**|*String*|Calculated Sunset timing in 24H format|
|**Sunset**|*String*|Calculated Sunrise timing in 24H format|
|**CodeSum**|*String*|Weather Type represented in codes|
|**Depth**|*Integer*|Snow Depth in inches|
|**Water1**|*Integer*|Amount of water equivalent from melted Snow|
|**SnowFall**|*Float*|SnowFall in precipitation|
|**PrecipTotal**|*Float*|Water precipitation|
|**StnPressure**|*Float*|Average Station Pressure|
|**SeaLevel**|*Float*|Average Sea Level Pressure|
|**ResultSpeed**|*Float*|Resultant Wind Speed|
|**ResultDir**|*Integer*|Resultant Wind Direction in Degrees|
|**AvgSpeed**|*Float*|Average Wind Speed|
              
Training data: The training set consists of data from 2007, 2009, 2011, and 2013.

|Feature|Python data Type|Description|
|---|---|---|
|**Date**|*String*|Date which the WNV test is performed|
|**Address**|*String*|Approximate address of the location of trap. |
|**Species**|*String*|Named species of mosquitoes|
|**Block**|*String*|Block number of address for the location of the trap|
|**Street**|*String*|Street name|
|**Trap**|*String*|Trap ID|
|**AddressNumberAndStreet**|*String*|Approximate address returned from GeoCoder|
|**Latitude**|*Float*|Latitude returned from Geocoder|
|**Longitude**|*Float*|Longitude returned from Geocoder|
|**AddressAccuracy**|*Integer*|Accuracy returned from GeoCoder|
|**NumMosquitos**|*Integer*|Number of mosquitoes caught in this trap|
|**WnvPresent**|*Integer*|Whether West Nile Virus was present in these mosquitos. 1 means WNV is present, and 0 means not present. 
|

Testing data: Dataset to predict the test results for 2008, 2010, 2012, and 2014.

|Feature|Python data Type|Description|
|---|---|---|
|**Id**|*Integer*|ID of the record|
|**Date**|*String*|Date which the WNV test is performed|
|**Address**|*String*|Approximate address of the location of trap. |
|**Species**|*String*|Species of mosquitoes|
|**Block**|*String*|Block number of address for the location of the trap|
|**Street**|*String*|Street name|
|**Trap**|*String*|Trap ID|
|**AddressNumberAndStreet**|*String*|Approximate address returned from GeoCoder|
|**Latitude**|*Float*|Latitude returned from Geocoder|
|**Longitude**|*Float*|Longitude returned from Geocoder|
|**AddressAccuracy**|*Integer*|Accuracy returned from GeoCoder|


## Methodology

The methodology used during the modelling process can be described as follows:

**1. Baseline**
- Apply baseline classifier to set the baseline scores for metrics that we _must_ beat

**2. Modelling with Base Features**
- Run various models with base set of 49 features (obtained after final EDA & feature engineering from before)

**3. Feature Expansion with Polynomial Feature Generation**
- Try to improve metrics by increasing number of features
- Use PolynomialFeatures to generate polynomial and interaction features to uncover new patterns in the data
- 2 methods to try and reduce noise/dimensionality:
    - Principal Component Analysis (PCA)
    - Filtering features by correlation with target
- Run models for each set of features

**4. Addressing Class Imbalance with SMOTE**
- Try to improve recall score & address class imbalance with SMOTE
- Run models with base set of 49 features, but fit to SMOTED dataset

**5. Model Evaluation**
- Compare the top 2 models from each method
- Evaluate metrics, keeping in mind problem statement
- Selecting best model based on balance of ROC-AUC score & recall
    - Evaluate most important features
    - Prepare Kaggle submission

For each step, we carried out the relevant preprocessing & modelling steps, gridsearching over several hyperparameters for each model to achieve the best results. The metric we optimised for was **ROC-AUC** score for the holdout set. We also gathered other metrics including F1 score, Precision, Recall and Accuracy.

**<u>Models</u>**

We considered the following 5 models throughout our process:
1. Logistic Regression
    - Simple & effective for binary classification problems
2. Gradient Boost
    - Produces a prediction model in the form of an ensemble of weak prediction models, typically decision trees, using gradient descent algorithms
3. XG Boost
    - Similar to gradient boost, but it uses a more regularised model formalisation to control over-fitting
4. Random Forest
    - Constructs decision trees at training time and outputs the class that is the mode of the classes
5. Extra Trees
    - Similar to Random Forest. However, the splits of the trees in the Random Forest are deterministic. It is random for extratrees
    
## Best Fit Model 


| Method        | Features |         Classifier         | Train ROC-AUC   Score | Holdout ROC-AUC   Score |    F1    | Precision |  Recall  | Accuracy | Target (Avg of   ROC-AUC & Recall) |
|---------------|----------|:--------------------------:|:---------------------:|:-----------------------:|:--------:|:---------:|:--------:|:--------:|:----------------------------------:|
| SMOTE         | 49       | GradientBoostingClassifier | 0.958587              | 0.842473                | 0.282132 | 0.179641  | 0.656934 | 0.819898 | 0.749704                           |
| SMOTE         | 49       | XGBClassifier              | 0.984198              | 0.847744                | 0.277154 | 0.186398  | 0.540146 | 0.848211 | 0.693945                           |
| Filtering     | 938      | XGBClassifier              | 0.942442              | 0.855403                | 0.16     | 0.923077  | 0.087591 | 0.950452 | 0.471497                           |
| Base Features | 49       | XGBClassifier              | 0.928438              | 0.870347                | 0.09589  | 0.777778  | 0.051095 | 0.948093 | 0.460721                           |
| Base Features | 49       | GradientBoostingClassifier | 0.891929              | 0.869525                | 0.055944 | 0.666667  | 0.029197 | 0.946913 | 0.449361                           |
| PCA           | 50 (PC)  | GradientBoostingClassifier | 0.907362              | 0.845655                | 0.054422 | 0.4       | 0.029197 | 0.94534  | 0.437426                           |
| Filtering     | 816      | XGBClassifier              | 0.918562              | 0.857152                | 0.028369 | 0.5       | 0.014599 | 0.946127 | 0.435876                           |
| PCA           | 50 (PC)  | RandomForestClassifier     | 0.940975              | 0.846118                | 0.028169 | 0.4       | 0.014599 | 0.945733 | 0.430359                           |


We can see that the models fitted on SMOTED data have far better recall scores than the rest, which were probably affected heavily by the class imbalance. Although the accuracy scores were also lower, minimising false negatives is of greater importance to us. Ultimately, we selected the GradientBoost model which trained on SMOTE data.
    
## Conclusions

<u>**Classification Model**</u>

The final model we built was successful in answering our problem statement (predictions on the possibility of West Nile Virus occurence on various locations in Chicago). Unfortunately, this model does not directly achieve the bigger goal of eradicating the West Nile Virus from Chicago. Most of the features in the model are beyond our control (such as weather), so inference would not directly help in suggesting ways to eradicate the virus.

However, this does not mean that the model is not helpful at all. We can utilise the model to improve existing efforts in eradicating the virus, such as spraying.

<u>**Optimize Spraying Parameters**</u>

Based on our findings above, we concluded that the current spraying efforts have not been very effective in controlling or eliminating the issue of West Nile Virus. In fact, [BeyondPesticides](https://www.beyondpesticides.org/resources/mosquitos-and-insect-borne-diseases/documents/the-truth-about-mosquitoes,-pesticides-and-west-nile-virus) also urges that spraying pesticides affects the public health and environment, in addition to not reducing WNV incidences. This is also supported by CDC, stating that spraying pesticides is usually the least efficient mosquito control technique [CDC](https://www.beyondpesticides.org/resources/mosquitos-and-insect-borne-diseases/documents/the-truth-about-mosquitoes,-pesticides-and-west-nile-virus).

Nevertheless, it could be improved in terms of both cost effectiveness and efficacy by taking into account the factors below:

1. Location
    - Spraying can be targeted at locations where our model predicts to have a high probability of the occurence of the virus
2. Time of year  
    - Spraying efforts can be ramped up at the appropriate time of the year (July to August) to reduce mosquito population
3. Spray and wind direction
    - Research shows that spraying along and against the wind will incur different efficacy. However, this needs further research as this can be a topic on its own.