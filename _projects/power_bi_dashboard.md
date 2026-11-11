---
layout: page
title: Power BI Seasonality Report and Daily Inventory Dashboard 
description: Seasonality Report and Daily Inventory Dashboard made using SQL and Power BI
img: assets/img/project_img/sql_powerbi_26/seasonality_report_img.png
importance: 1
category: work
giscus_comments: true
---

# Highlights
* Created an end-to-end supply chain analytics pipeline managing daily sales data for 50 SKUs across 5 national distribution centers 
* Produced an interactive yearly seasonality report and daily inventory health interface tracking stockout risks and creating automated daily product reorder tables
* Resolved a key inventory logic flaw within a synthetic dataset and simulated new synthetic data, giving three distinct seasonal demand curves.
* Automated product classification into seasonal clusters by leveraging Scikit-learn K-Means with Min-Max normalized sales data.
* Validated model flexibility by creating random shifts to ensure classification accuracy
  
[repository link](https://github.com/benji-better/sql_powerbi)


<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/project_img/sql_powerbi_26/seasonality_report_img.png" title="seasonality report" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/project_img/sql_powerbi_26/powerbi_daily_dash_day_1.png" title="Daily Dash 1" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/project_img/sql_powerbi_26/powerbi_daily_dash_day_2.png" title="Daily Dash 2" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

**Project Overview**

Resolved key modeling issues within a synthetic dataset and created new synthetic data to simulate seasonal demand. With this data Scikit-learn was used to automate product seasonality grouping to construct a product seasonality report within Power BI. An additional National Inventory Daily Actions/Health dashboard was created which flags stockout risks and quantifies financial requirements for procurement. 

**Tools Used:** 
Python(Pandas, Scikit-learn), PostgreSQL, Power BI, Google Sheets

**Key Techniques:** 
Synthetic Data Generation, K-Means Clustering, Data Normalization, Power BI DAX Calculations 

...

**Fixing Inventory Modeling**

The dataset that I started with was a great starting point; however, the model within the dataset showed replacement inventory arriving in the warehouse for a given product instantaneously when on-hand product dropped below the reorder point (this change is shown below). Therefore, adequate reorder sizes were estimated using a moving average of the past ten days for each warehouse/SKU combination, then these averages were increased by 20% to deal with increasing sales or random fluctuations. To fix the inventory record problem, I deleted inventory levels and erased all inventory data except the first date using a function within the spreadsheet. After this I built a new reorder system within the sheet to accurately model sales, inventory levels, reorder and receiving dates using supplier lead times.

[comment]: Fixed Inventory System
<div class="row">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/project_img/sql_powerbi_26/fixed_model.png" title="Fixed Inventory System" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
...

My first goal after setting up my workspace and fixing the modeling in the dataset was to create an interactive seasonality dashboard. So I loaded the data into Python and analyzed the seasonality of the data. However, all product sale trends seemed to follow the same line, but some had higher peaks and lower troughs. Because of this, all raw data for 15 SKUs was reversed except for SKU and the date so that the seasonality trend was reversed to test techniques on different trends. Next, new sales data was generated in a spreadsheet to simulate five SKUs without seasonality. This created three different seasonality trends: Spring Products, Fall Products, and Non-Seasonal Products. After simulating the new products/ seasonal trends, and applying the inventory fixes to the entire dataset, the data was imported into a Postgres database from a CSV.

[comment]: Reversed Seasonality
<div class="row">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/project_img/sql_powerbi_26/reversed_seasonality.png" title="Reversed Seasonality" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
...

**Clustering Products using SciKit-Learn**

I established a secure connection between python and my database using an ENV file to store my login credentials outside of my Python script. To cluster the data, sales for each product were grouped into sales by week, the weekly sales were normalized (min-max), and then put into a k-means clustering algorithm to assign each product to a seasonality cluster. Additionally, to test the flexibility of the clustering I created another SKU just for testing. For this SKU I shifted the sales of a seasonal product forward by three weeks and used random numbers centered around the first week’s sale value for the missing sales for those shifted weeks. The k-means clustering was still able to group this slightly shifted trend into its correct seasonal cluster. A csv with SKU numbers and their corresponding clusters was downloaded.

Additionally, after separating products into clusters, I normalized daily sales for each product across the year to find which days showed the highest and lowest sales days for the two clusters with seasonality. I analyzed for high/low values at quarterly, bi-annual, and annual intervals. When working on this clustering part of the project I learned that effective use of SQL queries can make it far simpler to filter the data vs doing the manipulation in python and pandas.

<iframe width="560" height="315" src="https://www.youtube.com/embed/zxJYHrIUd1Y?si=Ok6aQ1gmEbZLyVPW" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

...

**Creating Seasonality Dashboard in Power BI**

My postgres database was connected with Power BI and I imported my seasonality clusters csv and merged the two tables. In order for the line chart included in this report to be clean and easily interpretable, I only queried a few SKUs from each seasonality group. Annual and quarterly tables for high/low sales days by seasonal groups were also added. In addition, the financial metrics revenue vs forecast (%), and gross profit above forecast ($) were also included. Finally, the seasonality group slicer within the report streamlines the viewing of the trends of the seasonality groups and some of their key metrics.


**Daily National Inventory Dashboard**

[comment]: Daily Dash 3
<div class="row">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/project_img/sql_powerbi_26/powerbi_daily_dash_day_3.png" title="Daily Dash 3" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

...

My next dashboard is a daily inventory health/ actions dashboard. Because all of the data was from 2024 I created a measure called Scenario Day which is exactly two years ago from today’s date within Power BI, and with this my dashboard can update every day to simulate a daily dashboard. All of the measures for the dashboard come from this Scenario Day. In the center of the dashboard is a slicer that filters the information by warehouse. 

I made two new measures that count the number of SKUs that are stocked out and below safety level and with these measures column charts showing these two metrics by warehouse were added along with KPI cards showing total national stockout and national below safety counts. Additionally, I made tables showing stockouts and products below safety levels for these products and at which warehouse was having this shortage. I also included supplier lead times in the stockout and below safety tables as I feel that this is the best indicator for whether a product will get dangerously low with how my reorder size and reorder points currently are set in the dataset. 

Next, I made a measure that simulates whether a product below its reorder point is on reorder or not since I didn’t leave the ‘On Reorder’ indicator in the data set that I imported into my database. From this new ‘On Reorder’ indicator, I made a table showing all of the SKUs that need to be reordered and for which warehouse they need to be reordered. This list of reorders that need to be made could be exported to make these orders manually or to check that all automated Enterprise Resource Planning System orders went through for the day.  

To tie in the financial side of the supply chain, I also added KPI Cards showing revenue lost yesterday from stockouts, actual sales vs forecasted sales yesterday as a percentage, the total dollar amount of product needed to be reordered today, and revenue yesterday. 

**Next Steps / Further Improvements**

I would like to continue working on these dashboards by cleaning up their underlying queries. Specifically I would first improve the performance of the daily dashboard by only querying the days that will be used in the dashboard, instead of querying all 365 days of the year. Also, with this new synthetic dataset that I've constructed, I would like to do some things with turnover optimization and stockout avoidance.

**Original Dataset Used**

https://www.kaggle.com/datasets/ziya07/high-dimensional-supply-chain-inventory-dataset
