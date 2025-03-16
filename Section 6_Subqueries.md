## Employees Whose Manager Left the Company

```
Table: Employees

+-------------+----------+
| Column Name | Type     |
+-------------+----------+
| employee_id | int      |
| name        | varchar  |
| manager_id  | int      |
| salary      | int      |
+-------------+----------+
In SQL, employee_id is the primary key for this table.
This table contains information about the employees, their salary, and the ID of their manager. Some employees do not have a manager (manager_id is null). 

 

Find the IDs of the employees whose salary is strictly less than $30000 and whose manager left the company. When a manager leaves the company, their information is deleted from the Employees table, but the reports still have their manager_id set to the manager that left.

Return the result table ordered by employee_id.

The result format is in the following example.

 

Example 1:

Input:  
Employees table:
+-------------+-----------+------------+--------+
| employee_id | name      | manager_id | salary |
+-------------+-----------+------------+--------+
| 3           | Mila      | 9          | 60301  |
| 12          | Antonella | null       | 31000  |
| 13          | Emery     | null       | 67084  |
| 1           | Kalel     | 11         | 21241  |
| 9           | Mikaela   | null       | 50937  |
| 11          | Joziah    | 6          | 28485  |
+-------------+-----------+------------+--------+
Output: 
+-------------+
| employee_id |
+-------------+
| 11          |
+-------------+

Explanation: 
The employees with a salary less than $30000 are 1 (Kalel) and 11 (Joziah).
Kalel's manager is employee 11, who is still in the company (Joziah).
Joziah's manager is employee 6, who left the company because there is no row for employee 6 as it was deleted.
```

### SOLUTION

```sql
SELECT employee_id
FROM Employees
WHERE salary < 30000 AND manager_id NOT IN (
    SELECT employee_id FROM Employees
)
ORDER BY employee_id;
```


## Exchange Seats

```
Table: Seat

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| student     | varchar |
+-------------+---------+
id is the primary key (unique value) column for this table.
Each row of this table indicates the name and the ID of a student.
The ID sequence always starts from 1 and increments continuously.

 

Write a solution to swap the seat id of every two consecutive students. If the number of students is odd, the id of the last student is not swapped.

Return the result table ordered by id in ascending order.

The result format is in the following example.

 

Example 1:

Input: 
Seat table:
+----+---------+
| id | student |
+----+---------+
| 1  | Abbot   |
| 2  | Doris   |
| 3  | Emerson |
| 4  | Green   |
| 5  | Jeames  |
+----+---------+
Output: 
+----+---------+
| id | student |
+----+---------+
| 1  | Doris   |
| 2  | Abbot   |
| 3  | Green   |
| 4  | Emerson |
| 5  | Jeames  |
+----+---------+
Explanation: 
Note that if the number of students is odd, there is no need to change the last one's seat.
```

### SOLUTION

```sql
SELECT swap.id, COALESCE(seat.student, swap.student) AS student
FROM (
     SELECT id,
            student,
            CASE
                WHEN id % 2 != 0 THEN id + 1
                WHEN id % 2 = 0 THEN id - 1
            END AS id_to_swap
     FROM seat) swap LEFT JOIN seat
    ON swap.id_to_swap = seat.id;
```

> [!NOTE]
> To swap 2 consecutive seats, we create a column that contains the `id` for swapping. For seats with odd `id`, we add 1, and with even `id`, we subtract 1 (1 ⇒ 2 and 2 ⇒ 1 and so forth).
> We then join this table with the original `seat` to match the names to the `id_to_swap`, if the row has an odd `id` and is the last row ⇒ it has no name match in the `seat` table a.k.a. `NULL`
> ⇒ We use `COALESCE` to return its original `student` value.

`swap` table:

| id | student | id\_to\_swap |
| :--- | :--- | :--- |
| 1 | Abbot | 2 |
| 2 | Doris | 1 |
| 3 | Emerson | 4 |
| 4 | Green | 3 |
| 5 | Jeames | 6 |

After joining with `seat`:

| id  | student | id\_to\_swap | id  | student |
| :-- | :------ | :----------- | :-- | :------ |
| 1   | Abbot   | 2            | 2   | Doris   |
| 2   | Doris   | 1            | 1   | Abbot   |
| 3   | Emerson | 4            | 4   | Green   |
| 4   | Green   | 3            | 3   | Emerson |
| 5   | Jeames  | 6            | -   | -       |

## Movie Rating

```
Table: Movies

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| movie_id      | int     |
| title         | varchar |
+---------------+---------+
movie_id is the primary key (column with unique values) for this table.
title is the name of the movie.

 

Table: Users

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| user_id       | int     |
| name          | varchar |
+---------------+---------+
user_id is the primary key (column with unique values) for this table.
The column 'name' has unique values.

Table: MovieRating

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| movie_id      | int     |
| user_id       | int     |
| rating        | int     |
| created_at    | date    |
+---------------+---------+
(movie_id, user_id) is the primary key (column with unique values) for this table.
This table contains the rating of a movie by a user in their review.
created_at is the user's review date. 

 

Write a solution to:

    Find the name of the user who has rated the greatest number of movies. In case of a tie, return the lexicographically smaller user name.
    Find the movie name with the highest average rating in February 2020. In case of a tie, return the lexicographically smaller movie name.

The result format is in the following example.

 

Example 1:

Input: 
Movies table:
+-------------+--------------+
| movie_id    |  title       |
+-------------+--------------+
| 1           | Avengers     |
| 2           | Frozen 2     |
| 3           | Joker        |
+-------------+--------------+
Users table:
+-------------+--------------+
| user_id     |  name        |
+-------------+--------------+
| 1           | Daniel       |
| 2           | Monica       |
| 3           | Maria        |
| 4           | James        |
+-------------+--------------+
MovieRating table:
+-------------+--------------+--------------+-------------+
| movie_id    | user_id      | rating       | created_at  |
+-------------+--------------+--------------+-------------+
| 1           | 1            | 3            | 2020-01-12  |
| 1           | 2            | 4            | 2020-02-11  |
| 1           | 3            | 2            | 2020-02-12  |
| 1           | 4            | 1            | 2020-01-01  |
| 2           | 1            | 5            | 2020-02-17  | 
| 2           | 2            | 2            | 2020-02-01  | 
| 2           | 3            | 2            | 2020-03-01  |
| 3           | 1            | 3            | 2020-02-22  | 
| 3           | 2            | 4            | 2020-02-25  | 
+-------------+--------------+--------------+-------------+
Output: 
+--------------+
| results      |
+--------------+
| Daniel       |
| Frozen 2     |
+--------------+
Explanation: 
Daniel and Monica have rated 3 movies ("Avengers", "Frozen 2" and "Joker") but Daniel is smaller lexicographically.
Frozen 2 and Joker have a rating average of 3.5 in February but Frozen 2 is smaller lexicographically.
```

### SOLUTION

```sql
SELECT results  
FROM (  
     SELECT m.user_id, u.name AS results, COUNT(m.user_id) rating_count  
     FROM movierating m LEFT JOIN users u  
         USING (user_id)  
     GROUP BY m.user_id, u.name  
     ORDER BY rating_count DESC, u.name  
     LIMIT 1) top_rater  
UNION ALL  
SELECT results  
FROM (  
     SELECT r.movie_id, m.title AS results, AVG(r.rating) AS rating  
     FROM movierating r LEFT JOIN movies m  
         USING (movie_id)  
     WHERE EXTRACT(MONTH FROM r.created_at) = 2 AND EXTRACT(YEAR FROM r.created_at) = 2020  
     GROUP BY r.movie_id, m.title  
     ORDER BY rating DESC, m.title  
     LIMIT 1) top_movie;
```


## Restaurant Growth

```
Table: Customer

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| customer_id   | int     |
| name          | varchar |
| visited_on    | date    |
| amount        | int     |
+---------------+---------+
In SQL,(customer_id, visited_on) is the primary key for this table.
This table contains data about customer transactions in a restaurant.
visited_on is the date on which the customer with ID (customer_id) has visited the restaurant.
amount is the total paid by a customer.

 

You are the restaurant owner and you want to analyze a possible expansion (there will be at least one customer every day).

Compute the moving average of how much the customer paid in a seven days window (i.e., current day + 6 days before). average_amount should be rounded to two decimal places.

Return the result table ordered by visited_on in ascending order.

The result format is in the following example.

 

Example 1:

Input: 
Customer table:
+-------------+--------------+--------------+-------------+
| customer_id | name         | visited_on   | amount      |
+-------------+--------------+--------------+-------------+
| 1           | Jhon         | 2019-01-01   | 100         |
| 2           | Daniel       | 2019-01-02   | 110         |
| 3           | Jade         | 2019-01-03   | 120         |
| 4           | Khaled       | 2019-01-04   | 130         |
| 5           | Winston      | 2019-01-05   | 110         | 
| 6           | Elvis        | 2019-01-06   | 140         | 
| 7           | Anna         | 2019-01-07   | 150         |
| 8           | Maria        | 2019-01-08   | 80          |
| 9           | Jaze         | 2019-01-09   | 110         | 
| 1           | Jhon         | 2019-01-10   | 130         | 
| 3           | Jade         | 2019-01-10   | 150         | 
+-------------+--------------+--------------+-------------+
Output: 
+--------------+--------------+----------------+
| visited_on   | amount       | average_amount |
+--------------+--------------+----------------+
| 2019-01-07   | 860          | 122.86         |
| 2019-01-08   | 840          | 120            |
| 2019-01-09   | 840          | 120            |
| 2019-01-10   | 1000         | 142.86         |
+--------------+--------------+----------------+
Explanation: 
1st moving average from 2019-01-01 to 2019-01-07 has an average_amount of (100 + 110 + 120 + 130 + 110 + 140 + 150)/7 = 122.86
2nd moving average from 2019-01-02 to 2019-01-08 has an average_amount of (110 + 120 + 130 + 110 + 140 + 150 + 80)/7 = 120
3rd moving average from 2019-01-03 to 2019-01-09 has an average_amount of (120 + 130 + 110 + 140 + 150 + 80 + 110)/7 = 120
4th moving average from 2019-01-04 to 2019-01-10 has an average_amount of (130 + 110 + 140 + 150 + 80 + 110 + 130 + 150)/7 = 142.86
```

### SOLUTION

```sql
SELECT visited_on, amount, ROUND(amount::numeric / 7, 2) AS average_amount  
FROM (  
     SELECT DISTINCT visited_on,  
                     SUM(amount)  
                     OVER (ORDER BY visited_on RANGE BETWEEN INTERVAL '6 days' PRECEDING AND CURRENT ROW) AS amount  
     FROM customer  
     ORDER BY visited_on) running_sum  
WHERE visited_on >= (  
                    SELECT MIN(visited_on) + 6  
                    FROM customer);
```

> [!Note]
> The `runnning_sum` subquery calculates the sum of the current row and 6 rows before it. 
> We used the `frame_clause` argument in the `OVER()` clause of window functions by specifying an interval of 6 days preceding current row.
> The outside query simply calculates the average and filters out the result from the 7th row onwards by a >= condition on the min date + 6.


`running_sum` query:

| visited\_on | amount |
| :--- | :--- |
| 2019-01-01 | 100 |
| 2019-01-02 | 210 |
| 2019-01-03 | 330 |
| 2019-01-04 | 460 |
| 2019-01-05 | 570 |
| 2019-01-06 | 710 |
| 2019-01-07 | 860 |
| 2019-01-08 | 840 |
| 2019-01-09 | 840 |
| 2019-01-10 | 1000 |

Outer query:

| visited\_on | amount | average\_amount |
| :--- | :--- | :--- |
| 2019-01-07 | 860 | 122.86 |
| 2019-01-08 | 840 | 120 |
| 2019-01-09 | 840 | 120 |
| 2019-01-10 | 1000 | 142.86 |

## Friend Requests II: Who Has the Most Friends

```
Table: RequestAccepted

+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| requester_id   | int     |
| accepter_id    | int     |
| accept_date    | date    |
+----------------+---------+
(requester_id, accepter_id) is the primary key (combination of columns with unique values) for this table.
This table contains the ID of the user who sent the request, the ID of the user who received the request, and the date when the request was accepted.

 

Write a solution to find the people who have the most friends and the most friends number.

The test cases are generated so that only one person has the most friends.

The result format is in the following example.

 

Example 1:

Input: 
RequestAccepted table:
+--------------+-------------+-------------+
| requester_id | accepter_id | accept_date |
+--------------+-------------+-------------+
| 1            | 2           | 2016/06/03  |
| 1            | 3           | 2016/06/08  |
| 2            | 3           | 2016/06/08  |
| 3            | 4           | 2016/06/09  |
+--------------+-------------+-------------+
Output: 
+----+-----+
| id | num |
+----+-----+
| 3  | 3   |
+----+-----+
Explanation: 
The person with id 3 is a friend of people 1, 2, and 4, so he has three friends in total, which is the most number than any others.
```

### SOLUTION

```sql
SELECT requester_id AS id, SUM(requests) AS num  
FROM (  
     SELECT requester_id, COUNT(requester_id) AS requests  
     FROM requestaccepted  
     GROUP BY requester_id  
     UNION ALL  
     SELECT accepter_id, COUNT(accepter_id) AS requests  
     FROM requestaccepted  
     GROUP BY accepter_id) friend_count  
GROUP BY requester_id  
ORDER BY num DESC  
LIMIT 1;
```

> [!Note]
> Calculate the sum of friend requests a person sent and received, then joined together with `UNION ALL`. The total sum of that would be their current friends.

`friend_count` table:

| requester\_id | requests |
| :------------ | :------- |
| 3             | 1        |
| 2             | 1        |
| 1             | 2        |
| 3             | 2        |
| 4             | 1        |
| 2             | 1        |

## Investments in 2016

```
Table: Insurance

+-------------+-------+
| Column Name | Type  |
+-------------+-------+
| pid         | int   |
| tiv_2015    | float |
| tiv_2016    | float |
| lat         | float |
| lon         | float |
+-------------+-------+
pid is the primary key (column with unique values) for this table.
Each row of this table contains information about one policy where:
pid is the policyholder's policy ID.
tiv_2015 is the total investment value in 2015 and tiv_2016 is the total investment value in 2016.
lat is the latitude of the policy holder's city. It's guaranteed that lat is not NULL.
lon is the longitude of the policy holder's city. It's guaranteed that lon is not NULL.

 

Write a solution to report the sum of all total investment values in 2016 tiv_2016, for all policyholders who:

    have the same tiv_2015 value as one or more other policyholders, and
    are not located in the same city as any other policyholder (i.e., the (lat, lon) attribute pairs must be unique).

Round tiv_2016 to two decimal places.

The result format is in the following example.

 

Example 1:

Input: 
Insurance table:
+-----+----------+----------+-----+-----+
| pid | tiv_2015 | tiv_2016 | lat | lon |
+-----+----------+----------+-----+-----+
| 1   | 10       | 5        | 10  | 10  |
| 2   | 20       | 20       | 20  | 20  |
| 3   | 10       | 30       | 20  | 20  |
| 4   | 10       | 40       | 40  | 40  |
+-----+----------+----------+-----+-----+
Output: 
+----------+
| tiv_2016 |
+----------+
| 45.00    |
+----------+
Explanation: 
The first record in the table, like the last record, meets both of the two criteria.
The tiv_2015 value 10 is the same as the third and fourth records, and its location is unique.

The second record does not meet any of the two criteria. Its tiv_2015 is not like any other policyholders and its location is the same as the third record, which makes the third record fail, too.
So, the result is the sum of tiv_2016 of the first and last record, which is 45.
```

### SOLUTION

```sql
SELECT ROUND(SUM(tiv_2016::numeric), 2) as tiv_2016  
FROM insurance  
WHERE tiv_2015 IN (  
                  SELECT DISTINCT tiv_2015  
                  FROM insurance  
                  GROUP BY tiv_2015  
                  HAVING COUNT(tiv_2015) > 1)  
  AND (lat, lon) IN (  
                    SELECT lat, lon  
                    FROM insurance  
                    GROUP BY lat, lon  
                    HAVING COUNT(*) = 1);
```

> [!Note]
> There are 2 main conditions:
> - The `tiv_2016` value of an id must appear at least once in the `tiv_2015` column.
> - The combination of `lat` and `lon` of that id must be unique.

## Department Top Three Salaries

```
Table: Employee

+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| id           | int     |
| name         | varchar |
| salary       | int     |
| departmentId | int     |
+--------------+---------+
id is the primary key (column with unique values) for this table.
departmentId is a foreign key (reference column) of the ID from the Department table.
Each row of this table indicates the ID, name, and salary of an employee. It also contains the ID of their department.

 

Table: Department

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| name        | varchar |
+-------------+---------+
id is the primary key (column with unique values) for this table.
Each row of this table indicates the ID of a department and its name.

 

A company's executives are interested in seeing who earns the most money in each of the company's departments. A high earner in a department is an employee who has a salary in the top three unique salaries for that department.

Write a solution to find the employees who are high earners in each of the departments.

Return the result table in any order.

The result format is in the following example.

 

Example 1:

Input: 
Employee table:
+----+-------+--------+--------------+
| id | name  | salary | departmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 85000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
| 5  | Janet | 69000  | 1            |
| 6  | Randy | 85000  | 1            |
| 7  | Will  | 70000  | 1            |
+----+-------+--------+--------------+
Department table:
+----+-------+
| id | name  |
+----+-------+
| 1  | IT    |
| 2  | Sales |
+----+-------+
Output: 
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Max      | 90000  |
| IT         | Joe      | 85000  |
| IT         | Randy    | 85000  |
| IT         | Will     | 70000  |
| Sales      | Henry    | 80000  |
| Sales      | Sam      | 60000  |
+------------+----------+--------+
Explanation: 
In the IT department:
- Max earns the highest unique salary
- Both Randy and Joe earn the second-highest unique salary
- Will earns the third-highest unique salary

In the Sales department:
- Henry earns the highest salary
- Sam earns the second-highest salary
- There is no third-highest salary as there are only two employees
```

### SOLUTION

```sql
SELECT name AS Department,  
       em_name AS Employee,  
       salary AS Salary  
FROM (  
     SELECT departmentid,  
            name AS em_name,  
            salary,            DENSE_RANK() OVER (  
                PARTITION BY departmentid  
                ORDER BY salary DESC) AS sal_rank  
     FROM employee) ranking  
LEFT JOIN department d  
    ON ranking.departmentid = d.id  
WHERE sal_rank <= 3  
ORDER BY salary DESC, Department;
```

> [!NOTE]
> We use `DENSE_RANK()` to rank the salary of the employees descending per department. `DENSE_RANK()` was used to make sure that the numbering continues after ties.

Inner query result: `ranking`

| departmentid | em\_name | salary | sal\_rank |
| :--- | :--- | :--- | :--- |
| 1 | Max | 90000 | 1 |
| 1 | Joe | 85000 | 2 |
| 1 | Randy | 85000 | 2 |
| 1 | Will | 70000 | 3 |
| 1 | Janet | 69000 | 4 |
| 2 | Henry | 80000 | 1 |
| 2 | Sam | 60000 | 2 |

To select the top 3 earners of each department, we simply filter the `sal_rank` function for rows ≤ 3.
