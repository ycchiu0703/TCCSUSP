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

### 1. Feature 3~5： Age, Under 30, Senior Citizen

We believe that there is an obvious consideration relationship between the three features of 'Age', 'Under 30' and 'Senior Citizen', such as:

i. If 'Age' < 30, 'Under 30' should be "Yes" and 'Senior Citizen' should be "No"
ii. If 65 > 'Age' >= 30, then 'Under 30' should be "No", and 'Senior Citizen' should also be "No"
iii. If 'Age' >= 65 , then 'Under 30' should be "No", and 'Senior Citizen' should be "Yes" After observation, we found that 'Age', 'Under 30', 'Senior Citizen' three The features all have missing parts of each other, so we use the above features to make up the value:
  i.
