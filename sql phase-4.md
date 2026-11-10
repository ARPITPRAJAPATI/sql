
**Main Focus:** EXISTS, NOT EXISTS, Correlated Subqueries, GROUP BY, HAVING, COUNT, DISTINCT, IN.

---

## 1. EXISTS — Students with at least one enrollment

**Question:** Find students who have enrolled in at least one course.

### Query

```sql
SELECT *
FROM students s
WHERE EXISTS (
    SELECT 1
    FROM enrollments e
    WHERE e.student_id = s.student_id
);
```

### Concepts learned

- `EXISTS` checks whether a subquery returns at least one row.
- `SELECT 1` is commonly used because EXISTS only checks whether a matching record exists.
- The outer student is checked individually.
- A student with multiple enrollments still appears only once.

**Important:** EXISTS does not return the matching enrollment records. It only checks their existence.

---

## 2. NOT EXISTS — Students with no enrollment

**Question:** Find students who have not enrolled in any course.

### Query

```sql
SELECT *
FROM students s
WHERE NOT EXISTS (
    SELECT 1
    FROM enrollments e
    WHERE e.student_id = s.student_id
);
```

### Concepts learned

- `NOT EXISTS` checks whether the subquery returns zero rows.
- If no matching enrollment exists, the student is selected.
- If even one matching enrollment exists, the student is rejected.

**Remember:**

- EXISTS = At least one matching record.
- NOT EXISTS = No matching record.

---

## 3. Students enrolled in all available courses

**Question:** Find students who are enrolled in all 8 available courses.

### My original approach

I thought:

1. Every enrollment generates a new enrollment ID.
2. Group students using `student_id`.
3. Count their enrollment IDs.
4. Return students whose enrollment count equals 8.

This approach is logically correct if every student can enroll in a particular course only once.

### Improved query

```sql
SELECT s.*
FROM students s
WHERE s.student_id IN (
    SELECT e.student_id
    FROM enrollments e
    GROUP BY e.student_id
    HAVING COUNT(DISTINCT e.course_id) = 8
);
```

### Concepts learned

- `GROUP BY student_id` creates a separate group for each student.
- `COUNT(enrollment_id)` counts enrollment records.
- `COUNT(DISTINCT course_id)` counts unique courses.
- `HAVING` filters groups after aggregation.
- `IN` handles multiple student IDs returned by a subquery.

**Important:** DISTINCT prevents duplicate enrollment records for the same course from being counted as separate courses.

A better production approach is to compare against the actual number of courses in the `courses` table rather than hardcoding 8.

---

## 4. Students enrolled in courses but with no Completed course

**Question:** Find students who:

1. Have at least one enrollment.
2. Have not completed any course.

### Query

```sql
SELECT *
FROM students s
WHERE EXISTS (
    SELECT 1
    FROM enrollments e
    WHERE e.student_id = s.student_id
)
AND NOT EXISTS (
    SELECT 1
    FROM enrollments e
    WHERE e.student_id = s.student_id
    AND e.status = 'Completed'
);
```

### Concepts learned

This question combines two conditions:

- EXISTS checks whether the student has at least one enrollment.
- NOT EXISTS checks whether the student has zero Completed enrollments.
- AND ensures both conditions must be satisfied.

**My mistake:** Initially, I checked only whether a Completed enrollment existed in the entire table. I missed the correlated condition.

Correct correlation:

```sql
e.student_id = s.student_id
```

---

## 5. Students enrolled in Java but not Python

**Question:** Find students who enrolled in Java but did not enroll in Python.

Assume:

- Java = course_id 10
- Python = course_id 20

### Query

```sql
SELECT *
FROM students s
WHERE EXISTS (
    SELECT 1
    FROM enrollments e
    WHERE e.student_id = s.student_id
    AND e.course_id = 10
)
AND NOT EXISTS (
    SELECT 1
    FROM enrollments e
    WHERE e.student_id = s.student_id
    AND e.course_id = 20
);
```

### Concepts learned

- EXISTS checks Java enrollment.
- NOT EXISTS checks missing Python enrollment.
- AND combines both conditions.
- Each subquery checks the current student separately.

**Important pattern:**

```sql
WHERE EXISTS (...)
AND NOT EXISTS (...)
```

Means: A matching record must exist for the first condition, but no matching record must exist for the second condition.

---

## 6. Courses in which no student has enrolled

**Question:** Find courses that have no enrollment records.

### Query

```sql
SELECT *
FROM courses c
WHERE NOT EXISTS (
    SELECT 1
    FROM enrollments e
    WHERE e.course_id = c.course_id
);
```

### Concepts learned

- The outer query checks each course.
- The inner query searches for matching enrollments.
- If no enrollment is found, the course is returned.
- `c.course_id` connects the inner query to the current course.

This is another example of a correlated subquery.

**My performance:** Correctly solved independently.

---

## 7. Students enrolled in every course offered by their department

**Question:** Find students who enrolled in all courses belonging to their department.

### Tables

Students:

| student_id | name | department_id |
|---|---|---|
| 1 | Aman | 10 |
| 2 | Riya | 10 |
| 3 | Karan | 20 |

Courses:

| course_id | department_id |
|---|---|
| 101 | 10 |
| 102 | 10 |
| 201 | 20 |

Enrollments:

| student_id | course_id |
|---|---|
| 1 | 101 |
| 1 | 102 |
| 2 | 101 |
| 3 | 201 |

Expected output: Aman and Karan.

### Query

```sql
SELECT *
FROM students s
WHERE NOT EXISTS (
    SELECT 1
    FROM courses c
    WHERE c.department_id = s.department_id
    AND NOT EXISTS (
        SELECT 1
        FROM enrollments e
        WHERE e.student_id = s.student_id
        AND e.course_id = c.course_id
    )
);
```

### Detailed explanation

This query uses nested NOT EXISTS.

**Level 1 — Student**

Check each student individually.

**Level 2 — Courses**

Find courses belonging to that student's department.

**Level 3 — Enrollments**

Check whether the student has enrolled in each particular course.

### Why two NOT EXISTS?

Inner NOT EXISTS:

Checks whether the student is missing a particular course.

Outer NOT EXISTS:

Checks whether there is no such missing course.

If no missing course exists, the student is selected.

### Important concept

This pattern is also known as Relational Division.

It is useful when a question contains words like:

- ALL
- EVERY
- EACH
- ALL AVAILABLE COURSES

**My learning:** Initially, I was confused about why two NOT EXISTS statements were required. The key idea is to find missing records first, then check that no missing record exists.

---

## 8. Courses in which at least one student is enrolled

**Question:** Find courses that have at least one enrollment.

### Query

```sql
SELECT *
FROM courses c
WHERE EXISTS (
    SELECT 1
    FROM enrollments e
    WHERE e.course_id = c.course_id
);
```

### Concepts learned

This is the opposite of Question 6.

- Question 6 uses NOT EXISTS to find courses with zero enrollments.
- Question 8 uses EXISTS to find courses with at least one enrollment.

**Important:** Both queries use the same correlated condition, but their existence checks are opposite.

---

## 9. Students enrolled in more than 2 courses

**Question:** Find students who have enrolled in more than 2 courses.

### My first attempt

```sql
SELECT *
FROM students
WHERE EXISTS (
    SELECT 1
    FROM enrollments e
    GROUP BY e.student_id
    HAVING COUNT(e.enrollment_id) > 2
);
```

### Mistake

The subquery was not connected to the outer student.

It was checking whether any student in the entire enrollment table had more than 2 enrollments.

If that condition was true, EXISTS would return TRUE for every outer student.

### Improved query

```sql
SELECT s.*
FROM students s
WHERE s.student_id IN (
    SELECT e.student_id
    FROM enrollments e
    GROUP BY e.student_id
    HAVING COUNT(e.enrollment_id) > 2
);
```

### Concepts learned

- GROUP BY creates student-wise groups.
- COUNT counts enrollment records.
- HAVING filters grouped results.
- IN matches the returned student IDs against the outer query.

---

## 10. Students enrolled in exactly 2 different courses

**Question:** Find students who have enrolled in exactly 2 different courses.

### Query

```sql
SELECT *
FROM students s
WHERE s.student_id IN (
    SELECT e.student_id
    FROM enrollments e
    GROUP BY e.student_id
    HAVING COUNT(DISTINCT e.course_id) = 2
);
```

### Concepts learned

The difference between these two expressions:

```sql
COUNT(e.enrollment_id)
```

Counts enrollment records.

```sql
COUNT(DISTINCT e.course_id)
```

Counts unique courses.

For questions involving different or unique courses, COUNT(DISTINCT course_id) is safer.

---

# Important SQL Concepts Learned Today

## 1. EXISTS vs NOT EXISTS

| EXISTS | NOT EXISTS |
|---|---|
| At least one match | No matching record |
| Returns TRUE if a row exists | Returns TRUE if no row exists |
| Used to check presence | Used to check absence |

## 2. IN vs =

| Operator | Usage |
|---|---|
| `=` | Compare with a single value |
| `IN` | Compare with multiple possible values |

## 3. GROUP BY vs HAVING

- GROUP BY groups records.
- HAVING filters grouped records.
- WHERE filters individual records before grouping.

## 4. Correlated Subqueries

A correlated subquery refers to a column from its outer query.

Example:

```sql
WHERE e.student_id = s.student_id
```

The inner query checks the current outer student's ID.

## 5. COUNT vs COUNT DISTINCT

- COUNT(column) counts non-NULL values.
- COUNT(DISTINCT column) counts unique non-NULL values.

## 6. Nested NOT EXISTS

Used when we need to check that no required item is missing.

Typical question pattern:

"Find students who have completed ALL courses."

Logic:

1. Find courses.
2. Check if a student is missing any course.
3. Reject the student if a missing course exists.
4. Select the student if no missing course exists.

---


### Mistakes to revise

1. Forgetting to correlate the inner query with the outer query.
2. Using EXISTS without connecting the student ID.
3. Confusing enrollment count with unique course count.
4. Using `=` when a subquery can return multiple rows.
5. Confusing the purpose of inner and outer NOT EXISTS.

---
