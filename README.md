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
<h2>Index</h2>
  <ol>
    <li><a href="#projectoverview">Project Overview</a></li>
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

<h1 id="projectoverview">Project Overview</h1>
This project aims to develop a predictive model to optimize stock levels using historical sales data from SAP Business One. Efficient inventory management is crucial for businesses to meet customer demand while minimizing holding costs and avoiding overstock or stockouts.

By leveraging ten years of invoice data (2006-2016), we will analyze sales trends, customer purchasing behavior, and product demand to forecast optimal stock levels for each item.

By the end of the project, we expect to create a predictive tool that will enable the business to optimize its inventory levels, reduce costs, and improve overall supply chain efficiency. The insights gained from this model will support data-driven decision-making and enhance the company’s ability to meet customer demands with more precision.
 <p align="right">(<a href="#readme-top">back to top</a>)</p>

<h2 id="problemstatement">Problem Statement</h2>
Efficient inventory management is a challenge for many businesses, as maintaining the right stock levels is critical to balance supply and demand. Overstocking can lead to increased holding costs and wasted resources, while stockouts can result in lost sales, poor customer satisfaction, and potential damage to the company’s reputation.
 <p align="right">(<a href="#readme-top">back to top</a>)</p>

<h2 id="objectives">Objectives</h2>

1. Data Exploration and Preprocessing: Clean and prepare the dataset, ensuring that it is suitable for predictive analysis by addressing missing data, inconsistencies, and outliers.

2. Sales Pattern Analysis: Perform Exploratory Data Analysis (EDA) to identify patterns, in sales, which will help in understanding demand behavior.

3. Model Development: Use machine learning and time-series forecasting techniques to predict future demand and establish optimal stock levels for the business.

4. Evaluation and Optimization: Evaluate the model’s performance using appropriate metrics, refine the model to improve accuracy, and provide actionable insights for stock management.
 <p align="right">(<a href="#readme-top">back to top</a>)</p>

<h2 id="dataset">Dataset</h2>

The dataset consists of sales opportunities data from SAP Business One USA Demo Database. The following query extracts the required information from the system:

sql - HANA database
Copy code

**Table OOPR Opportunity Header:**
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

**<li>OpprId:** Unique identifier for each opportunity.</li>
**<li>CardCode:** Customer or lead code.</li>
**<li>CreateDate:** Date when the opportunity was created.</li>
**<li>OpenDate:** The start date of the opportunity.</li>
**<li>CloseDate:** The actual closing date.</li>
**<li>PredDate:** Predicted closing date.</li>
**<li>MaxSumLoc:** Potential amount for the opportunity.</li>
**<li>IntRate:** Interest level of the opportunity.</li>
**<li>SlpCode:** Sales representative assigned to the opportunity.</li>
**<li>Industry:** Industry category of the customer.</li>
**<li>Status:** Current status of the opportunity (Open, Won, or Lost).</li>
 <p align="right">(<a href="#readme-top">back to top</a>)</p>

<h2 id="modelingapproach">Modeling Approach</h2>
### Logistic Regression (Initial Phase)

**<li>Objective:** Predict whether an opportunity will be won or lost.</li>
**<li>Steps:**</li>
<li>Preprocess the dataset: handle missing values, normalize data, and encode categorical variables.</li>
<li>Train and validate the logistic regression model using key features such as opportunity creation date, interest level, and predicted close date.</li>
<li>Evaluate the model using metrics like accuracy, precision, recall, and F1 score.</li>

### Decay Model (Improvement Phase)

**<li>Objective:**  Improve the predictions by weighting key features (e.g., time since last interaction) with a decay function.</li>
**<li>Steps:**</li>
<li>Implement a decay model (e.g., BTYD model) to measure how time without activity affects the probability of losing an opportunity.</li>
<li>Combine the output of the decay model with the logistic regression model to improve predictions.</li>
<li>Re-evaluate the model performance using the same metrics as the initial phase.</li>
 <p align="right">(<a href="#readme-top">back to top</a>)</p>

<h2 id="evaluationmetrics">Evaluation Metrics</h2>
<li>Accuracy: Measures the percentage of correct predictions.</li>
<li>Precision: The proportion of true positive predictions among all positive predictions.</li>
<li>Recall: The ability of the model to find all the relevant opportunities.</li>
<li>F1 Score: The harmonic mean of precision and recall.</li>
 <p align="right">(<a href="#readme-top">back to top</a>)</p>

<h2 id="technologiesused">Technologies Used</h2>
<li>Python: For data preprocessing, modeling, and evaluation.</li>
<li>pandas: For data manipulation.</li>
<li>matplotlin: For data visualization.</li>
<li>seaborn: For data visualization.</li>
<li>scikit-learn: For building and evaluating machine learning models.</li>
<li>SAP Business One: For sourcing the data.</li>
<li>SQL: For data extraction from the HANA database.</li>
<li>pyMC (future improvement): For implementing the decay model.</li>
 <p align="right">(<a href="#readme-top">back to top</a>)</p>

<h2 id="howrunproject">How to Run the Project</h2>
1. Clone this repository.
2. Install the required dependencies:
```
pip install -r requirements.txt
```
3. Execute the notebook or script to run the logistic regression model.
4. Future Work: Implement the decay model and integrate it with the logistic regression model.
 <p align="right">(<a href="#readme-top">back to top</a>)</p>

<h2 id="futureenhancements">Future Enhancements</h2>
- Implement more advanced machine learning algorithms (e.g., Random Forest, Gradient Boosting) for comparison.
- Apply the decay model to weigh variables like time since last interaction and opportunity amount to refine predictions.
- Sentiment Analysis: SAP Business One includes a field for comments, where sales reps log their interactions with clients. In the future, I plan to analyze the sentiment of these comments using a sentiment analysis model. By incorporating this sentiment data into the prediction process, the model could better assess the likelihood of closing or losing an opportunity based on the tone and content of sales interactions.
- Deploy the model as a service to be used in production environments for real-time predictions.
 <p align="right">(<a href="#readme-top">back to top</a>)</p>

<h2 id="conclusion">Conclusion</h2>
This project provides an initial machine learning model to predict the probability of success or loss of sales opportunities in SAP Business One. With the planned improvements using decay models, this solution will help sales teams optimize their opportunity management processes and take preventive action to reduce the risk of losing valuable leads.
 <p align="right">(<a href="#readme-top">back to top</a>)</p>