<a name="readme-top"></a>
<br />
<div align="center">
<h1>Stock Level Prediction Using Sales Data from SAP Business One</h1>
<h2>BrainStation Capstone Project</h2>
<br/>
<div><img src="/notebooks/Assets/img/BrainStation_Main_Logo.png" width=""></div>
<br/>
<div><img src="/notebooks/Assets/img/SAPBusinessOne_Logo.png" width=""></div>
</div>
<br/>

1. [Project Overview](#projectoverview)

<h2>Index</h2>
  <ol>
    <li><a href="#projectoverview">project-overview</a></li>
        <li><a href="#projectcontext">Context</a></li>
    <li><a href="#problemstatement">Problem Statement</a></li>
    <li><a href="#objectives">Objectives</a></li>
    <li><a href="#dataset">Dataset</a></li>
    <li><a href="#datadictionary">Data Dictionary</a></li>
    <li><a href="#fetureengineering">Feture Engineering</a></li>
    <li><a href="#modelingapproach">Modeling Approach</a></li>
    <li><a href="#evaluationmetrics">Evaluation Metrics</a></li>
    <li><a href="#technologiesused">Technologies Used</a></li>
    <li><a href="#howrunproject">How to Run the Project</a></li>
    <li><a href="#futureenhancements">Future Enhancements</a></li>
    <li><a href="#conclusion">Conclusion</a></li>
  </ol>
  </details>
  <p align="right">(<a href="#readme-top">back to top</a>)</p>

# Project Overview

<h2 id="projectcontext">Context</h2>
Efficient inventory management is a challenge for many businesses, as maintaining the right stock levels is critical to balance supply and demand. Overstocking can lead to increased holding costs and wasted resources, while stockouts can result in lost sales, poor customer satisfaction, and potential damage to the company’s reputation.

This project aims to develop a predictive model to optimize stock levels using historical sales data from SAP Business One. Efficient inventory management is crucial for businesses to meet customer demand while minimizing holding costs and avoiding overstock or stockouts.

By leveraging ten years of invoice data (2006-2016), we will analyze sales trends, customer purchasing behavior, and product demand to forecast optimal stock levels for each item.

 <p align="right">(<a href="#readme-top">back to top</a>)</p>

<h2 id="problemstatement">Problem Statement</h2>
The lack of accurate demand forecasting can lead to misinformed purchasing and replenishment decisions, creating inefficiencies in the supply chain.
 <p align="right">(<a href="#readme-top">back to top</a>)</p>

<h2 id="objectives">Objectives</h2>

1. Data Exploration and Preprocessing: Clean and prepare the dataset, ensuring that it is suitable for predictive analysis by addressing missing data, inconsistencies, and outliers.

2. Sales Pattern Analysis: Perform Exploratory Data Analysis (EDA) to identify patterns, in sales, which will help in understanding demand behavior.

3. Model Development: Use machine learning and time-series forecasting techniques to predict future demand and establish optimal stock levels for the business.

4. Evaluation and Optimization: Evaluate the model’s performance using appropriate metrics, refine the model to improve accuracy, and provide actionable insights for stock management.
 <p align="right">(<a href="#readme-top">back to top</a>)</p>

<h2 id="dataset">Dataset</h2>

The dataset consists of sales data from the **SAP Business One USA demo database**. Below is a query and table that describes the fields used in the invoice query, providing detailed information about each column, its purpose, and the table it originates from.

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
    T1."Dscription", -- Item Description
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
    AND (T0."DocDate" >= '20060101' AND T0."DocDate" <= '20161231') -- Range of data provided by SAP in DEMO DB
ORDER BY T0."DocNum"

```

 <p align="right">(<a href="#readme-top">back to top</a>)</p>

<h2 id="datadictionary">Data Dictionary</h2>

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
| `T1."Dscription"` | Description of the item as it appears on the invoice, typically containing the item’s full name and additional details. | INV1    |
| `T1."Quantity"`   | Number of units sold of the item. Represents the quantity of the product included in the invoice.     | INV1    |
| `T1."WhsCode"`    | Code representing the warehouse from which the item is being shipped or managed. Helps track inventory across multiple locations. | INV1    |
| `T1."StockPrice"` | Cost of the item at the time of the invoice, based on stock valuation methods (e.g., FIFO, LIFO), used for cost analysis. | INV1    |
| `T1."PriceBefDi"` | Price of the item before any discounts are applied. This is the base price charged for the product.   | INV1    |
| `T1."DiscPrcnt"`  | Percentage discount applied to the item on the invoice. Represents the discount provided to the customer for that specific item. | INV1    |
| `T1."Price"`      | Final price of the item after the discount is applied. This is the amount that will be invoiced to the customer. | INV1    |
| `T1."LineTotal"`  | Total amount for the line item in the invoice, calculated as `Quantity * Price`, representing the total charge for that product in the invoice currency. | INV1    |

 <p align="right">(<a href="#readme-top">back to top</a>)</p>

<h2 id="fetureengineering">Feature Engineering</h2>
The key variables used in this model are:


 <p align="right">(<a href="#readme-top">back to top</a>)</p>

<h2 id="modelingapproach">Modeling Approach</h2>

EMPTY

 <p align="right">(<a href="#readme-top">back to top</a>)</p>

<h2 id="howrunproject">How to Run the Project</h2>
1. Clone this repository.
2. Install the required dependencies:
```
pip install -r requirements.txt
```
3. Execute the notebook or script to run the model

 <p align="right">(<a href="#readme-top">back to top</a>)</p>

<h2 id="futureenhancements">Future Enhancements</h2>

EMPTY

 <p align="right">(<a href="#readme-top">back to top</a>)</p>

<h2 id="conclusion">Conclusion</h2>

EMPTY

 <p align="right">(<a href="#readme-top">back to top</a>)</p>