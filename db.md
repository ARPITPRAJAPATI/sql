
# College Database — MySQL Setup

A relational college database created for SQL practice. It contains five related tables and sample records.

## 1. Database Overview

**Database Name:** `college_db`  
**SQL Dialect:** MySQL

### Tables

| Table | Description |
|---|---|
| departments | Department master records |
| students | Student details, marks, attendance, fees and admission date |
| courses | Courses offered by departments |
| instructors | Faculty details |
| enrollments | Maps students to courses and instructors |

## 2. Database Relationships

- One department can have many students.
- One department can offer many courses.
- One department can have many instructors.
- One student can have many enrollments.
- One course can have many enrollments.
- One instructor can be associated with many enrollments.

### ER Diagram

```text
departments
    |
    |----< students
    |
    |----< courses
    |
    |----< instructors

students
    |
    |----< enrollments >---- courses
                    |
                    |
                instructors
```

## 3. Complete MySQL Setup Script

Copy and execute the complete script in MySQL Workbench or MySQL terminal.

```sql
CREATE DATABASE IF NOT EXISTS college_db;
USE college_db;

-- 1. Departments
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(50) UNIQUE NOT NULL,
    hod VARCHAR(100)
);

-- 2. Students
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    age INT,
    city VARCHAR(50),
    department_id INT,
    marks DECIMAL(5,2),
    attendance DECIMAL(5,2),
    fees_paid DECIMAL(10,2),
    admission_date DATE,
    gender VARCHAR(10),
    FOREIGN KEY (department_id)
        REFERENCES departments(department_id)
);

-- 3. Courses
CREATE TABLE courses (
    course_id INT PRIMARY KEY,
    course_name VARCHAR(100) NOT NULL,
    credits INT,
    department_id INT,
    FOREIGN KEY (department_id)
        REFERENCES departments(department_id)
);

-- 4. Instructors
CREATE TABLE instructors (
    instructor_id INT PRIMARY KEY,
    instructor_name VARCHAR(100),
    department_id INT,
    salary DECIMAL(10,2),
    FOREIGN KEY (department_id)
        REFERENCES departments(department_id)
);

-- 5. Enrollments
CREATE TABLE enrollments (
    enrollment_id INT PRIMARY KEY,
    student_id INT,
    course_id INT,
    instructor_id INT,
    enrollment_date DATE,
    grade VARCHAR(5),
    status VARCHAR(20),
    FOREIGN KEY (student_id) REFERENCES students(student_id),
    FOREIGN KEY (course_id) REFERENCES courses(course_id),
    FOREIGN KEY (instructor_id) REFERENCES instructors(instructor_id)
);

-- Insert Departments
INSERT INTO departments VALUES
(1, 'CS', 'Dr. Sharma'),
(2, 'IT', 'Dr. Verma'),
(3, 'ECE', 'Dr. Singh'),
(4, 'ME', 'Dr. Gupta'),
(5, 'CE', 'Dr. Mishra');

-- Insert Students
INSERT INTO students VALUES
(1, 'Aarav', 21, 'Mumbai', 1, 85, 92, 45000, '2023-07-10', 'Male'),
(2, 'Priya', 20, 'Delhi', 2, 91, 96, 50000, '2024-07-12', 'Female'),
(3, 'Rahul', 22, 'Mumbai', 1, 72, 65, 40000, '2023-08-15', 'Male'),
(4, 'Neha', 19, 'Lucknow', 3, 88, 89, NULL, '2024-08-20', 'Female'),
(5, 'Aman', 21, 'Delhi', 2, 78, 75, 35000, '2025-07-05', 'Male'),
(6, 'Ananya', 20, 'Mumbai', 1, 95, 98, 50000, '2026-06-15', 'Female'),
(7, 'Rohit', 23, 'Jaipur', 4, 68, 58, 30000, '2023-09-01', 'Male'),
(8, 'Simran', 21, 'Lucknow', 3, 82, 91, 42000, '2026-10-20', 'Female'),
(9, 'Karan', 22, 'Delhi', 2, 75, 80, NULL, '2025-08-10', 'Male'),
(10, 'Priya', 20, 'Mumbai', 1, 89, 94, 48000, '2026-10-30', 'Female'),
(11, 'Ishita', 19, 'Pune', 5, 93, 97, 52000, '2024-07-01', 'Female'),
(12, 'Vikas', 22, 'Jaipur', 4, 55, 62, 25000, '2023-06-10', 'Male'),
(13, 'Arjun', 21, 'Delhi', 2, 84, 88, 40000, '2026-11-01', 'Male'),
(14, 'Meera', 20, 'Pune', 5, 77, 83, 38000, '2025-07-18', 'Female'),
(15, 'Aditya', 23, 'Mumbai', 1, 61, 55, 20000, '2024-09-15', 'Male'),
(16, 'Sneha', 21, 'Lucknow', 3, 90, 95, 45000, '2026-10-25', 'Female'),
(17, 'Kabir', 22, 'Jaipur', 4, 73, 70, 32000, '2025-08-22', 'Male'),
(18, 'Riya', 19, 'Pune', 5, 86, 92, NULL, '2026-11-03', 'Female'),
(19, 'Sahil', 20, 'Delhi', 2, 80, 85, 42000, '2024-07-25', 'Male'),
(20, 'Pooja', 21, 'Mumbai', 1, 97, 99, 55000, '2026-10-28', 'Female');

-- Insert Courses
INSERT INTO courses VALUES
(101, 'DBMS', 4, 1),
(102, 'Operating Systems', 4, 1),
(103, 'Computer Networks', 3, 2),
(104, 'Web Development', 3, 2),
(105, 'Digital Electronics', 4, 3),
(106, 'Mechanical Design', 4, 4),
(107, 'Structural Engineering', 4, 5),
(108, 'Data Structures', 4, 1);

-- Insert Instructors
INSERT INTO instructors VALUES
(201, 'Dr. Mehta', 1, 85000),
(202, 'Prof. Kapoor', 2, 75000),
(203, 'Dr. Reddy', 3, 80000),
(204, 'Prof. Joshi', 4, 70000),
(205, 'Dr. Khan', 5, 90000);

-- Insert Enrollments
INSERT INTO enrollments VALUES
(1, 1, 101, 201, '2025-08-01', 'A', 'Completed'),
(2, 1, 108, 201, '2025-08-05', 'B', 'Completed'),
(3, 2, 103, 202, '2025-08-02', 'A', 'Completed'),
(4, 2, 104, 202, '2025-08-06', 'A', 'Completed'),
(5, 3, 101, 201, '2025-08-03', 'C', 'Completed'),
(6, 4, 105, 203, '2025-08-04', 'B', 'Completed'),
(7, 5, 103, 202, '2025-08-07', 'B', 'Completed'),
(8, 6, 101, 201, '2026-08-01', 'A', 'Ongoing'),
(9, 7, 106, 204, '2025-08-08', 'C', 'Completed'),
(10, 8, 105, 203, '2026-08-02', 'A', 'Ongoing'),
(11, 9, 104, 202, '2025-08-09', 'B', 'Completed'),
(12, 10, 108, 201, '2026-08-03', 'A', 'Ongoing'),
(13, 11, 107, 205, '2025-08-10', 'A', 'Completed'),
(14, 12, 106, 204, '2025-08-11', 'D', 'Completed'),
(15, 13, 103, 202, '2026-08-04', 'B', 'Ongoing'),
(16, 14, 107, 205, '2025-08-12', 'B', 'Completed'),
(17, 15, 102, 201, '2025-08-13', 'C', 'Completed'),
(18, 16, 105, 203, '2026-08-05', 'A', 'Ongoing'),
(19, 17, 106, 204, '2025-08-14', 'B', 'Completed'),
(20, 18, 107, 205, '2026-08-06', 'A', 'Ongoing'),
(21, 19, 104, 202, '2025-08-15', 'B', 'Completed'),
(22, 20, 101, 201, '2026-08-07', 'A', 'Ongoing');

-- Verify Database
SHOW TABLES;

SELECT * FROM departments;
SELECT * FROM students;
SELECT * FROM courses;
SELECT * FROM instructors;
SELECT * FROM enrollments;
```

## 4. Database Statistics

| Table | Total Records |
|---|---:|
| Departments | 5 |
| Students | 20 |
| Courses | 8 |
| Instructors | 5 |
| Enrollments | 22 |
| **Total** | **60** |

## 5. Important Practice Scenarios

This database intentionally includes:

- Duplicate student names (`Priya`) for duplicate detection.
- NULL values in `fees_paid` for NULL handling.
- Multiple cities for filtering and grouping.
- Different marks and attendance values for aggregate practice.
- Multiple admission dates for date-based queries.
- Foreign-key relationships for INNER, LEFT and RIGHT JOIN practice.
- Multiple enrollments for one student to practice one-to-many relationships.

## 6. Useful SQL Commands

```sql
-- Select database
USE college_db;

-- View all tables
SHOW TABLES;

-- Check table structure
DESCRIBE students;
DESCRIBE departments;
DESCRIBE courses;
DESCRIBE instructors;
DESCRIBE enrollments;

-- Count records
SELECT COUNT(*) FROM students;
SELECT COUNT(*) FROM departments;
SELECT COUNT(*) FROM courses;
SELECT COUNT(*) FROM instructors;
SELECT COUNT(*) FROM enrollments;
```

## 7. Reset Database

To rebuild the database from scratch:

```sql
DROP DATABASE IF EXISTS college_db;
```

Then execute the complete setup script again.

**Note:** Dropping the database permanently deletes its tables and data.
