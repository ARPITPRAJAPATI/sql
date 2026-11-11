
**Database:** `college_db`  
**SQL Dialect:** MySQL 8+  
**Practice Tables:** `students`, `departments`, `courses`, `instructors`, `enrollments`

---

=

Today's focus was on advanced SQL concepts frequently asked in technical interviews:

- Window Functions
- Running Total
- `PARTITION BY` and `ORDER BY`
- `LAG()` and `LEAD()`
- CTE (Common Table Expressions)
- Ranking Functions
- Mixed interview-level SQL queries

## 2. Window Functions

### Q1. Department-wise Average Marks

**Task:** Display every student's details along with the average marks of their department.

```sql
SELECT *,
       AVG(marks) OVER(
           PARTITION BY department_id
       ) AS department_avg
FROM students;
```

**Concepts learned:**
- `AVG()` calculates the average.
- `OVER()` converts the aggregate into a window function.
- `PARTITION BY department_id` divides students into department-wise logical groups.
- Unlike `GROUP BY`, Window Functions preserve individual rows.

### Q2. Department-wise Highest Marks

**Task:** Display every student along with the highest marks in their department.

```sql
SELECT *,
       MAX(marks) OVER(
           PARTITION BY department_id
       ) AS department_high
FROM students;
```

**Concept learned:**

`MAX()` calculates the highest marks in each department, but every student remains in the result.

### Q3. Department-wise Running Total

**Task:** Calculate the cumulative marks of students in each department according to admission date.

```sql
SELECT *,
       SUM(marks) OVER(
           PARTITION BY department_id
           ORDER BY admission_date
           ROWS BETWEEN UNBOUNDED PRECEDING
           AND CURRENT ROW
       ) AS running_total
FROM students;
```

**Concepts learned:**

- `PARTITION BY`: Running total resets for every department.
- `ORDER BY`: Defines the sequence in which the running total is calculated.
- `UNBOUNDED PRECEDING`: Starts calculation from the first row of the partition.
- `CURRENT ROW`: Includes the current row.

The running total starts again from the first student's marks when a new department partition begins.

### Q4. Previous Student's Marks Using LAG()

**Task:** Display the previous student's marks within the same department, ordered by admission date.

```sql
SELECT *,
       LAG(marks) OVER(
           PARTITION BY department_id
           ORDER BY admission_date
       ) AS previous_marks
FROM students;
```

**Concept learned:**

`LAG()` accesses a value from a previous row without requiring a self-join.

The first student in each department gets `NULL` because there is no previous row.

### Q5. Next Student's Marks Using LEAD()

**Task:** Display the next student's marks within the same department.

```sql
SELECT *,
       LEAD(marks) OVER(
           PARTITION BY department_id
           ORDER BY admission_date
       ) AS next_marks
FROM students;
```

**Concept learned:**

`LEAD()` accesses a value from the next row.

The last student in each department gets `NULL`.

### Q6. Difference Between Current and Previous Marks

**Task:** Calculate the difference between the current student's marks and the previous student's marks.

```sql
SELECT *,
       LAG(marks) OVER(
           PARTITION BY department_id
           ORDER BY admission_date
       ) AS previous_marks,

       marks - LAG(marks) OVER(
           PARTITION BY department_id
           ORDER BY admission_date
       ) AS marks_difference

FROM students;
```

**Formula:**

`marks_difference = current marks - previous marks`

Example:

| Current Marks | Previous Marks | Difference |
|---:|---:|---:|
| 85 | NULL | NULL |
| 72 | 85 | -13 |
| 61 | 72 | -11 |
| 95 | 61 | 34 |

---

## 3. CTE — Common Table Expression

### Q7. Students Scoring Above College Average

**Task:** Find students whose marks are greater than the average marks of the entire college.

```sql
WITH avrg AS (
    SELECT AVG(marks) AS a
    FROM students
)
SELECT *
FROM students s
WHERE s.marks > (
    SELECT a
    FROM avrg
);
```

**Concepts learned:**

- `WITH` creates a temporary named result set called a CTE.
- The CTE calculates the overall college average.
- The main query compares every student's marks with that average.

**Mistake made during practice:**

Initially attempted:

```sql
WITH avrg AS (
    SELECT *, AVG(marks) AS a
    FROM students
)
SELECT *
FROM avrg v
WHERE v.marks > a;
```

The issue was using `SELECT *` with an aggregate without grouping. The requirement was to calculate one overall average, so the CTE should return only one aggregate value.

---

## 4. CTE + Window Functions

### Q8. Department-wise Top Scorer

**Task:** Find the highest-scoring student from every department using `ROW_NUMBER()`.

```sql
WITH top AS (
    SELECT *,
           ROW_NUMBER() OVER(
               PARTITION BY department_id
               ORDER BY marks DESC
           ) AS rn
    FROM students
)
SELECT *
FROM top
WHERE rn = 1;
```

**Concepts learned:**

- `ROW_NUMBER()` assigns a unique sequential number to every row.
- `PARTITION BY` restarts numbering for every department.
- `ORDER BY marks DESC` places the highest-scoring student first.
- `WHERE rn = 1` selects the top-ranked student.

**Mistake made during practice:**

Initially attempted to use `MAX(marks)` with a CTE and then filter using `IN`.

The problem was that comparing only marks against maximum marks from all departments could select a student from the wrong department if marks matched.

The `ROW_NUMBER()` approach correctly ranks students within their own department.

---

## 5. Ranking Functions

### Q9. Department-wise Student Ranking Using RANK()

**Task:** Rank students in every department according to marks.

```sql
SELECT
    name,
    department_id,
    marks,
    RANK() OVER(
        PARTITION BY department_id
        ORDER BY marks DESC
    ) AS department_rank
FROM students;
```

**Concept learned:**

`RANK()` gives equal ranks to tied values. The next rank is skipped.

### Q10. ROW_NUMBER(), RANK() and DENSE_RANK()

**Task:** Apply all three ranking functions to students.

```sql
SELECT
    name,
    department_id,
    marks,

    ROW_NUMBER() OVER(
        PARTITION BY department_id
        ORDER BY marks DESC
    ) AS row_num,

    RANK() OVER(
        PARTITION BY department_id
        ORDER BY marks DESC
    ) AS student_rank,

    DENSE_RANK() OVER(
        PARTITION BY department_id
        ORDER BY marks DESC
    ) AS dense_rank

FROM students;
```

**Important difference:**

For marks: `95, 90, 90, 80`

| Marks | ROW_NUMBER | RANK | DENSE_RANK |
|---:|---:|---:|---:|
| 95 | 1 | 1 | 1 |
| 90 | 2 | 2 | 2 |
| 90 | 3 | 2 | 2 |
| 80 | 4 | 4 | 3 |

- `ROW_NUMBER()`: Always assigns a unique number.
- `RANK()`: Same rank for ties, but skips subsequent ranks.
- `DENSE_RANK()`: Same rank for ties, without skipping ranks.

### Q11. Top 2 Students From Every Department

```sql
WITH ranked_students AS (
    SELECT
        student_id,
        name,
        department_id,
        marks,
        ROW_NUMBER() OVER(
            PARTITION BY department_id
            ORDER BY marks DESC, student_id
        ) AS rn
    FROM students
)
SELECT *
FROM ranked_students
WHERE rn <= 2;
```

**Concept learned:**

Using `ROW_NUMBER()` with a CTE makes it easy to retrieve the top N records from every group.

### Q12. Highest Paid Instructor in Each Department

**Task:** Find the highest-paid instructor in every department. Include ties.

```sql
WITH instructor_rank AS (
    SELECT
        instructor_id,
        instructor_name,
        department_id,
        salary,
        DENSE_RANK() OVER(
            PARTITION BY department_id
            ORDER BY salary DESC
        ) AS rnk
    FROM instructors
)
SELECT *
FROM instructor_rank
WHERE rnk = 1;
```

**Concept learned:**

`DENSE_RANK()` ensures that all instructors tied for the highest salary are returned.

---

## 6. Mixed Interview-level SQL Questions

### Q13. Students Scoring Above Their Department Average

```sql
WITH department_avg AS (
    SELECT
        department_id,
        AVG(marks) AS avg_marks
    FROM students
    GROUP BY department_id
)
SELECT
    s.student_id,
    s.name,
    s.department_id,
    s.marks,
    d.avg_marks
FROM students s
JOIN department_avg d
    ON s.department_id = d.department_id
WHERE s.marks > d.avg_marks;
```

**Concepts used:**
- CTE
- `GROUP BY`
- `AVG()`
- `JOIN`
- `WHERE`

### Q14. Students Having More Than One Enrollment

```sql
SELECT
    s.student_id,
    s.name,
    COUNT(e.enrollment_id) AS total_enrollments
FROM students s
JOIN enrollments e
    ON s.student_id = e.student_id
GROUP BY s.student_id, s.name
HAVING COUNT(e.enrollment_id) > 1;
```

**Concepts used:**
- `INNER JOIN`
- `GROUP BY`
- `COUNT()`
- `HAVING`

---

## 7. Important Concepts Learned Today

| Concept | Purpose |
|---|---|
| `OVER()` | Defines a window for a window function |
| `PARTITION BY` | Divides rows into logical groups |
| `ORDER BY` inside OVER | Defines row sequence |
| `ROWS BETWEEN` | Defines the window frame |
| `AVG() OVER()` | Department-wise average while preserving rows |
| `MAX() OVER()` | Department-wise maximum |
| `SUM() OVER()` | Running total |
| `LAG()` | Access previous row |
| `LEAD()` | Access next row |
| CTE | Creates a named temporary result set |
| `ROW_NUMBER()` | Unique row numbering |
| `RANK()` | Ranking with gaps after ties |
| `DENSE_RANK()` | Ranking without gaps after ties |

## 8. Mistakes and Learnings

1. `GROUP BY department_id` cannot freely return an individual `name` because multiple students belong to one department.
2. A scalar subquery must return one value. A subquery returning multiple department averages cannot be directly used with `>`.
3. Correlated subqueries compare each outer row with its matching group.
4. Window Functions preserve individual rows, unlike ordinary grouped aggregation.
5. `LAG()` and `LEAD()` do not need an explicit `ROWS BETWEEN` frame.
6. CTEs can be combined with Window Functions for top-N-per-group problems.
7. When comparing department-wise values, always keep the department relationship in the logic.
8. `ROW_NUMBER()`, `RANK()` and `DENSE_RANK()` have different tie-handling behaviour.


