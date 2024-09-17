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

sql
Copy code

-- TABLE OOPR Opportunity Header
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

## Feature Engineering
The key variables used in this model are:
<ol>
***<li>OpprId:*** Unique identifier for each opportunity.</li>
***<li>CardCode:*** Customer or lead code.</li>
***<li>CreateDate:*** Date when the opportunity was created.</li>
***<li>OpenDate:*** The start date of the opportunity.</li>
***<li>CloseDate:*** The actual closing date.</li>
***<li>PredDate:*** Predicted closing date.</li>
***<li>MaxSumLoc:*** Potential amount for the opportunity.</li>
***<li>IntRate:*** Interest level of the opportunity.</li>
***<li>SlpCode:*** Sales representative assigned to the opportunity.</li>
***<li>Industry:*** Industry category of the customer.</li>
***<li>Status:*** Current status of the opportunity (Open, Won, or Lost).</li>
</ol>

## Modeling Approach
### Logistic Regression (Initial Phase)

***<li>Objective:*** Predict whether an opportunity will be won or lost.</li>
***Steps:***
Preprocess the dataset: handle missing values, normalize data, and encode categorical variables.
Train and validate the logistic regression model using key features such as opportunity creation date, interest level, and predicted close date.
Evaluate the model using metrics like accuracy, precision, recall, and F1 score.

### Decay Model (Improvement Phase)

Objective: Improve the predictions by weighting key features (e.g., time since last interaction) with a decay function.
Steps:
Implement a decay model (e.g., BTYD model) to measure how time without activity affects the probability of losing an opportunity.
Combine the output of the decay model with the logistic regression model to improve predictions.
Re-evaluate the model performance using the same metrics as the initial phase.

## Evaluation Metrics
Accuracy: Measures the percentage of correct predictions.
Precision: The proportion of true positive predictions among all positive predictions.
Recall: The ability of the model to find all the relevant opportunities.
F1 Score: The harmonic mean of precision and recall.

## Technologies Used
Python: For data preprocessing, modeling, and evaluation.
pandas: For data manipulation.
scikit-learn: For building and evaluating machine learning models.
SAP Business One: For sourcing the data.
SQL: For data extraction from the HANA database.
pyMC (future improvement): For implementing the decay model.

## How to Run the Project
Clone this repository.
Install the required dependencies:
bash
Copy code
pip install -r requirements.txt
Execute the notebook or script to run the logistic regression model.
Future Work: Implement the decay model and integrate it with the logistic regression model.

## Future Enhancements
Implement more advanced machine learning algorithms (e.g., Random Forest, Gradient Boosting) for comparison.
Apply the decay model to weigh variables like time since last interaction and opportunity amount to refine predictions.
Deploy the model as a service to be used in production environments for real-time predictions.

## Conclusion
This project provides an initial machine learning model to predict the probability of success or loss of sales opportunities in SAP Business One. With the planned improvements using decay models, this solution will help sales teams optimize their opportunity management processes and take preventive action to reduce the risk of losing valuable leads.