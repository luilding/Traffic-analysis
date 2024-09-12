# Task 2


The object of task 2 within use case 10 was: "Identify high-speed zones and assess compliance with speed limits to improve road safety."

To achieve this, I undertook a number of tasks to identify areas of where speed issues were noted.

Two datasets were utilised from Melbourne Open Data. The "traffic-count-vehicle-classification-2014-2017.csv" file and "road-corridors.csv" file. 

Links here:

https://data.melbourne.vic.gov.au/explore/dataset/traffic-count-vehicle-classification-2014-2017/information/

https://data.melbourne.vic.gov.au/explore/dataset/road-corridors/information/


These were linked on the road_segemnt and seg_id columns. The data was cleaned and merged. Which has been uploaded to the Datapreprocessing pipeline folder.

From this, the "merged_traffic_data.csv" was created which was utilised for this task.

To tackle this task I wanted to utilise a machine learning model to discover and predict speeding incidents.

I generated a number of new featurs to capture:
- the day of the week
- month
- seasons
- hour of the day
- time windows (Morning, Afternoon, Evening, Night)
- a binary rush hour feature, true if it was within the hours of 7-9am and 4-6pm.
- I also combined a number of vehicle classes 3-12 were combined into one since they all represented heavy vehicles. This was because many of the vehicle class columns were sparse in the amount of data. This information was gathered from the accommpanied data dictionary with the Traffic Count Vehicle Classification 2014-2017 data.

I also generated an 'is_speeding' feature as our target variable. This was calculated from the 'maximum_speed' and 'speed_limit' features.

Relevant features were then selected as our x variables, to predict our target is_speeding variable.

A number of models were utilised (Random Forest, XGBoost, Logistic Regression) to compare performance and find the best fit for the data. 
I also generated a variety of model parameters to perform a grid search for the best features.

The best resulting parameters for each model were:
- Random Forest Best parameters: {'max_depth': 20, 'min_samples_leaf': 1, 'min_samples_split': 10, 'n_estimators': 100}

- XGBoost Best parameters: {'learning_rate': 0.1, 'max_depth': 7, 'n_estimators': 200}

- Logistic Regression Best parameters: {'C': 0.1, 'penalty': 'l1', 'solver': 'saga'}

Below shows a comparison of the each model with the best parameters found in the gridsearch:

![](./model_comparison.jpg)

As can be seen, the Random Forest and XGBoost models performed the best. The Logistic Regression model performed weaker but still with reasonable performance.

XGB received slightly higher results than Random Forest, with an ROC AUC of 0.9135 compared to Random Forest's 0.9101. As a result the XGB was the model utilised going forward.

Regarding it scores, the XGB model performed well, with an accuracy meaning 83% of predictions were correct. Its ROC AUC of 0.9135 indicates strong ability to distinguish between the classes (True vs. False), suggesting good overall classification performance. The cross-validation mean of 0.8325 shows that the model's performance is stable across different data splits.

Feature importance was performed for the XGB, showing that vehicle_class_1 (0.2638) and suburb (0.2378) are the most influential factors in predicting the outcome of speeding contributing to the model's decisions. Month (0.1333) and time_window_encoded (0.0735) also play slight roles, while features like is_weekend (0.0000) have no impact. 

The feature importances are relatively low, with the highest being only 0.2638, indicating that no single feature is strong in the predictions. This can however suggests the model relies on a combination of features.

![](./feature_importance.jpg)

The strongest XGB model was then used to predict speeding probabilties on the test data. A few key areas were focused on for results: high risk suburbs, times (by hour), and days of the week.

We can see that the Parkville was found to be the suburb most at risk of speeding with a 65.64% chance of speeding. North Melbourne, Docklands, South Yarra also were found to have greater than 50% chance of speeding. Suburbs such as Southbank and Carlton North were found to have a lower risk of speeding out of the high risk suburbs, receiving 15 and 2.9% respectively.

![](./suburbs.jpg)

For hours, 2 PM was considred the most likely with a 46.86% chance of speeding. The results show there was considered to the highest risk at 4-6pm and at 6 and 9 am.

![](./hours.jpg)

Wednesday was found to be the most likely day of the week to speed with a 48% chance. This was folloed by Monday and Sunday, whereas Thursday was considered the least likely.

![](./days.jpg)


To give visual insights, the predictions on different areas in each suburbs on the test data can be seen in this map:
You can view the interactive map showing high-risk suburbs [here](./high_risk_areas.html).