# Bank Loan Report 🏦
A comprehensive data analytics project on bank loan data using Excel & Power Query for data Cleaning and Transforming MySQL for database management and Power BI for data visualization and validation

## Introduction
This data analytics project aims to enhance our understanding of the bank's loan portfolio by developing key performance indicators (KPIs) and visualizations. We will calculate and monitor total loan applications, funded amounts, and amounts received, analyzing these metrics on a month-to-date (MTD) and month-over-month (MoM) basis. We will also assess average interest rates and debt-to-income ratios (DTI). KPIs for good and bad loans will be established, and a loan status grid view will provide comprehensive insights. Various charts will visualize monthly trends, regional analysis, loan terms, employment length, loan purposes, and home ownership impacts. Finally, a 'Details Dashboard' will offer a holistic view of key loan-related metrics for efficient access to critical information.

## Problem Statement
1. **Total Loan Applications**
2. **Total Funded Amount**
3. **Total Amount Received**
4. **Average Interest Rate**
5. **Average Debt-to-Income Ratio (DTI)**
6. **Good Loan v Bad Loan KPI’s**
7. **Loan Status Grid View**
8. **Metrics to be shown: Total Loan Applications, Total Funded Amount, and Total Amount Received**

## Transforming and Cleaning 
- **_Converting Date Columns from Text to Date in Power Query_**

1. **Add a Custom Column**:
    1. Go to the **Add Column** tab.
    2. Click on **Custom Column**.

2. **Create a Custom Column**:
    1. In the **Custom Column** dialog box, enter a name for the new column.
    2. Enter the following formula in the **Custom Column Formula** box:
        ```plaintext
        = try Date.FromText(Text.Middle([last_payment_date], 7, 4) & "-" & Text.Middle([last_payment_date], 4, 2) & "-" & Text.Start([last_payment_date], 2)) otherwise null
        ```

3. **Remove the Original Column**:
    1. Right-click on the original date column (e.g., `last_payment_date`).
    2. Select **Remove**.

4. **Rename the New Column**:
    1. Right-click on the new custom column.
    2. Select **Rename** and enter a suitable name.

5. **Change Data Type to Date**:
    1. Select the new custom column.
    2. Go to the **Transform** tab.
    3. Click on **Data Type** and select **Date**.

6. **Apply Changes**:
    1. Go to the **Home** tab.
    2. Click on **Close & Apply**.

Repeat these steps for all necessary date columns. After performing all these steps, the date columns will be correctly converted from text to date format.

- **_Formatting Date Columns for MySQL Compatibility_**

To ensure the data load process goes smoothly and to align with the default format in MySQL, I formatted the date columns to "yyyy-mm-dd". This standardization helps avoid any issues during the import process into MySQL.

### Steps to Customize Date Format in Excel

1. **Open Your Excel File**:
    - Open the Excel file containing the date columns you want to format.

2. **Select the Date Column**:
    - Click on the header of the column containing the dates to select the entire column.

3. **Open Format Cells Dialog**:
    - Right-click on the selected column.
    - Choose **Format Cells** from the context menu.

4. **Choose Custom Format**:
    - In the **Format Cells** dialog box, go to the **Number** tab.
    - Select **Custom** from the list on the left.

5. **Enter the Custom Date Format**:
    - In the **Type** field, enter the following format string:
        ```plaintext
        yyyy-mm-dd
        ```

6. **Apply the Custom Format**:
    - Click **OK** to apply the custom date format.

7. **Verify the Formatting**:
    - Ensure that the dates in the selected column are now displayed in the "yyyy-mm-dd" format.

By following these steps, you can customize the date format in Excel to ensure compatibility with MySQL, facilitating a smooth data import process.

## MySQL QUERIES
- Creating a Table with the same field name in the original dataset
```
CREATE TABLE loan_data (
    id INT AUTO_INCREMENT PRIMARY KEY,
    address_state VARCHAR(255),
    application_type VARCHAR(255),
    emp_length VARCHAR(255),
    emp_title VARCHAR(255),
    grade VARCHAR(255),
    home_ownership VARCHAR(255),
    issue_date DATE,
    last_credit_pull_date DATE,
    last_payment_date DATE,
    loan_status VARCHAR(255),
    next_payment_date DATE,
    member_id VARCHAR(255),
    purpose VARCHAR(255),
    sub_grade VARCHAR(255),
    term VARCHAR(255),
    verification_status VARCHAR(255),
    annual_income DECIMAL(15, 2),
    dti DECIMAL(5, 2),
    installment DECIMAL(15, 2),
    int_rate DECIMAL(5, 2),
    loan_amount DECIMAL(15, 2),
    total_acc INT,
    total_payment DECIMAL(15, 2)
);
```

- Importing the data using the **LOAD INFILE** Statement
```
LOAD DATA INFILE "C:/financial_loan1.csv"
INTO TABLE loan_data 
FIELDS TERMINATED BY ','
OPTIONALLY ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 LINES;
```
### Exploratory Data Analysis

**Total Loan Applications**
```
SELECT  COUNT(id) AS Total_Loan_Applications FROM loan_data;
```
![Total_Loan_Applications](https://github.com/user-attachments/assets/76129062-567d-4f4e-82f7-74978e67d094)

**MTD Loan Applications**
```
SELECT COUNT(*) AS MTD_Loan_Applications FROM loan_data
WHERE MONTH(issue_date) = 12;
```
![MTD_Loan_Applications](https://github.com/user-attachments/assets/ec813b92-23ab-4fcb-ba89-48b379102fb3)

**PMTD Loan Applications**
```
SELECT COUNT(*) AS PMTD_Loan_Applications FROM loan_data
WHERE MONTH(issue_date) = 11;
```
![PMTD Loan Applications](https://github.com/user-attachments/assets/27f580f4-7523-472e-9073-6676cb9c6193)

**MOM Change in loan applications**
```
SELECT 
    MONTH(issue_date) AS Month,
    COUNT(id) AS Total_Loan_Applications,
    LAG(COUNT(id)) OVER (ORDER BY MONTH(issue_date)) AS Previous_Month_Applications,
	(COUNT(id) - LAG(COUNT(id)) OVER (ORDER BY MONTH(issue_date))) AS MoM_Change
FROM 
    loan_data
WHERE 
    YEAR(issue_date) = 2021
GROUP BY 
	MONTH(issue_date)
ORDER BY 
	MONTH(issue_date);
```
![MOM Change in loan applications](https://github.com/user-attachments/assets/2dc2566c-b1d1-410a-8c85-ffe4f22648e0)

**Total Funded Amount**
```
SELECT ROUND(SUM(loan_amount)) AS Total_Funded_Amount FROM loan_data;    
```
![Total Funded Amount](https://github.com/user-attachments/assets/397df6a6-35ee-4a7f-87f4-74c1acd4cf74)

**MTD Total Funded Amount**
```
SELECT ROUND(SUM(loan_amount)) AS MTD_Total_Funded_Amount FROM loan_data
WHERE MONTH(issue_date) = 12;
```
![MTD Total Funded Amount   ](https://github.com/user-attachments/assets/ac078811-4bae-424a-96a1-110391fe54d0)

**PMTD Total Funded Amount**
```
SELECT ROUND(SUM(loan_amount)) AS PMTD_Total_Funded_Amount FROM loan_data
WHERE MONTH(issue_date) = 11;
```
![PMTD Total Funded Amount ](https://github.com/user-attachments/assets/c2c688de-ea69-4fa5-8c52-fac82f5bd6cb)

**MOM Changes in Funded Amount**
```
SELECT
	SELECT
    MONTH(issue_date) AS Month,
    CONCAT(ROUND(SUM(loan_amount)/1000000), 'M') AS Total_Funded_Amount,
    LAG(CONCAT(ROUND(SUM(loan_amount)/1000000), 'M')) OVER (ORDER BY MONTH(issue_date)) AS PM_Funded_Amount,
    CONCAT(ROUND(((SUM(loan_amount) - LAG(SUM(loan_amount)) OVER (ORDER BY MONTH(issue_date))) / LAG(SUM(loan_amount)) OVER (ORDER BY MONTH(issue_date))) * 100, 2), '%') AS MoM_Change
FROM
    loan_data
GROUP BY
    MONTH(issue_date) 
ORDER BY
    MONTH(issue_date);
```
![MOM Percentage Changes in Funded Amount](https://github.com/user-attachments/assets/745da1da-37dd-48d0-9279-30bd8a507b1b)

**Total Amount Received**
```
SELECT ROUND(SUM(total_payment)) AS Total_Amoumt_Received FROM loan_data;
```
![Total Amount Received](https://github.com/user-attachments/assets/303db43e-37be-4ae7-8949-f5da58520117)

**MTD Total Amount Received**
```
SELECT ROUND(SUM(total_payment)) AS MTD_Total_Amount_Received FROM loan_data
WHERE MONTH(issue_date) = 12;
```
![MTD Total Amount Received](https://github.com/user-attachments/assets/e9f444e7-6aca-400e-8159-4b9e82b20c46)

**MTD Total Amount Received**
```
SELECT ROUND(SUM(total_payment)) AS MTD_Total_Amount_Received FROM loan_data
WHERE MONTH(issue_date) = 11;
```
![PMTD Total Amount Received](https://github.com/user-attachments/assets/586d145a-d84c-4572-98ec-998ba21c552e)

**MOM Percentage Changes in Amount Received**
```
SELECT
	MONTH(issue_date) AS Month,
    CONCAT(ROUND(SUM(total_payment)/1000000),'M') AS Total_Amount_Received,
	LAG(CONCAT(ROUND(SUM(total_payment)/1000000), 'M')) OVER(ORDER BY MONTH(issue_date)) AS PM_Total_Amount_Received,
	CONCAT(ROUND(((SUM(total_payment) - LAG(SUM(total_payment)) OVER(ORDER BY MONTH(issue_date)))/ LAG(SUM(total_payment)) OVER(ORDER BY MONTH(issue_date))) * 100, 2), '%') AS MOM_Change
FROM
	loan_data
GROUP BY
	MONTH(issue_date)
ORDER BY
	MONTH(issue_date);
```
![MOM Percentage Changes in Amount Received](https://github.com/user-attachments/assets/4948cd54-2a98-4041-87ff-ce408a1a3267)

**Average Interest Rate**
```
SELECT ROUND(AVG(int_rate) * 100, 2) AS Avg_Interest_Rate FROM loan_data;
```
![Average Interest Rate](https://github.com/user-attachments/assets/f14a7b38-e568-48bb-b985-7e33d315f197)

**MTD Average Interest Rate**
```
SELECT ROUND(AVG(int_rate) * 100, 2) AS MTD_Avg_Interest_Rate FROM loan_data
WHERE MONTH(issue_date) = 12;
```
![MTD Average Interest Rate](https://github.com/user-attachments/assets/40f9fe37-30d4-4775-bfa7-446eccecdb37)

**PMTD Average Interest Rate**
```
SELECT ROUND(AVG(int_rate) * 100, 2) AS MTD_Avg_Interest_Rate FROM loan_data
WHERE MONTH(issue_date) = 11;
```
![PMTD Average Interest Rate](https://github.com/user-attachments/assets/87812294-9ee9-4cb8-a09f-cfe1f4b5737d)

**Good Loan Percentage**
```
SELECT
	CONCAT(ROUND((COUNT(CASE WHEN loan_status = 'Fully Paid' or loan_status = 'Current' THEN id END) * 100)
    /
    COUNT(id)), '%') AS Good_Loans_Applications
FROM
	loan_data;
```
![Good Loan Percentage](https://github.com/user-attachments/assets/0589fcff-0a5f-475c-89ac-4f80c8777fc8)

**Good Loan Applications**
```
SELECT COUNT(id) AS Good_Loan_Applications 
FROM loan_data
WHERE loan_status = 'Fully Paid' OR loan_status = 'Current';
```
![Good Loan Applications](https://github.com/user-attachments/assets/c07ee1cc-6ca6-4cc0-902d-5eef9dafd455)

**Good Loan Funded Amount**
```
SELECT ROUND(SUM(loan_amount)) AS Good_Loan_Funded_Amount
FROM loan_data
WHERE loan_status IN ('Fully Paid', 'Current');
```
![Good Loan Funded Amount](https://github.com/user-attachments/assets/ba911aba-59df-4200-bdd9-2349c17554c9)

**Good Loan Received Amount**
```
SELECT ROUND(SUM(Total_payment)) AS Good_Loan_Received_Amount
FROM loan_data
WHERE loan_status IN ('Fully Paid', 'Current');
```
![Good Loan Received Amount](https://github.com/user-attachments/assets/7d11b20d-3ddc-42c7-838e-9b9004f4f6e0)

**Bad Loan Percentage**
```
SELECT
	CONCAT(ROUND(COUNT(CASE WHEN loan_status = 'Charged Off' THEN id END) * 100
    /
    COUNT(id)), '%') AS Bad_Loans_Applications
FROM 
	loan_data;
```
![Bad Loan Percentage](https://github.com/user-attachments/assets/5500eddc-4ab2-4659-8564-3be948d76850)

**Bad Loan Applications**
```
SELECT COUNT(id) AS Bad_Loan_Applications 
FROM loan_data
WHERE loan_status = 'Charged Off' ;
```
![Bad Loan Applications](https://github.com/user-attachments/assets/a89d8e6f-07bd-4247-a876-5036a0466508)

**Bad Loan Funded Amount**
```
SELECT ROUND(SUM(loan_amount)) AS Bad_Loan_Funded_Amount
FROM loan_data
WHERE loan_status = 'Charged Off';
```
![Bad Loan Funded Amount](https://github.com/user-attachments/assets/6ecfe884-48dd-48b9-b8c0-65f723ad63b1)

**Bad Loan Received Amount**
```
SELECT ROUND(SUM(Total_payment)) AS Bad_Loan_Received_Amount
FROM loan_data
WHERE loan_status = 'Charged Off';
```
![Bad Loan Received Amount](https://github.com/user-attachments/assets/abe91d3a-4631-470b-9ba9-5573e6539c9b)

**LOAN STATUS**
```
 SELECT
        loan_status,
        COUNT(id) AS LoanCount,
        SUM(total_payment) AS Total_Amount_Received,
        SUM(loan_amount) AS Total_Funded_Amount,
        AVG(int_rate * 100) AS Interest_Rate,
        AVG(dti * 100) AS DTI
    FROM
        bank_loan_data
    GROUP BY
        loan_status;
```
![Loan Status](https://github.com/user-attachments/assets/83e2673d-8fef-44b7-8e8d-fa4ada403c95)

```
SELECT
	Loan_status,
    ROUND(SUM(loan_amount)) AS MTD_Loan_Funded_Amount,
    ROUND(SUM(total_payment)) AS MTD_Loan_Received_Amount
FROM
	loan_data
WHERE
	MONTH(issue_date) = 12
GROUP BY
	loan_status;
```
![Loan Sataus 2](https://github.com/user-attachments/assets/d56c188f-14d6-4536-9d64-6ce4b0ff8600)

**Monthly Trends by Issue Date**
```
SELECT
	MONTH(issue_date) AS Month_Numb,
	MONTHNAME(issue_date) AS Month_name,
    COUNT(id)AS Total_Loan_Applications,
    ROUND(SUM(loan_amount)) AS Total_Funded_Amount,
    ROUND(SUM(total_payment)) AS Total_Amount_Received
FROM
	loan_data
GROUP BY
	MONTH(issue_date), MONTHNAME(issue_date)
ORDER BY
	MONTH(issue_date);
```
![Monthly Trends by Issue Date](https://github.com/user-attachments/assets/00360e55-5bd9-4b4d-a4d6-daa0dbcfbe1a)

**Regional Analysis by State**
```
SELECT
	address_state,
    COUNT(id)AS Total_Loan_Applications,
    ROUND(SUM(loan_amount)) AS Total_Funded_Amount,
    ROUND(SUM(total_payment)) AS Total_Amount_Received
FROM
	loan_data
GROUP BY
	address_state
ORDER BY
	COUNT(id) DESC;
```
![Regional Analysis by State](https://github.com/user-attachments/assets/a1dfd1df-0107-44b5-b998-2e77b9aed5e0)

**Loan Term Analysis**
```
SELECT
	term,
    COUNT(id)AS Total_Loan_Applications,
    ROUND(SUM(loan_amount)) AS Total_Funded_Amount,
    ROUND(SUM(total_payment)) AS Total_Amount_Received
FROM
	loan_data
GROUP BY
	term
ORDER BY
	term;
```
![Loan Term Analysis](https://github.com/user-attachments/assets/02bc39d1-0d5c-4e46-af2a-9bb6eef83e35)

**Employee Length Analysis**
```
SELECT
	emp_length,
    COUNT(id)AS Total_Loan_Applications,
    ROUND(SUM(loan_amount)) AS Total_Funded_Amount,
    ROUND(SUM(total_payment)) AS Total_Amount_Received
FROM
	loan_data
GROUP BY
	emp_length
ORDER BY
	COUNT(id) DESC;
```
![Employee Length Analysis](https://github.com/user-attachments/assets/4b566706-2ca0-4832-85da-a550c3b12a4e)

**Loan Purpose Breakdown** 
```
SELECT
	purpose,
    COUNT(id)AS Total_Loan_Applications,
    ROUND(SUM(loan_amount)) AS Total_Funded_Amount,
    ROUND(SUM(total_payment)) AS Total_Amount_Received
FROM
	loan_data
GROUP BY
	purpose
ORDER BY
	COUNT(id) DESC;
```
![Loan Purpose Breakdown](https://github.com/user-attachments/assets/37debbc6-b919-4f88-b51e-91f217f8f618)

**Home Ownership Analysis**
```
SELECT
	home_ownership,
    COUNT(id)AS Total_Loan_Applications,
    ROUND(SUM(loan_amount)) AS Total_Funded_Amount,
    ROUND(SUM(total_payment)) AS Total_Amount_Received
FROM
	loan_data
GROUP BY
	home_ownership
ORDER BY
	COUNT(id) DESC;
```
![Home Ownership Analysis](https://github.com/user-attachments/assets/1861faa2-0e93-4b8b-8782-cfc107dbb9ff)

## Power BI Report 

**The Report Comprises Of 3 Pages:**
1. Summary Dashboard
2. Overview Dashboard
3. Details Dashboard

You can interact with the report [here](https://app.powerbi.com/view?r=eyJrIjoiNGViM2Q2MzItMzNkNC00MThhLThmODYtYTlmYjE2MWM5OWE1IiwidCI6ImZjZjMyMWUxLTU1OGQtNGQzMi1iZWI1LTk2MDAwNDRhZDBjNiJ9)

## Key Insights

**Let’s weave these insights into a narrative that highlights their significance:**
- The bank received a substantial number of **loan applications—38.6K in total**. This reflects the demand for financial assistance, which could be driven by various factors such as economic conditions, personal needs, or investment opportunities.
- The funded amount of **$435.8M indicates the bank’s commitment to supporting borrowers**. Understanding this figure helps us appreciate the scale of financial transactions involved.
- The majority of loans issued **(86.2%)** fall into the “good” category. These loans are likely associated with lower risk profiles, making them safer investments for the bank.
- However, the **13.8% of “bad” loans** represent potential risks. Monitoring this segment is crucial for risk management and ensuring the bank’s financial stability.
- Responsible borrowers who fully paid their loans **(11.64%)** positively contribute to the bank’s **revenue**. However, **charged-off loans (13.17%) are concerning, affecting the bank’s profitability**. **Analyzing patterns behind these defaults informs future lending strategies**.
- Analyzing loan applications by employee length provides insights into employee performance and efficiency. Purpose categorization informs product development.

## Recomendatios
- **Implement dynamic interest rates** that adjust based on borrower behavior. For example, if a borrower consistently pays on time, **offer rate reductions**. Conversely, **increase rates for higher-risk borrowers**.
- **To enhance decision-making efficiency**, focus on areas with higher application volumes but lower funding rates. Identifying potential bottlenecks or issues in the loan approval process can lead to better resource allocation and improved loan outcomes.
