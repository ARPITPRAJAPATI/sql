
**Database:** `college_db`  
**Focus:** Subqueries, Nested SELECT, IN, NOT IN, Correlated Subqueries, UNION, UNION ALL

---

## 1. Subqueries

A subquery is a query written inside another SQL query.

### Q1. Students scoring above the overall average

**Task:** Find students whose marks are greater than the average marks of all students.

```sql
SELECT name, marks
FROM students
WHERE marks > (
    SELECT AVG(marks)
    FROM students
);
```

**Concepts:**
- Inner query calculates average marks.
- Outer query compares each student's marks with that average.

**Status:** ✅ Solved

### Q2. Students belonging to Computer Science

**Task:** Find students who belong to the Computer Science department.

```sql
SELECT name, department_id
FROM students
WHERE department_id IN (
    SELECT department_id
    FROM departments
    WHERE department_name = 'CS'
);
```

**Concepts:**
- `IN` checks whether a value exists in the returned list.
- Subquery fetches the department ID.

**Important:** Use the exact department name stored in the database.

**Status:** ✅ Solved

### Q3. Students who are not enrolled in any course

**Task:** Find students whose IDs do not exist in the enrollments table.

```sql
SELECT student_id, name
FROM students
WHERE student_id NOT IN (
    SELECT student_id
    FROM enrollments
);
```

**Concepts:**
- `NOT IN` excludes matching values.
- Select `student_id` from enrollments, not `enrollment_id`.

**Important:** `NOT IN` can produce unexpected results if the subquery returns NULL. `NOT EXISTS` is often a safer alternative.

**Status:** ✅ Solved

### Q4. Students scoring above the highest marks in department 2

**Task:** Find students whose marks are greater than the maximum marks in department 2.

```sql
SELECT name, marks
FROM students
WHERE marks > (
    SELECT MAX(marks)
    FROM students
    WHERE department_id = 2
);
```

**Concepts:**
- `MAX()` finds the highest marks.
- The subquery returns one value.
- Outer query compares each student's marks against it.

**Status:** ✅ Solved

### Q5. Correlated Subquery — Above Department Average

**Task:** Find students whose marks are greater than the average marks of their own department.

```sql
SELECT s1.name, s1.marks, s1.department_id
FROM students s1
WHERE s1.marks > (
    SELECT AVG(s2.marks)
    FROM students s2
    WHERE s2.department_id = s1.department_id
);
```

**Important concepts:**

- `s1` represents the current student in the outer query.
- `s2` represents the students being checked by the inner query.
- `s2.department_id = s1.department_id` ensures that the average is calculated only for the current student's department.

**Common mistake:**

Incorrect:
```sql
WHERE s1.department_id = s1.department_id
```

This condition compares the same value with itself and is always true.

Correct:
```sql
WHERE s2.department_id = s1.department_id
```

**Status:** ⚠️ Attempted — concept needs revision.

---

## 2. UNION

`UNION` combines the results of two or more SELECT statements and removes duplicate rows.

### Basic syntax

```sql
SELECT column_name
FROM table1

UNION

SELECT column_name
FROM table2;
```

### Important UNION rules

1. Every SELECT must return the same number of columns.
2. Corresponding columns must have compatible data types.
3. `UNION` removes duplicate rows.
4. Column names do not have to be identical.
5. `ORDER BY` is generally written once, at the end.

### UNION vs UNION ALL

| UNION | UNION ALL |
|---|---|
| Removes duplicate rows | Keeps duplicate rows |
| Performs duplicate elimination | Does not perform duplicate elimination |
| Usually slower | Usually faster |

---

## 3. UNION Practice Questions

### Q1. Combine students from two departments

**Task:** Fetch students from department 1 and department 2. Remove duplicate rows.

```sql
SELECT *
FROM students
WHERE department_id = 1

UNION

SELECT *
FROM students
WHERE department_id = 2;
```

**Status:** ✅ Solved

### Q2. UNION ALL

**Task:** Fetch students from department 1 and department 2, preserving duplicates.

```sql
SELECT *
FROM students
WHERE department_id = 1

UNION ALL

SELECT *
FROM students
WHERE department_id = 2;
```

**Status:** ✅ Solved

### Q3. UNION with different conditions

**Task:** Find students who either:
- Have marks greater than or equal to 80.
- Have attendance greater than or equal to 90.

Return `name` and `marks`. Remove duplicate rows.

```sql
SELECT name, marks
FROM students
WHERE marks >= 80

UNION

SELECT name, marks
FROM students
WHERE attendance >= 90;
```

**Important:** UNION removes duplicate combinations of `name` and `marks`, not just duplicate names.

**Status:** ✅ Solved

### Q4. UNION with ORDER BY

**Task:** Combine students scoring at least 80 marks and students having at least 90 attendance. Sort the combined result by marks in descending order.

```sql
SELECT name, marks
FROM students
WHERE marks >= 80

UNION

SELECT name, marks
FROM students
WHERE attendance >= 90

ORDER BY marks DESC;
```

**Concepts:**
- `ORDER BY` comes after the complete UNION.
- `DESC` sorts highest to lowest.
- `ASC` sorts lowest to highest.

**Status:** ✅ Solved

### Q5. UNION across different tables

**Tables:**
- `students(name, city)`
- `instructors(instructor_id, instructor_name, department_id, salary)`

**Task:** Combine student names and instructor names into one result. Remove duplicates.

```sql
SELECT name AS person_name
FROM students

UNION

SELECT instructor_name AS person_name
FROM instructors;
```

**Important learnings:**
- Both queries must return the same number of columns.
- Corresponding columns must have compatible data types.
- Column names can differ between tables.
- No JOIN or matching WHERE condition is needed just to combine results.
- The alias in the first SELECT determines the displayed output column name.

**Status:** ✅ Solved

---


---

- A subquery is a query inside another query.
- `AVG()` and `MAX()` can be used inside subqueries.
- `IN` checks values against a returned list.
- `NOT IN` excludes matching values.
- Correlated subqueries refer to the outer query.
- `UNION` combines results and removes duplicate rows.
- `UNION ALL` combines results while preserving duplicates.
- `ORDER BY` sorts the final combined result.
