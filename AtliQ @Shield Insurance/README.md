Shield Insurance Company provides reliable and comprehensive insurance plans for individuals and businesses, ensuring protection from various risks. Known for its commitment to customer care and security, Shield stands out in the market for its focus on coverage reliability, helping customers feel safe and secure.

# Problem Statement    
Shield Insurance aims to improve its data-driven decision-making by implementing a Power BI dashboard for actionable insights into key performance metrics. To evaluate this, they are considering a collaboration with AtliQ Technologies. As a Proof of Concept, Shield Insurance requires a Pilot Project in Power BI to demonstrate AtliQ’s capability to meet their specific needs before committing to a full-scale project.

## 1. ASK  
### Questions: Preliminary Analysis  
- Show total customers, total revenue, daily revenue growth, daily customer growth as key metrics
- Month over month change% on key metrics
- Segment customers based on their age groups: 18-24, 25-30, 31-40, 41-50, 51-65, and 65+
- Total revenue split by age group, city
- Total customers split by age group, city
- Customers, daily customer growth trend by month
- Revenue, daily revenue growth trend by month
- Create a switch between revenue trend graph and customer trend graph
- Filters on sale mode, age group, city, month, policy ID
- Separate page for sales mode analysis
- Total customers split percentage by sales mode
- Total revenue split percentage by sales mode
- Trend of sales mode over month
- Separate page for age group analysis
- Age group vs expected settlement
- Age group vs sales mode
- Age group vs policy preference


## 2. PREPARE
### Data Storage:  
The dataset is completely available on the Code basis website platform where it stores and consolidates all available datasets for analysis.       


## 3. PROCESS
### Tools Used:
- Microsoft Excel
- Power BI, DAX Studio, DAX


### Data Used:  
This file contains all the meta information regarding the columns described in the CSV files. we have provided 5 CSV files:
1. dim_customer.csv
2. dim_date.csv
3. dim_policies.csv
4. fact_premiums.csv
5. fact_settlements.csv


### About Data:  
Column Description for dim_customer:  
This table contains all the information about the customers
1. customer_code: Unique code is given to each customer
2. dob: Customer's date of birth
3. city: It is the city where the customer is present  

Column Description for dim_date:  
This table contains the dates at daily, monthly levels and week numbers of the year  
1. date: date at the daily level
2. mmm_yy: date at the monthly level
3. day_type: weekday (Sunday, Monday, etc.)
4. week_no: week number of the year as per the date column

Column Description for dim_policies:  
This table contains all policies data  
1. policy_id: unique ID for a particular policy
2. base_cover: base cover amount for that particular policy
3. base_premium_amt(INR): The premium amount that the customer has to pay to get the policy

Column Description for fact_premiums:  
This table contains all information about policy orders.  
1. date: Date on which the policy is sold
2. customer_code: Unique code is given to each customer
3. Policy_id: Unique ID for each policy
4. sales_mode: mode of the sales (Offline-Agent, Offline-Direct, Online-App, Online-Website)
5. final_premium_amt(INR): The premium amount that is paid for that policy by the customer

Column Description for fact_settlements:  
This table contains information about policy settlement  
1. age: Age of the policyholder
2. settlement% : Percent of policy settlements happend for this age 


### Data Cleaning & Transformation:
- Microsoft Excel, Power Query was used to clean and transform raw data.
- From fact_settlement, remove the empty columns
- From fact_premiums, split sales_mode columns by delimiter '-' & renamed as Mode: Online/Offline , Mode: Through Medium
- Check all the data types & changed into necessary data type. Example: Settlement % 


# 4. ANALYZE
Data Analyzing  
Power BI was used to analyze data.


### Key Metrics:
- DRG: Daily Revenue Growth (DRG) can be calculated by dividing the total revenue earned in a specific month by the number of unique dates within that month. This calculation gives us a clear picture of how much, on average, the company's revenue is growing each day during that time period.
- DCG: Daily Customer Growth (DCR) measures the average daily increase in the customer base during a specific month. It's calculated by dividing the total new customers acquired in a month by the number of unique dates within that month.

DCR provides insights into the daily growth rate of the customer base, helping evaluate the company's ability to attract and retain customers on a day-to-day basis.


### Data Modelling:    
![Screenshot 2024-09-23 211849](https://github.com/user-attachments/assets/eeda14a6-e3c2-4f51-bf67-46a04bebad2c)


### New Column:  
- dim_customer:
  - Age = 2023 - YEAR(dim_customer[dob].[Date])
  - AgeGroup = SWITCH(TRUE(), dim_customer[Age] >= 18 && dim_customer[Age] <= 24, "18-24", dim_customer[Age] >= 25 && dim_customer[Age] <= 30, "25-30", dim_customer[Age] >= 31 && dim_customer[Age] <= 40, "31-40", dim_customer[Age] >= 41 && dim_customer[Age] <= 50, "41-50", dim_customer[Age] >= 51 && dim_customer[Age] <= 65, "51-65", "65+")
  - SettlementINDecimal = RELATED(fact_settlements[settlement %])
- dim_date:
  - Month = FORMAT(dim_date[date].[Date], "MMM")
  - SortedBy = SWITCH(TRUE(), dim_date[Month]="Nov", 1, dim_date[Month]="Dec", 2, dim_date[Month]="Jan", 3, dim_date[Month]="Feb", 4, dim_date[Month]="Mar", 5, 6)


### Measures:
- %CustomerChange = DIVIDE([Total Customers] - [LM Customers], [LM Customers], 0)
- %DCGChange = DIVIDE([DCG] - [LM DCG], [LM DCG], 0)
- %DRGChange = DIVIDE([DRG] - [LM DRG], [LM DRG], 0)
- %RevenueChange = DIVIDE([Total Revenue] - [LM Revenue], [LM Revenue], 0)
- Cus% = VAR _tot = CALCULATE([Total Customers], REMOVEFILTERS(fact_premiums)) RETURN DIVIDE([Total Customers], _tot)
- DCG = VAR _tot_cus = [Total Customers] VAR _date = DISTINCTCOUNT(dim_date[date]) RETURN DIVIDE(_tot_cus, _date, 0)
- DRG = VAR _tot_rev = [Total Revenue] VAR _date = DISTINCTCOUNT(dim_date[date]) RETURN DIVIDE(_tot_rev, _date, 0)
- Excepted Settlement = ROUND(SUMX(fact_premiums, fact_premiums[final_premium_amt(INR)]*(1+RELATED(dim_customer[SettlementINDecimal]))), 0)
- LM Customers = CALCULATE([Total Customers], PREVIOUSMONTH(dim_date[date]))
- LM DCG = CALCULATE([DCG], PREVIOUSMONTH(dim_date[date]))
- LM Revenue = CALCULATE([Total Revenue], PREVIOUSMONTH(dim_date[date]))
- Rev% = VAR _tot = CALCULATE([Total Revenue], REMOVEFILTERS(fact_premiums)) RETURN DIVIDE([Total Revenue], _tot)
- Total Customers = COUNT(dim_customer[customer_code])
- Total Revenue = SUM(fact_premiums[final_premium_amt(INR)])

# 5. SHARE
![Screenshot (309)](https://github.com/user-attachments/assets/5927ac1f-62b3-4480-b003-4acd370f7ebd)
![Screenshot (310)](https://github.com/user-attachments/assets/5c68c5b8-fa8b-4e1d-8f3d-7bae07c34589)
![Screenshot (311)](https://github.com/user-attachments/assets/c97a4674-6b4a-4eca-bc41-929efe37f54d)
![Screenshot (312)](https://github.com/user-attachments/assets/44f4ac83-3688-44a0-a670-d35df17ef822)
![Screenshot (313)](https://github.com/user-attachments/assets/7225885e-4d64-4931-8ac6-bd4c3687569a)



# 6. ACT	
### Insights:	
- **Customer Base:** Shield Insurance has a total of 26,841 customers.
- **Revenue:** The company generated a total revenue of 989.25 million.
- **Top City:** Delhi leads with 11,007 customers and revenue of 401.57 million, making it the top-performing region.
- **Top Age Group:** The 31-40 age group has the largest customer base with 11,171 customers, contributing the highest revenue of 343.76 million. This shows the company's strong appeal to younger and middle-aged demographics.
- **Monthly Trends:** March 2023 saw remarkable growth with an 85% increase in revenue and 82% rise in customer numbers. However, April 2023 experienced a sharp decline, with revenue dropping by 41.73% and customers by 41.41%.
- **Sales Channel:** The offline agent channel accounts for 55.41% of customers and 55.67% of total revenue, indicating that traditional agent-based sales remain the primary business driver.
- **Revenue by Sales Mode:** Revenue distribution across sales modes is fairly balanced, with percentages ranging from 12.60% to 16.27%.
- **Top Policy by Customers:** Policy ID “POL4321HEL” is the most popular, with 4,434 customers choosing it for their insurance needs.
- **Top Policy by Revenue:** Policy ID “POL2005HEL” has generated the highest revenue, amounting to 324.26 million.


### Recommendations:	
- **Focus on Digital Channels:** While the offline agent channel dominates, increasing investment in digital sales channels could diversify customer acquisition and boost revenue.
- **Target Younger Demographics:** Continue to engage the 31-40 age group with tailored offerings, as they contribute the highest customer base and revenue.
- **Address Monthly Fluctuations:** Investigate the reasons behind the sharp decline in April 2023 to prevent future drops and ensure consistent growth.
- **Promote Top-Performing Policies:** Highlight and expand marketing efforts around popular policies like "POL4321HEL" and high-revenue policies like "POL2005HEL."

Thank you for reading and evaluating my repo :)  Live Dashboard [Link](https://app.powerbi.com/view?r=eyJrIjoiZjc2ZGY1MWItOThjNS00YzlkLTgzNzItMWZlMjVmNzA2ZDM1IiwidCI6ImM2ZTU0OWIzLTVmNDUtNDAzMi1hYWU5LWQ0MjQ0ZGM1YjJjNCJ9&pageName=d88767304152b4646000)
