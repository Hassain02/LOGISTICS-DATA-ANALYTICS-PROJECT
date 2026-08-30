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



WEEK 3 - LOGISTICS DATA EXPLORATORY DATA ANALYSIS (EDA)


import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

pd.set_option("display.max_columns", None)
pd.set_option("display.width", 150)

print("=" * 70)
print("WEEK 3 - LOGISTICS DATA EXPLORATORY DATA ANALYSIS")
print("=" * 70)

1. LOAD CLEANED DATASET

file_path = r"C:\Users\LENOVO\Downloads\Unclean_Logistics_Dataset_12000plus_Rows.xlsx"

df = pd.read_excel(file_path)

print("\nDataset loaded successfully!")
print("Rows    :", df.shape[0])
print("Columns :", df.shape[1])

print("\nColumn Names:")
print(df.columns.tolist())

display(df.head())


2. CHECK REQUIRED COLUMNS

required_columns = [
    "Shipment_ID",
    "Distance_km",
    "Shipment_Weight_kg",
    "Quantity",
    "Shipping_Cost_INR",
    "Promised_Delivery_Days",
    "Actual_Delivery_Days",
    "Customer_Rating",
    "Fuel_Surcharge_INR",
    "Warehouse_Processing_Hours",
    "Temperature_C",
    "Transport_Mode",
    "Carrier",
    "Warehouse_ID",
    "Product_Category"
]

missing_required = [
    col for col in required_columns
    if col not in df.columns
]

if missing_required:

    print("\nERROR: The following required columns are missing:")
    
    for col in missing_required:
        print("-", col)

    raise ValueError(
        "Required columns are missing from the Excel file."
    )

else:
    print("\nAll required columns are available.")


 3. CREATE DELIVERY DELAY COLUMN IF MISSING

if "Delivery_Delay_Days" not in df.columns:

    df["Delivery_Delay_Days"] = (
        df["Actual_Delivery_Days"]
        - df["Promised_Delivery_Days"]
    )

    print("\nCreated column: Delivery_Delay_Days")

else:

    print("\nDelivery_Delay_Days already exists.")


 4. CREATE LATE DELIVERY COLUMN IF MISSING

if "Late_Delivery" not in df.columns:

    df["Late_Delivery"] = (
        df["Delivery_Delay_Days"] > 0
    ).astype(int)

    print("Created column: Late_Delivery")

else:

    print("Late_Delivery already exists.")


 5. CREATE COST PER KM IF MISSING

if "Cost_per_km" not in df.columns:

    df["Cost_per_km"] = (
        df["Shipping_Cost_INR"]
        / df["Distance_km"]
    )

    print("Created column: Cost_per_km")

else:

    print("Cost_per_km already exists.")


6. CREATE ORDER WEEK IF MISSING

if "Order_Week" not in df.columns:

    df["Order_Date"] = pd.to_datetime(
        df["Order_Date"],
        errors="coerce"
    )

    df["Order_Week"] = (
        df["Order_Date"]
        .dt.isocalendar()
        .week
    )

    print("Created column: Order_Week")

else:

    print("Order_Week already exists.")


7. BASIC DATASET INFORMATION

print("\n" + "=" * 70)
print("DATASET INFORMATION")
print("=" * 70)

print("\nDataset Shape:")
print(df.shape)

print("\nData Types:")
print(df.dtypes)

print("\nDataset Information:")
df.info()


8. DESCRIPTIVE STATISTICS

print("\n" + "=" * 70)
print("DESCRIPTIVE STATISTICS")
print("=" * 70)

print("\nNumerical Statistics:")

display(
    df.describe()
)


print("\nCategorical Statistics:")

display(
    df.describe(include="object")
)


9. MISSING VALUE ANALYSIS

print("\n" + "=" * 70)
print("MISSING VALUE ANALYSIS")
print("=" * 70)

missing_values = df.isnull().sum()

missing_table = pd.DataFrame({
    "Column": missing_values.index,
    "Missing_Values": missing_values.values
})

missing_table = missing_table[
    missing_table["Missing_Values"] > 0
]

if len(missing_table) == 0:

    print("No missing values found.")

else:

    display(missing_table)

 10. DUPLICATE ANALYSIS
print("\n" + "=" * 70)
print("DUPLICATE ANALYSIS")
print("=" * 70)

duplicate_count = df.duplicated().sum()

print(
    "Duplicate rows:",
    duplicate_count
)


 11. MAIN LOGISTICS KPIs

print("\n" + "=" * 70)
print("MAIN LOGISTICS KPIs")
print("=" * 70)

total_shipments = len(df)

on_time_rate = (
    (df["Late_Delivery"] == 0)
    .mean()
    * 100
)

late_delivery_rate = (
    (df["Late_Delivery"] == 1)
    .mean()
    * 100
)

average_delivery = (
    df["Actual_Delivery_Days"]
    .mean()
)

average_cost = (
    df["Shipping_Cost_INR"]
    .mean()
)

average_rating = (
    df["Customer_Rating"]
    .mean()
)

average_processing = (
    df["Warehouse_Processing_Hours"]
    .mean()
)

total_shipping_cost = (
    df["Shipping_Cost_INR"]
    .sum()
)

average_distance = (
    df["Distance_km"]
    .mean()
)


print(
    "Total Shipments               :",
    total_shipments
)

print(
    "On-Time Delivery Rate         :",
    round(on_time_rate, 2),
    "%"
)

print(
    "Late Delivery Rate            :",
    round(late_delivery_rate, 2),
    "%"
)

print(
    "Average Delivery Days         :",
    round(average_delivery, 2)
)

print(
    "Average Shipping Cost         :",
    round(average_cost, 2),
    "INR"
)

print(
    "Total Shipping Cost           :",
    round(total_shipping_cost, 2),
    "INR"
)

print(
    "Average Customer Rating       :",
    round(average_rating, 2)
)

print(
    "Average Warehouse Processing  :",
    round(average_processing, 2),
    "hours"
)

print(
    "Average Distance              :",
    round(average_distance, 2),
    "km"
)


12. KPI SUMMARY TABLE

kpi_summary = pd.DataFrame({

    "KPI": [
        "Total Shipments",
        "On-Time Delivery Rate (%)",
        "Late Delivery Rate (%)",
        "Average Delivery Days",
        "Average Shipping Cost (INR)",
        "Total Shipping Cost (INR)",
        "Average Customer Rating",
        "Average Warehouse Processing Hours",
        "Average Distance (km)"
    ],

    "Value": [
        total_shipments,
        round(on_time_rate, 2),
        round(late_delivery_rate, 2),
        round(average_delivery, 2),
        round(average_cost, 2),
        round(total_shipping_cost, 2),
        round(average_rating, 2),
        round(average_processing, 2),
        round(average_distance, 2)
    ]
})

print("\nKPI SUMMARY")

display(kpi_summary)


 13. DELIVERY DAYS DISTRIBUTION

print("\n" + "=" * 70)
print("DELIVERY DAYS DISTRIBUTION")
print("=" * 70)

plt.figure(figsize=(8, 5))

plt.hist(
    df["Actual_Delivery_Days"],
    bins=20
)

plt.title(
    "Distribution of Actual Delivery Days"
)

plt.xlabel(
    "Actual Delivery Days"
)

plt.ylabel(
    "Number of Shipments"
)

plt.tight_layout()
plt.show()


14. CARRIER PERFORMANCE

print("\n" + "=" * 70)
print("CARRIER PERFORMANCE")
print("=" * 70)

carrier_performance = df.groupby(
    "Carrier"
).agg(

    Shipments=(
        "Shipment_ID",
        "count"
    ),

    Average_Delivery_Days=(
        "Actual_Delivery_Days",
        "mean"
    ),

    On_Time_Rate=(
        "Late_Delivery",
        lambda x:
        (1 - x.mean()) * 100
    ),

    Average_Shipping_Cost=(
        "Shipping_Cost_INR",
        "mean"
    )

).round(2)

carrier_performance = (
    carrier_performance
    .sort_values(
        "On_Time_Rate",
        ascending=False
    )
)

display(carrier_performance)


 15. CARRIER ON-TIME CHART

plt.figure(figsize=(9, 5))

carrier_performance[
    "On_Time_Rate"
].plot(
    kind="bar"
)

plt.title(
    "On-Time Delivery Rate by Carrier"
)

plt.xlabel(
    "Carrier"
)

plt.ylabel(
    "On-Time Delivery Rate (%)"
)

plt.xticks(
    rotation=45,
    ha="right"
)

plt.tight_layout()
plt.show()

16. TRANSPORT MODE PERFORMANCE

print("\n" + "=" * 70)
print("TRANSPORT MODE PERFORMANCE")
print("=" * 70)

mode_performance = df.groupby(
    "Transport_Mode"
).agg(

    Shipments=(
        "Shipment_ID",
        "count"
    ),

    Average_Delivery_Days=(
        "Actual_Delivery_Days",
        "mean"
    ),

    On_Time_Rate=(
        "Late_Delivery",
        lambda x:
        (1 - x.mean()) * 100
    ),

    Average_Shipping_Cost=(
        "Shipping_Cost_INR",
        "mean"
    )

).round(2)

mode_performance = (
    mode_performance
    .sort_values(
        "On_Time_Rate",
        ascending=False
    )
)

display(mode_performance)


17. TRANSPORT MODE COST CHART

plt.figure(figsize=(8, 5))

mode_performance[
    "Average_Shipping_Cost"
].plot(
    kind="bar"
)

plt.title(
    "Average Shipping Cost by Transport Mode"
)

plt.xlabel(
    "Transport Mode"
)

plt.ylabel(
    "Average Shipping Cost (INR)"
)

plt.tight_layout()
plt.show()


18. SHIPPING COST VS DISTANCE

print("\n" + "=" * 70)
print("SHIPPING COST VS DISTANCE")
print("=" * 70)

plt.figure(figsize=(8, 5))

plt.scatter(
    df["Distance_km"],
    df["Shipping_Cost_INR"],
    alpha=0.3
)

plt.title(
    "Shipping Cost vs Distance"
)

plt.xlabel(
    "Distance (km)"
)

plt.ylabel(
    "Shipping Cost (INR)"
)

plt.tight_layout()
plt.show()


 19. WEEKLY PERFORMANCE

print("\n" + "=" * 70)
print("WEEKLY DELIVERY PERFORMANCE")
print("=" * 70)

weekly_performance = df.groupby(
    "Order_Week"
).agg(

    Shipments=(
        "Shipment_ID",
        "count"
    ),

    Average_Delivery_Days=(
        "Actual_Delivery_Days",
        "mean"
    ),

    On_Time_Rate=(
        "Late_Delivery",
        lambda x:
        (1 - x.mean()) * 100
    ),

    Average_Shipping_Cost=(
        "Shipping_Cost_INR",
        "mean"
    )

).round(2)

display(weekly_performance)


 20. WEEKLY ON-TIME DELIVERY CHART

plt.figure(figsize=(10, 5))

plt.plot(
    weekly_performance.index,
    weekly_performance["On_Time_Rate"],
    marker="o"
)

plt.title(
    "Weekly On-Time Delivery Rate"
)

plt.xlabel(
    "Order Week"
)

plt.ylabel(
    "On-Time Delivery Rate (%)"
)

plt.grid(True)

plt.tight_layout()
plt.show()

 21. WEEKLY AVERAGE DELIVERY CHART

plt.figure(figsize=(10, 5))

plt.plot(
    weekly_performance.index,
    weekly_performance[
    "Average_Delivery_Days"
    ],
    marker="o"
)

plt.title(
    "Weekly Average Delivery Days"
)

plt.xlabel(
    "Order Week"
)

plt.ylabel(
    "Average Delivery Days"
)

plt.grid(True)

plt.tight_layout()
plt.show()


22. CORRELATION ANALYSIS

print("\n" + "=" * 70)
print("CORRELATION ANALYSIS")
print("=" * 70)

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

# Make sure only existing columns are used

numeric_columns = [
    col
    for col in numeric_columns
    if col in df.columns
]

correlation_matrix = (
    df[numeric_columns]
    .corr()
)

display(
    correlation_matrix.round(2)
)


 23. CORRELATION VISUALIZATION

plt.figure(figsize=(11, 8))

plt.imshow(
    correlation_matrix,
    aspect="auto"
)

plt.colorbar()

plt.xticks(
    range(
        len(correlation_matrix.columns)
    ),
    correlation_matrix.columns,
    rotation=90
)

plt.yticks(
    range(
        len(correlation_matrix.columns)
    ),
    correlation_matrix.columns
)

plt.title(
    "Correlation Matrix of Logistics Variables"
)

plt.tight_layout()
plt.show()


 24. LATE DELIVERY ANALYSIS

print("\n" + "=" * 70)
print("LATE DELIVERY ANALYSIS")
print("=" * 70)

late_shipments = df[
    df["Late_Delivery"] == 1
]

on_time_shipments = df[
    df["Late_Delivery"] == 0
]

print(
    "Total Shipments    :",
    len(df)
)

print(
    "On-Time Shipments  :",
    len(on_time_shipments)
)

print(
    "Late Shipments     :",
    len(late_shipments)
)

print(
    "Late Delivery Rate :",
    round(
        len(late_shipments)
        / len(df)
        * 100,
        2
    ),
    "%"
)


 25. DELIVERY DELAY ANALYSIS

print("\n" + "=" * 70)
print("DELIVERY DELAY ANALYSIS")
print("=" * 70)

delay_summary = (
    df["Delivery_Delay_Days"]
    .describe()
)

display(delay_summary)

26. DELIVERY DELAY HISTOGRAM

plt.figure(figsize=(8, 5))

plt.hist(
    df["Delivery_Delay_Days"],
    bins=20
)

plt.title(
    "Distribution of Delivery Delay"
)

plt.xlabel(
    "Delivery Delay (Days)"
)

plt.ylabel(
    "Number of Shipments"
)

plt.tight_layout()
plt.show()


 27. BEST AND WORST CARRIER

print("\n" + "=" * 70)
print("CARRIER FINDINGS")
print("=" * 70)

best_carrier = (
    carrier_performance[
        "On_Time_Rate"
    ].idxmax()
)

best_carrier_rate = (
    carrier_performance[
        "On_Time_Rate"
    ].max()
)

worst_carrier = (
    carrier_performance[
        "On_Time_Rate"
    ].idxmin()
)

worst_carrier_rate = (
    carrier_performance[
        "On_Time_Rate"
    ].min()
)

print(
    "Best Carrier:",
    best_carrier,
    "-",
    round(
        best_carrier_rate,
        2
    ),
    "%"
)

print(
    "Lowest Performing Carrier:",
    worst_carrier,
    "-",
    round(
        worst_carrier_rate,
        2
    ),
    "%"
)


 28. BEST TRANSPORT MODE

print("\n" + "=" * 70)
print("TRANSPORT MODE FINDINGS")
print("=" * 70)

best_mode = (
    mode_performance[
        "On_Time_Rate"
    ].idxmax()
)

best_mode_rate = (
    mode_performance[
        "On_Time_Rate"
    ].max()
)

most_expensive_mode = (
    mode_performance[
        "Average_Shipping_Cost"
    ].idxmax()
)

most_expensive_cost = (
    mode_performance[
        "Average_Shipping_Cost"
    ].max()
)

print(
    "Best Transport Mode:",
    best_mode,
    "-",
    round(
        best_mode_rate,
        2
    ),
    "%"
)

print(
    "Most Expensive Transport Mode:",
    most_expensive_mode,
    "-",
    round(
        most_expensive_cost,
        2
    ),
    "INR"
)


 29. COST PER KM ANALYSIS

print("\n" + "=" * 70)
print("COST PER KILOMETRE ANALYSIS")
print("=" * 70)

print(
    "Average Cost per KM:",
    round(
        df["Cost_per_km"].mean(),
        2
    ),
    "INR/km"
)

high_cost_shipments = (
    df.sort_values(
        "Cost_per_km",
        ascending=False
    ).head(10)
)

print("\nTop 10 Highest Cost-per-KM Shipments:")

display(
    high_cost_shipments[
        [
            "Shipment_ID",
            "Distance_km",
            "Shipping_Cost_INR",
            "Cost_per_km",
            "Transport_Mode",
            "Carrier"
        ]
    ]
)

 30. TOP ANALYTICAL FINDINGS

print("\n" + "=" * 70)
print("TOP ANALYTICAL FINDINGS")
print("=" * 70)

print(
    "\n1. Overall On-Time Delivery Rate:",
    round(on_time_rate, 2),
    "%"
)

print(
    "2. Average Delivery Duration:",
    round(average_delivery, 2),
    "days"
)

print(
    "3. Average Shipping Cost:",
    round(average_cost, 2),
    "INR"
)

print(
    "4. Best Performing Carrier:",
    best_carrier,
    "(",
    round(best_carrier_rate, 2),
    "% on-time)"
)

print(
    "5. Lowest Performing Carrier:",
    worst_carrier,
    "(",
    round(worst_carrier_rate, 2),
    "% on-time)"
)

print(
    "6. Best Transport Mode:",
    best_mode,
    "(",
    round(best_mode_rate, 2),
    "% on-time)"
)

print(
    "7. Most Expensive Transport Mode:",
    most_expensive_mode,
    "(",
    round(most_expensive_cost, 2),
    "INR average cost)"
)

31. SAVE ALL WEEK 3 RESULTS

output_file = "Week3_EDA_Results.xlsx"

with pd.ExcelWriter(
    output_file,
    engine="openpyxl"
) as writer:

    kpi_summary.to_excel(
        writer,
        sheet_name="KPI_Summary",
        index=False
    )

    carrier_performance.to_excel(
        writer,
        sheet_name="Carrier_Performance"
    )

    mode_performance.to_excel(
        writer,
        sheet_name="Transport_Mode"
    )

    weekly_performance.to_excel(
        writer,
        sheet_name="Weekly_Performance"
    )

    correlation_matrix.to_excel(
        writer,
        sheet_name="Correlation"
    )

    delay_summary.to_frame(
        name="Value"
    ).to_excel(
        writer,
        sheet_name="Delay_Summary"
    )

print("\n" + "=" * 70)
print("WEEK 3 EDA COMPLETED SUCCESSFULLY")
print("=" * 70)

print(
    "\nResults saved as:",
    output_file
)
 




