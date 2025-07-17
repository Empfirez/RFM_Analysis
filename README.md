# RFM_Analysis



## Table of Contents
- [Project Overview](#project-overview)
- [Data Sources](#data-sources)
- [Tools](#tools)
- [Data Cleaning/Preparation](#data-cleaningpreparation)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Data Analysis](#data-analysis)
- [Data Visualization](#data-visualization)
- [Results/Findings](#resultsfindings)
- [Recommendations](#recommendations)
- [Limitations](#limitations)





### Project Overview
Customer segmentation is the process of splitting up the customer base into different groups based on shared charateristics such as spending habits, age or gender. It allows businesses to easily identity and target specific groups for more effective marketing campaigns.
In particular, RFM analysis is used to segment customers based on their purchasing behaviour through metrics such as Recency(R) of purchase, Frequency(F) of purchase as well as the total Monetary(M) value of their purchases. By analyzing these three factors, businesses
are able to better understand their customers' spending habits and identify not only their most loyal customers but also the customers that are most likely to churn.



### Data Sources

https://archive.ics.uci.edu/ml/datasets/Online+Retail+II

This dataset contains all the transactions occurring for a UK-based and registered, non-store online retail between 01/12/2009 and 09/12/2011.

### Variables
**InvoiceNo:** Invoice number. Nominal. A 6-digit integral number uniquely assigned to each transaction. If this code starts with the letter 'c', it indicates a cancellation.

**StockCode:** Product (item) code. Nominal. A 5-digit integral number uniquely assigned to each distinct product. 

**Description:** Product (item) name. Nominal. 

**Quantity:** The quantities of each product (item) per transaction. Numeric.	

**InvoiceDate:** Invice date and time. Numeric. The day and time when a transaction was generated. 

**UnitPrice:** Unit price. Numeric. Product price per unit in sterling (Â£). 

**CustomerID:** Customer number. Nominal. A 5-digit integral number uniquely assigned to each customer. 

**Country:** Country name. Nominal. The name of the country where a customer resides.

Additionally, the **invoice number** is a 6-digit integral number uniquely assigned to each transaction. If this code starts with the letter 'c', it indicates a **cancellation**. 


### Tools/Libraries

- VSCode(Python)
- Pandas
- Matplotlib
- Plotly


### Data Cleaning/Preparation

In the data preparation phase of the project, we performed the following tasks:
1.  Data loading and inspection
2.  Formatting data fields into their appropriate data types
3.  Handling of missing customerID values and invoice date from the dataset and checking for any duplicates
4.  Removing cancelled invoices from the dataset
5.  Removing all unit prices with $0 values from the dataset
   

### Exploratory Data Analysis

EDA involves exploring the dataset and creating new features to uncover insights into customer spending patterns as well as calculating RFM values. The steps taken are as follows:
-  Created new column LastTransaction which stores the latest transaction date of each customer
-  Created new column days_since_last_trans which takes the difference between the latest transaction date in the dataset and the latest transaction date for each customerID
-  Created TotalAmount column which stores the total amount spent for each transaction
-  Created rfm_df which displays days since last transaction, total number of transactions and total spendings for each unique customerID



### Data Analysis

During the analysis phase, the following steps were taken to calculate Recency, Frequency and Monetary values for RFM analysis:

#### Calculating r,f,m values

```python
r = pd.qcut(rfm_df['days_since_last_trans'], q=5, labels=range(5,0,-1))
f = pd.qcut(rfm_df['TotalCount'].rank(method='first'),5, labels=[1, 2, 3, 4, 5], duplicates='drop')
m = pd.qcut(rfm_df['TotalSpendings'], q=5, labels=range(1,6))
```

#### Assigning rfm values to the dataframe

```python
rfm_df = rfm_df.assign(R=r.values, F=f.values, M=m.values)
```
<img width="1484" height="244" alt="image" src="https://github.com/user-attachments/assets/ba5d5133-0a24-4470-b029-e0587fa6d225" />



#### Calculating total rfm value for each customerID

```python
rfm_df['rfm_total'] = rfm_df[['R','F','M']].sum(axis=1)
```
<img width="1059" height="246" alt="image" src="https://github.com/user-attachments/assets/55908d2a-34df-4d11-869d-f295344cb4e5" />


#### Calculating RFM score from the total rfm values

```
rfm_df['rfm_score'] = pd.qcut(rfm_df['rfm_total'], 10, labels=range(1,11)) 
```
<img width="1116" height="246" alt="image" src="https://github.com/user-attachments/assets/0af66c5c-f7bd-47c9-b846-d6a8dba99058" />


#### Splitting customers into 5 segments based on their RFM scores

```
def label_segment(x):
    if x >= 9:
        return 'Champions'
    elif x >= 7:
        return 'Loyal Customers'
    elif x >= 5:
        return 'Potential Loyalist'
    elif x >= 3:
        return 'Needs Attention'
    else:
        return 'At Risk'
    
rfm_df['Segment_Label'] = rfm_df['rfm_score'].apply(label_segment)   
segment_counts = rfm_df['Segment_Label'].value_counts()  
```
<img width="1307" height="247" alt="image" src="https://github.com/user-attachments/assets/e39a97f3-8fcd-4e27-8be0-60d418fa1ecf" />


### Data Visualization

#### Customer Segmentation Bar Chart
<img width="790" height="488" alt="image" src="https://github.com/user-attachments/assets/6ac9a3d2-b8c3-4ccc-984e-5ffbc43172e8" />


#### Customer Segmentation Pie Chart
<img width="490" height="428" alt="image" src="https://github.com/user-attachments/assets/1758393f-d6b6-4db7-8edc-6e8442c7bf8e" />


#### Customer Segmentation World Map
<img width="1622" height="431" alt="image" src="https://github.com/user-attachments/assets/e9f5942c-ac1f-4a2e-8cc6-646204abdf13" />



### Results/Findings

After careful analysis, the results are as follows:
1.  14.9% of total customers are champions with an average spending of $8121, with an average transaction count of 14 and has made a purchase within the last 9 days on average.
2.  20.4% of total customers are at risk of churning with an average spending of only $230, average transaction count of 1 and has made a purchase within the last 224 days on average.
3.  47.6% of total customers are either at risk of churning or needs attention
4.  Most of the purchases are made within the United Kingdom which is expected for a UK based and registered company
5.  France and Germany constitutes the next highest purchase counts
6.  14.7% of customers from the UK are champions relative to its total customer base, compared to 23.4% from Germany and 23% from France
7.  20.7% of customers from the UK are at risk relative to its total customer base, compared to 12.8% from Germany and 23% from France

   
  


### Recommendations

Based on analysis above, here are some recommended actions to take in order to increase sales revenue:
1. Retain high value customers such as champions and loyal customers by implementing exclusive loyalty programs as well as premium perks
2. Offer referral rewards for high value customers to try and bring over more potential champions/loyal customers
3. Win back at risk customers by implementing welcome back discounts or coupons to increase engagement
4. Send out surveys for both champions and at risk customers to have a better understanding of customer satisfaction as well as any areas of improvement
5. As Frace and Germany show higher champion ratios compared to UK, more effort should be spent on growing the France and Germant market through marketing campaigns
6. Engage in product bundling and cross-selling based on customer interests
7. Calculate Customer Lifetime Value to know which customer segments are worth targeting
8. Analyze any seasonal trends and engage in seasonal promotions for high value customers
   




### Limitations

-  While RFM is a powerful model for customer segmentation, it only considers a few key factors while excluding other meaningful factors such as age,preferences,brand loyalty and external competition which limits its effectiveness
-  Equal weighting is given to all three factors which might not give an accurate representation of RFM value as recency is more indicative of future spending behaviour
-  RFM analysis also does not capture changes in customer behaviour which means that customers might not show up as being at risk until it is too late 
-  Due to some customerIDs having multiple countries, the first country is used for analysis instead
  
