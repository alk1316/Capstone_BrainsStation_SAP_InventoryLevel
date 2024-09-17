# Optimizing Sales Opportunity Management with Machine Learning - for ERP SAP Business One
## Project Overview
This project aims to predict the sales opportunities most likely to be lost in SAP Business One, helping sales teams prioritize their efforts more effectively. Initially, a Logistic Regression model will be used to predict the success or failure of opportunities. In later phases, a decay model will be implemented to improve the predictions by accounting for factors like time without interaction.

## Problem Statement
Sales representatives often lose track of opportunities that remain open for extended periods, which increases the likelihood of them being lost. This project seeks to solve the issue by predicting which opportunities have a higher probability of failure, enabling sales teams to take preventive action.

## Objectives
Initial Model: Use a Logistic Regression model to predict whether an opportunity will be won or lost.
Model Improvement: Incorporate a decay model to weigh variables such as time since last interaction, number of quotes, and opportunity amount.
Provide actionable insights to sales teams based on the model’s output.
## Dataset

The dataset consists of sales opportunities data from SAP Business One USA Demo Database. The following query extracts the required information from the system:

sql - HANA database
Copy code

**Table OOPR Opportunity Header:**
```
SELECT 
    T0."OpprId",  -- Opportunity Id
    T0."CardCode", -- Customer/Lead Code
    T1."GroupCode", -- Customer/Lead Group Code
    T2."GroupName", -- Customer/Lead Group Name  
    T0."CreateDate", -- Opportunity Creation Date
    T0."OpenDate",    -- Opportunity Start Date
    T0."CloseDate",   -- Opportunity Closing Date
    T0."PredDate",   -- Predicted Closing Date
    T0."MaxSumLoc",  -- Opportunity Potential Amount 
    T0."IntRate",   -- Interest Level (3=Low, 2=Medium, 1=High, -1=NoInterestLevel)
    T0."SlpCode",  -- Main Sales Emp.
    T0."Industry",  -- Customer Industry
    T0."Source",    -- Opportunity Source
    T0."Status"     -- Status; O=Open, W=Win, L=Lost
FROM 
    "OOPR" T0  -- Opportunity Table
INNER JOIN 
    "OCRD" T1 ON T0."CardCode" = T1."CardCode"  -- Customer Table
INNER JOIN 
    "OCRG" T2 ON T1."GroupCode" = T2."GroupCode"  -- Customer Group Table
WHERE 
    T0."OpprType" = 'R';  -- Only Customer Opportunities excluding Vendor Opport.
```

**Child Table OPR1 PR Opportunity Header:**
```
SELECT 
    T0."OpprId", -- Opportunity Id
    T0."OpenDate", -- Stage Start Date
    T0."CloseDate", -- Stage Closing Date
    T0."Step_Id",   -- Stage Key
    T0."ClosePrcnt",  -- Percentage Rate; percentage associated with the progress of each stage
    T0."MaxSumLoc",  -- Potential Amount; Potencial Amount the same in header table
    T0."WtSumLoc"  -- Weighted Amount; Potencial Amount by Percentage Rate
FROM 
    "OPR1" T0;  -- Opportunity Stages Tab
```
## Data Dictionary

<table>
    <thead>
        <tr>
            <th>Table</th>
            <th>Field Name</th>
            <th>Description</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><strong>OOPR</strong></td>
            <td><strong>OpprId</strong></td>
            <td>Opportunity ID</td>
        </tr>
        <tr>
            <td><strong>OOPR</strong></td>
            <td><strong>CardCode</strong></td>
            <td>Customer/Lead Code</td>
        </tr>
        <tr>
            <td><strong>OCRD</strong></td>
            <td><strong>GroupCode</strong></td>
            <td>Customer/Lead Group Code</td>
        </tr>
        <tr>
            <td><strong>OCRG</strong></td>
            <td><strong>GroupName</strong></td>
            <td>Customer/Lead Group Name</td>
        </tr>
        <tr>
            <td><strong>OOPR</strong></td>
            <td><strong>CreateDate</strong></td>
            <td>Opportunity Creation Date</td>
        </tr>
        <tr>
            <td><strong>OOPR</strong></td>
            <td><strong>OpenDate</strong></td>
            <td>Opportunity Start Date</td>
        </tr>
        <tr>
            <td><strong>OOPR</strong></td>
            <td><strong>CloseDate</strong></td>
            <td>Opportunity Closing Date</td>
        </tr>
        <tr>
            <td><strong>OOPR</strong></td>
            <td><strong>PredDate</strong></td>
            <td>Predicted Closing Date</td>
        </tr>
        <tr>
            <td><strong>OOPR</strong></td>
            <td><strong>MaxSumLoc</strong></td>
            <td>Opportunity Potential Amount</td>
        </tr>
        <tr>
            <td><strong>OOPR</strong></td>
            <td><strong>IntRate</strong></td>
            <td>Interest Level (3=Low, 2=Medium, 1=High, -1=NoInterestLevel)</td>
        </tr>
        <tr>
            <td><strong>OOPR</strong></td>
            <td><strong>SlpCode</strong></td>
            <td>Main Sales Employee Code</td>
        </tr>
        <tr>
            <td><strong>OOPR</strong></td>
            <td><strong>Industry</strong></td>
            <td>Customer Industry</td>
        </tr>
        <tr>
            <td><strong>OOPR</strong></td>
            <td><strong>Source</strong></td>
            <td>Opportunity Source</td>
        </tr>
        <tr>
            <td><strong>OOPR</strong></td>
            <td><strong>Status</strong></td>
            <td>Opportunity Status (O=Open, W=Win, L=Lost)</td>
        </tr>
        <tr>
            <td><strong>OPR1</strong></td>
            <td><strong>OpprId</strong></td>
            <td>Opportunity ID (Stage Table)</td>
        </tr>
        <tr>
            <td><strong>OPR1</strong></td>
            <td><strong>OpenDate</strong></td>
            <td>Stage Start Date</td>
        </tr>
        <tr>
            <td><strong>OPR1</strong></td>
            <td><strong>CloseDate</strong></td>
            <td>Stage Closing Date</td>
        </tr>
        <tr>
            <td><strong>OPR1</strong></td>
            <td><strong>Step_Id</strong></td>
            <td>Stage Key</td>
        </tr>
        <tr>
            <td><strong>OPR1</strong></td>
            <td><strong>ClosePrcnt</strong></td>
            <td>Percentage Rate (Progress of each stage)</td>
        </tr>
        <tr>
            <td><strong>OPR1</strong></td>
            <td><strong>MaxSumLoc</strong></td>
            <td>Potential Amount (Same as in header table)</td>
        </tr>
        <tr>
            <td><strong>OPR1</strong></td>
            <td><strong>WtSumLoc</strong></td>
            <td>Weighted Amount (Potential Amount by Percentage Rate)</td>
        </tr>
    </tbody>
</table>




<table>
    <thead>
        <tr>
            <th>Table</th>
            <th>Field Name</th>
            <th>Description</th>
        </tr>
    </thead>
    <tbody>
    <tr>
      <td><strong>OOPR</strong></td>
      <td><strong>OpprId</strong></td>
      <td>Opportunity ID</td>
    </tr>
    </tbody>
    </table>

Table	Field Name	Description
OOPR	OpprId	Opportunity ID
OOPR	CardCode	Customer/Lead Code
OCRD	GroupCode	Customer/Lead Group Code
OCRG	GroupName	Customer/Lead Group Name
OOPR	CreateDate	Opportunity Creation Date
OOPR	OpenDate	Opportunity Start Date
OOPR	CloseDate	Opportunity Closing Date
OOPR	PredDate	Predicted Closing Date
OOPR	MaxSumLoc	Opportunity Potential Amount
OOPR	IntRate	Interest Level (3=Low, 2=Medium, 1=High, -1=NoInterestLevel)
OOPR	SlpCode	Main Sales Employee Code
OOPR	Industry	Customer Industry
OOPR	Source	Opportunity Source
OOPR	Status	Opportunity Status (O=Open, W=Win, L=Lost)
OPR1	OpprId	Opportunity ID (Stage Table)
OPR1	OpenDate	Stage Start Date
OPR1	CloseDate	Stage Closing Date
OPR1	Step_Id	Stage Key
OPR1	ClosePrcnt	Percentage Rate (Progress of each stage)
OPR1	MaxSumLoc	Potential Amount (Same as in header table)
OPR1	WtSumLoc	Weighted Amount (Potential Amount by Percentage Rate)



## Feature Engineering
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


## Modeling Approach
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

## Evaluation Metrics
<li>Accuracy: Measures the percentage of correct predictions.</li>
<li>Precision: The proportion of true positive predictions among all positive predictions.</li>
<li>Recall: The ability of the model to find all the relevant opportunities.</li>
<li>F1 Score: The harmonic mean of precision and recall.</li>

## Technologies Used
<li>Python: For data preprocessing, modeling, and evaluation.</li>
<li>pandas: For data manipulation.</li>
<li>matplotlin: For data visualization.</li>
<li>seaborn: For data visualization.</li>
<li>scikit-learn: For building and evaluating machine learning models.</li>
<li>SAP Business One: For sourcing the data.</li>
<li>SQL: For data extraction from the HANA database.</li>
<li>pyMC (future improvement): For implementing the decay model.</li>

## How to Run the Project
1. Clone this repository.
2. Install the required dependencies:
```
pip install -r requirements.txt
```
3. Execute the notebook or script to run the logistic regression model.
4. Future Work: Implement the decay model and integrate it with the logistic regression model.

## Future Enhancements
- Implement more advanced machine learning algorithms (e.g., Random Forest, Gradient Boosting) for comparison.
- Apply the decay model to weigh variables like time since last interaction and opportunity amount to refine predictions.
- Sentiment Analysis: SAP Business One includes a field for comments, where sales reps log their interactions with clients. In the future, I plan to analyze the sentiment of these comments using a sentiment analysis model. By incorporating this sentiment data into the prediction process, the model could better assess the likelihood of closing or losing an opportunity based on the tone and content of sales interactions.
- Deploy the model as a service to be used in production environments for real-time predictions.

## Conclusion
This project provides an initial machine learning model to predict the probability of success or loss of sales opportunities in SAP Business One. With the planned improvements using decay models, this solution will help sales teams optimize their opportunity management processes and take preventive action to reduce the risk of losing valuable leads.