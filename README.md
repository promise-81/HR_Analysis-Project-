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
    
 2. What is the race and ethnicity  breakdown of current employees
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
4. Show the distribution of current employees working at headquaters and remote locations?

```sql
SELECT LOCATION,COUNT(*) AS EMP_COUNT
       FROM HR
       WHERE TERMDATE = '0000-00-00'
       GROUP BY LOCATION 
       ORDER BY EMP_COUNT DESC;
```

5. What is the average length of employment in year by terminated employees?

  ```sql
SELECT AVG(DATEDIFF(DAY,HIRE_DATE,TERMDATE)/365)* 1.0 AS AVG_YEAR_OF_EMPLOYMENT
   FROM HR
   WHERE TERMDATE <> '0000-00-00' AND TERMDATE <>GETDATE() AND TERMDATE <= '2020-12-31';
  ```

6. Show the gender distribution in department and job titles

```sql
 -- JOBTITLE DISTRIBUTION

    SELECT JOBTITLE, GENDER,
      COUNT(*)AS EMP_COUNT FROM HR
      WHERE TERMDATE ='0000-00-00'
      GROUP BY JOBTITLE, GENDER
      ORDER BY EMP_COUNT DESC;

 -- DEPARTMENT DIISTRIBUTION

      SELECT DEPARTMENT, GENDER,
      COUNT(*)AS EMP_COUNT FROM HR
      WHERE TERMDATE = '0000-00-00'
      GROUP BY DEPARTMENT, GENDER
      ORDER BY DEPARTMENT;
```

7. What is the department with the highest turnover rate?

```sql
 SELECT DEPARTMENT,
        EMP_COUNT,
        TERMINATED_EMP_COUNT,
        ROUND((TERMINATED_EMP_COUNT/EMP_COUNT) * 0.10,2) AS TERMINATION_RATE
 FROM(
      SELECT DEPARTMENT,
            COUNT(*) AS EMP_COUNT,
            SUM(CASE WHEN TERMDATE<>'0000-00-00' AND TERMDATE <> GETDATE() AND TERMDATE <='2020-12-31' THEN 1 ELSE 0 END) AS TERMINATED_EMP_COUNT
            FROM HR
            GROUP BY DEPARTMENT
            
     ) AS SUBQUERY
     ORDER BY TERMINATED_EMP_COUNT DESC;
```

8. Show the distribution of Employees location across the states

```sql
SELECT LOCATION_STATE,
      COUNT(*) AS EMP_COUNT FROM HR
      WHERE TERMDATE = '0000-00-00'
      GROUP BY LOCATION_STATE
      ORDER BY EMP_COUNT DESC;
```

9. How have the company  employees changed over time based on hires and terminations?

```sql
 SELECT YEARS,
        HIRES,
        TERMINATIONS,
        HIRES - TERMINATIONS AS NET_CHANGE,
        ROUND((HIRES-TERMINATIONS)*1.0/HIRES * 100,0) AS NET_CHANGE_PERCENT
        FROM(
            SELECT YEAR(HIRE_DATE) AS YEARS,
                   COUNT(ID) AS HIRES,
                   SUM(CASE WHEN TERMDATE <> '0000-00-00' AND TERMDATE <> GETDATE() AND TERMDATE <= '2020-12-31' THEN 1 ELSE 0 END) AS TERMINATIONS
                   FROM HR
                   GROUP BY YEAR(hire_date)) AS SUBQUERY
 ORDER BY YEARS ASC;
```

10. What is the tenure distribution for each department?

```sql
SELECT DEPARTMENT,
       ROUND(AVG(DATEDIFF(DAY,HIRE_DATE, TERMDATE)/365),1) AS AVG_TENURE
FROM HR
WHERE TERMDATE<>'0000-00-00' AND TERMDATE <= GETDATE() AND TERMDATE <= '2020-12-31'
GROUP BY DEPARTMENT 
ORDER BY AVG_TENURE DESC;
```

### Results/Findings 

The Analysis Results are summarised as follows 

### Recommendations 


### Limitations 


### References

