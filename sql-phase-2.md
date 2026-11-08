
**Database:** `college_db`  
**Topic:** SQL JOINs, GROUP BY, HAVING & Aggregate Functions

---

## Q1. INNER JOIN — Student & Department

**Question:** Display each student's name along with their department name.

```sql
SELECT
    students.name,
    departments.department_name
FROM students
INNER JOIN departments
    ON students.department_id = departments.department_id;
```

**Concept:** INNER JOIN returns only matching records from both tables.

---

## Q2. LEFT JOIN — All Students

**Question:** Display all students with their department names, including students without a matching department.

```sql
SELECT
    students.name,
    departments.department_name
FROM students
LEFT JOIN departments
    ON students.department_id = departments.department_id;
```

**Concept:** LEFT JOIN preserves all records from the left table.

---

## Q3. RIGHT JOIN — All Departments

**Question:** Display all departments and their associated students, including departments without students.

```sql
SELECT
    students.name,
    departments.department_name
FROM students
RIGHT JOIN departments
    ON students.department_id = departments.department_id;
```

**Concept:** RIGHT JOIN preserves all records from the right table.

---

## Q4. Multiple Table JOINs

**Question:** Display student name, enrolled course name and instructor name.

**Tables:** students, enrollments, courses, instructors.

```sql
SELECT *
FROM students
INNER JOIN enrollments
    ON students.student_id = enrollments.student_id
INNER JOIN courses
    ON enrollments.course_id = courses.course_id
INNER JOIN instructors
    ON enrollments.instructor_id = instructors.instructor_id;
```

**Concept:** Multiple JOINs connect three or more related tables.

---

## Q5. JOIN + WHERE

**Question:** Display student name and course name, but only for enrollments whose status is `Completed`.

```sql
SELECT
    students.name,
    courses.course_name,
    enrollments.status
FROM students
INNER JOIN enrollments
    ON students.student_id = enrollments.student_id
INNER JOIN courses
    ON enrollments.course_id = courses.course_id
WHERE enrollments.status = 'Completed';
```

**Concept:** WHERE filters records before grouping.

---

## Q6. JOIN + GROUP BY

**Question:** Display each department name and the total number of students in that department.

```sql
SELECT
    departments.department_name,
    COUNT(students.student_id) AS total_students
FROM students
INNER JOIN departments
    ON departments.department_id = students.department_id
GROUP BY departments.department_name;
```

**Concept:** GROUP BY creates groups and COUNT() counts records in each group.

---

## Q7. JOIN + GROUP BY + ORDER BY

**Question:** Display each course name and its total enrolled students. Sort from highest enrollment to lowest.

```sql
SELECT
    courses.course_name,
    COUNT(students.student_id) AS total_students
FROM students
INNER JOIN enrollments
    ON enrollments.student_id = students.student_id
INNER JOIN courses
    ON courses.course_id = enrollments.course_id
GROUP BY courses.course_id
ORDER BY total_students DESC;
```

**Concept:** ORDER BY DESC sorts results in descending order.

---

## Q8. LEFT JOIN + COUNT

**Question:** Display all departments, including departments without any students, along with their student count.

```sql
SELECT
    departments.department_name,
    COUNT(students.student_id) AS total_students
FROM departments
LEFT JOIN students
    ON departments.department_id = students.department_id
GROUP BY departments.department_id, departments.department_name;
```

**Concept:**
- LEFT JOIN preserves all departments.
- COUNT(column) ignores NULL values.
- Departments without students return 0.

**Important:** Use COUNT(students.student_id), not COUNT(*).

---

## Q9. JOIN + GROUP BY + HAVING

**Question:** Display department names where the number of students is greater than 5.

```sql
SELECT
    departments.department_name,
    COUNT(students.student_id) AS total_students
FROM students
INNER JOIN departments
    ON departments.department_id = students.department_id
GROUP BY departments.department_id
HAVING total_students > 5;
```

**Concept:** HAVING filters groups after GROUP BY.

---

## Q10. JOIN + Multiple Aggregate Functions

**Question:** For each department, display:
- Department name
- Total students
- Average student marks
- Maximum student marks

```sql
SELECT
    departments.department_name,
    COUNT(students.student_id) AS total_students,
    AVG(students.marks) AS average_marks,
    MAX(students.marks) AS highest_marks
FROM students
INNER JOIN departments
    ON departments.department_id = students.department_id
GROUP BY departments.department_id;
```

**Concept:**
- COUNT() → Total records
- AVG() → Average value
- MAX() → Maximum value

---

## Q11. JOIN + WHERE + GROUP BY + HAVING + ORDER BY

**Question:** Display course names and total enrolled students following these conditions:

1. Count only enrollments with status `Completed`.
2. Show only courses having more than 2 completed enrollments.
3. Sort from highest completed enrollment to lowest.

```sql
SELECT
    courses.course_name,
    COUNT(enrollments.student_id) AS total_students
FROM courses
INNER JOIN enrollments
    ON courses.course_id = enrollments.course_id
WHERE enrollments.status = 'Completed'
GROUP BY courses.course_id, courses.course_name
HAVING COUNT(enrollments.student_id) > 2
ORDER BY total_students DESC;
```

**Concept:**
- WHERE filters completed enrollments.
- GROUP BY groups records course-wise.
- HAVING filters courses with more than 2 completed enrollments.
- ORDER BY sorts the final result.

---



| Topic | Questions |
|---|---:|
| INNER JOIN | 1 |
| LEFT JOIN | 2 |
| RIGHT JOIN | 1 |
| Multiple JOINs | 1 |
| JOIN + WHERE | 1 |
| JOIN + GROUP BY | 3 |
| JOIN + HAVING | 2 |
| Aggregate Functions | 1 |

**Total Practice Questions: 11**

## Important Revision Notes

### 1. JOIN Direction

```sql
A LEFT JOIN B
```
Preserves all records from A.

```sql
A RIGHT JOIN B
```
Preserves all records from B.

Equivalent:

```sql
A RIGHT JOIN B = B LEFT JOIN A
```

### 2. SQL Execution Order (Logical)

1. FROM
2. JOIN / ON
3. WHERE
4. GROUP BY
5. HAVING
6. SELECT
7. ORDER BY

### 3. Aggregate Functions

```sql
COUNT()
SUM()
AVG()
MIN()
MAX()
```

### 4. WHERE vs HAVING

| WHERE | HAVING |
|---|---|
| Filters individual rows | Filters groups |
| Before GROUP BY | After GROUP BY |
| Normal conditions | Aggregate conditions |

---
