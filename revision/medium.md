

---

## Q1. Format last name in uppercase and first name in lowercase, separated by a comma. Sort by first name descending.

```sql
SELECT CONCAT(UPPER(last_name), ',', LOWER(first_name)) AS full_name
FROM patients
ORDER BY first_name DESC;
```

**Learning:**
- `UPPER()` converts text to uppercase.
- `LOWER()` converts text to lowercase.
- `CONCAT()` combines strings.
- `DESC` sorts in descending order.

## Q2. Find province-wise total height where the sum is at least 7000.

```sql
SELECT province_id, SUM(height) AS sum_height
FROM patients
GROUP BY province_id
HAVING SUM(height) >= 7000;
```

**Learning:**
- `GROUP BY` creates province-wise groups.
- `SUM()` calculates total height.
- `HAVING` filters groups after aggregation.
- `WHERE` is for rows; `HAVING` is for groups.

## Q3. Display patients who have allergies, sorted by allergy and first name.

```sql
SELECT first_name, last_name, allergies
FROM patients
WHERE allergies IS NOT NULL
ORDER BY allergies ASC, first_name ASC;
```

**Learning:**
- `IS NOT NULL` excludes missing values.
- Multiple columns can be used in `ORDER BY`.

## Q4. Find patients who have been admitted at least once.

```sql
SELECT patient_id, first_name, last_name
FROM patients p
WHERE EXISTS (
    SELECT 1
    FROM admissions a
    WHERE a.patient_id = p.patient_id
);
```

**Learning:**
- `EXISTS` checks whether at least one matching row exists.
- `SELECT 1` is used because EXISTS only checks row existence.
- At least once means one or more records.

## Q5. Find patients who have been admitted more than 3 times.

```sql
SELECT p.patient_id, p.first_name, p.last_name,
       COUNT(*) AS total
FROM patients p
JOIN admissions a
    ON p.patient_id = a.patient_id
GROUP BY p.patient_id, p.first_name, p.last_name
HAVING COUNT(*) > 3;
```

**Learning:**
- JOIN connects patients and admissions.
- GROUP BY creates one group per patient.
- COUNT counts admissions.
- HAVING filters patients with more than 3 admissions.

## Q6. Find patients who have never been admitted.

```sql
SELECT patient_id, first_name, last_name
FROM patients p
WHERE NOT EXISTS (
    SELECT 1
    FROM admissions a
    WHERE a.patient_id = p.patient_id
);
```

**Learning:**
- `NOT EXISTS` checks that no matching row exists.
- This is an anti-join pattern.
- The inner query is connected to the current patient using `p.patient_id`.

## Q7. Display patients admitted more than once with their total admissions, highest first.

```sql
SELECT p.first_name, p.last_name,
       COUNT(*) AS total
FROM patients p
JOIN admissions a
    ON p.patient_id = a.patient_id
GROUP BY p.patient_id, p.first_name, p.last_name
HAVING COUNT(*) > 1
ORDER BY total DESC;
```

**Learning:**
- COUNT calculates admissions per patient.
- HAVING filters groups.
- ORDER BY DESC puts the highest count first.

## Q8. Find patients born in 1970 or later.

```sql
SELECT first_name, last_name, birth_date
FROM patients
WHERE birth_date >= '1970-01-01'
ORDER BY birth_date ASC;
```

**Learning:**
- Comparing complete dates is more precise than comparing only years.
- `>=` includes the boundary date.
- ASC sorts oldest to newest.

## Q9. Find the tallest patient(s).

```sql
SELECT first_name, last_name, height
FROM patients
WHERE height = (
    SELECT MAX(height)
    FROM patients
);
```

**Learning:**
- Inner query finds maximum height.
- Outer query finds patients matching that height.
- Multiple patients can share the maximum height.

## Q10. Find patients whose first name is shared by another patient.

```sql
SELECT patient_id, first_name, last_name
FROM patients
WHERE first_name IN (
    SELECT first_name
    FROM patients
    GROUP BY first_name
    HAVING COUNT(*) > 1
);
```

**Learning:**
- GROUP BY groups identical first names.
- HAVING COUNT(*) > 1 finds duplicates.
- IN checks whether a name belongs to the returned list.

**Important:** An uncorrelated EXISTS only checks whether the inner query returns any rows. It does not automatically match each outer patient's name.

## Q11. Find patients born in the same year as patient_id = 1.

```sql
SELECT first_name, last_name, birth_date
FROM patients
WHERE YEAR(birth_date) = (
    SELECT YEAR(birth_date)
    FROM patients
    WHERE patient_id = 1
);
```

**Learning:**
- Inner query gets patient 1's birth year.
- Outer query compares every patient's birth year with it.
- This is a scalar subquery because one value is returned.

## Q12. Find patients born after patient_id = 5.

```sql
SELECT patient_id, first_name, last_name, birth_date
FROM patients
WHERE birth_date > (
    SELECT birth_date
    FROM patients
    WHERE patient_id = 5
);
```

**Learning:**
- Inner query finds patient 5's birth date.
- Outer query finds patients with a later birth date.
- Compare full dates when the question says born after.

## Q13. Find patients whose height is greater than the overall average height.

```sql
SELECT first_name, last_name, height
FROM patients
WHERE height > (
    SELECT AVG(height)
    FROM patients
);
```

**Learning:**
- AVG calculates the overall average.
- Inner query returns one value.
- Outer query compares every patient's height with that value.

## Q14. Find patients whose height is greater than the average height of their own province.

```sql
SELECT first_name, last_name, height
FROM patients a
WHERE a.height > (
    SELECT AVG(b.height)
    FROM patients b
    WHERE b.province_id = a.province_id
);
```

**Learning: Correlated Subquery**

- `a` represents the current patient in the outer query.
- `b` represents records used by the inner query.
- `b.province_id = a.province_id` filters patients to the current patient's province.
- AVG calculates that province's average height.
- The outer query compares the current patient's height with that average.

**Most important concept:** The inner query uses `a.province_id` from the outer query. Therefore, it depends on the current outer row. This is called a correlated subquery.

## Q15. Find the second tallest patient(s).

```sql
SELECT first_name, last_name, height
FROM patients
WHERE height = (
    SELECT MAX(height)
    FROM patients
    WHERE height < (
        SELECT MAX(height)
        FROM patients
    )
);
```

**Learning: Nested Subquery**

Three levels:

1. Innermost query finds the maximum height.
2. Middle query excludes that maximum and finds the next maximum.
3. Outer query returns all patients with that second-highest height.

**Important:** This finds the second-highest distinct height. If multiple patients have that height, all are returned.

## Q16. Find patients who have the same height as patient_id = 1.

```sql
SELECT patient_id, first_name, last_name, height
FROM patients
WHERE height = (
    SELECT height
    FROM patients
    WHERE patient_id = 1
);
```

**Learning:**
- Inner query gets the height of patient 1.
- Outer query finds all patients with that height.
- `=` works because the inner query returns one value.

## Q17. Find patients whose height is greater than every patient's height in province_id = 10.

```sql
SELECT patient_id, first_name, last_name, height
FROM patients
WHERE height > (
    SELECT MAX(height)
    FROM patients
    WHERE province_id = 10
);
```

**Learning:**
- Inner query calculates maximum height in province 10.
- Anything greater than that maximum is greater than every height in that province.
- MAX converts the comparison into a single value.

## Q18. Find patients who share their height with at least one other patient.

```sql
SELECT a.patient_id, a.first_name, a.last_name, a.height
FROM patients a
WHERE EXISTS (
    SELECT 1
    FROM patients b
    WHERE b.height = a.height
      AND b.patient_id <> a.patient_id
);
```

**Learning: Correlated EXISTS**

- `a` is the current patient.
- `b` searches for another patient.
- `b.height = a.height` finds a matching height.
- `b.patient_id <> a.patient_id` prevents matching the patient with themselves.
- EXISTS returns TRUE if at least one other matching patient exists.

**Important mistake:** If you remove the patient ID inequality condition, a patient can match their own row and every patient may qualify.

---

