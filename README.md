# HR Analytics SQL Queries

This repository showcases my ability to use SQL to solve common business problems in the field of People Analytics. The queries are structured to conduct descriptive analytics about the salary demographics within the company for this mock HR dataset.

---

### Key Skills Demonstrated

* **Data Aggregation** (using `GROUP BY`, `SUM()`, `AVG()`)
* **Data Manipulation** (`JOIN`s, `WHERE`, `ORDER BY`, `LIMIT`)
* **Common Table Expressions** (CTEs)
* **Window Functions** (`RANK()`, `NTILE()`, `PARTITION BY`)
---

# Phase 1: Understanding Compensation Demographics and Disparity

Synthetix Solutions wants a foundational understanding of who their employees are and what they're paid to uncover the companies basic structure and where compensation disparities might lie to set a benchmark for further analysis.


### Problem 1: What's the highest salary within the company?

Find the highest Salary in the company?

**Query:**
```sql
SELECT
	MAX(salary)
FROM
	hr_employee_info
```
### Problem 2: Are there salary differences between employees who work over time versus those who do not?

Calculate the average Salary for employees who work Overtime versus those who do not.

**Query:**
```sql
SELECT
	per.overtime,
	AVG(emp.salary) AS avg_salary
FROM
	hr_employee_info emp
INNER JOIN 
	hr_performance per on emp.employeeid = per.employeeid
GROUP BY 
	per.overtime
```

### Problem 3: Who are the highest paid employees within the company?

List the top 5 highest-paid employees in the entire company, including their Job_Role and Department.

**Query:**
 ```sql
SELECT
	employeeid,
	job_role,
	department,
	salary,
	RANK () OVER(ORDER BY salary DESC) AS dept_rank
FROM
	hr_employee_info
ORDER BY 
	salary DESC
LIMIT 5
```
### Problem 4: What employees have a salary greater than the company avergae salary?

Find all employees who have a Salary that is above the company-wide average Salary.

**Query:**
```sql
SELECT
	emp.employeeid,
	emp.salary
FROM
	hr_employee_info emp
INNER JOIN 
	hr_performance per ON emp.employeeid = per.employeeid 
WHERE
	emp.salary > 126511.020408163265												
;
```
---
# Phase 2: Analyzing Salary Equity and Identifying Top Talent

This phase uses more complex functions to evaluate if the compensation structure is fair and where the high-value employees are clustered.

### Problem 5: What job_role has the highest average Salary for employees who have been with the company for less than 2 year?

Find the Job_Role that has the highest average Salary for employees who have been with the company for less than 2 year

**Query:**
 ```sql
SELECT
	job_role,
	years_at_company,
	ROUND(AVG(salary),0) AS avg_salary
FROM 
	hr_employee_info emp
INNER JOIN
	hr_performance per ON emp.employeeid = per.employeeid
WHERE 
	years_at_company < 2
GROUP BY 
	job_role,
	years_at_company
ORDER BY 
	avg_salary DESC
LIMIT 1
```

### Problem 6: Who are the highest paid employees within the company?

List the top 5 highest-paid employees in the entire company, including their Job_Role and Department.

**Query:**
 ```sql
SELECT
	employeeid,
	job_role,
	department,
	salary,
	RANK () OVER(ORDER BY salary DESC) AS dept_rank
FROM
	hr_employee_info
ORDER BY 
	salary DESC
LIMIT 5
```

### Problem 7: Who are the 5 highest paid employess from each department?

Identify the top 5 highest-paid employees within each Department based on Salary.

**Query:**
```sql
WITH ranked_employees AS (SELECT
        employeeID,
        department,
        salary,
        RANK() OVER(PARTITION BY Department ORDER BY Salary DESC) AS dept_Rank
    FROM
        hr_employee_info
)
SELECT
	employeeid,
	department,
	salary
FROM 
	ranked_employees
WHERE 
	dept_rank <=5
ORDER BY 
	salary
;
```

### Problem 8: What percentage of employees salary make up each department total salary?

Calculate the Salary of all employees as a percentage of their Department's total Salary expenditure

**Query:**
```sql
WITH department_total_salary AS (
	SELECT 	-- This query finds the total salary of each department
		department,
		SUM(salary) AS total_salary
	FROM
		hr_employee_info
	GROUP BY 
		department
)

SELECT	-- This query ranks employees as a percentage of their Department's total Salary expenditure.
	emp.employeeid,
	emp.department,
	ds.total_salary,
	CAST(emp.salary AS DECIMAL)/ds.total_salary *100 AS percentage_of_total_salary,
	ROW_NUMBER () OVER(PARTITION BY emp.department ORDER BY emp.salary DESC) AS Emp_salary
FROM
	department_total_salary ds
INNER JOIN
	hr_employee_info emp ON ds.department = emp.department
```

### Problem 9: Which employees earn 10% more than the average salary of their job role?

Identify all employees whose Salary is in the top 10% of their specific Job_Role.

**Query:**
```sql
WITH avg_job_salary AS (
	SELECT
		job_role,
		ROUND(AVG(salary), 0) AS avg_salary
	FROM 
		hr_employee_info
	GROUP BY 
		job_role
),
Salary_quartile AS (
	SELECT
		employeeid,
		job_role,
		salary,
		NTILE(10) OVER (PARTITION BY job_role ORDER BY salary DESC) AS salary_quartile
	FROM 
		hr_employee_info
)

SELECT
	sq.employeeid,
	sq.job_role,
	sq.salary_quartile,
	Cast(sq.salary AS DECIMAL) /ajs.avg_salary * 100 AS Percentage_of_avg_salary
FROM 
	salary_quartile sq
INNER JOIN 
	avg_job_salary ajs ON sq.job_role = ajs.job_role
WHERE 
	sq.salary_quartile = 1 
```
