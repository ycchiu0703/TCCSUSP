# Telephone-Company-Customers-Stop-Using-Service-Prediction
5.6k training data over 6 classes

## Data Analysis

1. There are 5634 training sets in total, but only 4226 of them have labels
2. Regardless of Train, test, the number of missing values for each feature is similar
3. In Train, the proportion of the final classification is seriously biased

![images](https://github.com/ycchiu0703/Telephone-Company-Customers-Stop-Using-Service-Prediction/blob/main/images/Data%20distribution.jpg)

4. All data are distributed on the West Coast of the United States

![images](https://github.com/ycchiu0703/Telephone-Company-Customers-Stop-Using-Service-Prediction/blob/main/images/Total_Heatmap.jpg)

5. From the heat map of each category, there are many categories in Los Angeles, but because Los Angeles is originally a densely populated area, it is normal

![images](https://github.com/ycchiu0703/Telephone-Company-Customers-Stop-Using-Service-Prediction/blob/main/images/Each_Category_Heatmap.jpg)

## Feature Engineering

### Feature 3~5： Age, Under 30, Senior Citizen

We believe that there is an obvious consideration relationship between the three features of 'Age', 'Under 30' and 'Senior Citizen', such as:

* If 'Age' < 30, 'Under 30' should be "Yes" and 'Senior Citizen' should be "No"
* If 65 > 'Age' >= 30, then 'Under 30' should be "No", and 'Senior Citizen' should also be "No"
* If 'Age' >= 65 , then 'Under 30' should be "No", and 'Senior Citizen' should be "Yes" After observation, we found that 'Age', 'Under 30', 'Senior Citizen' three The features all have missing parts of each other, so we use the above features to make up the value:
  1. When 'Age' < 30 and 'Under 30' is a missing value, fill in the missing value: "Yes"
  2. When 'Age' >= 65 and 'Senior Citizen' is a missing value, fill in the missing value: "Yes"
  3. When 'Under 30' == "Yes" and 'Age' is a missing value, fill in the missing value: the mean of 'Age' < 30
  4. When 'Senior Citizen' == 'Yes' and 'Age' is a missing value, fill in the missing value: the mean of 'Age' >= 65
  5. When 'Under 30' == "No' and 'Senior Citizen' == "No" and 'Age' is a missing value, add the missing value: 65 > 'Age' >= 30 mean
  6. After the above complements 1~5, all the remaining missing values of 'Age' will be filled up: the mean of all 'Age' is calculated to be about 46.67527672739572
  7. Since 65 > all mean of 'Age' >= 30, the corresponding missing values of 'Under 30' and 'Senior Citizen' are filled with "No"

### Feature Importance of Xgboost

* 'Population' has a significant impact on the prediction of xgboost. In fact, after repeatedly filling in the missing values and adjusting the model, the importance of 'Population' still remains in the first position, which shows that 'Population' has a great influence on the prediction of xgboost
* The importance of 'City' for xgboost prediction is also in the top ten. After repeated filling and model adjustment, 'City' still has a certain degree of influence
* Although the two Features 'Country' and 'State' are not as important to xgboost prediction as 'Population' and 'City', they are still on the list. We believe that perfecting the missing values of these two Features will allow xgboost to give more accurate prediction results.
* Therefore, we decided to try to use other Features to fill the gaps of the above features. It can be inferred that 'Country', 'State', 'City' and 'Population' can basically be obtained from 'Zip Code' or 'Lat' Long' to get the corresponding information. At the same time, we found that we can get the corresponding 'Zip Code' from 'Lat Long' to make it more complete, so as to fill in the missing values of other features.



