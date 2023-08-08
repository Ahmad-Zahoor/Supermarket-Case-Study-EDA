# Supermarket Case Study in Python
![Star Badge](https://img.shields.io/static/v1?label=%F0%9F%8C%9F&message=If%20Useful&style=style=flat&color=BC4E99)
[![View Main Folder](https://img.shields.io/badge/View-Main_Folder-971901?)](https://github.com/Ahmad-Zahoor/Cyclistic-Bike-Project)
[![View Repositories](https://img.shields.io/badge/View-My_Repositories-blue?logo=GitHub)](https://github.com/Ahmad-Zahoor?tab=repositories)
[![View My Profile](https://img.shields.io/badge/View-My_Profile-green?logo=GitHub)](https://github.com/Ahmad-Zahoor)

## 📕 Table Of Contents
* [SCENARIO](#scenario)
* [ASK](#ask)
* [PREPARE](#prepare)
* [PROCESS](#process)
* [ANALYZE](#analyze)
* [SHARE](#share)
* [ACT](#act)


## SCENARIO

> You are a data analyst working for a large supermarket chain. The company has recently acquired a dataset that contains detailed information about customer transactions in their stores. The dataset covers a period of 3 months and includes data such as the date and time of each transaction, the products purchased, the price of each product, the total amount spent, and other relevant details. The supermarket wants a detail insights from their data to know customer behaveior and purchase pattern to improve business performance.

     
## ASK
> In this phase, I will identify the main problem that facing by Supermarket chain. I will also understand the needs of Stakeholders.

##### Problem:

>Your task is to analyze the provided supermarket dataset to gain valuable insights into customer behavior and spending patterns. The management team is particularly interested to increase their sales.

#### Stakeholders:

1. Supermarket Owner
2. Sales Executive of Supermarket
3. Marketing team

#### Questions need to solve by EDA:

1. How does the total amount spent vary with the day of the week? Are there any significant spending patterns on specific days?

2. What is the distribution of transaction amounts across different times of the day? Are there any peak spending hours?

3. Show top 5 Product Lines by Total Sales?

4. Which branch has the highest total sales and which one has the lowest?

5. What is the distribution of customers by gender and customer type?

6. How does the average rating vary across different product lines?

7. What is the average gross income and gross margin percentage for each branch?

8. How do sales vary across different months?


## PREPARE

>In this phase i downloaded the dataset from [kaggle](https://www.kaggle.com/datasets/aungpyaeap/supermarket-sales)


## PROCESS

>In this phase, I will clean the data by removing duplicates, handling missing values, identify outlier and make the data accurate for analysis.

#### Import libraries

```r
import numpy as np
import pandas as pd
import seaborn as sns
import warnings
import matplotlib.pyplot as plt
warnings.filterwarnings('ignore') 
import plotly.express as px
import plotly.graph_objects as go
from plotly import tools
from plotly.subplots import make_subplots
from plotly.offline import iplot, init_notebook_mode
from datetime import datetime
init_notebook_mode()
```

#### Load Data

>We read the data through the Python Pandas library, in order to convert it into a data frame, and then facilitate analysis

```r
df = pd.read_csv('supermarket_sales.csv')
```

Records in Dataset

```r
# Show shape of data
df.shape
```

#### Fixing data types

```r
# Dislpaly all columns with its types

df.info()
```

```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 1000 entries, 0 to 999
Data columns (total 17 columns):
 #   Column                   Non-Null Count  Dtype  
---  ------                   --------------  -----  
 0   Invoice ID               1000 non-null   object 
 1   Branch                   1000 non-null   object 
 2   City                     1000 non-null   object 
 3   Customer type            1000 non-null   object 
 4   Gender                   1000 non-null   object 
 5   Product line             1000 non-null   object 
 6   Unit price               1000 non-null   float64
 7   Quantity                 1000 non-null   int64  
 8   Tax 5%                   1000 non-null   float64
 9   Total                    1000 non-null   float64
 10  Date                     1000 non-null   object 
 11  Time                     1000 non-null   object 
 12  Payment                  1000 non-null   object 
 13  cogs                     1000 non-null   float64
 14  gross margin percentage  1000 non-null   float64
 15  gross income             1000 non-null   float64
 16  Rating                   1000 non-null   float64
dtypes: float64(7), int64(1), object(9)
memory usage: 132.9+ KB
```

>First part of the data cleaning process is to fix the data types of all the columns in order to make them easier to manipulate and be more manageable. It should be noted that for several columns the data type was changed to strings, when the data types are displayed, they show up as objects as strings are a type of object in pandas.

#### Parse date columns


```r
df['Date'] = pd.to_datetime(df['Date'], format="%m/%d/%Y")

```

#### Feature Creation

>After going through our business task, we need to add more columns for detail analysis:

**Starting Hour**
```r
df['Hour'] = df['Time'].str.split(':', expand=True).astype(int)[0]
```

**Day of Week**

```r
df['day_of_week'] = df['Date'].dt.strftime('%a')

day_order = ["Mon", "Tue", "Wed", "Thu", "Fir","Sat", "Sun"]

day_categories = pd.CategoricalDtype(categories=day_order, ordered=True)
df['day_of_week'] = df['day_of_week'].astype(day_categories)

```

**Month**
```r

df['month'] = df['Date'].dt.strftime('%m')

```


#### Identifying missing values

```r
df.isnull().sum()
```

```
Invoice ID                   0
Branch                       0
City                         0
Customer type                0
Gender                       0
Product line                 0
Unit price                   0
Quantity                     0
Tax 5%                       0
Total                        0
Date                         0
Time                         0
Payment                      0
cogs                         0
gross margin percentage      0
gross income                 0
Rating                       0
Hour                         0
day_of_week                139
month                        0
dtype: int64

```
>On Friday the Supermarket is close so for this reason it show NaN values so we can remove those records.

```r
df = df.dropna()
```

#### Handling duplicates

```r
df[df.duplicated('Invoice ID')]
```
>There was no duplicate records in the dataset.

#### Handling outliers

```r
# We will make a simple description of the data through statistical information
df.describe()

```
**Observations**

>All column names above are numeric data from count , it appears from the data that there are no null values , This is defined by describing the shape of the data. The standard deviation of a data set tells how dispersed the data is from the mean. There is no any extream value found as an outlier


#### Save the Clean data file
```r

df.to_csv("clean_df.csv", index=False)

```

#### Load data for Analysis
```r
new_df = pd.read_csv('clean_df.csv')

```

## ANALYZE

>Now it’s time to analyze the data and look for key information that we can perform analysis on.
>As mentioned just a moment ago, it is imperative to always remind yourself of the business task at hand during this stage. In order to answer business questions, it would be beneficial to plot a few of our observations revolving around:

1. How does the total amount spent vary with the day of the week? Are there any significant spending patterns on specific days?

2. What is the distribution of transaction amounts across different times of the day? Are there any peak spending hours?

3. Show top 5 Product Lines by Total Sales?

4. Which branch has the highest total sales and which one has the lowest?

5. What is the distribution of customers by gender and customer type?

6. How does the average rating vary across different product lines?

7. What is the average gross income and gross margin percentage for each branch?

8. How do sales vary across different months?

**1. How does the total amount spent vary with the day of the week? Are there any significant spending patterns on specific days?**

```r
day_order = ["Mon", "Tue", "Wed", "Thu", "Fir","Sat", "Sun"]

day_categories = pd.CategoricalDtype(categories=day_order, ordered=True)
new_df['day_of_week'] = new_df['day_of_week'].astype(day_categories)

pd.pivot_table(new_df, index="day_of_week", values="Total", aggfunc=['sum'], margins=True, margins_name="Total Sum")

daily_spents = new_df.groupby(["day_of_week"])["Total"].sum().reset_index()

```

```r
# Now create a bar chart day_of_week and Total
px.bar(daily_spents, x="day_of_week", y="Total",
       title="Daily Total Sales",
       labels={"day_of_week":"Week Days", "Total": "Total Spent Amount"},
       hover_name = 'day_of_week', hover_data = {'day_of_week': True, 'Total': True})
```
<p align="center">
  <img src="/Images/daily_total_sales.png">

Observation:

>The total daily spent are very high on Tuesday and Saturday. Due to Friday off the sales are very high on Saturday.

**2. What is the distribution of transaction amounts across different times of the day? Are there any peak spending hours?**

```r
new_df["Formatted Hours"] = new_df["Hour"].apply(lambda x: f"{x - 12} PM" if x>12 else (f"{x} PM" if x==12 else f"{x} AM" ))

hours_order = ["10 AM","11 AM","12 PM","1 PM","2 PM","3 PM","4 PM","5 PM","6 PM","7 PM","8 PM"]

hour_categories = pd.CategoricalDtype(categories=hours_order, ordered=True)
new_df["Formatted Hours"] = new_df["Formatted Hours"].astype(hour_categories)

pd.pivot_table(new_df, index="Formatted Hours", values="Total", aggfunc=['sum'], margins=True, margins_name="Total Sum")

hourly_spent = new_df.groupby(["Formatted Hours"])["Total"].sum().reset_index()
```

```r
px.line(hourly_spent, x="Formatted Hours", y="Total",
        title="Transaction Distribution by Daily Hours",
        labels={"Formatted Hours":"Daily Hours", "Total":"Total Amount"},
        hover_name="Formatted Hours", hover_data={"Formatted Hours":True, "Total":True})


```
<p align="center">
  <img src="/Images/transaction_hourly.png">

>Observation:

1. The hourly transaction distribution chart show that the high amount spend in evening 7 PM. It's the time when people come to home and do some purchasing from the store. 
2. On other hours of the day the sales are normal.
3. On 8 PM the sales are very low because this is the time of closing the store.

**3. Show top 5 Product Lines by Total Sales?**

```r
product_lines = new_df.groupby(["Product line"])["Total"].sum().reset_index()[0:5]

sorted_product_lines = product_lines.sort_values(by="Total", ascending=False)

```

```r
product_fig =px.bar(sorted_product_lines, y="Product line", x="Total",
        title="Top 5 Product Lines by Total Sales",
        text="Total",
        labels={"Product line":"Product Categories", "month":"Months"},
        hover_data={"Product line":True, "Total":True},
        )

product_fig.update_layout(yaxis={'categoryorder':'total ascending'})
```

<p align='center'>
<img src="/Images/top_products.png">

>Observation:

>The most profitable categories are **Food and beverages** and **Electronic accessories** among top 5 categories.
We need to more focus on **Health and beauty** category which is low in sales


**4. Which branch has the highest total sales and which one has the lowest?**

```r
branch_group = new_df.groupby(["Branch"])["Total"].sum().reset_index()
```
```r
px.bar(branch_group, x="Branch", y="Total",
       color="Branch",
       text="Total",
       title="Total Sales by different Branches",
       labels={"Branch":"Superstore Branch", "Total": "Total Sales"},
       hover_name="Branch", hover_data={"Branch":True, "Total":True})
```

<p align='center'>
<img src='/Images/branches.png'>

>Observation:

>The total sales by different branches are almost same.

**5. What is the distribution of customers by gender and customer type?**

```r
pd.pivot_table(new_df, index=["Gender", "Customer type"], values="Invoice ID", aggfunc=["count"], margins=True, margins_name="Total Count")
```

```r
group_gender_customer_type = new_df.groupby(["Gender", "Customer type"])["Invoice ID"].count().reset_index()

```

```r
px.bar(group_gender_customer_type, x="Gender", y="Invoice ID",
       color="Customer type",
       title="Distribution of Customers by Gender and Customer Type",
       text="Invoice ID",
       labels={"Gender": "Gender", "Customer type":"Customer type", "Invoice ID":"Count"},
       hover_name="Customer type", hover_data={"Customer type":True, "Gender":True, "Invoice ID":True})
```

<p align='center'>
<img src='/Images/customer_distribution.png'>

>Observation:

>These is no any high distribution difference between Male and Female Cusotomer type

**6: How does the average rating vary across different product lines?**

```r
pd.pivot_table(new_df, index=["Product line"], values="Rating", aggfunc=["mean"], margins=True, margins_name="Total Average")

group_by_rating = np.round(new_df.groupby(["Product line"])["Rating"].mean().reset_index(),2)

```

```r
px.bar(group_by_rating, x="Product line", y="Rating",
       title="Average Rating by different Product line",
       text="Rating",
       labels={"Product line":"Product Categories", "Rating": "Average Rating"},
       hover_name="Product line", hover_data={"Product line":True, "Rating":True})
```

<p align='center'>
<img src='/Images/average_rating.png'>

>Observations:

1. The most rating product categories are **Food and beverages** and **Fashion accessories**.
2. The remaining product categories ratings are almost same.


**7. What is the average gross income and gross margin percentage for each branch?**

```r
group_income_margin = np.round(new_df.groupby(["Product line"])[["gross income", "gross margin percentage"]].mean().reset_index(),2)

```
```r
px.bar(group_income_margin, x="Product line", y='gross income',
       title="Average Gross Income and Gross Margin Percentage by Product Categories",
       color="gross margin percentage",
       labels={"Product line":"Product Categories", "value":"Value"},
       text="gross income",
       hover_name="Product line", hover_data={"Product line":True, "gross income":True, "gross margin percentage":True},
       color_discrete_sequence=['blue', 'green'])
```
<p align='center'>
<img src='/Images/average_gross_income.png'>

>Observations:

1. The Average Gross Income for **Home and lifestyle" category is 16.45 which is high from other categories.
2. The Gross Margin Percentage for all Product categories are same which is 4.76%

**8. How do sales vary across different months?**

```r
mapping_months = {
    1:'Jan',
    2:'Feb',
    3:'Mar'
}

new_df['month'] = new_df['month'].apply(lambda x: mapping_months.get(x))
group_monthly_sales = new_df.groupby(["month"])["Total"].sum().reset_index()

```
```r
month_order = ["Jan", "Feb", "Mar"]
monthly_sales_fig = px.bar(group_monthly_sales, x="month", y="Total",
                           title="Total Sales by Months",
                           text="Total",
                           labels={"month":"Months", "Total":"Total Sales"},
                           hover_name="month", hover_data={"month":True, "Total":True},
                           category_orders={"month":month_order}
                           )
# Format text labels as thousands (k) and add to the traces
formatted_labels = [f"{val/1000:.2f} k" for val in group_monthly_sales['Total']]
monthly_sales_fig.update_traces(text=formatted_labels, textposition='inside')  # Place text labels outside the bars

monthly_sales_fig.show()
```
<p align="center">
<img src="/Images/sales_by_month.png">

>Observations

>The highest sales month is January which generated total sales 105.07 k then follow by March and then February.


### SHARE

>Let's go through the main finds and try to arrive at a conclusion.

1. The total daily spent are very high on Tuesday and Saturday. Due to Friday off the sales are very high on Saturday.

2. The hourly transaction distribution chart show that the high amount spend in evening 7 PM. It's the time when people come to home and do some purchasing from the store. 
   On other hours of the day the sales are normal.
3. On 8 PM the sales are very low because this is the time of closing the store.

4. The most profitable categories are **Food and beverages** and **Electronic accessories** among top 5 categories.
We need to more focus on **Health and beauty** category which is low in sales

5. The total sales by different branches are almost same.

6. These is no any high distribution difference between Male and Female Cusotomer type

7. he most rating product categories are **Food and beverages** and **Fashion accessories** and remaining product categories ratings are almost same.

8. The Average Gross Income for **Home and lifestyle" category is 16.45 which is high from other categories.

9. The Gross Margin Percentage for all Product categories are same which is 4.76%

10. The highest sales month is January which generated total sales 105.07 k then follow by March and then February.

## ACT

>The act phase would be done by the marketing team of the company. The main takeaway will be the top three recommendations for the marketing.

### Recommendations

- My top three recommendations based on my analysis

1. The sales are very high on Tuesday and Saturday so we need to give dicounts on less sales products on these days.

2. Give offers to our Normal customers that will increase chances of becomming Members of the Store.

3. We need advertisment in the month of February to increase the sales in that month.