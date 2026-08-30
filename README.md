# LOGISTICS-DATA-ANALYTICS-PROJECT
The project simulates a multi-city logistics operation handling shipments through warehouses and multiple transport modes and carriers. Management wants to improve delivery reliability while controlling transportation and warehouse costs.
Project Objectives
•	Define a realistic logistics delivery-performance problem and measurable KPIs.
•	Prepare a reliable analytical dataset from intentionally unclean shipment records.
•	Identify delivery, cost and operational patterns using Python-based EDA.
•	Develop and evaluate a predictive model for actual delivery duration.

WEEK 1 – Strategic Planning and Data Exploration

1. Logistics Scenario
  The project simulates a multi-city logistics operation handling shipments through warehouses and multiple transport modes and carriers. Management wants to       improve delivery reliability while controlling transportation and warehouse costs.
2. Business Problem
  The business needs to understand the factors associated with late deliveries and high shipment costs, identify performance differences among carriers and         transport modes, and establish an analytical process that can support future operational decisions.
3. KPIs
 KPI	 Value
Total Shipments	12000.00
On-Time Delivery Rate	60.84
Average Delivery Days	3.78
Average Shipping Cost (INR)	11632.44
Average Customer Rating	3.00
Average Warehouse Processing Hours	11.99
4. Data Science Methods
•	Regression to forecast actual delivery duration.
•	Clustering as a possible future method for grouping similar shipments or operational segments.
•	Optimization to support carrier/resource allocation and cost minimization.
•	EDA and visualization to diagnose bottlenecks and cost drivers.
5. Roadmap
Data collection → Data-quality assessment → Cleaning/preprocessing → EDA → Visualization → Predictive modeling → Evaluation → Optimization recommendations
6. Python Illustration
Import pandas as pd 
df= pd.read_excel(r"C:\Users\LENOVO\Downloads\Unclean_Logistics_Dataset_12000plus_Rows.xlsx")
df.shape
df.dtypes
df.info()
df.describe(include=’all’)
df.isnull().sum()
This completes the strategic foundation for the project and establishes the variables and KPIs to be examined in later weeks.


WEEK 2 – Data Collection, Cleaning and Preprocessing


The raw dataset contained 12,150 rows and 21 columns. After replacing blank strings with missing values, 150 duplicate rows were identified and removed. Categorical variables were standardized, dates were parsed, numeric fields were validated, missing values were imputed, and selected outliers were capped using the IQR method.
1. Issues Identified
•	Missing values in rating, carrier, distance, processing time, temperature and product category.
•	Duplicate shipment records.
•	Inconsistent capitalization and whitespace.
•	Mixed date representations.
•	Non-positive distance, weight and shipping-cost values.
•	Extreme shipping-cost and delivery-time observations.
•	Blank strings that required conversion to true missing values.
2. Cleaning Actions
•	Blank strings converted to missing values.
•	Exact duplicate records removed.
•	Categorical text trimmed and standardized.
•	Date columns converted to datetime.
•	Numeric columns converted to numeric types.
•	Impossible non-positive values converted to missing values.
•	Missing numeric values filled with medians; categorical values filled with modes.
•	Selected numerical outliers detected with the IQR rule and capped rather than blindly deleting observations.
•	Derived fields created: delivery delay, late-delivery flag, cost per km, order week and order month.
3. Cleaning Summary
Measure	Result
Raw rows	12150
Raw columns	21
Duplicate rows removed	150
Invalid distance values	30
Invalid weight values	25
Invalid cost values	20
Invalid ratings	0
Cleaned rows	12000
4. Python Cleaning Illustration
import pandas as pd
import numpy as np

Load the dataset

df = pd.read_excel(r"C:\Users\LENOVO\Downloads\Unclean_Logistics_Dataset_12000plus_Rows.xlsx")
    

1. Define column groups
categorical_columns = [
    "Origin",
    "Destination",
    "Transport_Mode",
    "Carrier",
    "Shipment_Status",
    "Warehouse_ID",
    "Product_Category"
]

date_columns = [
    "Order_Date",
    "Ship_Date",
    "Delivery_Date"
]

numeric_columns = [
    "Distance_km",
    "Shipment_Weight_kg",
    "Quantity",
    "Shipping_Cost_INR",
    "Promised_Delivery_Days",
    "Actual_Delivery_Days",
    "Customer_Rating",
    "Fuel_Surcharge_INR",
    "Warehouse_Processing_Hours",
    "Temperature_C"
]

2. Replace blank cells with missing values

df = df.replace(r"^\s*$", pd.NA, regex=True)

 3. Remove duplicate rows

print("Duplicates before:", df.duplicated().sum())

df = df.drop_duplicates()

print("Duplicates after:", df.duplicated().sum())


 4. Clean categorical columns

for col in categorical_columns:
    df[col] = (
        df[col]
        .astype("string")
        .str.strip()
        .str.title()
    )

 5. Convert date columns

for col in date_columns:
    df[col] = pd.to_datetime(
        df[col],
        errors="coerce",
        dayfirst=True
    )


 6. Convert numerical columns

for col in numeric_columns:
    df[col] = pd.to_numeric(
        df[col],
        errors="coerce"
    )

7. Check missing values

print("\nMissing values:")
print(df.isnull().sum())


8. Handle invalid numerical values
   
Distance cannot be zero/negative

df.loc[df["Distance_km"] <= 0, "Distance_km"] = np.nan

 Weight cannot be zero/negative
 
df.loc[df["Shipment_Weight_kg"] <= 0, "Shipment_Weight_kg"] = np.nan

Shipping cost cannot be zero/negative

df.loc[df["Shipping_Cost_INR"] <= 0, "Shipping_Cost_INR"] = np.nan

 Customer rating should be between 1 and 5
df.loc[
    (df["Customer_Rating"] < 1) |
    (df["Customer_Rating"] > 5),
    "Customer_Rating"
] = np.nan


 9. Fill missing numerical values with median


for col in numeric_columns:
    df[col] = df[col].fillna(df[col].median())


10. Fill missing categorical values with mode


for col in categorical_columns:
    if df[col].isnull().any():
        mode_value = df[col].mode()[0]
        df[col] = df[col].fillna(mode_value)

 11. Fill missing dates

for col in date_columns:
    df[col] = df[col].ffill().bfill()

 12. Create useful logistics features

df["Delivery_Delay_Days"] = (
    df["Actual_Delivery_Days"]
    - df["Promised_Delivery_Days"]
)

df["Late_Delivery"] = (
    df["Delivery_Delay_Days"] > 0
).astype(int)

df["Cost_per_km"] = (
    df["Shipping_Cost_INR"] /
    df["Distance_km"]
)

df["Order_Week"] = (
    df["Order_Date"]
    .dt.isocalendar()
    .week
)

df["Order_Month"] = (
    df["Order_Date"]
    .dt.to_period("M")
    .astype(str)
)

 13. Final verification

print("\nFinal dataset shape:")
print(df.shape)

print("\nRemaining missing values:")
print(df.isnull().sum().sum())

print("\nFirst 5 rows:")
display(df.head())
The cleaned dataset is saved separately as Cleaned_Logistics_Dataset_Final.xlsx so the raw evidence is preserved.


WEEK 3 – Advanced Data Analysis and Visualization

The cleaned dataset was analyzed to understand delivery reliability, cost patterns, carrier performance, transport-mode differences and weekly operational trends.
3.1 Carrier Performance
Carrier	Shipments	Avg_Delivery_Days	On_Time_Rate	Avg_Cost_INR
Bluedart	1742	3.75	62.86	11344.29
Fedex	1558	3.71	61.75	11524.09
Dhl	3726	3.81	60.31	11705.46
Delhivery	3261	3.78	60.29	11681.29
Ecom Express	1713	3.80	60.19	11772.20
3.2 Transport Mode Performance
Transport_Mode	Shipments	Avg_Delivery_Days	On_Time_Rate	Avg_Cost_INR
Rail	3995	3.76	62.25	11822.99
Road	4002	3.79	60.29	11548.19
Air	4003	3.79	59.98	11526.51
3.3 Weekly Performance
Order_Week	Shipments	Avg_Delivery_Days	On_Time_Rate	Avg_Shipping_Cost
1.00	453.00	3.78	56.73	12169.06
2.00	584.00	3.65	61.13	11610.72
5.00	155.00	3.36	64.52	12380.55
6.00	600.00	3.71	62.83	11829.88
7.00	238.00	3.61	60.50	11549.13
9.00	152.00	3.97	62.50	12130.34
10.00	600.00	3.83	57.67	11453.26
11.00	245.00	3.69	62.04	11494.49
14.00	554.00	3.65	64.44	11341.21
15.00	536.00	3.82	59.14	11811.71
18.00	234.00	4.00	61.54	11623.81
19.00	594.00	3.85	61.28	11318.82
3.4 Correlation Matrix
index	Distance_km	Shipment_Weight_kg	Quantity	Shipping_Cost_INR	Promised_Delivery_Days	Actual_Delivery_Days	Customer_Rating	Fuel_Surcharge_INR	Warehouse_Processing_Hours	Temperature_C
Distance_km	1.00	-0.01	0.01	0.83	-0.00	0.00	-0.00	0.78	-0.00	-0.00
Shipment_Weight_kg	-0.01	1.00	-0.00	0.07	-0.02	-0.01	0.00	-0.01	0.01	-0.00
Quantity	0.01	-0.00	1.00	-0.01	-0.01	-0.01	0.00	0.00	-0.01	0.01
Shipping_Cost_INR	0.83	0.07	-0.01	1.00	0.00	0.02	0.00	0.66	-0.01	0.00
Promised_Delivery_Days	-0.00	-0.02	-0.01	0.00	1.00	0.72	-0.01	-0.00	0.01	-0.01
Actual_Delivery_Days	0.00	-0.01	-0.01	0.02	0.72	1.00	-0.02	-0.00	-0.00	-0.00
Customer_Rating	-0.00	0.00	0.00	0.00	-0.01	-0.02	1.00	-0.00	-0.01	0.00
Fuel_Surcharge_INR	0.78	-0.01	0.00	0.66	-0.00	-0.00	-0.00	1.00	-0.00	-0.00
Warehouse_Processing_Hours	-0.00	0.01	-0.01	-0.01	0.01	-0.00	-0.01	-0.00	1.00	0.01
Temperature_C	-0.00	-0.00	0.01	0.00	-0.01	-0.00	0.00	-0.00	0.01	1.00
 
Distribution of Actual Delivery Days
 
On-Time Delivery Rate by Carrier
 
Average Shipping Cost by Transport Mode
 
Shipping Cost vs Distance
 
Weekly On-Time Delivery Rate
3.5 Findings
The prepared dataset has an overall on-time delivery rate of 60.84% and an average actual delivery time of 3.78 days. Carrier and transport-mode comparisons show that operational performance differs across categories, so allocation decisions should consider both reliability and cost rather than a single KPI. The weekly chart provides a simple monitoring mechanism for identifying changes in service performance over time. The cost-versus-distance chart can be used to investigate routes with unusually high expenditure. Correlation analysis provides a first diagnostic view of relationships among numerical variables, while recognizing that correlation does not establish causation.






