# Hospital and Medical Providers Recommendation
## 1. Introduction
The objective for this project is to train a set of machine learning models to predict the “relative price for Inpatient and Outpatient service” by nationwide healthcare providers. This prediction could be either a direct relative price for Inpatient and Outpatient service or a category labeling to which category the hospital belongs to. Category can be of 3 classes including low, medium and high resembling cost/price. The prediction in terms of relative price might not be accurate enough but the classes are expected to be in the reasonable categories.
## 2. Dataset Overview
1)	Detailed_Data.xlsx : This is the file containing the “labels” for a subset of hospitals from all over the US. The labels could be the standardized price , total allowed amount or relative price(inpatient / outpatient or both). The study that details how exactly the variables in this file are calculated is found [here](https://www.rand.org/health-care/projects/price-transparency/hospital-pricing/round2.html)
2)	Rand_hcris_cy_hosp_a_2020_05_01.csv.zip : This is the zipped full hospital cost report information system (HCRIS) data file having all the financial metrics for all hospitals across several years .The detailed description of each variable in this file is provided in the files in the folder above.
3)	Medicare IPPS and charge data : This dataset contains the utilization and charge data for providers participating in IPPS (from hereby referred to as ‘IPPS hospitals’) for 2011-2017, an example for 2017 can be found [here](https://www.cms.gov/Research-Statistics-Data-and-Systems/Statistics-Trends-and-Reports/Medicare-Provider-Charge-Data/Inpatient2017). You can download files for other years similarly.
4)	Medicare IPPS Final Rule data files : These datasets additional hospital level variables such as Wage Index for Provider , Resident to Bed Ratio - Indirect Medical Education , Disproportionate Share Ratio , Number of Cases , CBSA etc. and other relevant info on inpatient services performed by the IPPS hospitals such as Weights for MS-DRGs(codes used to identify the specific inpatient service), Average length of stay for each etc. You can download all relevant data files from [this page](https://www.cms.gov/Medicare/Medicare-Fee-for-Service-Payment/AcuteInpatientPPS/FY2020-IPPS-Final-Rule-Home-Page-Items/FY2020-IPPS-Final-Rule-Data-Files)
5)	Census Data : The American Community Survey(ACS) website provides over 10,000+ metrics on the US  population such as household income , age , healthcare spend etc. at different geographic levels such as county , census tract , zip code etc. They also have apis in different programming languages . We recommend using this particular data set called ACS 5 year data(2009-2018) which is the latest that they have. Check it out [here](https://www.census.gov/data/developers/data-sets/acs-5year.html)

## 3. Feature Engineering
After merging the OPPS, IPPS, Detailed_Data and rand_hcris dataset, we have 87 features and 3329 data points in our final merged data set. We trained classification and regression models with the best subset of 87 features as our benchmark models. In order to improve our model’s performance, we processed feature engineering.

### 1. Categorical features

Firstly, we have investigated categorical features. And we dropped categorical features that had too many unique values and low predication power such as ‘hospital name’, ‘street address’. The reason behind this is if we transformed such categorical to dummy variables, it would add thousands of additional dummy features to the data which would increase the model complexity and cause an overfitting problem and lead to high variance. We had also transformed some categorical variables’ format to ‘yes/no’ which included if the hospital system is independent - if independent ipps? Or if independent cah? Also, if the hospital is a critical access system.

### 2. Missing data

Secondly, we have investigated missing data. Our approach for missing data was imputation, but if we performed imputation on too many data points, it would introduce more errors in our model and thus reduce the predictability. Therefore, we looped through features that had missing data, and dropped features that had more than 65% Nan. This brought down the data to 52 features in total. We then experimented with different imputers like simple imputer to impute with mean/median values or fill in with a special value indicating missing field and then a knn imputer which imputed the missing values based on the 8 nearest neighbors that had value filled in.

### 3. Data Transformation

Before imputing the missing values on each column, we have tried transforming each numerical column in different ways that included Standard Transformation to have all the columns fixed in a particular range, having Box-Cox Transformation (pre transforming all into positive values) and lastly the quartile transformation which had the best results of all. Quantile transformation transforms each column to be in quantiles representing closer to normal distribution as the label to be predicted or the response variable was already following a normal distribution.

### 4. Correlation matrix

Thirdly, we investigated the correlation between all predicting variables and response variables using different correlation methods like Pearson and Spearman correlation. And we found Pearson correlation was the appropriate method to determine the predictiveness on the predicting variables when compared with the relative price. We tried including only the features that had at least 0.01 correlation with the response variable. Also, among the features that had high correlation with response variable - we have tested for correlation among the predicting variables themselves and removed features that had high correlation with each other (Ex: Total discharge, total discharge.1, total discharge.2 - these 3 had high correlation with each other and Total discharge.1 had highest correlation with the response variable - so we have included only Total discharges.1 excluding Total discharges and Total discharges.2). These reduced features count to 35.

## 4. Models
| Model| Description | Code | 
| :---: | :------ | :---: |
| Regression | We used regression models to predict the response variable relative price for inpatient and outpatient services, then rank them based on these predictions. We have trained the labeled dataset using multiple regression models such as linear regression, elastic net, and k nearest neighbor. We also tried different ensemble models like Random Forest and Gradient Boosting  | [Notebook](https://github.com/qviet1602/medxoom_hospital_ranking/blob/master/modeling/modeling_Regression.ipynb) |
| Classification | We used classification models to bucket all the hospitals into three different classes low (0-100), medium (100-400) and high cost (greater than 400). For classification, we tried multiple classification algorithms such as support vector machine, multilayer perceptron (MLP), Decision tree. We also tried different ensemble models like AdaBoost and Gradient Boosting  | [Notebook](https://github.com/qviet1602/medxoom_hospital_ranking/blob/master/modeling/modeling_classification.ipynb) |
