# Summarizing Data with SQL

## Summary Statistics
32) How many rows are in the `pets` table? --13 rows
```sql
SELECT *
FROM pets
```

33) How many female pets are in the `pets` table? --7
```sql
SELECT *
FROM pets
WHERE sex='F';
```

34) How many female cats are in the `pets` table? --4
```sql
SELECT *
FROM pets
WHERE species='cat' AND sex='F';
```

35) What's the mean age of pets in the `pets` table? --5.23076923076923
```sql
SELECT AVG(age)
FROM pets;
```

36) What's the mean age of dogs in the `pets` table? --6.5
```sql
SELECT AVG(age)
FROM pets
WHERE species='dog';
```

37) What's the mean age of male dogs in the `pets` table? - 8.33333333333333
```sql
SELECT AVG(age)
FROM pets
WHERE species='dog' AND sex='M';
```

38) What's the count, mean, minimum, and maximum of pet ages in the `pets` table? --13 count, 5.23076923076923 mean, 1 min, 10 max
    * _NOTE:_ SQLite doesn't have built-in formulas for standard deviation or median!
```sql
SELECT COUNT(age), AVG(age), MIN(age), MAX(age)
FROM pets;
```

39) Repeat the previous problem with the following stipulations:
    * Round the average to one decimal place.
    * Give each column a human-readable column name (for example, "Average Age")
```sql
SELECT 
    ROUND(COUNT(age), 1) AS count_of_ages, 
    ROUND(AVG(age), 1) AS average_age, 
    ROUND(MIN(age), 1) AS min_age, 
    ROUND(MAX(age), 1) AS max_age
FROM pets;
```

40) How many rows in `employees_null` have missing salaries? -- 10 rows
```sql
SELECT *
FROM employees_null
WHERE salary IS NULL;
```

41) How many salespeople in `employees_null` having _nonmissing_ salaries? --60 rows
```sql
SELECT * 
FROM employees_null
WHERE salary IS NOT NULL AND job='Sales';
```

42) What's the mean salary of employees who joined the company after 2010? Go back to the usual `employees` table for this one. -- 78554.0882352941
    * _Hint:_ You may need to use the `CAST()` function for this. To cast a string as a float, you can do `CAST(x AS REAL)`
```sql
SELECT AVG(CAST(salary AS DECIMAL(10,2))) AS mean_salary
FROM employees
WHERE CAST(strftime('%Y', startdate) AS INTEGER) > 2010;
```

43) What's the mean salary of employees in Swiss Francs? --75727.5605
    * _Hint:_ Swiss Francs are abbreviated "CHF" and 1 USD = 0.97 CHF.
```sql
SELECT AVG(salary * 0.97) AS mean_salary_chf
FROM employees;
```

44) Create a query that computes the mean salary in USD as well as CHF. Give the columns human-readable names (for example "Mean Salary in USD"). Also, format them with comma delimiters and currency symbols.
    * _NOTE:_ Comma-delimiting numbers is only available for integers in SQLite, so rounding (down) to the nearest dollar or franc will be done for us.
    * _NOTE2:_ The symbols for francs is simply `Fr.` or `fr.`. So an example output will look like `100,000 Fr.`.

## Aggregating Statistics with GROUP BY
45) What is the average age of `pets` by species? --cat 4.33333333333333, dog 6.5, lobster 3.0
```sql
SELECT AVG(age)
FROM pets
GROUP BY species;
```

46) Repeat the previous problem but make sure the species label is also displayed! Assume this behavior is always being asked of you any time you use `GROUP BY`. --cat 4.33333333333333, dog 6.5, lobster 3.0
```sql
SELECT species, AVG(age)
FROM pets
GROUP BY species;
```

47) What is the count, mean, minimum, and maximum age by species in `pets`?
```sql
SELECT species, COUNT(age), AVG(age), MIN(age), MAX(age)
FROM pets
GROUP BY species;
```

48) Show the mean salaries of each job title in `employees`. --Administrator 71986.1428571429, IT 71381.0, Operations 74055.25, Sales 80778.0441176471
```sql
SELECT job, AVG(salary)
FROM employees
GROUP BY job;
```

49) Show the mean salaries in New Zealand dollars of each job title in `employees`.
    * _NOTE:_ 1 USD = 1.65 NZD.
```sql
SELECT job, AVG(salary * 1.65) AS mean_salary_nzd
FROM employees
GROUP BY job;
```

50) Show the mean, min, and max salaries of each job title in `employees`, as well as the numbers of employees in each category.
```sql
SELECT job, AVG(salary), MIN(salary), MAX(salary), COUNT(salary)
FROM employees
GROUP BY job;
```

51) Show the mean salaries of each job title in `employees` sorted descending by salary.
```sql
SELECT job, AVG(salary)
FROM employees
GROUP BY job
ORDER BY salary DESC;
```

52) What are the top 5 most common first names among `employees`?
```sql
SELECT firstname, COUNT(firstname) as freq
FROM employees
GROUP BY firstname
ORDER BY freq DESC
LIMIT 5;
```

53) Show all first names which have exactly 2 occurrences in `employees`.
```sql
SELECT firstname, COUNT(firstname) as freq
FROM employees
GROUP BY firstname
HAVING freq=2;
```

54) Take a look at the `transactions` table to get a idea of what it contains. Note that a transaction may span multiple rows if different items are purchased as part of the same order. The employee who made the order is also given by their ID.
```sql
SELECT *
FROM transactions;
```

55) Show the top 5 largest orders (and their respective customer) in terms of the numbers of items purchased in that order.
```sql
SELECT customer, quantity
FROM transactions
ORDER BY quantity DESC
LIMIT 5;
```

56) Show the total cost of each transaction.
    * _Hint:_ The `unit_price` column is the price of _one_ item. The customer may have purchased multiple.
    * _Hint2:_ Note that transactions here span multiple rows if different items are purchased.
```sql
SELECT order_id, SUM(unit_price * quantity) AS total_cost
FROM transactions
GROUP BY order_id;
```

57) Show the top 5 transactions in terms of total cost.
```sql
SELECT order_id, SUM(unit_price * quantity) AS total_cost
FROM transactions
GROUP BY order_id
ORDER BY total_cost DESC
LIMIT 5;
```

58) Show the top 5 customers in terms of total revenue (ie, which customers have we done the most business with in terms of money?)
```sql
SELECT customer, SUM(unit_price * quantity) AS total_cost
FROM transactions
GROUP BY customer
ORDER BY total_cost DESC
LIMIT 5;
```

59) Show the top 5 employees in terms of revenue generated (ie, which employees made the most in sales?)
```sql
SELECT employee_id, SUM(unit_price * quantity) AS total_cost
FROM transactions
GROUP BY employee_id
ORDER BY total_cost DESC
LIMIT 5;
```

60) Which customer worked with the largest number of employees?
    * _Hint:_ This is a tough one! Check out the `DISTINCT` keyword.

61) Show all customers who've done more than $80,000 worth of business with us.
```sql
SELECT customer, SUM(unit_price * quantity) AS total_cost
FROM transactions
GROUP BY customer
HAVING total_cost>80000
ORDER BY total_cost DESC;
```
