# HR_Analysis-Project

## Table of Contents

-	[Project Overview](#Project-Overview)
-	[Data Source](#Data-Source)
-	[Tool Used](#Tool-Used)
-	[Data Understanding](#Data-Understanding)
-	[Data Cleaning/Preparation](#Data-Cleaning/Preparation)
-	[Exploratory Data Analysis](#Exploratory-Data-Analysis)
-	[Results/Findings](#Results/Findings)
-	[Recommendations](#Recommendations)
-	[Limitations](#Limitations)
-	[References](#References)

### Project Overview

This project aims to provide insights on the Employee Management for an International Company over the year (2000-2020). By Analysing various aspects of the Employee data, to identify Hiring and Terminations Trends, gain deeper understanding of the employee distribution within the company.

### Data Source
HR_Data ; A primary dataset to be used for analysis “Hr.csv” for the analysis, containing detailed information of the Company Employees

### Tool Used

-	Excel- To view the dataset
-	SQL- For Data Cleaning and Data Analysis 
-	Power BI-For Data Visualization 

### Data Understanding

 Before the data cleaning phase, a brief view of the Dataset was made, to gain deeper understanding into each Column of the Dataset.

### Data Cleaning/Preparation

In this phase, the following task was done
-	Data Import/Loading:  Imported the CSV flat file into SQL
-	Data inspection: To confirm the data type

 ```sql
EXEC sp_help 'hr';
```

-	Data Cleaning/ formatting

```sql
UPDATE hr
set termdate = '0000-00-00'
where termdate is null;
```

```sql
UPDATE HR
SET TERMDATE = LEFT(TERMDATE,10);
```

### Exploratory Data Analysis
 
This involved 	exploring the dataset to answer these questions;
 1. What is the gender breakdown of current employees
    
    ```sql
    SELECT GENDER,
       COUNT(*) AS EMP_COUNT FROM HR
       WHERE TERMDATE = '0000-00-00'
       GROUP BY GENDER
       ORDER BY EMP_COUNT DESC;
    ```
    
 2. What is the rae and ethnicity  breakdown of current employees
```sql
SELECT RACE,
       COUNT(*) AS RACE_COUNT FROM HR
       WHERE TERMDATE = '0000-00-00'
       GROUP BY RACE
       ORDER BY RACE_COUNT DESC;
```
 3. What is the age distribution of current employees

```sql
-- I created an age column for the employees as of the year 2020
    ALTER TABLE HR
    ADD AGE INT;
    UPDATE HR
    SET AGE = DATEDIFF(YEAR, BirthDate, '2020-12-31')
    SELECT AGE FROM HR

    -- MIN.EMPLOYEE AGE
   SELECT  MIN(AGE) AS YOUNGEST FROM HR
   
    -- MAX EMPLOYEE AGE 
   SELECT MAX(AGE) AS OLDEST FROM HR

-- AGE GROUP DISTRIBUTION 

    SELECT
    CASE
      WHEN AGE >=20 AND AGE <=30 THEN '20-30'
      WHEN AGE >=31 AND AGE <=40 THEN '31-40'
      WHEN AGE >=41 AND AGE <=50 THEN '41-50'
      WHEN AGE >=51 AND AGE <=60 THEN '51-60'
      ELSE '60+'
      END
      AS AGE_GROUP
     ,COUNT(ID) AS EMP_COUNT FROM HR
     WHERE TERMDATE = '0000-00-00'
     GROUP BY CASE
      WHEN AGE >=20 AND AGE <=30 THEN '20-30'
      WHEN AGE >=31 AND AGE <=40 THEN '31-40'
      WHEN AGE >=41 AND AGE <=50 THEN '41-50'
      WHEN AGE >=51 AND AGE <=60 THEN '51-60'
      ELSE '60+'
      END
     ORDER BY AGE_GROUP,EMP_COUNT DESC;

     -- AGE_GROUP DISTRIBUTION BY GENDER 

     SELECT
    CASE
      WHEN AGE >=20 AND AGE <=30 THEN '20-30'
      WHEN AGE >=31 AND AGE <=40 THEN '31-40'
      WHEN AGE >=41 AND AGE <=50 THEN '41-50'
      WHEN AGE >=51 AND AGE <=60 THEN '51-60'
      ELSE '60+'
      END
      AS AGE_GROUP, GENDER
     ,COUNT(ID) AS EMP_COUNT FROM HR
     WHERE TERMDATE = '0000-00-00'
     GROUP BY CASE
      WHEN AGE >=20 AND AGE <=30 THEN '20-30'
      WHEN AGE >=31 AND AGE <=40 THEN '31-40'
      WHEN AGE >=41 AND AGE <=50 THEN '41-50'
      WHEN AGE >=51 AND AGE <=60 THEN '51-60'
      ELSE '60+'
      END,GENDER
     ORDER BY AGE_GROUP,GENDER;
```
 4. 


 

### Results/Findings 

The Analysis Results are summarised as follows 

### Recommendations 


### Limitations 


### References

