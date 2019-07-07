---
layout: post
title:      "Hypothesis Testing with the Northwind Database"
date:       2019-07-04 20:43:07 -0400
permalink:  hypothesis_testing_with_the_northwind_database
---


## About the Project
The Northwind Database was created by Microsoft to simulate a real-world business database through which data analysts can create statistical models and test business-related hypotheses. As a real-world simulation, it also contains imperfections, which analysts may encounter in their day-to-day work.

Our goal in this project was to ask at least four questions that we could test using the data accumulated by the fictional Northwind. These questions will be of primary importance to stakeholders. 

H,,0,, is known as the null hypothesis; it’s our currently accepted base statement, which our experiment will either reject or accept. H,,a,, is known as the alternative hypothesis, which becomes a possibility once our null hypothesis is rejected.


## 1. Do Discounts Affect the Number of Products Sold? 

### The Process
For our first question, we want to know if discounts affect the number of products sold. Our null and alternative hypotheses are then:
* H,,0,,: Discounts do not affect the number of products sold.
* H,,a,,: Discounts affect the number of products sold.

Our first step, of course, is to import all the necessary libraries:
```
import sqlite3
import sqlalchemy
from sqlalchemy import create_engine
from sqlalchemy.orm import Session, sessionmaker
from sqlalchemy import inspect
import pandas as pd
import itertools
import numpy as np
from scipy import stats
import statsmodels.api as sm
from statsmodels.formula.api import ols

engine = create_engine("sqlite:///Northwind_small.sqlite", echo=True)
Session = sessionmaker(bind=engine)
session = Session()
inspector = inspect(engine)
```
The next thing I like to do is explore the database. Initially, inspecting the table names:
```
print(inspector.get_table_names())
```
And, then the column names of each of the tables. If we simple use print(inspector.get_columns(‘Table Name’), we will end up with a long block of difficult-to-read output.

Instead, a quick bit of code (like that below) will save our eyes and our sanity:
```
def get_columns_info(col_name):
    cols_list = inspector.get_columns(col_name)
    
    print("Table Name: {}".format(col_name))
    print("")
    
    for column in cols_list:
        print("Name: {} \t Type: {}".format(column['name'], column['type']))
```

Now that we’re familiar with the Northwind Database and the information within it, it’s time to focus on the data relevant to our research question. The quantity of products ordered and the discount given (if any) are found in the ‘OrderDetail’ table. We will create a data frame and set it equal to the pandas function read_sql_query() containing the SQL command requesting the information we need. For our purposes, it will be better to create two separate data frames—one containing the orders without discounts and another containing orders with discounts.

```
df1 = pd.read_sql_query('''SELECT * FROM OrderDetail WHERE OrderDetail.Discount = 0.0''', engine)
print(df1)

df2 = pd.read_sql_query('''SELECT * FROM OrderDetail WHERE OrderDetail.Discount > 0.0''', engine)
print(df2)
```

We can then find the mean of the quantities with and without discounts:
```
df1['Quantity'].mean()
df2['Quantity'].mean()
```
And plot the two data frames as bar charts:
```
no_disc = df1.groupby('ProductId')['Quantity'].mean()
disc = df2.groupby('ProductId')['Quantity'].mean()
plt.figure(figsize=(16,5))
plt.bar(disc.index, disc.values, alpha=1, label='Discount', color='g')
plt.bar(no_disc.index, no_disc.values, alpha=0.8, label='No Discount', color='b')
plt.legend()
plt.title('Average Quantity Ordered With/Without Discount')
plt.xlabel('Product ID')
plt.ylabel('Average Quantity')
plt.show()
```

![](https://i.imgur.com/QTGZ3M7.png)

### Testing for Statistical Significance
We will use the bootstrap sampling method to perform our resampling calculations and Cohen’s d to measure the effect size.
```
def bootstrap(sample, n):
    bootstrap_sampling_dist = []
    for i in range(n):
        bootstrap_sampling_dist.append(np.random.choice(sample, size=len(sample), replace=True).mean())
    return np.array(bootstrap_sampling_dist)
		
def Cohen_d(group1, group2):

    diff = group1.mean() - group2.mean()
    n1, n2 = len(group1), len(group2)
    var1 = group1.var()
    var2 = group2.var()

    pooled_var = (n1 * var1 + n2 * var2) / (n1 + n2)

    d = diff / np.sqrt(pooled_var)
    return abs(d)
```
Now we can perform our t-test. If the p-value is lower than 0.025, we will reject the null hypothesis.
```
discounts_significance_df = pd.DataFrame(columns=['Discount %','Null Hypothesis','Cohens d'], index=None)

discounts = [0.01, 0.02, 0.03, 0.04, 0.05, 0.06, 0.1, 0.15, 0.2, 0.25]
control = df1[df1['Discount']==0.0]['Quantity']
for i in discounts:
    experimental = df2[df2['Discount']==i]['Quantity']
    st, p = stats.ttest_ind(control, experimental)
    d = Cohen_d(experimental, control)
    discounts_significance_df = discounts_significance_df.append( { 'Discount %' : str(i*100)+'%' , 'Null Hypothesis' : 'Reject' if p < 0.025 else 'Failed', 'Cohens d' : d } , ignore_index=True)    

discounts_significance_df
```

We can see from the results that the discounts of 1%, 2%, 3%, 4%, and 6% are two small to be relevant to our test. 


### Results
We can also see that for the discounts of 5%, 10%, 15%, 20%, and 25% offering a discount affects the total number of products sold.

Our next step will be to find out if there is a statistically significant difference amongst the discounts.

### 1B. Is there a statistically significant difference amongst the discounts given?
* H,,0,,: There is no statistically significant difference between the discounts.
* H,,a,,: There is a statistically significant difference between the discounts.

For this we will you a similar bit of code as before:
```
discounts = np.array([0.05, 0.1, 0.15, 0.2, 0.25])
comb = itertools.combinations(discounts, 2)
discount_levels_df = pd.DataFrame(columns=['Discount %','Null Hypothesis','Cohens d'], index=None)

for i in comb:
    
    control = df2[df2['Discount']==i[0]]['Quantity']
    experimental = df2[df2['Discount']==i[1]]['Quantity']
    
    st, p = stats.ttest_ind(experimental, control)
    d = Cohen_d(experimental, control)
    
    discount_levels_df = discount_levels_df.append( { 'Discount %' : str(i[0]*100)+'% - '+str(i[1]*100)+'%', 'Null Hypothesis' : 'Reject' if p < 0.05 else 'Failed', 'Cohens d' : d } , ignore_index=True)    

discount_levels_df.sort_values('Cohens d', ascending=False)
```

### Results
There is no statistically significant difference between the discounts. So, while we can advise Northwind that providing a discount does indicate an increase in the number of products sold, the level of discount makes no statistical difference.

## 2. Effect of Discounts on Total Sale Price
* H,,0,,: Discounts have no effect on the total amount a customer spends per order.
* H,,a,,: Discounts have a statistically significant effect on the total amount a customer spends per order.

For this question we will follow the same process as before by creating two data frames—one for orders with discounts and one for orders without—and calculating the means:
```
df1 = pd.read_sql_query('''SELECT UnitPrice, Quantity, Discount FROM OrderDetail WHERE Discount = 0.0''', engine)
print(df1)

for i in df1:
    OrderTotalNoDisc = df1.UnitPrice*df1.Quantity

print(OrderTotalNoDisc.mean())

df2 = pd.read_sql_query('''SELECT UnitPrice, Quantity, Discount FROM OrderDetail WHERE Discount > 0.0''', engine)
print(df2)

for i in df2:
    OrderTotalDisc = df2.UnitPrice*df2.Quantity

print(OrderTotalDisc.mean())
```

And visualize the results:
```
plt.plot(OrderTotalNoDisc)
plt.ylabel('Total Price Per Order')
plt.xlabel('Order Number')
plt.axhline(y=OrderTotalNoDisc.mean(), color='r', linestyle='-', label='Average Total Price/Order = 570')
plt.legend()
plt.show()

plt.plot(OrderTotalDisc)
plt.ylabel('Total Price Per Order')
plt.xlabel('Order Number')
plt.axhline(y=OrderTotalDisc.mean(), color='r', linestyle='-', label='Average Total Price/Order = 720')
plt.legend()
plt.show()
```

![](https://i.imgur.com/SubcMy5.png)
![](https://i.imgur.com/y9tCswg.png)

### Results
We can see that the mean total sale price of orders with discounts is greater than the mean total sale price of orders without discounts.
```
discounts_levels_price_df = pd.DataFrame(columns=['Discount %','Null Hypothesis','Cohens d'], index=None)

discounts = [0.05, 0.1, 0.15, 0.2, 0.25]
control = df1[df1['Discount']==0.0]['Quantity']
for i in discounts:
    experimental = df2[df2['Discount']==i]['Quantity']
    st, p = stats.ttest_ind(control, experimental)
    d = Cohen_d(experimental, control)
    discounts_levels_price_df = discounts_levels_price_df.append( { 'Discount %' : str(i*100)+'%' , 'Null Hypothesis' : 'Reject' if p < 0.025 else 'Failed', 'Cohens d' : d } , ignore_index=True)    

discounts_levels_price_df
```
We can also see from the rejected null hypotheses that the discounts make a statistically significant difference.

### 2B. Is there a statistically significant difference amongst the discounts in relation to total amount customers spend per order?

* H,,0,,: There is no statistically significant difference amongst the discounts in relation to total amount customers spend per order.
* H,,a,,: There is a statistically significant difference amongst the discounts in relation to total amount customers spend per order.

For this test we will use similar code to before:
```
discounts = np.array([0.05, 0.1, 0.15, 0.2, 0.25])
comb = itertools.combinations(discounts, 2)
discount_levels_price_diff_df = pd.DataFrame(columns=['Discount %','Null Hypothesis','Cohens d'], index=None)

for i in comb:
    
    control = df2[df2['Discount']==i[0]]['TotalSalePrice']
    experimental = df2[df2['Discount']==i[1]]['TotalSalePrice']
    
    st, p = stats.ttest_ind(experimental, control)
    d = Cohen_d(experimental, control)
    
    discount_levels_price_diff_df = discount_levels_price_diff_df.append( { 'Discount %' : str(i[0]*100)+'% - '+str(i[1]*100)+'%', 'Null Hypothesis' : 'Reject' if p < 0.05 else 'Failed', 'Cohens d' : d } , ignore_index=True)    

discount_levels_price_diff_df.sort_values('Cohens d', ascending=False)
```
### Results
Interestingly, unlike when considering total number of products with each discount, there is a statistically significant difference between certain discounts when considering the total amount a customer spends per order.

This makes sense, however, as a customer on-the-fence regarding the order may decide that a discount of 25% is within their purchasing abilities as opposed to only 10%. The same theory applies to the difference between 5% and 10%. 

## 3. Does the “holiday season” affect the number of orders completed?

* H,,0,,: The “holiday season” does not effect the number of orders completed.
* H,,a,,: The “holiday season” has a statistically significant effect on the number of orders completed.

This was the most difficult test, as it required utilizing a time series. The first step was using an SQL query to select the data needed. 

```
OrdersByDate = pd.read_sql_query('''SELECT Id, OrderDate, ShipCountry FROM [Order] ORDER BY OrderDate''', engine)
print(OrdersByDate)
```

Then we need to convert the 'Order Date' column to date_time format and group the data by month.
```
OrdersByDate.OrderDate = pd.to_datetime(OrdersByDate.OrderDate, format='%Y-%m-%d')

month_groups = OrdersByDate.groupby(pd.Grouper(key='OrderDate', freq = 'M'))
list(month_groups)
```
And we can visualize the results.
```
month_groups.count().plot()
plt.ylabel('Number of Orders Per Month')
plt.xlabel('Order Date')
plt.legend('')
plt.show()
```

![](https://i.imgur.com/BprMgKd.png)

At last we can take the perform an ANOVA test.
```
OrdersByDate['Month'] = OrdersByDate.OrderDate.dt.month
OrdersByDate['NumberofOrders'] = OrdersByDate.groupby('Month').agg('count')
OrdersByDate.groupby('Month')

formula = 'NumberofOrders ~ C(Month)'
lm = ols(formula, OrdersByDate).fit()
table = sm.stats.anova_lm(lm, typ=2)
print(table)
```

### Results
There is no statististically significant difference in the number of orders in any given month. The holiday season does not seem to affect the number of orders placed. 

## 4. Are women or men more productive in the workplace?

To test this we need to decide on a measure of productivity. A quick inspection of the job titles reveals that the majority of the employees are sales representatives. We will measure their productivity by the number of orders they completed.

We first separate out the male sales reps from the female sales reps using their titles of courtesy (Mr./Ms./Mrs) into two data frames:
```
salesrep_df = titles_df[titles_df['Title']=='Sales Representative']
print(salesrep_df)

male_salesreps_df = salesrep_df[salesrep_df['TitleOfCourtesy']=='Mr.']
female_salesreps_df = salesrep_df[salesrep_df['TitleOfCourtesy']!='Mr.']
```
And plot them visually:
```
plt.bar(male_salesreps_df['EmployeeId'], male_salesreps_df['COUNT('], label='Sales by Male Sales Reps')
plt.bar(female_salesreps_df['EmployeeId'], female_salesreps_df['COUNT('], label='Sales by Female Sales Reps')
plt.ylabel('Number of Sales')
plt.xlabel('EmployeeId')
plt.legend()
plt.show()
```

![](https://i.imgur.com/2zl3bTO.png)

We then run a t-test and calculate Cohen’s d to measure effect size:
```
stats.ttest_ind(male_salesreps_df['COUNT('], female_salesreps_df['COUNT('])

Cohen_d(male_salesreps_df['COUNT('], female_salesreps_df['COUNT('])
```

### Results
We can see from Cohen’s d that the effect size is very large, indicating we are trying to compare two unequal groups. Therefore, even though we can see that 75% of the female employees complete more sales than their male counterparts, we cannot say that there is a statistically significant difference because the samples are not comparative. 

I believe that if there were an equal number of male and female sales representatives, we may see a different result. However, with the information given, we cannot say that there is any statistically significant difference between the productivity of men and the productivity of women as measured by the number of orders filled.

## In Summary:
By utilizing databases from fictional yet realistic companies like Northwind, data science students can practice formulating and testing hypotheses, visualizing data, and interpreting their results in the same way they would be expected in their future career.

The correct application and interpretation of t-tests and effect sizes are vital to the burgeoning data scientist, as is the efficient synthesis of SQL and Pandas. If you have any questions about this post or the contain within, please feel free to comment below or DM me.
