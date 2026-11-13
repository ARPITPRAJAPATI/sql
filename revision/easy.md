

**Coverage:** Easy Q1–16 + Medium Q1–18  
**Database:** Hospital DB  
**SQL Dialect:** MySQL

---

# PART 1 – EASY SQL QUESTIONS

## Q1. Display first_name, last_name and gender of all male patients.

```sql
SELECT first_name, last_name, gender
FROM patients
WHERE gender = 'M';
```

**Learning:**
- `SELECT` chooses columns.
- `FROM` specifies the table.
- `WHERE` filters rows.
- Text values are written inside quotes.

## Q2. Find patients whose height is greater than 160 and weight is greater than 70.

```sql
SELECT first_name, last_name, birth_date
FROM patients
WHERE height > 160
  AND weight > 70;
```

**Learning:**
- `AND` means both conditions must be true.
- `OR` means at least one condition must be true.

## Q3. Find patients who have allergies and live in Hamilton.

```sql
SELECT first_name, last_name, allergies
FROM patients
WHERE allergies IS NOT NULL
  AND city = 'Hamilton';
```

**Learning:**
- `IS NULL` checks missing values.
- `IS NOT NULL` checks values that are present.
- Never use `= NULL`.

## Q4. Display unique birth years in ascending order.

```sql
SELECT DISTINCT YEAR(birth_date) AS birth_year
FROM patients
ORDER BY birth_year ASC;
```

**Learning:**
- `YEAR()` extracts the year from a date.
- `DISTINCT` removes duplicates.
- `ORDER BY ASC` sorts in ascending order.

## Q5. Find first names that occur only once.

```sql
SELECT first_name
FROM patients
GROUP BY first_name
HAVING COUNT(*) = 1;
```

**Learning:**
- `GROUP BY` creates groups of identical values.
- `COUNT(*)` counts rows in each group.
- `HAVING` filters groups after aggregation.
- `WHERE` filters individual rows before grouping.

## Q6. Find patients whose first name starts and ends with 's' and has at least 6 characters.

```sql
SELECT first_name, patient_id
FROM patients
WHERE first_name LIKE 's%'
  AND first_name LIKE '%s'
  AND CHAR_LENGTH(first_name) >= 6;
```

**Learning:**
- `'s%'` means starts with s.
- `'%s'` means ends with s.
- `'%s%'` means contains s anywhere.
- `CHAR_LENGTH()` returns the number of characters.

## Q7. Find patients who were admitted with Dementia.

```sql
SELECT p.patient_id, p.first_name, p.last_name
FROM patients p
INNER JOIN admissions a
    ON p.patient_id = a.patient_id
WHERE a.diagnosis = 'Dementia';
```

**Learning:**
- `JOIN` combines related tables.
- `ON` defines how rows are connected.
- `patient_id` connects patients and admissions.
- `diagnosis` belongs to the admissions table.

## Q8. Sort first names by their length, then alphabetically.

```sql
SELECT first_name
FROM patients
ORDER BY CHAR_LENGTH(first_name) ASC,
         first_name ASC;
```

**Learning:**
- Multiple columns can be used in `ORDER BY`.
- First sorting condition gets priority.
- Second condition resolves ties.

## Q9. Count male and female patients in a single row.

```sql
SELECT
    COUNT(CASE WHEN gender = 'M' THEN 1 END) AS male_count,
    COUNT(CASE WHEN gender = 'F' THEN 1 END) AS female_count
FROM patients;
```

**Learning:**
- `CASE WHEN` performs conditional logic.
- `COUNT()` ignores NULL values.
- Conditional aggregation can calculate multiple categories in one row.
- No `GROUP BY` is required when the entire table should produce one summary row.

## Q10. Find patients who have Penicillin or Morphine allergies.

```sql
SELECT first_name, last_name, allergies
FROM patients
WHERE allergies IN ('Penicillin', 'Morphine');
```

**Learning:**

Equivalent using OR:

```sql
WHERE allergies = 'Penicillin'
   OR allergies = 'Morphine';
```

Important mistake:

```sql
WHERE allergies = 'Penicillin' OR 'Morphine'
```

This is incorrect because each comparison must be written completely.

## Q11. Find repeated patient-diagnosis combinations.

```sql
SELECT patient_id, diagnosis, COUNT(*) AS total
FROM admissions
GROUP BY patient_id, diagnosis
HAVING COUNT(*) > 1;
```

**Learning:**
- Group by both columns when the combination must be unique.
- `COUNT(*) > 1` identifies repeated combinations.
- `diagnosis` belongs to admissions, not patients.

## Q12. Count patients in each city, highest count first.

```sql
SELECT city, COUNT(*) AS total_patients
FROM patients
GROUP BY city
ORDER BY total_patients DESC, city ASC;
```

**Learning:**
- `GROUP BY city` creates one group per city.
- `COUNT(*)` counts patients in each city.
- `DESC` sorts highest to lowest.
- A second sorting condition can be added for ties.

## Q13. Combine records from patients and doctors.

```sql
SELECT first_name, last_name
FROM patients

UNION ALL

SELECT first_name, last_name
FROM doctors;
```

**Learning:**
- `UNION` combines results and removes duplicate rows.
- `UNION ALL` combines results and keeps duplicates.
- Both queries must return the same number of columns in compatible positions.

## Q14. Find how frequently each allergy occurs.

```sql
SELECT allergies, COUNT(*) AS total
FROM patients
WHERE allergies IS NOT NULL
GROUP BY allergies
ORDER BY total DESC;
```

**Learning:**
- Filter NULL before grouping.
- Group identical allergies.
- Count their occurrences.
- Sort by frequency.

## Q15. Find patients born in the 1970s.

```sql
SELECT first_name, last_name, birth_date
FROM patients
WHERE YEAR(birth_date) BETWEEN 1970 AND 1979
ORDER BY birth_date ASC;
```

**Learning:**
- The 1970s means 1970–1979 inclusive.
- `BETWEEN` includes both boundaries.
- `YEAR()` extracts the year.

## Q16. Combine first and last names into full_name.

```sql
SELECT CONCAT(first_name, ' ', last_name) AS full_name
FROM patients;
```

**Learning:**
- `CONCAT()` joins strings.
- A space must be added explicitly.
- `AS` gives a column an alias.

---

