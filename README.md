# HR Analytics SQL Queries

This repository showcases my ability to use SQL to solve common business problems in the field of People Analytics. The queries are structured from beginner to advanced to demonstrate a progression of skills, using a mock HR dataset.

---

### Key Skills Demonstrated

* **Data Aggregation** (using `GROUP BY`, `SUM()`, `AVG()`)
* **Data Manipulation** (`JOIN`s, `WHERE`, `ORDER BY`, `LIMIT`)
* **Common Table Expressions** (CTEs)
* **Window Functions** (`RANK()`, `NTILE()`, `PARTITION BY`)
---

## Beginner Queries

These queries demonstrate a solid understanding of fundamental SQL concepts, including basic filtering and aggregation.

### Problem 1: Number of employees with low work life balance scores

How many employees have a Work_Life_Balance score of 1?

**Query:**
```sql
SELECT
	COUNT(*) AS num_employees
FROM
	(SELECT
		employeeid,
		work_life_balance
	FROM
		hr_performance
	WHERE
		work_life_balance = 1)
;
```
### Problem 2: Male demegraphics in the sales department

 How many employees have a Gender of 'Male' in the 'Sales' Department?
```sql
SELECT
	COUNT(*)
FROM
	(SELECT
		employeeid,
		gender,
		department
	FROM 
		hr_employee_info
	WHERE 
		gender = 'Male' and department = 'Sales')
