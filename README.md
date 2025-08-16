-- =============================
-- CUSTOMER CHURN ANALYSIS SCRIPT (Aligned to Customer_Data.csv)
-- =============================

-- 1) Create & use DB
CREATE DATABASE IF NOT EXISTS db_churn;
USE db_churn;

-- 2) (Optional) Rename table if needed
-- RENAME TABLE customer TO customers;

-- 3) View all tables
SHOW TABLES;

-- 4) Preview sample data
SELECT * FROM customers LIMIT 10;

-- =============================
-- BASIC CUSTOMER INFORMATION
-- =============================

-- Total customers
SELECT COUNT(*) AS total_customers FROM customers;

-- Distinct genders
SELECT DISTINCT Gender FROM customers;

-- Average age of customers
SELECT AVG(Age) AS avg_age FROM customers;

-- Married customers count
SELECT COUNT(*) AS married_customers FROM customers WHERE Married = 'Yes';

-- Customer distribution by state
SELECT 
    State, 
    COUNT(*) AS total_customers
FROM customers
GROUP BY State
ORDER BY total_customers DESC;

-- =============================
-- CHURN ANALYSIS (BUSINESS VIEW)
-- =============================

-- Overall churn summary
SELECT 
    COUNT(*) AS total_customers,
    SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) AS churned_customers,
    ROUND(SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS churn_rate_percent
FROM customers;

-- Churn by state
SELECT 
    State,
    COUNT(*) AS total_customers,
    SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) AS churned_customers,
    ROUND(SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS churn_rate_percent
FROM customers
GROUP BY State
ORDER BY churn_rate_percent DESC;

-- Churn by gender
SELECT 
    Gender,
    COUNT(*) AS total_customers,
    SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) AS churned_customers,
    ROUND(SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS churn_rate_percent
FROM customers
GROUP BY Gender;

-- Churn by senior status (derived from Age)
SELECT  
    CASE WHEN Age >= 60 THEN 'Senior (60+)' ELSE 'Non-Senior' END AS senior_flag,
    COUNT(*) AS total_customers,
    SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) AS churned_customers,
    ROUND(SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS churn_rate_percent
FROM customers
GROUP BY senior_flag;

-- Churn by age group
SELECT 
    CASE 
        WHEN Age < 30 THEN 'Under 30'
        WHEN Age BETWEEN 30 AND 50 THEN '30-50'
        ELSE 'Above 50'
    END AS age_group,
    COUNT(*) AS total_customers,
    SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) AS churned_customers,
    ROUND(SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS churn_rate_percent
FROM customers
GROUP BY age_group
ORDER BY churn_rate_percent DESC;

-- Churn by tenure group (months)
SELECT 
    CASE 
        WHEN Tenure_in_Months BETWEEN 0 AND 12 THEN '0-12 months'
        WHEN Tenure_in_Months BETWEEN 13 AND 24 THEN '13-24 months'
        WHEN Tenure_in_Months BETWEEN 25 AND 36 THEN '25-36 months'
        WHEN Tenure_in_Months BETWEEN 37 AND 48 THEN '37-48 months'
        WHEN Tenure_in_Months BETWEEN 49 AND 60 THEN '49-60 months'
        ELSE '60+ months'
    END AS tenure_group,
    COUNT(*) AS total_customers,
    SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) AS churned_customers,
    ROUND(SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS churn_rate_percent
FROM customers
GROUP BY tenure_group
ORDER BY churn_rate_percent DESC;

-- Churn by contract type
SELECT 
    Contract,
    COUNT(*) AS total_customers,
    SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) AS churned_customers,
    ROUND(SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS churn_rate_percent
FROM customers
GROUP BY Contract
ORDER BY churn_rate_percent DESC;

-- Churn by payment method
SELECT  
    Payment_Method,
    COUNT(*) AS total_customers,
    SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) AS churned_customers,
    ROUND(SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS churn_rate_percent
FROM customers
GROUP BY Payment_Method
ORDER BY churn_rate_percent DESC;

-- Churn by internet service (Yes/No)
SELECT  
    Internet_Service,
    COUNT(*) AS total_customers,
    SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) AS churned_customers,
    ROUND(SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS churn_rate_percent
FROM customers
GROUP BY Internet_Service
ORDER BY churn_rate_percent DESC;

-- (Bonus) Churn by internet type (Cable / Fiber Optic / DSL)
SELECT  
    Internet_Type,
    COUNT(*) AS total_customers,
    SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) AS churned_customers,
    ROUND(SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS churn_rate_percent
FROM customers
WHERE Internet_Type IS NOT NULL
GROUP BY Internet_Type
ORDER BY churn_rate_percent DESC;

-- Churn by monthly charge range
SELECT  
    CASE 
        WHEN Monthly_Charge < 30 THEN '<30'
        WHEN Monthly_Charge BETWEEN 30 AND 60 THEN '30-60'
        WHEN Monthly_Charge BETWEEN 61 AND 90 THEN '61-90'
        ELSE '90+'
    END AS charges_group,
    COUNT(*) AS total_customers,
    SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) AS churned_customers,
    ROUND(SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS churn_rate_percent
FROM customers
GROUP BY charges_group
ORDER BY churn_rate_percent DESC;

-- =============================
-- 📌 OPTIONAL DEEPER INSIGHTS
-- =============================

-- Churn by Add-on Services (TV, Movies, Security, Premium Support)
SELECT 
    'Streaming_TV' AS service,
    COUNT(*) AS total_customers,
    SUM(CASE WHEN Customer_Status = 'Churned' AND Streaming_TV = 'Yes' THEN 1 ELSE 0 END) AS churned_customers,
    ROUND(
        SUM(CASE WHEN Customer_Status = 'Churned' AND Streaming_TV = 'Yes' THEN 1 ELSE 0 END) 
        * 100.0 / NULLIF(SUM(CASE WHEN Streaming_TV = 'Yes' THEN 1 ELSE 0 END), 0), 2
    ) AS churn_rate_percent
FROM customers
UNION ALL
SELECT 
    'Streaming_Movies',
    COUNT(*),
    SUM(CASE WHEN Customer_Status = 'Churned' AND Streaming_Movies = 'Yes' THEN 1 ELSE 0 END),
    ROUND(
        SUM(CASE WHEN Customer_Status = 'Churned' AND Streaming_Movies = 'Yes' THEN 1 ELSE 0 END) 
        * 100.0 / NULLIF(SUM(CASE WHEN Streaming_Movies = 'Yes' THEN 1 ELSE 0 END), 0), 2
    )
FROM customers
UNION ALL
SELECT 
    'Online_Security',
    COUNT(*),
    SUM(CASE WHEN Customer_Status = 'Churned' AND Online_Security = 'Yes' THEN 1 ELSE 0 END),
    ROUND(
        SUM(CASE WHEN Customer_Status = 'Churned' AND Online_Security = 'Yes' THEN 1 ELSE 0 END) 
        * 100.0 / NULLIF(SUM(CASE WHEN Online_Security = 'Yes' THEN 1 ELSE 0 END), 0), 2
    )
FROM customers
UNION ALL
SELECT 
    'Premium_Support',
    COUNT(*),
    SUM(CASE WHEN Customer_Status = 'Churned' AND Premium_Support = 'Yes' THEN 1 ELSE 0 END),
    ROUND(
        SUM(CASE WHEN Customer_Status = 'Churned' AND Premium_Support = 'Yes' THEN 1 ELSE 0 END) 
        * 100.0 / NULLIF(SUM(CASE WHEN Premium_Support = 'Yes' THEN 1 ELSE 0 END), 0), 2
    )
FROM customers;

-- Churn by Multiple Lines
SELECT 
    Multiple_Lines,
    COUNT(*) AS total_customers,
    SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) AS churned_customers,
    ROUND(SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS churn_rate_percent
FROM customers
GROUP BY Multiple_Lines;

-- Churn by Paperless Billing
SELECT 
    Paperless_Billing,
    COUNT(*) AS total_customers,
    SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) AS churned_customers,
    ROUND(SUM(CASE WHEN Customer_Status = 'Churned' THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS churn_rate_percent
FROM customers
GROUP BY Paperless_Billing;

-- =============================
-- END OF SCRIPT
-- =============================
