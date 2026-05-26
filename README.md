# Instructo
SQL Server database for an online learning platform - schema design, audit triggers, reporting views, and stored procedures with full transaction management.
# Instructo — Learning Platform Database & Analytical Reporting

> Relational database system built in **Microsoft SQL Server 2019** for an online learning and assessment platform. Covers schema design, data population, analytical reporting views, audit triggers, stored procedures with cursor logic, and full transaction management.

---

## Tech Stack

![SQL Server](https://img.shields.io/badge/Microsoft%20SQL%20Server-2019-CC2927?style=flat&logo=microsoftsqlserver&logoColor=white)
![T-SQL](https://img.shields.io/badge/T--SQL-Query%20Language-0078D4?style=flat)
![SSMS](https://img.shields.io/badge/SSMS-Management%20Studio-gray?style=flat)

---

## Project Overview

**Instructo** is an online learning and assessment platform database designed to manage educational courses, student assessments, quiz responses, certifications, and user activity. The database serves as a central repository for:

- User profiles (students and instructors)
- Course content and structure
- Assessment and quiz management
- Student score tracking and certification issuance
- Full audit logging of all data changes

Built as a final project for **IFT 530 — Database Management** at Arizona State University.

**Authors:** Sinchana Praveen, Tanmay Godse (The Data Architects — Group 65)

---

## Schema Overview

The database `The_Data_Architects` contains **10 normalized tables** with enforced referential integrity:

| Table | Description |
|---|---|
| `Users` | Stores student and instructor accounts with role-based access control |
| `Courses` | Course catalog linked to instructor users |
| `Course_Content` | Text-based content attached to each course |
| `Assessments` | Assessments (quizzes, exams, projects) belonging to courses |
| `Quizzes` | Quiz metadata linked to assessments |
| `Quiz_Questions` | Individual questions within each quiz |
| `Quiz_Responses` | Student answers to each quiz question |
| `Student_Scores` | Score records per student per assessment |
| `Certifications` | Issued certifications upon course completion |
| `Audit_Log` | Immutable log of all INSERT, UPDATE, and DELETE operations |

### ER Diagram

> 📎 See `The_Data_Architects_Group_No_65_Final.docx` for the full entity-relationship diagram (Step 2).

---

## Features

### 1. Normalized Schema Design
- All tables in **3NF** with primary keys, foreign key constraints, and `NOT NULL` / `CHECK` validations
- Role enforcement via `CHECK (role IN ('student', 'instructor'))` on the Users table

### 2. Analytical Reporting Views
Three views built for operational and performance reporting:

| View | Purpose |
|---|---|
| `EnrolledStudents` | Lists students enrolled per course — for instructor enrollment tracking |
| `CoursePerformanceOverview` | Average assessment score per course using `AVG()` + `GROUP BY` — analogous to member outcomes reporting |
| `ActiveCoursesContent` | Shows recently created course content for student discovery |

### 3. Audit Logging Triggers
Three DML triggers on the `Users` table automatically log all data changes to `Audit_Log`:

- `AfterInsertUsers` — logs new user creation with timestamp
- `AfterUpdateUsers` — logs username changes using `INSERTED` / `DELETED` tables
- `AfterDeleteUsers` — logs user removal with identifying details

### 4. Stored Procedure with Cursor
`ProcessStudentCertificationsAdvanced` — a stored procedure that:
- Iterates through all student-course pairs using a `LOCAL FAST_FORWARD` cursor
- Checks whether a student has passed all assessments in a course (score ≥ 50)
- Issues certifications via `INSERT` into `Certifications` if eligible
- Logs every certification event to `Audit_Log`
- Wraps all operations in `BEGIN TRANSACTION` / `COMMIT` with full `ROLLBACK` on error via `TRY...CATCH`

### 5. Stored Procedure & UDF
- `GetUserByEmail` — parameterized stored procedure for user lookup by email
- `CalculateTotalMarks(@CourseID)` — scalar UDF returning the sum of all assessment marks for a given course

---

## Key SQL Concepts Demonstrated

| Concept | Where Used |
|---|---|
| Normalized schema (3NF) | Full database design |
| Referential integrity (FK constraints) | All 10 tables |
| Role-based data validation (`CHECK`) | `Users.role` column |
| DML triggers (`AFTER INSERT/UPDATE/DELETE`) | `Audit_Log` population |
| Cursor-based iteration (`LOCAL FAST_FORWARD`) | `ProcessStudentCertificationsAdvanced` |
| ACID-compliant transactions (`BEGIN / COMMIT / ROLLBACK`) | Certification stored procedure |
| Aggregate reporting views (`AVG`, `GROUP BY`) | `CoursePerformanceOverview` |
| Scalar UDF | `CalculateTotalMarks` |
| `TRY...CATCH` error handling | Certification stored procedure |
| `SCOPE_IDENTITY()` | Retrieving newly inserted certification ID |
| `INSERTED` / `DELETED` virtual tables | Update and delete triggers |

---

## How to Run

### Prerequisites
- Microsoft SQL Server 2019 (or later)
- SQL Server Management Studio (SSMS)

### Steps

1. **Clone the repository**
   ```bash
   git clone https://github.com/SinchanaPraveen/<repo-name>.git
   ```

2. **Open SSMS** and connect to your SQL Server instance

3. **Open** `IFT_530_Final_Scripts.txt` in SSMS

4. **Run the script** — it is organized sequentially by step:

   | Step | What it does |
   |---|---|
   | Step 1 | Creates the database `The_Data_Architects` and all 10 tables |
   | Step 2 | ER Diagram — see the `.docx` file |
   | Step 3 | Populates all tables with sample data |
   | Step 4 | Creates 3 analytical reporting views |
   | Step 5 | Creates INSERT / UPDATE / DELETE audit triggers |
   | Step 6 | Creates stored procedure `GetUserByEmail` and UDF `CalculateTotalMarks` |
   | Step 7 | Creates `ProcessStudentCertificationsAdvanced` stored procedure with cursor |

5. **Verify** using the testing scripts included after each step

---

## Sample Queries

**Course performance overview:**
```sql
SELECT * FROM CoursePerformanceOverview;
-- Returns: CourseTitle, AverageScore for all courses with recorded scores
```

**Issue certifications for all eligible students:**
```sql
EXEC ProcessStudentCertificationsAdvanced;
-- Iterates all student-course pairs, issues certs, logs to Audit_Log
```

**Total marks for a course:**
```sql
SELECT dbo.CalculateTotalMarks(1) AS TotalMarks;
-- Returns sum of all assessment marks for course_id = 1
```

**View full audit trail:**
```sql
SELECT * FROM Audit_Log ORDER BY timestamp DESC;
-- Returns all logged INSERT / UPDATE / DELETE events
```

---

## Project Structure

```
instructo-db/
├── IFT_530_Final_Scripts.txt                   # All SQL scripts (Steps 1–7)
├── The_Data_Architects_Group_No_65_Final.docx  # Full documentation + ER diagram
└── README.md
```

---

## Course Context

| Field | Detail |
|---|---|
| Course | IFT 530 — Database Management Systems |
| Institution | Arizona State University |
| SQL Server | Microsoft SQL Server 2019 |
| Team | The Data Architects (Group 65) |
| Date | December 2024 |
