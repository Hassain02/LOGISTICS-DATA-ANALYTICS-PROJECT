# Logistics Data Analytics Project

## Project Overview

This project simulates a multi-city logistics operation handling shipments through multiple warehouses, transport modes, and logistics carriers.

The main objective is to analyze shipment delivery performance, identify cost and operational patterns, compare carrier and transport-mode performance, and establish a data analytics workflow that can support future logistics decisions.

The project follows a week-wise data analytics approach:

**Strategic Planning → Data Cleaning → Exploratory Data Analysis → Predictive Modeling → Business Recommendations**

## Project Objectives

The project aims to:

* Define a realistic logistics delivery-performance problem.
* Identify measurable logistics KPIs.
* Prepare a reliable analytical dataset from intentionally unclean shipment records.
* Perform data cleaning and preprocessing using Python.
* Analyze delivery reliability and transportation costs.
* Compare performance across carriers and transport modes.
* Analyze weekly operational trends.
* Identify relationships between logistics variables using correlation analysis.
* Develop a predictive model for actual delivery duration in the next stage of the project.

## Dataset

The project uses an intentionally unclean logistics shipment dataset.

### Raw Dataset

* **Rows:** 12,150
* **Columns:** 21
* **Duplicate records:** 150
* **Cleaned records:** 12,000

The raw dataset contains realistic data-quality issues such as:

* Missing values
* Duplicate records
* Inconsistent capitalization
* Leading/trailing whitespace
* Mixed date formats
* Invalid numerical values
* Non-positive distance, weight, and shipping-cost values
* Extreme values and outliers
* Blank cells

### Main Variables

The dataset contains information related to:

* Shipment ID
* Origin
* Destination
* Order Date
* Ship Date
* Delivery Date
* Transport Mode
* Carrier
* Shipment Status
* Warehouse ID
* Product Category
* Distance
* Shipment Weight
* Quantity
* Shipping Cost
* Promised Delivery Days
* Actual Delivery Days
* Customer Rating
* Fuel Surcharge
* Warehouse Processing Hours
* Temperature

# Week 1 – Strategic Planning and Data Exploration

## Logistics Scenario

The project simulates a multi-city logistics operation where shipments are processed through warehouses and delivered using multiple transportation modes and carriers.

Management wants to improve delivery reliability while controlling transportation and warehouse-related costs.

## Business Problem

The business needs to understand:

* Factors associated with late deliveries.
* Factors associated with high shipment costs.
* Differences in performance among carriers.
* Differences between transportation modes.
* Weekly changes in delivery performance.
* Relationships between operational and cost variables.

The analysis is intended to support future operational decision-making.

## Key KPIs

| KPI                                |      Value |
| ---------------------------------- | ---------: |
| Total Shipments                    |     12,000 |
| On-Time Delivery Rate              |     60.84% |
| Average Delivery Days              |       3.78 |
| Average Shipping Cost              | ₹11,632.44 |
| Average Customer Rating            |       3.00 |
| Average Warehouse Processing Hours |      11.99 |

## Data Science Methods

The project considers the following analytical methods:

* **Exploratory Data Analysis (EDA):** Identify delivery, cost, and operational patterns.
* **Regression:** Predict actual delivery duration.
* **Clustering:** Potentially group similar shipments or operational segments.
* **Optimization:** Support carrier/resource allocation and cost minimization.
* **Visualization:** Communicate logistics performance and identify bottlenecks.

## Project Roadmap

text
Data Collection
      ↓
Data Quality Assessment
      ↓
Data Cleaning & Preprocessing
      ↓
Exploratory Data Analysis
      ↓
Visualization
      ↓
Predictive Modeling
      ↓
Model Evaluation
      ↓
Optimization & Recommendations


# Week 2 – Data Collection, Cleaning and Preprocessing

The raw dataset contained 12,150 rows and 21 columns.

After cleaning, 150 duplicate records were removed, resulting in a final analytical dataset containing 12,000 records.

## Data Quality Issues Identified

The raw dataset contained:

* Missing customer ratings.
* Missing carrier information.
* Missing distance values.
* Missing warehouse processing values.
* Missing temperature values.
* Missing product categories.
* Duplicate shipment records.
* Inconsistent capitalization.
* Extra whitespace.
* Mixed date formats.
* Invalid distance values.
* Invalid shipment weight values.
* Invalid shipping-cost values.
* Extreme delivery-time observations.
* Extreme shipping-cost observations.
* Blank cells.

## Cleaning Actions

The following preprocessing steps were performed:

1. Blank strings were converted to missing values.
2. Exact duplicate records were identified and removed.
3. Categorical variables were trimmed and standardized.
4. Date columns were converted to datetime format.
5. Numerical columns were converted to numeric data types.
6. Invalid non-positive distance values were converted to missing values.
7. Invalid non-positive weight values were converted to missing values.
8. Invalid non-positive shipping costs were converted to missing values.
9. Customer ratings outside the 1–5 range were treated as invalid.
10. Missing numerical values were filled using median values.
11. Missing categorical values were filled using mode values.
12. Missing dates were forward/backward filled.
13. Selected numerical outliers were identified using the IQR method and capped.
14. Additional analytical variables were created.

## Derived Variables

The following features were created during preprocessing:

### Delivery Delay

Delivery_Delay_Days =
Actual_Delivery_Days - Promised_Delivery_Days

### Late Delivery Flag

Late_Delivery = 1
when actual delivery exceeded promised delivery time.

### Cost per Kilometre

Cost_per_km =
Shipping_Cost_INR / Distance_km


### Order Week

The order date was converted into an ISO calendar week to support weekly performance analysis.

### Order Month

The order date was also converted into a month-based analytical variable.

## Cleaning Summary

| Measure                 | Result |
| ----------------------- | -----: |
| Raw Rows                | 12,150 |
| Raw Columns             |     21 |
| Duplicate Rows Removed  |    150 |
| Invalid Distance Values |     30 |
| Invalid Weight Values   |     25 |
| Invalid Cost Values     |     20 |
| Invalid Ratings         |      0 |
| Final Cleaned Rows      | 12,000 |

The cleaned dataset was saved separately so that the original raw dataset remained available as evidence of the data-cleaning process.


# Week 3 – Advanced Data Analysis and Visualization

The cleaned dataset was analyzed to understand:

* Delivery reliability.
* Carrier performance.
* Transportation-mode performance.
* Shipping-cost patterns.
* Weekly delivery trends.
* Relationships between numerical variables.
* Delivery delays.

## 3.1 Carrier Performance

| Carrier      | Shipments | Avg Delivery Days | On-Time Rate | Avg Cost (INR) |
| ------------ | --------: | ----------------: | -----------: | -------------: |
| Bluedart     |     1,742 |              3.75 |       62.86% |      11,344.29 |
| Fedex        |     1,558 |              3.71 |       61.75% |      11,524.09 |
| Dhl          |     3,726 |              3.81 |       60.31% |      11,705.46 |
| Delhivery    |     3,261 |              3.78 |       60.29% |      11,681.29 |
| Ecom Express |     1,713 |              3.80 |       60.19% |      11,772.20 |

### Observation

Bluedart recorded the highest on-time delivery rate among the listed carriers at **62.86%** and also had the lowest average shipping cost among the carriers shown.

Ecom Express had the lowest on-time delivery rate at **60.19%** and the highest average shipping cost at **₹11,772.20**.

These results suggest that carrier selection should consider both reliability and cost.



## 3.2 Transport Mode Performance

| Transport Mode | Shipments | Avg Delivery Days | On-Time Rate | Avg Cost (INR) |
| -------------- | --------: | ----------------: | -----------: | -------------: |
| Rail           |     3,995 |              3.76 |       62.25% |      11,822.99 |
| Road           |     4,002 |              3.79 |       60.29% |      11,548.19 |
| Air            |     4,003 |              3.79 |       59.98% |      11,526.51 |

### Observation

Rail had the highest on-time delivery rate at **62.25%** and the shortest average delivery time at **3.76 days**.

Air had the lowest on-time delivery rate at **59.98%**, despite having a slightly lower average cost than road and rail in this dataset.

Therefore, transportation-mode selection should consider the required service level rather than assuming that faster or more expensive transportation automatically produces better delivery reliability.



## 3.3 Weekly Performance

Weekly analysis was performed using the `Order_Week` variable.

The analysis tracks:

* Shipment volume.
* Average delivery duration.
* On-time delivery rate.
* Average shipping cost.

### Key Weekly Observations

* Week 5 recorded the highest on-time delivery rate among the displayed weeks at **64.52%**.
* Week 14 recorded an on-time delivery rate of **64.44%**.
* Week 1 recorded the lowest on-time delivery rate among the displayed weeks at **56.73%**.
* Week 18 had the highest average delivery duration at **4.00 days**.
* Week 5 had the highest average shipping cost at **₹12,380.55**.

Weekly monitoring can help identify periods where operational performance changes significantly.



# 3.4 Correlation Analysis

Correlation analysis was performed on the main numerical logistics variables.

Important relationships identified include:

| Variable Pair                                  | Correlation |
| ---------------------------------------------- | ----------: |
| Distance vs Shipping Cost                      |        0.83 |
| Distance vs Fuel Surcharge                     |        0.78 |
| Shipping Cost vs Fuel Surcharge                |        0.66 |
| Promised Delivery Days vs Actual Delivery Days |        0.72 |

### Interpretation

The strongest observed relationship is between **distance and shipping cost**, with a correlation of **0.83**. This indicates that longer shipment distances are strongly associated with higher shipping costs.

Distance also has a strong positive relationship with fuel surcharge, with a correlation of **0.78**.

Promised delivery duration and actual delivery duration have a correlation of **0.72**, indicating that promised delivery duration is strongly associated with actual delivery duration.

Most other variables show weak relationships.

Correlation analysis is used as an initial diagnostic tool. **Correlation does not establish causation**, so these relationships should be investigated further using predictive modeling and additional analysis.



# 3.5 Visualizations

The Week 3 analysis includes the following visualizations:

### Distribution of Actual Delivery Days

A histogram was used to understand the distribution and spread of actual shipment delivery times.

### On-Time Delivery Rate by Carrier

A bar chart was used to compare delivery reliability across carriers.

### Average Shipping Cost by Transport Mode

A bar chart was used to compare transportation costs across rail, road, and air.

### Shipping Cost vs Distance

A scatter plot was used to examine the relationship between shipment distance and shipping expenditure.

### Weekly On-Time Delivery Rate

A line chart was used to monitor changes in delivery reliability across order weeks.

These visualizations provide an operational view of logistics performance and help identify potential areas for improvement.



# Overall Findings

The cleaned dataset has an overall on-time delivery rate of **60.84%** and an average actual delivery time of **3.78 days**.

Carrier performance varies across both reliability and cost. Bluedart recorded the highest on-time delivery rate among the analyzed carriers, while Ecom Express recorded the lowest.

Among transportation modes, rail showed the strongest on-time performance, while air recorded the lowest on-time rate in the analyzed dataset.

Weekly analysis shows that delivery performance changes across different periods, making weekly monitoring useful for identifying operational fluctuations.

The strong relationship between distance and shipping cost indicates that route distance is an important cost driver. Fuel surcharge also shows a strong relationship with distance.

Overall, logistics decisions should not rely on a single KPI. Carrier reliability, transportation mode, delivery duration, shipment distance, and cost should be considered together.



# Project Structure

A suggested GitHub repository structure is:

LOGISTICS-DATA-ANALYTICS-PROJECT/
│
├── README.md
│
├── data/
│   ├── Unclean_Logistics_Dataset_12000plus_Rows.xlsx
│   └── Cleaned_Logistics_Dataset_Final.xlsx
│
├── notebooks/
│   ├── Week1_Strategic_Planning.ipynb
│   ├── Week2_Data_Cleaning.ipynb
│   ├── Week3_EDA.ipynb
│   └── Week4_Predictive_Modeling.ipynb
│
├── reports/
│   ├── Week1_Report.pdf
│   ├── Week2_Report.pdf
│   ├── Week3_Report.pdf
│   └── Week4_Report.pdf
│
├── outputs/
│   └── Week3_EDA_Results.xlsx
│
└── requirements.txt


# Technologies Used

The project uses Python-based data analytics tools.

### Programming Language

* Python

### Libraries

* Pandas
* NumPy
* Matplotlib
* OpenPyXL
* Scikit-learn *(for predictive modeling in Week 4)*

### Development Environment

* Jupyter Notebook
* Anaconda

# Expected Week 4 – Predictive Modeling

The next stage of the project focuses on predictive modeling.

The primary objective is to develop a model capable of predicting:

Actual_Delivery_Days


Potential input variables include:

* Distance
* Shipment Weight
* Quantity
* Transport Mode
* Carrier
* Promised Delivery Days
* Warehouse Processing Hours
* Temperature
* Fuel Surcharge
* Other relevant operational variables

Potential regression models include:

* Linear Regression
* Decision Tree Regression
* Random Forest Regression

The models can be evaluated using appropriate regression metrics such as:

* MAE
* RMSE
* R²

The best-performing model can then be used to support logistics planning and delivery-duration estimation.


# Business Recommendations

Based on the Week 3 analysis:

1. **Monitor carrier performance regularly** using on-time delivery rate and average delivery cost.

2. **Consider both cost and reliability** when selecting carriers instead of optimizing only for shipping cost.

3. **Evaluate transportation modes according to service requirements**, since the observed performance differs between rail, road, and air.

4. **Monitor weekly delivery performance** to identify periods of declining service quality.

5. **Investigate high-cost routes**, particularly shipments with unusually high cost relative to distance.

6. **Use distance as an important cost-planning variable**, given its strong relationship with shipping cost.

7. **Use predictive modeling in Week 4** to identify factors associated with actual delivery duration.


# Conclusion

This project establishes a complete logistics analytics workflow starting with an intentionally unclean dataset and progressing through data quality assessment, cleaning, preprocessing, exploratory analysis, visualization, and predictive modeling.

The Week 1 analysis established the business problem and KPIs. Week 2 transformed the raw shipment records into a cleaner analytical dataset. Week 3 analyzed delivery reliability, transportation costs, carrier performance, transport modes, weekly trends, and numerical relationships.

The next stage is predictive modeling, which can extend the analysis from understanding historical logistics performance to estimating future delivery duration.


## Author

**Logistics Data Analytics Project**

**Tools:** Python | Pandas | NumPy | Matplotlib | Jupyter Notebook | Excel

