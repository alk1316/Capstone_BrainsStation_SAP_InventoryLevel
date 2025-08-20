<a name="readme-top"></a>
<br />
<div align="center">
<h1>📌💡 Prediction of Purchase Time and Quantity by Customer and Product 📊🚀</h1>
<h2>BrainStation Capstone Project</h2>
<br/>
<div><img src="/notebooks/Assets/img/BrainStation_Main_Logo.png" width=""></div>
<br/>
</div>
<br/>

# Table of Contents 📝
1. [Project Overview](#project-overview)
    - [Introduction](#introduction)
    - [Project Motivation](#project-motivation)
    - [The Opportunity](#the-opportunity)
    - [Business Case](#business-case)
    - [Problem](#problem)
    - [Objectives](#objectives)
    - [Expected Outcome](#expected-outcome) 
2. [Dataset](#dataset)
    - [Dataset Gathering](#dataset-gathering) 
    - [Data Dictionary](#data-dictionary) 
3. [Project Roadmap](#project-roadmap)
    - [Data Collection](#data-collection) 
    - [Cleaning and Preprocessing](#cleaning-and-preprocessing) 
    - [Exploratory Data Analysis)](#exploratory-data-analysis)
    - [Model Selection)](#model-selection)
    - [Model Evaluation)](#model-evaluation)
    - [Model Deployment)](#model-deployment)
    - [Repository)](#repository)
    - [Learnings)](#learnings)
    - [Conclusion)](#conclusion)
    - [Next Steps)](#next-steps)
  <p align="right">(<a href="#readme-top">back to top</a>)</p>

# Project Overview 👨‍💻

## Introduction 📝
Efficient inventory management is a challenge for many businesses, as maintaining the right stock levels is critical to balance supply and demand. Overstocking can lead to increased holding costs and wasted resources, while stockouts can result in lost sales, poor customer satisfaction, and potential damage to the company’s reputation.

This project aims to answer the following two questions:

 1. **What is the average purchase time for each customer?** With this information, we can estimate when the customer will make their next purchase.
 2. **What is the average purchase quantity of the items?** This information allows us to anticipate the inventory quantities needed to cover future customer orders.

## Project Motivation 💭

This is a real work case:

At work a customer required a report (query) to calculate the time and quantity products to be purchase for each client.

Considering that a simple calculation of average purchase times and quantities is not sufficient, the idea is to apply a more efficient model to predict this information.

Although a simple calculation was requested at work, it raised the question of whether there is a way to obtain this information using a machine learning predictive model. This became the main motivation for the project: to answer the two previously mentioned questions, which represent a real business requirement.

## The Opportunity ✔️

A data scientist should be capable of tackling any problem where data serves as the primary resource for answering key questions.

This project represents a personal opportunity to apply the knowledge gained during the Bootcamp to a real-world scenario, and it aims to identify ways to address a critical issue in the business world. By leveraging business data, the goal is to improve decision-making related to inventory quantities, sales information, and purchasing trends.

As such, this project is both a personal growth opportunity and a chance to propose a solution that businesses genuinely need.

## Business Case 📈

For security reasons, the company's name is omitted.

However a brief information of the company:

The company is located in the USA and specializes in the manufacturing and printing of foodservice products.

Its main lines of business include: napkins, cups, bags, and TechnoLiners.

<br/>
<div><img src="/notebooks/Assets/img/BusinessLines.JPG" width=""></div>
<br/>
</div>


## Problem ❗⚠️
The lack of accurate demand forecasting can lead to misinformed purchasing and replenishment decisions, creating inefficiencies in the supply chain and affecting the commercial relationship with the client.

## Objectives 📌

1. **Data Exploration and Preprocessing:** Clean and prepare the dataset, ensuring that it is suitable for predictive analysis by addressing missing data, inconsistencies, and outliers.

2. **Sales Pattern Analysis:** Perform Exploratory Data Analysis (EDA) to identify patterns, in sales, which will help in understanding demand behavior.

3. **Model Development:** Use machine learning and time-series forecasting techniques to predict future demand and establish optimal stock levels for the business.

4. **Evaluation and Optimization:** Evaluate the model’s performance using appropriate metrics, refine the model to improve accuracy, and provide actionable insights for stock management.

## Expected Outcome: 📊
By the end of the project, we expect to answer the two main questions:
 1. **What is the average purchase time for each customer?**
 2. **What is the average purchase quantity of the items?**

By creating a predictive tool that will enable the business to optimize its inventory levels, reduce costs, and improve overall supply chain efficiency.

The insights gained from this model will support data-driven decision-making and enhance the company’s ability to meet customer demands with more precision.
 <p align="right">(<a href="#readme-top">back to top</a>)</p>

# Dataset 🔢

## Dataset Gathering ⌨

Since the dataset to be used contains real company information, it cannot be shared publicly.

The data was extracted directly from the client's ERP system using the following query, which pulls all sales transaction information, resulting in a total of 16,724 rows and 28 columns.

Below is a query and table that describes the fields used in the invoice query, providing detailed information about each column, its purpose, and the table it originates from.

```
SELECT 
    T0."DocEntry",  -- Internal Id Invoice
    T0."DocNum",    -- Invoice Number
    T0."CardCode",  -- Customer Code
    T0."CardName",  -- Customer Name
    T2."GroupCode", -- Customer Group Code
    T4."GroupName", -- Customer Group Name 
    T2."Country",  -- Customer Country Code
    T5."Name" "CountryName", -- Customer Country Name
    T2."State1",  -- Customer State Code
    T6."Name" "StateName", -- Customer State Name
    T2."City",   -- Customer City
    T0."DocDate", -- Invoice Date
    T0."SlpCode", -- Sale Employee Code
    T0."DocCur", -- Invoice currency
    T0."DocRate", -- Invoice currency rate 
    T0."DocTotal", -- Invoice Total in Main Currency USD
    T0."DocTotalFC", -- Invoice Total in Foreign Currency
    T1."ItemCode", -- Item Code
    T3."ItmsGrpCod", -- Item Group Code
    T7."ItmsGrpNam", -- Item Group Name
    T3."ItemName", -- Item Description
    T1."Quantity", -- Quantity sold
    T1."WhsCode", -- Warehouse code
    T1."StockPrice", -- Cost of item in Invoice
    T1."PriceBefDi", -- Price before discount Invoiced
    T1."DiscPrcnt",  -- Discount per item in Invoice
    T1."Price",  -- Price after discount Invoiced
    T1."LineTotal" -- Total per Line in Main Currency USD (quatity per price after discount)

FROM "OINV" T0
INNER JOIN "INV1" T1 ON T0."DocEntry" = T1."DocEntry"
INNER JOIN "OCRD" T2 ON T0."CardCode" = T2."CardCode"
INNER JOIN "OITM" T3 ON T1."ItemCode" = T3."ItemCode"
INNER JOIN "OCRG" T4 ON T2."GroupCode" = T4."GroupCode"
INNER JOIN "OCRY" T5 ON T2."Country" = T5."Code"
LEFT JOIN "OCST" T6 ON T2."State1" = T6."Code"
INNER JOIN "OITB" T7 ON T3."ItmsGrpCod" = T7."ItmsGrpCod"

WHERE 
    T0."DocType" = 'I' -- Only Item Invoices
	AND T0."CANCELED"='N'
    AND T3."InvntItem"='Y'
ORDER BY T0."DocDate"

```

## Data Dictionary 🔣

| Column            | Description                                                                                         | Table   |
|-------------------|-----------------------------------------------------------------------------------------------------|---------|
| `T0."DocEntry"`   | Unique identifier for the invoice. This is the internal ID used in the system to reference the invoice. | OINV    |
| `T0."DocNum"`     | Invoice number assigned to the document. This is the number typically used for referencing invoices in reports and communications. | OINV    |
| `T0."CardCode"`   | Code that identifies the customer in the system. This is a unique identifier for each customer.       | OINV    |
| `T0."CardName"`   | Name of the customer as registered in the system. It corresponds to the company's or individual’s official name. | OINV    |
| `T2."GroupCode"`  | Code representing the customer group, used to categorize customers into different segments (e.g., retail, wholesale, etc.). | OCRD    |
| `T4."GroupName"`  | Descriptive name of the customer group, providing a more user-friendly label for the group code.     | OCRG    |
| `T2."Country"`    | Code representing the country of the customer, often using ISO standards for country codes.          | OCRD    |
| `T5."Name"`       | Full name of the country corresponding to the country code, giving a human-readable version of the customer's location. | OCRY    |
| `T2."State1"`     | Code representing the state or province of the customer, used in regions where such administrative divisions exist. | OCRD    |
| `T6."Name"`       | Full name of the state or province, corresponding to the state code. Provides a human-readable location identifier. | OCST    |
| `T2."City"`       | City where the customer is located, providing more granular geographic detail of the customer's address. | OCRD    |
| `T0."DocDate"`    | Date the invoice was created or issued. This is the official date of the transaction for accounting and auditing purposes. | OINV    |
| `T0."SlpCode"`    | Code that identifies the sales employee responsible for the invoice. This is used for tracking sales by representative. | OINV    |
| `T0."DocCur"`     | Currency in which the invoice was issued (e.g., USD, EUR), specifying the monetary unit for the transaction. | OINV    |
| `T0."DocRate"`    | Exchange rate applied if the invoice currency differs from the company’s main currency, showing the conversion factor. | OINV    |
| `T0."DocTotal"`   | Total amount of the invoice in the company's main currency (USD), including all charges and taxes. | OINV    |
| `T0."DocTotalFC"` | Total amount of the invoice in the foreign currency (if applicable), providing the total cost in the currency used for the transaction. | OINV    |
| `T1."ItemCode"`   | Code that uniquely identifies the item being sold, as registered in the inventory system.            | INV1    |
| `T3."ItmsGrpCod"` | Code representing the item group, categorizing items into different segments (e.g., electronics, clothing, etc.). | OITM    |
| `T7."ItmsGrpNam"` | Descriptive name of the item group, providing a human-readable label for the item group code.        | OITB    |
| `T1."ItemName"` | Description of the item, typically containing the item’s full name and additional details. | OITM    |
| `T1."Quantity"`   | Number of units sold of the item. Represents the quantity of the product included in the invoice.     | INV1    |
| `T1."WhsCode"`    | Code representing the warehouse from which the item is being shipped or managed. Helps track inventory across multiple locations. | INV1    |
| `T1."StockPrice"` | Cost of the item at the time of the invoice, based on stock valuation methods (e.g., FIFO, LIFO), used for cost analysis. | INV1    |
| `T1."PriceBefDi"` | Price of the item before any discounts are applied. This is the base price charged for the product.   | INV1    |
| `T1."DiscPrcnt"`  | Percentage discount applied to the item on the invoice. Represents the discount provided to the customer for that specific item. | INV1    |
| `T1."Price"`      | Final price of the item after the discount is applied. This is the amount that will be invoiced to the customer. | INV1    |
| `T1."LineTotal"`  | Total amount for the line item in the invoice, calculated as `Quantity * Price`, representing the total charge for that product in the invoice currency. | INV1    |

 <p align="right">(<a href="#readme-top">back to top</a>)</p>

# Project Roadmap 📖

<br/>
<div><img src="/notebooks/Assets/img/RoadMap.JPG" width=""></div>
<br/>
</div>

# 1. Data Collection 📚+🔺

Retrieve the necessary sales information to answer the two key questions of this project. A database query was used to extract sales data from customer's ERP SAP, including invoice number, customer, purchase date, items purchased, quantities, prices, and totals, as well as additional related information that can be used for other types of business analysis.

# 2. Cleaning and Preprocessing 👩🏻‍💻📓✍🏻💡

In this section, we will proceed with the initial data cleaning process, converting date data to datetime format, transforming object data into numerical values, removing any leading or trailing white spaces, and handling missing values if necessary.

By the end of this section, we will have a clearer understanding of the data and which information can be used for our purposes.

# 3. Exploratory Data Analysis 🕵

As part of the Data Exploration, the following information is analyzed:

- Sales Over Time Analysis

- Customer Analysis

    - Total sales per customer
    - Total sales per customer group
    - Sales evolution over time per customer

- Product Analysis

    - Top-selling products by total amount an quantity
    - Sales distribution by product group
    - Temporal trends by product and total sales amount

- Correlation Analysis

At the end of this analysis, we will focus our predictive model exclusively on the Top 5 customers and their most purchased top product.
 <p align="right">(<a href="#readme-top">back to top</a>)</p>

# 4. Model Selection 🧠

With the goal of answering our two questions:

 1. What is the estimate purchase time for each customer?
 2. What is the estimate purchase quantity of the items?

We will apply the following approaches:

- Projections based on statistical calculations:

    - Simple average calculation of the purchased time by customer
    - Simple average calculation of the purchased time by customer and quantities per product

- Linear Regression:
Task: Split the dataset into training, validation, and test sets
Objective: Train and fine-tune models to predict estimated purchase time per every customer.

For the linear regression, we will start with just two independent variables, quantity and price, to predict our dependent variable, 'days_between_purchase'.


- Time Series:
With the aim of forecasting quantities to be sold, we decided to apply a time series model, which is ideal for this type of case where detecting seasonality and trend patterns is essential.
 <p align="right">(<a href="#readme-top">back to top</a>)</p>

# 5. Model Evaluation 🎯
To evaluate each model, we will first apply metrics specific to each one. For example, in the case of linear regression, R² and MSE will help us assess how well the model predicts the days between each purchase.

However, the final evaluation will involve comparing each projection method against the actual values for the period from September 2023 to October 2024.

We will analyze how closely the projected results match the actual data, using the same historical dataset for consistency.

For the estimated days between purchases, we will compare the simple average method against the real values, and the linear regression method against the real values. Ultimately, we will identify which of the two methods is closer to the actual values and provides higher precision.

Regarding quantity calculations, we will determine the average quantities purchased per customer and product, representing the average quantity per order. This value will then be compared to the real purchase quantities.

Similarly, the projections generated by the time series model will be evaluated against the real quantities and the averages to determine the best option.
<p align="right">(<a href="#readme-top">back to top</a>)</p>

# 6. Model Deployment 🚀

After applying each model only for the client Bunzl R3 Philadelphia and after being adjusted the same models can be applied to the rest of the clients.

The results are as follows:

**Days Between Purchase - Average vs Linear Regression**

Performing a Linear Regression to predict days_between each purchase:

<br/>
<div><img src="/notebooks/Assets/img/LinearReg.JPG" width=""></div>
<br/>
</div>

**R2 = 0.439:**

The model now explains 43.9% of the variability in the dependent variable y = days_between using the independent variables X = Quantity, Price. 
This means that almost 44% of the observed differences in days_between can be attributed to changes in Quantity and Price. 

While it is not a perfect fit, it represents a significant improvement and demonstrates that Quantity and Price are relevant variables for explaining days_between.

**MSE = 81.99:**

The Mean Squared Error (MSE) measures the average squared error between the actual values and the predictions. A lower MSE value indicates that the model's predictions are closer to the real values. 

Compared to the initial model where MSE were 350, this number has decreased, indicating better prediction accuracy.


The following table shows a comparison between each method **Simple Average** and **Linear Regression** vs the real values.

<br/>
<div><img src="/notebooks/Assets/img/avg_lin.JPG" width=""></div>
<br/>
</div>

The linear method calculation shows an advantage over the linear regression method, as for 15 out of 28 records, the average calculation is closer to the actual value.

However, the difference is not very significant, as 13 records are more accurate with the linear regression method.

**Time Series Evaluation**

**Quantities Forecast - Comparing Average vs Time Series with real Values for 2024**

In the following charts we can observe the time serie decompised in following information:

- Original: The original data presented in monthly values.
- Trend: The trend component, isolated from seasonality.
- Seasonality: This chart displays only the seasonal component. As we can see, there is significant variation in the temporal patterns.
- Residual: Also known as noise, this represents external factors that cannot be controlled and affect the data's evolution.


<br/>
<div><img src="/notebooks/Assets/img/ts_decomp.JPG" width=""></div>
<br/>
</div>

This graph shows the historical data and the sales projection for the analyzed item '215X178WHBDC23.0M'.
The following table displays the projected values for each month that are expected to be purchased by the customer Bunzl.

<br/>
<div><img src="/notebooks/Assets/img/ts.JPG" width=""></div>
<br/>
</div>


We can compare the projected values against the actual ones.

As additional information, the average quantity purchased by this customer for the analyzed item is **350 units**.

This will serve as input to **compare** the **average method** against the **time series** projections.

In the following table we can see the real values and the projected ones.

<br/>
<div><img src="/notebooks/Assets/img/ts_vsreal.JPG" width=""></div>
<br/>
</div>


As can be seen from the actual values, there is a purchasing pattern of approximately 600 units every two months from September 2023 to October 2024.

However, the time series does not capture this similar purchasing pattern, showing values spread across all months.

The projection by time series is very close to the average but neither of them to the real values.

There is no need to calculate a delta between the actual and projected values, as it is clearly evident that the time series adjustments are necessary to align with the actual values.

 <p align="right">(<a href="#readme-top">back to top</a>)</p>


# 7. Repository 📁

* `notebooks`
    - contains all final notebooks involved in the project

* `docs`
    - contains final report, presentations which summarize the project

* `references`
    - contains papers / tutorials used in the project

* `src`
    - Contains the project source code (refactored from the notebooks)

* `.gitignore`
    - Part of Git, includes files and folders to be ignored by Git version control

* `README.md`
    - Project landing page (this page)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

# 8. Learnings 👨‍💻

- **Managing DataFrames correctly** This is crucial, especially when creating new DataFrames that are related to others and used as filters. If the structure of any DataFrame is unintentionally altered, it can cause a cascade of issues, making error detection much more difficult. Therefore, it’s essential to carefully maintain the structure of each DataFrame and be aware of where and how it is being used. This helps ensure data integrity, reduces debugging time, and prevents unexpected problems in downstream analyses or models.

- **Limitations of simple average calculation:** While calculating the average time between purchases by customer and product provides a general overview, it fails to capture important variations like seasonal changes or irregular purchases. This method does not account for non-linear or erratic behaviors, leading to less accurate predictions when buying patterns aren’t consistent.

- **Importance of handling outliers before applying a predictive model:** When running linear regression, the R² improved as outliers were removed. Initially, the model had an R² of 0.13, but after addressing the outliers, it improved to an R² of 0.43.

- **Testing the model with multiple variables:** Although the final linear regression model uses quantity and price to estimate purchase time, other variables were tested, such as product type, day, month, and year, to identify whether any time-related factors were more significant in each sale's timing.

After evaluating each model, the simplest option provided the best result: This does not mean it cannot be improved by incorporating more variables, but it serves as a good starting point.

- **Time series decomposition:** The importance of decomposing time series to clearly identify trends, seasonality, and noise, enabling them to be treated independently.

Removing potentially atypical values: Instead of removing them entirely, limits can be set, and winsorization can be applied.

- **Model evaluation:** The importance of evaluating each model against actual values and determining the delta, as this provides insight into how one model compares to another.

# 9. Conclusion ✅

The two primary original questions:

Estimated date of the next purchase.
Estimated purchase quantities.

These models establishes a baseline for data-driven decision-making, enabling the company to better plan operations and proactively address the needs of its key clients.

1. **Simple Average Calculation:** We calculated the average number of days between each customer's purchases based on historical sales data.
2. **Linear Regression:** A linear regression model was trained, achieving 43% accuracy in predicting estimated purchase times using quantities and prices as variables.
3. **Time Series Analysis:** A time series model projected monthly sales quantities for 2024.

 <p align="right">(<a href="#readme-top">back to top</a>)</p>

 # 10. Next Steps ➡️

- Improve each of the models presented evaluating new variables. 
- Apply new models and compare them with the current ones like non-linear models. 
- Enhance the time series models by applying ARIMA and SARIMA.
- In the market there are specialized libraries to predict demand.  One of them is pymc-marketing https://github.com/pymc-labs/pymc-marketing  it would be and option to apply this models and compare the performance. 


 <p align="right">(<a href="#readme-top">back to top</a>)</p>
