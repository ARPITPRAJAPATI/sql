

**Practice Database:** `college_db`  
**SQL Dialect:** MySQL  
**Session Target:** 20 Questions  
**Focus:** Filtering, Sorting, Aggregate Functions, Grouping, CASE, Subqueries and Date Functions.



### Q1 — Filter Students by Department

**Question:** Fetch complete details of all students who belong to the CS department.

```sql
SELECT *
FROM students
WHERE department_id = 1;
```

**Learning:** Use `WHERE` to filter rows. Department ID `1` represents CS.

### Q2 — AND Operator

**Question:** Fetch students whose marks are greater than 80 and attendance is greater than 90.

```sql
SELECT *
FROM students
WHERE marks > 80 AND attendance > 90;
```

**Learning:** `AND` requires both conditions to be true.

### Q3 — OR Operator

**Question:** Fetch complete details of students from Mumbai or Delhi.

```sql
SELECT *
FROM students
WHERE city = 'Mumbai' OR city = 'Delhi';
```

**Learning:** `OR` returns rows when at least one condition is true.

### Q4 — IN Operator

**Question:** Fetch students who belong to CS, IT or ECE departments.

```sql
SELECT *
FROM students
WHERE department_id IN (1, 2, 3);
```

**Learning:** `IN` is a concise alternative to multiple `OR` conditions on the same column.

### Q5 — BETWEEN Operator

**Question:** Fetch students whose marks are between 70 and 90, including both boundaries.

```sql
SELECT *
FROM students
WHERE marks BETWEEN 70 AND 90;
```

**Learning:** `BETWEEN` is inclusive.

### Q6 — LIKE Operator

**Question:** Fetch students whose names end with the letter A.

```sql
SELECT *
FROM students
WHERE name LIKE '%A';
```

**Learning:**
- `%A` — Ends with A.
- `A%` — Starts with A.
- `%A%` — Contains A.

### Q7 — IS NULL

**Question:** Fetch the name and fees_paid of students whose fees_paid is NULL.

```sql
SELECT name, fees_paid
FROM students
WHERE fees_paid IS NULL;
```

**Learning:** Use `IS NULL` or `IS NOT NULL`. Never use `= NULL`.

### Q8 — ORDER BY

**Question:** Show student names and marks, sorted from highest marks to lowest.

```sql
SELECT name, marks
FROM students
ORDER BY marks DESC;
```

**Learning:**
- `DESC` — Descending order.
- `ASC` — Ascending order.

### Q9 — LIMIT

**Question:** Fetch the top 3 students by marks, showing name and marks.

```sql
SELECT name, marks
FROM students
ORDER BY marks DESC
LIMIT 3;
```

**Learning:** `LIMIT` restricts the number of returned rows. Always sort before applying LIMIT for top-N questions.

### Q10 — COUNT

**Question:** Count the total number of students.

```sql
SELECT COUNT(student_id) AS total_students
FROM students;
```

**Learning:**
- `COUNT(*)` counts rows.
- `COUNT(column)` counts non-NULL values.

### Q11 — AVG with GROUP BY

**Question:** Calculate average marks for every department. Show department_id and average_marks.

```sql
SELECT department_id, AVG(marks) AS average_marks
FROM students
GROUP BY department_id;
```

**Learning:** `GROUP BY` creates groups. Aggregate functions calculate results for each group.

### Q12 — COUNT with GROUP BY

**Question:** Count how many students belong to each city.

```sql
SELECT city, COUNT(student_id) AS total_students
FROM students
GROUP BY city;
```

**Learning:** Group by the category whose separate counts you need.

### Q13 — HAVING

**Question:** Find departments whose average marks are greater than 80.

```sql
SELECT department_id, AVG(marks) AS average_marks
FROM students
GROUP BY department_id
HAVING AVG(marks) > 80;
```

**Learning:**
- `WHERE` filters individual rows before grouping.
- `GROUP BY` creates groups.
- `HAVING` filters groups after grouping.

### Q14 — MAX with GROUP BY

**Question:** Find the highest marks in each department.

```sql
SELECT department_id, MAX(marks) AS highest_marks
FROM students
GROUP BY department_id;
```

**Learning:** `MAX()` returns the maximum value in each group.

### Q15 — Subquery with Overall Average

**Question:** Fetch complete details of students whose marks are greater than the overall average marks.

```sql
SELECT *
FROM students
WHERE marks > (SELECT AVG(marks) FROM students);
```

**Learning:** The inner query calculates the overall average. The outer query compares each student's marks against that result.

### Q16 — Second Highest Distinct Marks

**Question:** Find the second-highest distinct marks value.

```sql
SELECT MAX(marks) AS second_highest_marks
FROM students
WHERE marks < (SELECT MAX(marks) FROM students);
```

**Learning:** First find the highest marks. Then find the maximum value below it. This returns the second-highest distinct value.

### Q17 — Duplicate Names

**Question:** Find names that appear more than once, along with their count.

```sql
SELECT name, COUNT(name) AS total_name
FROM students
GROUP BY name
HAVING COUNT(name) > 1;
```

**Learning:** Group by the value to check for duplicates, then use `HAVING COUNT(...) > 1`.

In this sample database, `Priya` appears twice.

### Q18 — CASE WHEN

**Question:** Display each student's name and grade:

- Marks >= 90: A
- Marks >= 75: B
- Marks >= 60: C
- Otherwise: D

```sql
SELECT
    name,
    CASE
        WHEN marks >= 90 THEN 'A'
        WHEN marks >= 75 THEN 'B'
        WHEN marks >= 60 THEN 'C'
        ELSE 'D'
    END AS grade
FROM students;
```

**Learning:** `CASE` evaluates conditions from top to bottom. Put higher thresholds first.

### Q19 — Filter by Admission Year

**Question:** Fetch complete details of students admitted in 2026.

```sql
SELECT *
FROM students
WHERE YEAR(admission_date) = 2026;
```

**Alternative date-range approach:**

```sql
SELECT *
FROM students
WHERE admission_date >= '2026-01-01'
  AND admission_date < '2027-01-01';
```

**Learning:** A half-open date range can be more index-friendly than applying a function to the date column.

### Q20 — Subquery with Average Fees

**Question:** Fetch complete details of students whose fees_paid is greater than the average non-NULL fees_paid.

```sql
SELECT *
FROM students
WHERE fees_paid > (SELECT AVG(fees_paid) FROM students);
```

**Learning:** `AVG()` ignores NULL values. Rows with NULL fees_paid do not satisfy the comparison.

---

## Important Day 1 Learnings

### 1. Filtering and Operators

| Requirement | SQL |
|---|---|
| Both conditions must be true | `AND` |
| At least one condition must be true | `OR` |
| Match one value from a list | `IN (...)` |
| Match an inclusive range | `BETWEEN ... AND ...` |
| Check NULL | `IS NULL` |
| Starts with A | `LIKE 'A%'` |
| Ends with A | `LIKE '%A'` |
| Contains A | `LIKE '%A%'` |
| Not equal | `<>` |

### 2. Sorting and Limiting

```sql
ORDER BY marks DESC;
ORDER BY marks ASC;
ORDER BY department_id, marks DESC;
LIMIT 3;
```

For a top-N question, sort first and then apply `LIMIT`.

### 3. Aggregate Functions

| Function | Purpose |
|---|---|
| `COUNT()` | Count rows or non-NULL values |
| `SUM()` | Calculate total |
| `AVG()` | Calculate average |
| `MAX()` | Find maximum |
| `MIN()` | Find minimum |

### 4. GROUP BY vs HAVING

```sql
SELECT department_id, AVG(marks)
FROM students
GROUP BY department_id
HAVING AVG(marks) > 80;
```

- `GROUP BY`: Creates groups.
- `HAVING`: Filters grouped results.
- `WHERE`: Filters rows before grouping.

### 5. Subquery Pattern

```sql
SELECT *
FROM students
WHERE marks > (SELECT AVG(marks) FROM students);
```

A subquery is a query inside another query. The inner query supplies a value or result used by the outer query.

### 6. NULL and Aggregate Behaviour

- `IS NULL` checks missing values.
- `COUNT(column)` ignores NULL values.
- `AVG(column)` ignores NULL values.
- `SUM(column)` ignores NULL values.
- Comparisons such as `fees_paid > 40000` do not return rows where fees_paid is NULL.

### 7. CASE WHEN Pattern

```sql
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    ELSE default_result
END
```

Conditions are checked in order.

---


