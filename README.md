# Data-Cleaning-
When extracting raw transactional logs, transaction systems might contain missing values, data input errors, inconsistent text formatting, duplicate entries and missing unit prices which would definitely affect the decision of a business right from revenue to cost and down to customers.
On this project, I did some data cleaning on an e-commerce raw transactional log to evaluate their Q1 sales performance.  



**ApexTech Direct**

ApexTech Direct is a fast-growing e-commerce retailer selling consumer electronics and home office furniture. The business operations team extracted raw transactional logs for Q1 2024 to evaluate sales performance across customer segments, product lines and global fulfilment regions.
However, because transaction systems were recently merged, the sales ledger contains missing values, data input errors, inconsistent text formatting, duplicate entries and missing unit prices.

**Data Cleaning & Analysis**  

**1.	 Data Hygiene & Deduplication**

 The IT infrastructure double-logged several transactions during server maintenance. 
Identified and removed exact duplicate records across all columns in the sales table without affecting valid individual purchases.<br>
•	Initial Rows: 840<br>
•	Duplicate Rows Removed: 40<br>
•	Unique Rows Remaining: 800<br>

**2.	Standardization of Region & Payment Metadata**

 Regional teams logged entries manually, creating conflicting naming conventions (e.g., North America, north america, NA, N. America).<br> 
**Standardized the Region column into three clean categories:<br>** North America, Europe, and Asia-Pacific.<br> Standardized Payment_Method into clean categories:<br> Credit Card, PayPal, Bank Transfer, and Cash on Delivery.<br>

North America, north america, NA, N. America =	North America<br>
Europe, EU, europe =	Europe<br>
Asia-Pacific, APAC, asia =	Asia-Pacific<br>
Blank / NaN	= Unknown<br>

**Payment Method Standardization Table**

Credit Card, credit card, CC	= Credit Card<br>
PayPal, paypal, PalPay =	PayPal<br>
Bank Transfer, wire transfer =	Bank Transfer<br>
Cash on Delivery = COD	Cash on Delivery<br>
Blank / NaN	= Unknown<br>

**3.	Handling Missing & Erroneous Values**

 Some order logs contain missing (NaN), zero, amd negative quantities due to return errors or failed system syncs.
Filtered out and isolated invalid quantity entries (Quantity <= 0 or missing). Imputed and isolated missing Region and Payment_Method fields using an explicitly defined "Unknown" tag.<br>
•	Filter Condition: Exclude records where Quantity_Ordered <= 0 or missing.<br>
•	Records Excluded: 60 invalid rows (containing zeros, negative quantities, missing values).<br>
•	Valid Operational Dataset: 740 clean transaction records.<br>

**4.	Product Catalog Mapping via XLOOKUP**

  The main transactional file only contains Product_ID, leaving management unable to calculate total revenue or evaluate product category performance.<br>
Using XLOOKUP (to populate Product_Name, Category, and Unit_Price from the Product Catalog Lookup table into the main Sales ledger).<br> 
Handled lookup errors (such as PROD_999 or INVALID_ID) by returning "Unmapped Product" or $0.00 price the built-in if_not_found parameter in XLOOKUP.<br>
=XLOOKUP(D2, Catalog!$A:$A, Catalog!$B:$B, "Unmapped Product")   For Product Name<br>
=XLOOKUP(D2, Catalog!$A:$A, Catalog!$C:$C, "Unmapped Category")  For Category<br>
=XLOOKUP(D2, Catalog!$A:$A, Catalog!$D:$D, 0.00)                 For Unit Price<br>

**5.	Commercial Revenue & Performance Analytics**

Leadership needs to evaluate Q1 sales figures.<br>
Created a calculated column Total_Sales = Quantity_Ordered * Unit_Price and built a pivot summary answering:<br>
1.	What is the total net revenue generated per standardized Region?<br>
2.	Which Category had the highest total unit volume sold?<br>
3.	What percentage of total transactions resulted from unmapped product IDs?<br>

visuals here

**1. Net Revenue by Region**
•	North America: $62,815.94<br>
•	Asia-Pacific: $62,695.76 <br>
•	Europe: $56,071.25<br>
•	Unknown Region: $19,693.68<br>
•	Total Net Revenue: $201,276.63<br>

**2. Volume Sold by Product Category**
•	Electronics: 868 units (Highest Volume)<br>
•	Accessories: 313 units<br>
•	Furniture: 310 units<br>
•	Unmapped Category: 137 units<br>

**3. Unmapped Data Leakage**
•	Unmapped Transactions: 61 rows (8.22% of clean transactions)<br>
•	Impact: System integration errors resulted in 8.22% of orders lacking valid product catalog mapping (PROD_999 or INVALID_ID).<br>
