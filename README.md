# Telephone-Company-Customers-Stop-Using-Service-Prediction
5.6k training data over 6 classes

## Data Analysis

1. There are 5634 training sets in total, but only 4226 of them have labels.
2. Regardless of Train, test, the number of missing values for each feature is similar.
3. In Train, the proportion of the final classification is seriously biased.

<!-- ![images](https://github.com/ycchiu0703/Telephone-Company-Customers-Stop-Using-Service-Prediction/blob/main/images/Data%20distribution.jpg) -->
<img src="https://github.com/ycchiu0703/Telephone-Company-Customers-Stop-Using-Service-Prediction/blob/main/images/Data%20distribution.jpg" width="50%" height="50%">

4. All data are distributed on the West Coast of the United States.

![images](https://github.com/ycchiu0703/Telephone-Company-Customers-Stop-Using-Service-Prediction/blob/main/images/Total_Heatmap.jpg)

5. From the heat map of each category, there are many categories in Los Angeles, but because Los Angeles is originally a densely populated area, it is normal.

<!-- ![images](https://github.com/ycchiu0703/Telephone-Company-Customers-Stop-Using-Service-Prediction/blob/main/images/Each_Category_Heatmap.jpg) -->
<img src="https://github.com/ycchiu0703/Telephone-Company-Customers-Stop-Using-Service-Prediction/blob/main/images/Each_Category_Heatmap.jpg" width="50%" height="50%">
## Feature Engineering

### Feature 3~5： Age, Under 30, Senior Citizen

We believe that there is an obvious consideration relationship between the three features of 'Age', 'Under 30' and 'Senior Citizen', such as:

* If 'Age' < 30, 'Under 30' should be "Yes" and 'Senior Citizen' should be "No".
* If 65 > 'Age' >= 30, then 'Under 30' should be "No", and 'Senior Citizen' should also be "No".
* If 'Age' >= 65 , then 'Under 30' should be "No", and 'Senior Citizen' should be "Yes" After observation, we found that 'Age', 'Under 30', 'Senior Citizen' three The features all have missing parts of each other, so we use the above features to make up the value:
  * When 'Age' < 30 and 'Under 30' is a missing value, fill in the missing value: "Yes".
  * When 'Age' >= 65 and 'Senior Citizen' is a missing value, fill in the missing value: "Yes".
  * When 'Under 30' == "Yes" and 'Age' is a missing value, fill in the missing value: the mean of 'Age' < 30.
  * When 'Senior Citizen' == 'Yes' and 'Age' is a missing value, fill in the missing value: the mean of 'Age' >= 65.
  * When 'Under 30' == "No' and 'Senior Citizen' == "No" and 'Age' is a missing value, add the missing value: 65 > 'Age' >= 30 mean.
  * After the above complements 1~5, all the remaining missing values of 'Age' will be filled up: the mean of all 'Age' is calculated to be about 46.67527672739572.
  * Since 65 > all mean of 'Age' >= 30, the corresponding missing values of 'Under 30' and 'Senior Citizen' are filled with "No".

### Feature Importance of Xgboost

* 'Population' has a significant impact on the prediction of xgboost. In fact, after repeatedly filling in the missing values and adjusting the model, the importance of 'Population' still remains in the first position, which shows that 'Population' has a great influence on the prediction of xgboost.
* The importance of 'City' for xgboost prediction is also in the top ten. After repeated filling and model adjustment, 'City' still has a certain degree of influence
* Although the two Features 'Country' and 'State' are not as important to xgboost prediction as 'Population' and 'City', they are still on the list. We believe that perfecting the missing values of these two Features will allow xgboost to give more accurate prediction results.
* Therefore, we decided to try to use other Features to fill the gaps of the above features. It can be inferred that 'Country', 'State', 'City' and 'Population' can basically be obtained from 'Zip Code' or 'Lat' Long' to get the corresponding information. At the same time, we found that we can get the corresponding 'Zip Code' from 'Lat Long' to make it more complete, so as to fill in the missing values of other features.

### Feature 12~14： Lat Long, Latitude, Longitude
* After first observing the feature of 'Lat Long', it is found that the content is (Lat, Long), where "Lat" is the longitude and "Long" is the latitude. At the same time, 'Latitude' and 'Longitude' are exactly the corresponding longitude and latitude. Therefore, we use this property to complement the 'Lat Long' row:
  * When both 'Latitude' and 'Longitude' have values and 'Lat Long' is missing, use ('Latitude', 'Longitude') to complement 'Lat Long'.

### Feature 11： Zip Code
* We use [geopy](https://geopy.readthedocs.io/en/stable/) to convert the Zip Code corresponding to 'Lat Long' and use it to complement: 
  * When 'Lat Long' has a value and 'Zip Code' is missing, use the Zip Code converted from 'Lat Long' to 'Zip Code' for complement.

### Feature 8： Country
* When using the package converted by 'Zip Code', we found that there is no conversion of 'Country', so we use 'Lat Long' to convert the corresponding 'Country' and use it to complement:
  * When 'Lat Long' has value and 'Zip Code' is missing, use the Zip Code converted from 'Lat Long' to complement 'Zip Code'.

### Feature 9, 10, 15： State, City, Population
* We use the [search](https://uszipcode.readthedocs.io/uszipcode/search.html) to convert the State, City and Population corresponding to 'Zip Code', and use them to complement:
  * When 'Zip Code' has a value and 'State' is missing, use the State converted from 'Zip Code' to complement 'State', and if there is a missing value after complementing, add 'unknow' to it.
  * When 'Zip Code' has a value and 'City' is missing, use the City converted from 'Zip Code' to complement 'City', and if there is a missing value after the complement, add 'unknow'.
  * When 'Zip Code' has a value and 'Population' is a missing value, use the Population converted from 'Zip Code' to complement 'Population'. If there is a missing value after the complement, add the mean of all 'Population'.
* Note: It is worth noting that the 'City' converted with 'Zip Code' may not have a value,we speculate that it may be related to the regional planning of the United States. Some users may be in more remote places, so he may not belong to any city, but basically there must be a 'State' to which he belongs.
* In fact, the conversion method of [geopy](https://geopy.readthedocs.io/en/stable/) can also use 'Lat Long' to convert the corresponding 'State', 'City' and 'Population'
  * We also considered the solution of using 'Lat Long' to complement the value, but because even after using 'Lat Long' to complete the value, we still need to use 'Zip Code' to complete the value.
* As a result, the number of values filled by the two schemes is almost the same, but it is too time-consuming to use 'Lat Long' to convert compared to 'Zip Code' (you need to use the browser to find the information corresponding to 'Lat Long' every time. ), so we finally chose to convert with 'Zip Code'.

### Feature 2 ：Gender 
* We experimented with two ways of Encoding, namely "Frequency Endcoing" and "Label Endcoing", and found that "Label Endcoing" has more significant results for xgboost than "Frequency Endcoing".
* Therefore, in the selection of the best solution, we are more inclined to use "Label Endcoing" for 'Gender'.

### Feature 6~7: Dependents, Number of Dependents 
* We first fill the empty value of "Number of Dependents" with 0, then if "Number of Dependents" is greater than zero, then fill in "Yes" for Dependents, and finally perform label encoding to convert Yes to 1, and No to 0.

### The Method of Endcoing and Complement Value of Each Feature

<!-- ![images](https://github.com/ycchiu0703/Telephone-Company-Customers-Stop-Using-Service-Prediction/blob/main/images/Endcoing_1.jpg)
![images](https://github.com/ycchiu0703/Telephone-Company-Customers-Stop-Using-Service-Prediction/blob/main/images/Endcoing_2.jpg) -->
<img src="https://github.com/ycchiu0703/Telephone-Company-Customers-Stop-Using-Service-Prediction/blob/main/images/Endcoing_1.jpg" width="50%" height="50%">
<img src="https://github.com/ycchiu0703/Telephone-Company-Customers-Stop-Using-Service-Prediction/blob/main/images/Endcoing_2.jpg" width="50%" height="50%">
