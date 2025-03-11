## The Number of Employees Which Report to Each Employee

```
Table: Employees

+-------------+----------+
| Column Name | Type     |
+-------------+----------+
| employee_id | int      |
| name        | varchar  |
| reports_to  | int      |
| age         | int      |
+-------------+----------+
employee_id is the column with unique values for this table.
This table contains information about the employees and the id of the manager they report to. Some employees do not report to anyone (reports_to is null). 

 

For this problem, we will consider a manager an employee who has at least 1 other employee reporting to them.

Write a solution to report the ids and the names of all managers, the number of employees who report directly to them, and the average age of the reports rounded to the nearest integer.

Return the result table ordered by employee_id.

The result format is in the following example.

 

Example 1:

Input: 
Employees table:
+-------------+---------+------------+-----+
| employee_id | name    | reports_to | age |
+-------------+---------+------------+-----+
| 9           | Hercy   | null       | 43  |
| 6           | Alice   | 9          | 41  |
| 4           | Bob     | 9          | 36  |
| 2           | Winston | null       | 37  |
+-------------+---------+------------+-----+
Output: 
+-------------+-------+---------------+-------------+
| employee_id | name  | reports_count | average_age |
+-------------+-------+---------------+-------------+
| 9           | Hercy | 2             | 39          |
+-------------+-------+---------------+-------------+
Explanation: Hercy has 2 people report directly to him, Alice and Bob. Their average age is (41+36)/2 = 38.5, which is 39 after rounding it to the nearest integer.

Example 2:

Input: 
Employees table:
+-------------+---------+------------+-----+ 
| employee_id | name    | reports_to | age |
|-------------|---------|------------|-----|
| 1           | Michael | null       | 45  |
| 2           | Alice   | 1          | 38  |
| 3           | Bob     | 1          | 42  |
| 4           | Charlie | 2          | 34  |
| 5           | David   | 2          | 40  |
| 6           | Eve     | 3          | 37  |
| 7           | Frank   | null       | 50  |
| 8           | Grace   | null       | 48  |
+-------------+---------+------------+-----+ 
Output: 
+-------------+---------+---------------+-------------+
| employee_id | name    | reports_count | average_age |
| ----------- | ------- | ------------- | ----------- |
| 1           | Michael | 2             | 40          |
| 2           | Alice   | 2             | 37          |
| 3           | Bob     | 1             | 37          |
+-------------+---------+---------------+-------------+
```

### SOLUTION

```sql
SELECT e1.employee_id,  
       e1.name, 
	   COUNT(e2.reports_to) AS reports_count,  
       ROUND(AVG(e2.age)) AS average_age  
FROM employees e1 JOIN employees e2  
    ON e1.employee_id = e2.reports_to  
GROUP BY e1.employee_id, e1.name  
HAVING COUNT(e2.reports_to) > 0  
ORDER BY e1.employee_id;
```


## Primary Department for Each Employee

```
Table: Employee

+---------------+---------+
| Column Name   |  Type   |
+---------------+---------+
| employee_id   | int     |
| department_id | int     |
| primary_flag  | varchar |
+---------------+---------+
(employee_id, department_id) is the primary key (combination of columns with unique values) for this table.
employee_id is the id of the employee.
department_id is the id of the department to which the employee belongs.
primary_flag is an ENUM (category) of type ('Y', 'N'). If the flag is 'Y', the department is the primary department for the employee. If the flag is 'N', the department is not the primary.

 

Employees can belong to multiple departments. When the employee joins other departments, they need to decide which department is their primary department. Note that when an employee belongs to only one department, their primary column is 'N'.

Write a solution to report all the employees with their primary department. For employees who belong to one department, report their only department.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
Employee table:
+-------------+---------------+--------------+
| employee_id | department_id | primary_flag |
+-------------+---------------+--------------+
| 1           | 1             | N            |
| 2           | 1             | Y            |
| 2           | 2             | N            |
| 3           | 3             | N            |
| 4           | 2             | N            |
| 4           | 3             | Y            |
| 4           | 4             | N            |
+-------------+---------------+--------------+
Output: 
+-------------+---------------+
| employee_id | department_id |
+-------------+---------------+
| 1           | 1             |
| 2           | 1             |
| 3           | 3             |
| 4           | 3             |
+-------------+---------------+
Explanation: 
- The Primary department for employee 1 is 1.
- The Primary department for employee 2 is 1.
- The Primary department for employee 3 is 3.
- The Primary department for employee 4 is 3.
```

### SOLUTION

```sql
SELECT employee_id, department_id  
FROM employee  
WHERE (employee_id) IN (  
                       SELECT employee_id  
                       FROM employee  
                       GROUP BY employee_id  
                       HAVING COUNT(employee_id) = 1)  
   OR primary_flag = 'Y'  
ORDER BY employee_id;
```

> [!explanation] 
> To select the primary department for each employee, we have 2 conditions:
> 1. Selecting the employees with the `'Y'` flag.
> 2. Among the ones with the `'N'` flag, there are discarded rows where the employees are not a primary member, but there are also the ones that only belong in one department (since they are also  labeled with the `'N'` flag). => **Selecting the ones that appear only once.**


## Triangle Judgement

```
Table: Triangle

+-------------+------+
| Column Name | Type |
+-------------+------+
| x           | int  |
| y           | int  |
| z           | int  |
+-------------+------+
In SQL, (x, y, z) is the primary key column for this table.
Each row of this table contains the lengths of three line segments.

 

Report for every three line segments whether they can form a triangle.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
Triangle table:
+----+----+----+
| x  | y  | z  |
+----+----+----+
| 13 | 15 | 30 |
| 10 | 20 | 15 |
+----+----+----+
Output: 
+----+----+----+----------+
| x  | y  | z  | triangle |
+----+----+----+----------+
| 13 | 15 | 30 | No       |
| 10 | 20 | 15 | Yes      |
+----+----+----+----------+
```

### SOLUTION

```sql
SELECT x,  
       y,       
       z,       
       CASE  
           WHEN y < x + z AND y > ABS(x - z) THEN 'Yes'  
           ELSE 'No'  
       END AS triangle  
FROM Triangle;
```


## Consecutive Numbers

```
Table: Logs

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| num         | varchar |
+-------------+---------+
In SQL, id is the primary key for this table.
id is an autoincrement column starting from 1.

 

Find all numbers that appear at least three times consecutively.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
Logs table:
+----+-----+
| id | num |
+----+-----+
| 1  | 1   |
| 2  | 1   |
| 3  | 1   |
| 4  | 2   |
| 5  | 1   |
| 6  | 2   |
| 7  | 2   |
+----+-----+
Output: 
+-----------------+
| ConsecutiveNums |
+-----------------+
| 1               |
+-----------------+
Explanation: 1 is the only number that appears consecutively for at least three times.
```

### SOLUTION

**My method:**

```sql
SELECT DISTINCT num AS ConsecutiveNums  
FROM (  
     SELECT id,  
            num,            
            LAG(num, 1) OVER (ORDER BY id) AS prev_num,  
            LAG(num, 2) OVER (ORDER BY id) AS prev_2_num  
     FROM logs) prev_rows  
WHERE num = prev_num  
  AND prev_num = prev_2_num;
```

> [!Explanation]
> Since we need to check for 3 consecutive numbers, what we need to do is retrieve the 2 previous rows from the current one and check if the 3 are equal. In the case of a consecutive number, our result would look like this:

| id  | num | prev_num | prev_2_num |
| --- | --- | -------- | ---------- |
| 1   | 1   |          |            |
| 2   | 1   | 1        |            |
| 3   | 1   | 1        | 1          |

## Product Price at a Given Date

```
Table: Products

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| product_id    | int     |
| new_price     | int     |
| change_date   | date    |
+---------------+---------+
(product_id, change_date) is the primary key (combination of columns with unique values) of this table.
Each row of this table indicates that the price of some product was changed to a new price at some date.

 

Write a solution to find the prices of all products on 2019-08-16. Assume the price of all products before any change is 10.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
Products table:
+------------+-----------+-------------+
| product_id | new_price | change_date |
+------------+-----------+-------------+
| 1          | 20        | 2019-08-14  |
| 2          | 50        | 2019-08-14  |
| 1          | 30        | 2019-08-15  |
| 1          | 35        | 2019-08-16  |
| 2          | 65        | 2019-08-17  |
| 3          | 20        | 2019-08-18  |
+------------+-----------+-------------+
Output: 
+------------+-------+
| product_id | price |
+------------+-------+
| 2          | 50    |
| 1          | 35    |
| 3          | 10    |
+------------+-------+
```


### SOLUTION

```sql
SELECT DISTINCT product_id, 10 AS price  
FROM products  
WHERE product_id NOT IN (  
                        SELECT DISTINCT product_id  
                        FROM products  
                        WHERE change_date <= '2019-08-16')  
UNION  
SELECT product_id, new_price AS price  
FROM products  
WHERE (product_id, change_date) IN (  
                                   SELECT product_id, MAX(change_date) AS date  
                                   FROM products  
                                   WHERE change_date <= '2019-08-16'  
                                   GROUP BY product_id);
```

> [!Explanation]
> **The first query:** Finds all products that have change dates later than `2019-08-16` and hard codes the value 10 to them since it's the default value before any price change.
> **The second query:** Selects the **closest** price change dates of each product to `2019-08-16` and assign the latest price.


## Last Person to Fit in the Bus

```
Table: Queue

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| person_id   | int     |
| person_name | varchar |
| weight      | int     |
| turn        | int     |
+-------------+---------+
person_id column contains unique values.
This table has the information about all people waiting for a bus.
The person_id and turn columns will contain all numbers from 1 to n, where n is the number of rows in the table.
turn determines the order of which the people will board the bus, where turn=1 denotes the first person to board and turn=n denotes the last person to board.
weight is the weight of the person in kilograms.

 

There is a queue of people waiting to board a bus. However, the bus has a weight limit of 1000 kilograms, so there may be some people who cannot board.

Write a solution to find the person_name of the last person that can fit on the bus without exceeding the weight limit. The test cases are generated such that the first person does not exceed the weight limit.

Note that only one person can board the bus at any given turn.

The result format is in the following example.

 

Example 1:

Input: 
Queue table:
+-----------+-------------+--------+------+
| person_id | person_name | weight | turn |
+-----------+-------------+--------+------+
| 5         | Alice       | 250    | 1    |
| 4         | Bob         | 175    | 5    |
| 3         | Alex        | 350    | 2    |
| 6         | John Cena   | 400    | 3    |
| 1         | Winston     | 500    | 6    |
| 2         | Marie       | 200    | 4    |
+-----------+-------------+--------+------+
Output: 
+-------------+
| person_name |
+-------------+
| John Cena   |
+-------------+
Explanation: The folowing table is ordered by the turn for simplicity.
+------+----+-----------+--------+--------------+
| Turn | ID | Name      | Weight | Total Weight |
+------+----+-----------+--------+--------------+
| 1    | 5  | Alice     | 250    | 250          |
| 2    | 3  | Alex      | 350    | 600          |
| 3    | 6  | John Cena | 400    | 1000         | (last person to board)
| 4    | 2  | Marie     | 200    | 1200         | (cannot board)
| 5    | 4  | Bob       | 175    | ___          |
| 6    | 1  | Winston   | 500    | ___          |
+------+----+-----------+--------+--------------+
```

### SOLUTION

```sql
SELECT person_name  
FROM (---Calculating the running total of weight after each person gets on the bus  
     SELECT person_name, turn, weight, SUM(weight) OVER (ORDER BY turn) AS cum_weight 
     FROM queue  
     ORDER BY turn) running_total  
WHERE cum_weight <= 1000  
ORDER BY turn DESC  
LIMIT 1;
```


> [!Explanation] 

First we calculate the running total of weight:

| person\_name | turn | weight | cum\_weight |
| :--- | :--- | :--- | :--- |
| Alice | 1 | 250 | 250 |
| Alex | 2 | 350 | 600 |
| John Cena | 3 | 400 | 1000 |
| Marie | 4 | 200 | 1200 |
| Bob | 5 | 175 | 1375 |
| Winston | 6 | 500 | 1875 |
Then we filter out the `person_name` to the point where `cum_weight` is equal to or smaller than 1000. Sort that by `turn` descending and pick the top 1 as that is the latest person to step on the bus.

## Count Salary Categories

```
Table: Accounts

+-------------+------+
| Column Name | Type |
+-------------+------+
| account_id  | int  |
| income      | int  |
+-------------+------+
account_id is the primary key (column with unique values) for this table.
Each row contains information about the monthly income for one bank account.

 

Write a solution to calculate the number of bank accounts for each salary category. The salary categories are:

    "Low Salary": All the salaries strictly less than $20000.
    "Average Salary": All the salaries in the inclusive range [$20000, $50000].
    "High Salary": All the salaries strictly greater than $50000.

The result table must contain all three categories. If there are no accounts in a category, return 0.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
Accounts table:
+------------+--------+
| account_id | income |
+------------+--------+
| 3          | 108939 |
| 2          | 12747  |
| 8          | 87709  |
| 6          | 91796  |
+------------+--------+
Output: 
+----------------+----------------+
| category       | accounts_count |
+----------------+----------------+
| Low Salary     | 1              |
| Average Salary | 0              |
| High Salary    | 3              |
+----------------+----------------+
Explanation: 
Low Salary: Account 2.
Average Salary: No accounts.
High Salary: Accounts 3, 6, and 8.
```

### SOLUTION

```sql
SELECT category, SUM(count) AS accounts_count  
FROM (  
     SELECT category, COUNT(account_id) AS count  
     FROM (  
          SELECT account_id,  
                 income,                 
                 CASE  
                     WHEN income < 20000 THEN 'Low Salary'  
                     WHEN income BETWEEN 20000 AND 50000 THEN 'Average Salary'  
                     ELSE 'High Salary'  
                 END AS category  
          FROM Accounts) categorize  
     GROUP BY category  
     UNION  
     SELECT 'High Salary', 0  
     UNION  
     SELECT 'Average Salary', 0  
     UNION  
     SELECT 'Low Salary', 0) has_category  
GROUP BY category;
```
