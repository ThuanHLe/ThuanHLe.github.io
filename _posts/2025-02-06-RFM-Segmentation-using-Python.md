---
layout: post
title: RFM Segmentation using Python
image: "/posts/customer-segmentation.png"
tags: [Data Cleaning, Data Wrangling, Data Analysis, Visualization, Python]
---

In this project we apply data cleaning, data analysis and visualization to segment customers for future marketing purposes. 

# Table of contents

- [00. Project Overview](#overview-main)
    - [Context](#overview-context)
    - [Actions](#overview-actions)
- [01. Data Overview & Preparation](#data-overview)
- [02. Applying Chi-Square Test For Independence](#chi-square-application)
- [03. Analysing The Results](#chi-square-results)
- [04. Discussion](#discussion)

___

# Project Overview  <a name="overview-main"></a>

### Context <a name="overview-context"></a>

SuperStore is a global retail company with a vast customer base.
To celebrate Christmas and the New Year, the Marketing department aims to launch campaigns to appreciate loyal customers and attract potential ones. However, they have yet to segment customers for this year due to the large dataset, which makes manual processing unfeasible as done in previous years. Therefore, they seek support from the Data Analytics team to develop a customer segmentation model, allowing for targeted marketing strategies for each customer group.
The Marketing Director has suggested using the RFM (Recency, Frequency, Monetary) model. In the past, when the company was smaller, the team could manually calculate and segment customers using Excel. However, given the current data volume, they request the Data team to develop an automated segmentation pipeline using Python.

<br>
### Actions <a name="overview-actions"></a>

For this assignment, we need to conduct exploratory data analysis (EDA) and visualize the distribution of RFM Modeling to better understand the distribution of the user profile, the change of the profile by time, and the change of the profile by specific location

In term of exploratory data analysis, we could follow these steps:
* Understand about the data (data type, data value).
* Check missing data type & handling missing data.
* Check duplicated data type & handle duplicated data.
* Check outliers & handle outliers.

After that, we visualize user profile with seaborn and matplotlib library to find insights and suggest appropriate actions for our marketing campaign.
* Overal distribution of the RFM modelling (Learning + Insight).
* The change for distribution of RFM Modeling throughout the time (Learning + Insight).
* Understand the customer segmentation by location (Learning + Insight).
* Understand the customer segmenation by the date user entered the product.

___

# Data Overview & Preparation  <a name="data-overview"></a>

#### Exploratory Data Analysis
For this task, we are looking to import packages, understand about the data type & value, detect inappropriate data, handle missing data and duplicates

**Import packages**

```python
# Import Packages
! pip install pandas-profiling
! pip install pydantic-settings
! pip install ydata_profiling
! pip install squarify

import pandas as pd
from ydata_profiling import ProfileReport
import seaborn as sns
import numpy as np

import matplotlib.pyplot as plt
import squarify    # pip install squarify (algorithm for treemap)

```
<br>
#### Understand about the data
**Load data set**
<br>
```python
#Load Dataset
from google.colab import drive
drive.mount('/content/drive')
path = '/content/drive/MyDrive/Unigap Project 3/'

df = pd.read_excel(path + 'ecommerce retail.xlsx', sheet_name = 'ecommerce retail')
df.head()

```
<br>
**Get information about data type & value**
<br>
```python
#Get info about data type & data value
# information of the table help detect data type of each column
print(df.info())

print('')

# detect value of each column (min, max, count,...)
print(df.describe())

print ('')

# df.head()
df = pd.read_excel(path + 'ecommerce retail.xlsx', sheet_name = 'ecommerce retail')
df.head()

```
Output:
```

<class 'pandas.core.frame.DataFrame'>
RangeIndex: 541909 entries, 0 to 541908
Data columns (total 8 columns):
 #   Column       Non-Null Count   Dtype         
---  ------       --------------   -----         
 0   InvoiceNo    541909 non-null  object        
 1   StockCode    541909 non-null  object        
 2   Description  540455 non-null  object        
 3   Quantity     541909 non-null  int64         
 4   InvoiceDate  541909 non-null  datetime64[ns]
 5   UnitPrice    541909 non-null  float64       
 6   CustomerID   406829 non-null  float64       
 7   Country      541909 non-null  object        
dtypes: datetime64[ns](1), float64(2), int64(1), object(4)
memory usage: 33.1+ MB
None

            Quantity                    InvoiceDate      UnitPrice  
count  541909.000000                         541909  541909.000000   
mean        9.552250  2011-07-04 13:34:57.156386048       4.611114   
min    -80995.000000            2010-12-01 08:26:00  -11062.060000   
25%         1.000000            2011-03-28 11:34:00       1.250000   
50%         3.000000            2011-07-19 17:17:00       2.080000   
75%        10.000000            2011-10-19 11:27:00       4.130000   
max     80995.000000            2011-12-09 12:50:00   38970.000000   
std       218.081158                            NaN      96.759853   

          CustomerID  
count  406829.000000  
mean    15287.690570  
min     12346.000000  
25%     13953.000000  
50%     15152.000000  
75%     16791.000000  
max     18287.000000  
std      1713.600303  

```
![alt text](/img/posts/python-table-info.png "Python EDA – Table Info")
<br>

From the output above, we can see there’s negative number in Quantity & Unit Price column. Next, we would explore why it is negative.
**Detect negative columns (Quantity <0)**
<br>
```python
# Detect Quantity column < 0
# Quick check why Quantity < 0
print('Print values with Quantity < 0')
print(df[df.Quantity < 0].head())
print('')

# Continue to check if Quantity < 0 values are from ‘cancelled’ transactions
print(‘check if Quantity < 0 are from cancelled transaction')
df['InvoiceNo'] = df['InvoiceNo'].astype(str)
df['check_cancel'] = df['InvoiceNo'].apply(lambda x: True if x[0] == 'C' else False)
print(df[(df.Quantity < 0) & (df.check_cancel == True)].head())

print('')
df[(df.Quantity < 0) & (df.check_cancel == False)].head()

```
 Output:
```
Print values with Quantity < 0
    InvoiceNo StockCode                       Description  Quantity  \
141   C536379         D                          Discount        -1   
154   C536383    35004C   SET OF 3 COLOURED  FLYING DUCKS        -1   
235   C536391     22556    PLASTERS IN TIN CIRCUS PARADE        -12   
236   C536391     21984  PACK OF 12 PINK PAISLEY TISSUES        -24   
237   C536391     21983  PACK OF 12 BLUE PAISLEY TISSUES        -24   

            InvoiceDate  UnitPrice  CustomerID         Country  
141 2010-12-01 09:41:00      27.50     14527.0  United Kingdom  
154 2010-12-01 09:49:00       4.65     15311.0  United Kingdom  
235 2010-12-01 10:24:00       1.65     17548.0  United Kingdom  
236 2010-12-01 10:24:00       0.29     17548.0  United Kingdom  
237 2010-12-01 10:24:00       0.29     17548.0  United Kingdom  

check if Quantity < 0 are from cancelled transaction
    InvoiceNo StockCode                       Description  Quantity  \
141   C536379         D                          Discount        -1   
154   C536383    35004C   SET OF 3 COLOURED  FLYING DUCKS        -1   
235   C536391     22556    PLASTERS IN TIN CIRCUS PARADE        -12   
236   C536391     21984  PACK OF 12 PINK PAISLEY TISSUES        -24   
237   C536391     21983  PACK OF 12 BLUE PAISLEY TISSUES        -24   

            InvoiceDate  UnitPrice  CustomerID         Country  check_cancel  
141 2010-12-01 09:41:00      27.50     14527.0  United Kingdom          True  
154 2010-12-01 09:49:00       4.65     15311.0  United Kingdom          True  
235 2010-12-01 10:24:00       1.65     17548.0  United Kingdom          True  
236 2010-12-01 10:24:00       0.29     17548.0  United Kingdom          True  
237 2010-12-01 10:24:00       0.29     17548.0  United Kingdom          True  
```
![alt text](/img/posts/python-cancel-trans-table.png "Python EDA – Cancelled Transaction")

<br>
**Detect negative columns (Price <0)**
```python
# Detect why (Price < 0)
# Quick check why Unit Price < 0
print('Print values that UnitPrice < 0')
df[df.UnitPrice < 0].head()

```
Output:
![alt text](/img/posts/python-negative-price.png "Python EDA – Cancelled Transaction")

<br>
**Handle inappropriate data types and values**
```python
# Correct data types and values
# Convert data type to the right format
print(df.columns)
print('')

column_list = ['InvoiceNo','StockCode','Description','CustomerID','Country']
for c in column_list:
     df[c] = df[c].astype(str)

# drop inappropriate data value 
## drop data value has UnitPrice < 0
df = df[df['UnitPrice'] > 0]
## drop data has Quantity < 0
df = df[df['Quantity'] > 0]
## drop cancelled transaction
df = df[df.check_cancel == False]
df = df.replace('nan', None)
df = df.replace('Nan',None)
df.shape

```
Output:
```
Index(['InvoiceNo', 'StockCode', 'Description', 'Quantity', 'InvoiceDate',
       'UnitPrice', 'CustomerID', 'Country', 'check_cancel'],
      dtype='object')

(530104, 9)
```
**Check data type again**
```python
df.dtypes
```
Output:
```
InvoiceNo	object
StockCode	object
Description	object
Quantity	int64
InvoiceDate	datetime64[ns]
UnitPrice	float64
CustomerID	object
Country	object
check_cancel	bool

dtype: object
```

___

<br>
# Applying Chi-Square Test For Independence <a name="chi-square-application"></a>

<br>
#### State Hypotheses & Acceptance Criteria For Test

The very first thing we need to do in any form of Hypothesis Test is state our Null Hypothesis, our Alternate Hypothesis, and the Acceptance Criteria (more details on these in the section above)

In the code below we code these in explcitly & clearly so we can utilise them later to explain the results.  We specify the common Acceptance Criteria value of 0.05.

```python

# specify hypotheses & acceptance criteria for test
null_hypothesis = "There is no relationship between mailer type and signup rate.  They are independent"
alternate_hypothesis = "There is a relationship between mailer type and signup rate.  They are not independent"
acceptance_criteria = 0.05

```

<br>
#### Calculate Observed Frequencies & Expected Frequencies

As mentioned in the section above, in a Chi-Square Test For Independence, the *observed frequencies* are the true values that we’ve seen, in other words the actual rates per group in the data itself.  The *expected frequencies* are what we would *expect* to see based on *all* of the data combined.

The below code:

* Summarises our dataset to a 2x2 matrix for *signup_flag* by *mailer_type*
* Based on this, calculates the:
    * Chi-Square Statistic
    * p-value
    * Degrees of Freedom
    * Expected Values
* Prints out the Chi-Square Statistic & p-value from the test
* Calculates the Critical Value based upon our Acceptance Criteria & the Degrees Of Freedom
* Prints out the Critical Value

```python

# aggregate our data to get observed values
observed_values = pd.crosstab(campaign_data["mailer_type"], campaign_data["signup_flag"]).values

# run the chi-square test
chi2_statistic, p_value, dof, expected_values = chi2_contingency(observed_values, correction = False)

# print chi-square statistic
print(chi2_statistic)
>> 1.94

# print p-value
print(p_value)
>> 0.16

# find the critical value for our test
critical_value = chi2.ppf(1 - acceptance_criteria, dof)

# print critical value
print(critical_value)
>> 3.84

```
<br>
Based upon our observed values, we can give this all some context with the sign-up rate of each group.  We get:

* Mailer 1 (Low Cost): **32.8%** signup rate
* Mailer 2 (High Cost): **37.8%** signup rate

From this, we can see that the higher cost mailer does lead to a higher signup rate.  The results from our Chi-Square Test will provide us more information about how confident we can be that this difference is robust, or if it might have occured by chance.

We have a Chi-Square Statistic of **1.94** and a p-value of **0.16**.  The critical value for our specified Acceptance Criteria of 0.05 is **3.84**

**Note** When applying the Chi-Square Test above, we use the parameter *correction = False* which means we are applying what is known as the *Yate's Correction* which is applied when your Degrees of Freedom is equal to one.  This correction helps to prevent overestimation of statistical signficance in this case.

___

<br>
# Analysing The Results <a name="chi-square-results"></a>

At this point we have everything we need to understand the results of our Chi-Square test - and just from the results above we can see that, since our resulting p-value of **0.16** is *greater* than our Acceptance Criteria of 0.05 then we will _retain_ the Null Hypothesis and conclude that there is no significant difference between the signup rates of Mailer 1 and Mailer 2.

We can make the same conclusion based upon our resulting Chi-Square statistic of **1.94** being _lower_ than our Critical Value of **3.84**

To make this script more dynamic, we can create code to automatically interpret the results and explain the outcome to us...

```python

# print the results (based upon p-value)
if p_value <= acceptance_criteria:
    print(f"As our p-value of {p_value} is lower than our acceptance_criteria of {acceptance_criteria} - we reject the null hypothesis, and conclude that: {alternate_hypothesis}")
else:
    print(f"As our p-value of {p_value} is higher than our acceptance_criteria of {acceptance_criteria} - we retain the null hypothesis, and conclude that: {null_hypothesis}")

>> As our p-value of 0.16351 is higher than our acceptance_criteria of 0.05 - we retain the null hypothesis, and conclude that: There is no relationship between mailer type and signup rate.  They are independent


# print the results (based upon Chi Square Statistic)
if chi2_statistic >= critical_value:
    print(f"As our chi-square statistic of {chi2_statistic} is higher than our critical value of {critical_value} - we reject the null hypothesis, and conclude that: {alternate_hypothesis}")
else:
    print(f"As our chi-square statistic of {chi2_statistic} is lower than our critical value of {critical_value} - we retain the null hypothesis, and conclude that: {null_hypothesis}")
    
>> As our chi-square statistic of 1.9414 is lower than our critical value of 3.841458820694124 - we retain the null hypothesis, and conclude that: There is no relationship between mailer type and signup rate.  They are independent

```
<br>
As we can see from the outputs of these print statements, we do indeed retain the null hypothesis.  We could not find enough evidence that the signup rates for Mailer 1 and Mailer 2 were different - and thus conclude that there was no significant difference.

___

<br>
# Discussion <a name="discussion"></a>

While we saw that the higher cost Mailer 2 had a higher signup rate (37.8%) than the lower cost Mailer 1 (32.8%) it appears that this difference is not significant, at least at our Acceptance Criteria of 0.05.

Without running this Hypothesis Test, the client may have concluded that they should always look to go with higher cost mailers - and from what we've seen in this test, that may not be a great decision.  It would result in them spending more, but not *necessarily* gaining any extra revenue as a result

Our results here also do not say that there *definitely isn't a difference between the two mailers* - we are only advising that we should not make any rigid conclusions *at this point*.  

Running more A/B Tests like this, gathering more data, and then re-running this test may provide us, and the client more insight!
