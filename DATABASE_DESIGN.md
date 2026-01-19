# Database Design Documentation
# Student Academic Tracker System

**Version:** 1.0  
**Last Updated:** January 2026  
**Database Management System:** MySQL 8.0+

---

## Table of Contents
1. [Overview](#overview)
2. [Database Schema Summary](#database-schema-summary)
3. [Entity-Relationship Diagram](#entity-relationship-diagram)
4. [Detailed Table Specifications](#detailed-table-specifications)
5. [Relationships and Constraints](#relationships-and-constraints)
6. [Indexes and Performance](#indexes-and-performance)
7. [Data Integrity Rules](#data-integrity-rules)
8. [Database Conventions](#database-conventions)

---

## Overview

### Purpose
The Student Academic Tracker database is designed to manage all aspects of an academic institution's student tracking system, including:
- Student enrollment and course management
- Assignment and quiz tracking
- Grade calculation and performance ranking
- Attendance monitoring
- Notification system
- Event management
- Lecture note distribution

### Technology Stack
- **Database:** MySQL 8.0+
- **ORM Framework:** Spring Data JPA (Hibernate)
- **Connection Pool:** HikariCP (default with Spring Boot)
- **Migration Tool:** JPA Auto DDL (spring.jpa.hibernate.ddl-auto)

### Database Architecture
- **Total Tables:** 27 entities
- **Join Tables:** 1 (student_course)
- **Composite Keys:** 1 (S_result)
- **Enumerations:** 2 (Role, NotificationType)

---

## Database Schema Summary

### Entity Categories

#### 1. User Management (5 tables)
| Table | Description | Primary Key |
|-------|-------------|-------------|
| `student` | Student information and authentication | Student_id (String) |
| `admin` | Administrator credentials | Admin_id (String) |
| `admin_profile` | Extended admin profile information | adminID (String) |
| `user` | General user profiles | reg_no (String) |
| `instructor` | Instructor information | Instructor_id (String) |

#### 2. Academic Structure (2 tables)
| Table | Description | Primary Key |
|-------|-------------|-------------|
| `course` | Course catalog | Course_id (String) |
| `department` | Department information | Department_id (String) |

#### 3. Course Content (5 tables)
| Table | Description | Primary Key |
|-------|-------------|-------------|
| `assignment` | Assignment definitions | id (Long, Auto) |
| `quiz` | Quiz definitions | id (Long, Auto) |
| `lecture_note` | Lecture materials | id (Long, Auto) |
| `examsheet` | Exam schedules and sheets | id (Long, Auto) |
| `course_weights` | Grade calculation weights | courseId (String) |

#### 4. Student Submissions (1 table)
| Table | Description | Primary Key |
|-------|-------------|-------------|
| `student_assignment` | Student assignment submissions | id (Long, Auto) |

#### 5. Academic Records (7 tables)
| Table | Description | Primary Key |
|-------|-------------|-------------|
| `s_result` | Student grades and results | Composite (Student_id, Course_id) |
| `s_rank` | Student GPA rankings | Student_id (String) |
| `s_attend` | Attendance records | id (Long, Auto) |
| `combined_marks` | Aggregated marks from multiple sources | id (Long, Auto) |
| `assignment_mark_total` | Total assignment marks | id (Long, Auto) |
| `quiz_mark_total` | Total quiz marks | id (Long, Auto) |
| `exam_mark_total` | Total exam marks | id (Long, Auto) |

#### 6. Communication (2 tables)
| Table | Description | Primary Key |
|-------|-------------|-------------|
| `notification` | System notifications | id (Long, Auto) |
| `student_notification` | Student-specific notifications | id (Long, Auto) |

#### 7. Events (2 tables)
| Table | Description | Primary Key |
|-------|-------------|-------------|
| `event` | Student events | eventId (Long, Auto) |
| `event_admin` | Admin events | eventId (Long, Auto) |

#### 8. Join Tables (1 table)
| Table | Description | Columns |
|-------|-------------|---------|
| `student_course` | Many-to-Many: Student ↔ Course | Student_id (FK), CId (FK) |

#### 9. Helper Classes (2 entities)
| Entity | Type | Purpose |
|--------|------|---------|
| `Role` | Enum | User role enumeration (STUDENT) |
| `NotificationType` | Enum | Notification type (ASSIGNMENT, QUIZ) |
| `SResultId` | Composite ID Class | Composite key for S_result |

---

## Entity-Relationship Diagram

### Complete ER Diagram

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                        STUDENT ACADEMIC TRACKER DATABASE                         │
└─────────────────────────────────────────────────────────────────────────────────┘

┌──────────────────────────┐
│       DEPARTMENT         │
├──────────────────────────┤
│ PK: Department_id        │
│     Department_name      │
│     no_of_students       │
└──────────────────────────┘


┌──────────────────────────┐                    ┌──────────────────────────┐
│         ADMIN            │                    │      ADMIN_PROFILE       │
├──────────────────────────┤                    ├──────────────────────────┤
│ PK: Admin_id             │                    │ PK: adminID              │
│     Password             │                    │     name                 │
│     Admin_name           │                    │     email                │
│     Status               │                    │     contact              │
└──────────────────────────┘                    │     location             │
         │                                       └──────────────────────────┘
         │ Creates/Manages
         │
         ├────────────────────────────────────┐
         │                                    │
         ▼                                    ▼
┌──────────────────────────┐          ┌──────────────────────────┐
│      ASSIGNMENT          │          │      LECTURE_NOTE        │
├──────────────────────────┤          ├──────────────────────────┤
│ PK: id (Auto)            │          │ PK: id (Auto)            │
│     assignmentName       │          │     lectureNoteName      │
│     deadLine             │          │     publishDate          │
│     message              │          │     message              │
│     filePath             │          │     filePath             │
│ FK: adminId              │          │ FK: adminId              │
│ FK: courseId             │          │ FK: courseId             │
│     fileContent (BLOB)   │          │     fileContent (BLOB)   │
│     fileType             │          │     fileType             │
└──────────────────────────┘          └──────────────────────────┘
         │                                    ▲
         │ Has submissions                    │
         │                                    │
         ▼                                    │
┌──────────────────────────┐                 │
│  STUDENT_ASSIGNMENT      │                 │
├──────────────────────────┤                 │
│ PK: id (Auto)            │                 │
│     assignmentName       │                 │
│     filePath             │                 │
│ FK: studentId            │                 │
│ FK: courseId             │                 │
│ FK: assignmentId         │                 │
│     fileContent (BLOB)   │                 │
│     fileType             │                 │
└──────────────────────────┘                 │
         ▲                                    │
         │                                    │
         │                                    │
┌────────┴────────────────────────────────────┴────────┐
│                                                       │
│                      STUDENT                          │
├───────────────────────────────────────────────────────┤
│ PK: Student_id                                        │
│     Student_email                                     │
│     Student_name                                      │
│     Password                                          │
│     Student_Calendar                                  │
│     Student_Result                                    │
│     Student_Rank                                      │
│     role (Enum: STUDENT)                              │
│                                                       │
│ Implements: UserDetails (Spring Security)             │
└───────────────────────────────────────────────────────┘
         │                   │                  │
         │                   │                  │
         │ Many-to-Many      │ Has results      │ Receives
         │                   │                  │
         ▼                   ▼                  ▼
┌──────────────────┐  ┌──────────────────┐  ┌────────────────────────┐
│ STUDENT_COURSE   │  │    S_RESULT      │  │ STUDENT_NOTIFICATION   │
│  (Join Table)    │  ├──────────────────┤  ├────────────────────────┤
├──────────────────┤  │ PK: Student_id   │  │ PK: id (Auto)          │
│ FK: Student_id   │  │ PK: Course_id    │  │ FK: student_id         │
│ FK: CId          │  │     Grade        │  │ FK: assignment_id      │
└──────────────────┘  │     Value        │  │     isRead             │
         │            │                  │  │     course_id          │
         │            │ Composite Key:   │  │     message            │
         │            │ SResultId        │  └────────────────────────┘
         │            └──────────────────┘           │
         │                                           │
         │                                    References
         │                                           │
         ▼                                           ▼
┌───────────────────────────────────────────────────────┐
│                      COURSE                           │
├───────────────────────────────────────────────────────┤
│ PK: Course_id                                         │
│     Course_Name                                       │
│     Course_Description                                │
│     Course_offered_by                                 │
└───────────────────────────────────────────────────────┘
         │                   │                  │
         │                   │                  │
         │ Has weights       │ Has content      │ Tracked in
         │                   │                  │
         ▼                   ▼                  ▼
┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐
│ COURSE_WEIGHTS   │  │      QUIZ        │  │   S_ATTEND       │
├──────────────────┤  ├──────────────────┤  ├──────────────────┤
│ PK: courseId     │  │ PK: id (Auto)    │  │ PK: id (Auto)    │
│     examPercent  │  │ FK: Admin_id     │  │ FK: studentId    │
│     assignPercent│  │ FK: Course_id    │  │ FK: courseId     │
│     quizPercent  │  │     quizName     │  │     totalDays    │
└──────────────────┘  │     deadLine     │  │     presentDays  │
                      │     quizCode     │  │     absentDays   │
                      │     message      │  └──────────────────┘
                      └──────────────────┘


┌────────────────────────────────────────────────────────────────┐
│                    MARKS AGGREGATION TABLES                     │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────────────┐  ┌─────────────────────┐             │
│  │  COMBINED_MARKS     │  │ ASSIGNMENT_MARK_    │             │
│  ├─────────────────────┤  │       TOTAL         │             │
│  │ PK: id (Auto)       │  ├─────────────────────┤             │
│  │     source          │  │ PK: id (Auto)       │             │
│  │ FK: studentId       │  │ FK: studentId       │             │
│  │ FK: courseId        │  │ FK: courseId        │             │
│  │     percentage      │  │     percentage      │             │
│  └─────────────────────┘  └─────────────────────┘             │
│                                                                 │
│  ┌─────────────────────┐  ┌─────────────────────┐             │
│  │  QUIZ_MARK_TOTAL    │  │  EXAM_MARK_TOTAL    │             │
│  ├─────────────────────┤  ├─────────────────────┤             │
│  │ PK: id (Auto)       │  │ PK: id (Auto)       │             │
│  │ FK: studentId       │  │ FK: studentId       │             │
│  │ FK: courseId        │  │ FK: courseId        │             │
│  │     percentage      │  │     percentage      │             │
│  └─────────────────────┘  └─────────────────────┘             │
│                                                                 │
│  ┌─────────────────────┐                                       │
│  │      S_RANK         │                                       │
│  ├─────────────────────┤                                       │
│  │ PK: Student_id      │                                       │
│  │     Average_result  │                                       │
│  └─────────────────────┘                                       │
│                                                                 │
└────────────────────────────────────────────────────────────────┘


┌────────────────────────────────────────────────────────────────┐
│                    NOTIFICATION SYSTEM                          │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────────────────────────────────┐                  │
│  │          NOTIFICATION                   │                  │
│  ├─────────────────────────────────────────┤                  │
│  │ PK: id (Auto)                           │                  │
│  │     message                             │                  │
│  │     createdAt (LocalDateTime)           │                  │
│  │     isRead                              │                  │
│  │     type (Enum: ASSIGNMENT/QUIZ)        │                  │
│  │ FK: course_id                           │                  │
│  └─────────────────────────────────────────┘                  │
│                                                                 │
└────────────────────────────────────────────────────────────────┘


┌────────────────────────────────────────────────────────────────┐
│                      EVENT MANAGEMENT                           │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌───────────────────┐         ┌───────────────────┐          │
│  │      EVENT        │         │   EVENT_ADMIN     │          │
│  ├───────────────────┤         ├───────────────────┤          │
│  │ PK: eventId       │         │ PK: eventId       │          │
│  │ FK: studentId     │         │ FK: adminId       │          │
│  │     eventName     │         │     eventName     │          │
│  │     description   │         │     description   │          │
│  │     eventDate     │         │     eventDate     │          │
│  └───────────────────┘         └───────────────────┘          │
│                                                                 │
└────────────────────────────────────────────────────────────────┘


┌────────────────────────────────────────────────────────────────┐
│                    ADDITIONAL ENTITIES                          │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────────────┐         ┌─────────────────────┐      │
│  │      USER           │         │    INSTRUCTOR       │      │
│  ├─────────────────────┤         ├─────────────────────┤      │
│  │ PK: reg_no          │         │ PK: Instructor_id   │      │
│  │     name            │         │     Instructor_name │      │
│  │     email           │         │     Status          │      │
│  │     contact         │         │     lab             │      │
│  │     location        │         └─────────────────────┘      │
│  └─────────────────────┘                                       │
│                                                                 │
│  ┌─────────────────────┐                                       │
│  │    EXAMSHEET        │                                       │
│  ├─────────────────────┤                                       │
│  │ PK: id (Auto)       │                                       │
│  │     url             │                                       │
│  │     type            │                                       │
│  │ FK: courseId        │                                       │
│  │ FK: adminId         │                                       │
│  └─────────────────────┘                                       │
│                                                                 │
└────────────────────────────────────────────────────────────────┘
```

### Simplified Core Relationship Diagram

```
┌──────────┐         ┌──────────┐         ┌──────────────┐
│  ADMIN   │────────▶│  COURSE  │◀────────│   STUDENT    │
└──────────┘         └──────────┘         └──────────────┘
     │ Creates           │ Has                    │ Enrolls in
     │                   │                        │
     ▼                   ▼                        ▼
┌──────────────┐   ┌──────────────┐      ┌──────────────┐
│ ASSIGNMENT   │   │  QUIZ        │      │  S_RESULT    │
│ LECTURE_NOTE │   │  EXAMSHEET   │      │  S_ATTEND    │
│ COURSE_WEIGHT│   │              │      │  S_RANK      │
└──────────────┘   └──────────────┘      └──────────────┘
     │                                            │
     │ Submits                                    │ Aggregates
     ▼                                            ▼
┌──────────────────┐                    ┌──────────────────┐
│ STUDENT_         │                    │ COMBINED_MARKS   │
│ ASSIGNMENT       │                    │ *_MARK_TOTAL     │
└──────────────────┘                    └──────────────────┘
```

---

## Detailed Table Specifications

### 1. Student Table
**Entity:** `student`  
**Purpose:** Store student information and authentication credentials

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| Student_id | VARCHAR(255) | PRIMARY KEY | Student registration number (e.g., "E/20/055") |
| Student_email | VARCHAR(255) | NOT NULL | Student email address |
| Student_name | VARCHAR(255) | NOT NULL | Full name of student |
| Password | VARCHAR(255) | NOT NULL | BCrypt encrypted password |
| Student_Calendar | VARCHAR(255) | - | Google Calendar link |
| Student_Result | VARCHAR(255) | - | Result summary |
| Student_Rank | INT | DEFAULT 0 | Current rank/position |
| role | VARCHAR(50) | NOT NULL | Enum: 'STUDENT' |

**Relationships:**
- Many-to-Many with `course` via `student_course`
- One-to-Many with `student_assignment`
- One-to-Many with `student_notification`
- One-to-Many with `s_result`
- One-to-Many with `event`

**Implements:** Spring Security UserDetails interface

**Indexes:**
- PRIMARY KEY on `Student_id`
- INDEX on `Student_email` (for login lookup)

---

### 2. Course Table
**Entity:** `course`  
**Purpose:** Store course catalog information

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| Course_id | VARCHAR(255) | PRIMARY KEY | Unique course identifier (e.g., "CS101") |
| Course_Name | VARCHAR(255) | NOT NULL | Full course name |
| Course_Description | TEXT | - | Course description |
| Course_offered_by | VARCHAR(255) | - | Department or instructor |

**Relationships:**
- Many-to-Many with `student` via `student_course`
- One-to-Many with `assignment`
- One-to-Many with `quiz`
- One-to-Many with `lecture_note`
- One-to-Many with `s_result`
- One-to-One with `course_weights`

**Indexes:**
- PRIMARY KEY on `Course_id`

---

### 3. Student_Course Table (Join Table)
**Entity:** `student_course`  
**Purpose:** Many-to-Many relationship between students and courses

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| Student_id | VARCHAR(255) | FOREIGN KEY | References student.Student_id |
| CId | VARCHAR(255) | FOREIGN KEY | References course.Course_id |

**Constraints:**
- PRIMARY KEY (Student_id, CId)
- FOREIGN KEY Student_id REFERENCES student(Student_id)
- FOREIGN KEY CId REFERENCES course(Course_id)

---

### 4. Assignment Table
**Entity:** `assignment`  
**Purpose:** Store assignment definitions created by admins

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| id | BIGINT | PRIMARY KEY, AUTO_INCREMENT | Unique assignment ID |
| assignmentName | VARCHAR(255) | NOT NULL | Assignment title |
| deadLine | VARCHAR(255) | - | Submission deadline |
| message | TEXT | - | Assignment instructions |
| filePath | VARCHAR(500) | - | Path to assignment file |
| adminId | VARCHAR(255) | - | Admin who created it |
| courseId | VARCHAR(255) | - | Associated course |
| fileContent | BLOB | - | Binary file content |
| fileType | VARCHAR(100) | - | MIME type |

**Relationships:**
- Many-to-One with `admin` (via adminId)
- Many-to-One with `course` (via courseId)
- One-to-Many with `student_assignment`
- One-to-Many with `student_notification`

**Indexes:**
- PRIMARY KEY on `id`
- INDEX on `courseId`
- INDEX on `adminId`

---

### 5. Student_Assignment Table
**Entity:** `student_assignment`  
**Purpose:** Store student submissions for assignments

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| id | BIGINT | PRIMARY KEY, AUTO_INCREMENT | Unique submission ID |
| assignmentName | VARCHAR(255) | - | Assignment name reference |
| filePath | VARCHAR(500) | - | Path to submitted file |
| studentId | VARCHAR(255) | NOT NULL | Student who submitted |
| courseId | VARCHAR(255) | NOT NULL | Course assignment belongs to |
| assignmentId | BIGINT | - | Reference to assignment |
| fileContent | BLOB | - | Binary file content |
| fileType | VARCHAR(100) | - | MIME type |

**Relationships:**
- Many-to-One with `student` (via studentId)
- Many-to-One with `assignment` (via assignmentId)
- Many-to-One with `course` (via courseId)

**Indexes:**
- PRIMARY KEY on `id`
- INDEX on `studentId`
- INDEX on `assignmentId`
- UNIQUE INDEX on (studentId, assignmentId) to prevent duplicate submissions

---

### 6. Quiz Table
**Entity:** `quiz`  
**Purpose:** Store quiz definitions

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| id | BIGINT | PRIMARY KEY, AUTO_INCREMENT | Unique quiz ID |
| Admin_id | VARCHAR(255) | - | Admin who created the quiz |
| Course_id | VARCHAR(255) | NOT NULL | Associated course |
| quizName | VARCHAR(255) | NOT NULL | Quiz title |
| deadLine | VARCHAR(255) | - | Quiz deadline |
| quizCode | INT | - | Access code for quiz |
| message | TEXT | - | Quiz instructions |

**Relationships:**
- Many-to-One with `admin` (via Admin_id)
- Many-to-One with `course` (via Course_id)

**Indexes:**
- PRIMARY KEY on `id`
- INDEX on `Course_id`

---

### 7. S_Result Table
**Entity:** `s_result`  
**Purpose:** Store student grades for courses

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| Student_id | VARCHAR(255) | PRIMARY KEY | Student identifier |
| Course_id | VARCHAR(255) | PRIMARY KEY | Course identifier |
| Grade | VARCHAR(10) | - | Letter grade (A, B, C, etc.) |
| Value | DOUBLE | - | Numeric grade value |

**Composite Key:** Uses `SResultId` class
- Student_id + Course_id together form the primary key

**Relationships:**
- Many-to-One with `student` (via Student_id)
- Many-to-One with `course` (via Course_id)

**Indexes:**
- COMPOSITE PRIMARY KEY on (Student_id, Course_id)

---

### 8. S_Attend Table
**Entity:** `s_attend`  
**Purpose:** Track student attendance

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| id | BIGINT | PRIMARY KEY, AUTO_INCREMENT | Unique record ID |
| studentId | VARCHAR(255) | NOT NULL | Student identifier |
| courseId | VARCHAR(255) | NOT NULL | Course identifier |
| totalDays | INT | DEFAULT 0 | Total class days |
| presentDays | INT | DEFAULT 0 | Days present |
| absentDays | INT | DEFAULT 0 | Days absent |

**Relationships:**
- Many-to-One with `student` (via studentId)
- Many-to-One with `course` (via courseId)

**Indexes:**
- PRIMARY KEY on `id`
- UNIQUE INDEX on (studentId, courseId)

---

### 9. S_Rank Table
**Entity:** `s_rank`  
**Purpose:** Store student GPA rankings

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| Student_id | VARCHAR(255) | PRIMARY KEY | Student identifier |
| Average_result | DOUBLE | - | GPA or average grade |

**Relationships:**
- One-to-One with `student` (via Student_id)

**Indexes:**
- PRIMARY KEY on `Student_id`
- INDEX on `Average_result` (for ranking queries)

---

### 10. Combined_Marks Table
**Entity:** `combined_marks`  
**Purpose:** Aggregate marks from multiple sources (assignments, quizzes, exams)

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| id | BIGINT | PRIMARY KEY, AUTO_INCREMENT | Unique record ID |
| source | VARCHAR(100) | NOT NULL | Source of marks (assignment/quiz/exam) |
| studentId | VARCHAR(255) | NOT NULL | Student identifier |
| courseId | VARCHAR(255) | NOT NULL | Course identifier |
| percentage | DOUBLE | NOT NULL | Percentage score |

**Indexes:**
- PRIMARY KEY on `id`
- INDEX on (studentId, courseId)

---

### 11. Assignment_Mark_Total Table
**Entity:** `assignment_mark_total`  
**Purpose:** Total assignment marks for a student in a course

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| id | BIGINT | PRIMARY KEY, AUTO_INCREMENT | Unique record ID |
| studentId | VARCHAR(255) | NOT NULL | Student identifier |
| courseId | VARCHAR(255) | NOT NULL | Course identifier |
| percentage | DOUBLE | NOT NULL | Total assignment percentage |

**Indexes:**
- PRIMARY KEY on `id`
- UNIQUE INDEX on (studentId, courseId)

---

### 12. Quiz_Mark_Total Table
**Entity:** `quiz_mark_total`  
**Purpose:** Total quiz marks for a student in a course

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| id | BIGINT | PRIMARY KEY, AUTO_INCREMENT | Unique record ID |
| studentId | VARCHAR(255) | NOT NULL | Student identifier |
| courseId | VARCHAR(255) | NOT NULL | Course identifier |
| percentage | DOUBLE | NOT NULL | Total quiz percentage |

**Indexes:**
- PRIMARY KEY on `id`
- UNIQUE INDEX on (studentId, courseId)

---

### 13. Exam_Mark_Total Table
**Entity:** `exam_mark_total`  
**Purpose:** Total exam marks for a student in a course

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| id | BIGINT | PRIMARY KEY, AUTO_INCREMENT | Unique record ID |
| studentId | VARCHAR(255) | NOT NULL | Student identifier |
| courseId | VARCHAR(255) | NOT NULL | Course identifier |
| percentage | DOUBLE | NOT NULL | Total exam percentage |

**Indexes:**
- PRIMARY KEY on `id`
- UNIQUE INDEX on (studentId, courseId)

---

### 14. Course_Weights Table
**Entity:** `course_weights`  
**Purpose:** Define grading weight distribution for each course

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| courseId | VARCHAR(255) | PRIMARY KEY | Course identifier |
| examPercentage | DOUBLE | NOT NULL | Weight for exams (0-100) |
| assignmentPercentage | DOUBLE | NOT NULL | Weight for assignments (0-100) |
| quizPercentage | DOUBLE | NOT NULL | Weight for quizzes (0-100) |

**Business Rule:** examPercentage + assignmentPercentage + quizPercentage = 100

**Relationships:**
- One-to-One with `course` (via courseId)

**Indexes:**
- PRIMARY KEY on `courseId`

---

### 15. Lecture_Note Table
**Entity:** `lecture_note`  
**Purpose:** Store lecture materials and notes

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| id | BIGINT | PRIMARY KEY, AUTO_INCREMENT | Unique note ID |
| lectureNoteName | VARCHAR(255) | NOT NULL | Note title |
| publishDate | VARCHAR(255) | - | Publication date |
| message | TEXT | - | Additional information |
| filePath | VARCHAR(500) | - | Path to file |
| adminId | VARCHAR(255) | - | Admin who uploaded |
| courseId | VARCHAR(255) | NOT NULL | Associated course |
| fileContent | BLOB | - | Binary file content |
| fileType | VARCHAR(100) | - | MIME type |

**Relationships:**
- Many-to-One with `admin` (via adminId)
- Many-to-One with `course` (via courseId)

**Indexes:**
- PRIMARY KEY on `id`
- INDEX on `courseId`

---

### 16. Notification Table
**Entity:** `notification`  
**Purpose:** System-wide notifications

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| id | BIGINT | PRIMARY KEY, AUTO_INCREMENT | Unique notification ID |
| message | TEXT | NOT NULL | Notification message |
| createdAt | DATETIME | NOT NULL | Creation timestamp |
| isRead | BOOLEAN | DEFAULT FALSE | Read status |
| type | VARCHAR(50) | NOT NULL | Enum: ASSIGNMENT or QUIZ |
| course_id | VARCHAR(255) | - | Related course |

**Relationships:**
- Many-to-One with `course` (via course_id)

**Indexes:**
- PRIMARY KEY on `id`
- INDEX on `course_id`
- INDEX on `createdAt` (for chronological queries)

---

### 17. Student_Notification Table
**Entity:** `student_notification`  
**Purpose:** Student-specific notifications with relationships

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| id | BIGINT | PRIMARY KEY, AUTO_INCREMENT | Unique notification ID |
| student_id | VARCHAR(255) | FOREIGN KEY, NOT NULL | References student |
| assignment_id | BIGINT | FOREIGN KEY, NOT NULL | References assignment |
| isRead | BOOLEAN | DEFAULT FALSE | Read status |
| course_id | VARCHAR(255) | - | Related course |
| message | TEXT | - | Notification message |

**Relationships:**
- Many-to-One with `student` (via student_id)
- Many-to-One with `assignment` (via assignment_id)

**Constraints:**
- FOREIGN KEY student_id REFERENCES student(Student_id)
- FOREIGN KEY assignment_id REFERENCES assignment(id)

**Indexes:**
- PRIMARY KEY on `id`
- INDEX on `student_id`
- INDEX on `assignment_id`

---

### 18. Event Table
**Entity:** `event`  
**Purpose:** Student personal events and calendar items

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| eventId | BIGINT | PRIMARY KEY, AUTO_INCREMENT | Unique event ID |
| studentId | VARCHAR(255) | NOT NULL | Student who owns the event |
| eventName | VARCHAR(255) | NOT NULL | Event name |
| description | TEXT | - | Event description |
| eventDate | VARCHAR(255) | - | Event date/time |

**Relationships:**
- Many-to-One with `student` (via studentId)

**Indexes:**
- PRIMARY KEY on `eventId`
- INDEX on `studentId`

---

### 19. Event_Admin Table
**Entity:** `event_admin`  
**Purpose:** Admin-created events (school-wide announcements)

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| eventId | BIGINT | PRIMARY KEY, AUTO_INCREMENT | Unique event ID |
| adminId | VARCHAR(255) | NOT NULL | Admin who created the event |
| eventName | VARCHAR(255) | NOT NULL | Event name |
| description | TEXT | - | Event description |
| eventDate | VARCHAR(255) | - | Event date/time |

**Relationships:**
- Many-to-One with `admin` (via adminId)

**Indexes:**
- PRIMARY KEY on `eventId`
- INDEX on `adminId`

---

### 20. Admin Table
**Entity:** `admin`  
**Purpose:** Administrator authentication and basic info

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| Admin_id | VARCHAR(255) | PRIMARY KEY | Admin identifier |
| Password | VARCHAR(255) | NOT NULL | BCrypt encrypted password |
| Admin_name | VARCHAR(255) | NOT NULL | Full name |
| Status | VARCHAR(50) | - | Active/Inactive status |

**Relationships:**
- One-to-Many with `assignment`
- One-to-Many with `quiz`
- One-to-Many with `lecture_note`
- One-to-Many with `examsheet`
- One-to-Many with `event_admin`

**Indexes:**
- PRIMARY KEY on `Admin_id`

---

### 21. Admin_Profile Table
**Entity:** `admin_profile`  
**Purpose:** Extended admin profile information

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| adminID | VARCHAR(255) | PRIMARY KEY | Admin identifier |
| name | VARCHAR(255) | - | Full name |
| email | VARCHAR(255) | - | Email address |
| contact | VARCHAR(255) | - | Phone number |
| location | VARCHAR(255) | - | Office location |

**Relationships:**
- One-to-One with `admin` (via adminID)

**Indexes:**
- PRIMARY KEY on `adminID`

---

### 22. User Table
**Entity:** `user`  
**Purpose:** General user profiles (auxiliary user type)

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| reg_no | VARCHAR(255) | PRIMARY KEY | Registration number |
| name | VARCHAR(255) | - | Full name |
| email | VARCHAR(255) | - | Email address |
| contact | VARCHAR(255) | - | Phone number |
| location | VARCHAR(255) | - | Location |

**Indexes:**
- PRIMARY KEY on `reg_no`

---

### 23. Instructor Table
**Entity:** `instructor`  
**Purpose:** Instructor/Teacher information

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| Instructor_id | VARCHAR(255) | PRIMARY KEY | Instructor identifier |
| Instructor_name | VARCHAR(255) | NOT NULL | Full name |
| Status | VARCHAR(50) | - | Active/Inactive |
| lab | VARCHAR(255) | - | Lab/Room assignment |

**Indexes:**
- PRIMARY KEY on `Instructor_id`

---

### 24. Department Table
**Entity:** `department`  
**Purpose:** Department/Faculty information

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| Department_id | VARCHAR(255) | PRIMARY KEY | Department identifier |
| Department_name | VARCHAR(255) | NOT NULL | Department name |
| no_of_students | INT | DEFAULT 0 | Student count |

**Indexes:**
- PRIMARY KEY on `Department_id`

---

### 25. Examsheet Table
**Entity:** `examsheet`  
**Purpose:** Exam schedules and timetables

| Column Name | Data Type | Constraints | Description |
|------------|-----------|-------------|-------------|
| id | BIGINT | PRIMARY KEY, AUTO_INCREMENT | Unique exam sheet ID |
| url | VARCHAR(500) | - | External URL to exam sheet |
| type | VARCHAR(100) | - | Exam type (midterm, final, etc.) |
| courseId | VARCHAR(255) | NOT NULL | Associated course |
| adminId | VARCHAR(255) | - | Admin who created it |

**Relationships:**
- Many-to-One with `course` (via courseId)
- Many-to-One with `admin` (via adminId)

**Indexes:**
- PRIMARY KEY on `id`
- INDEX on `courseId`

---

### 26. Role (Enum)
**Entity:** `Role`  
**Type:** Java Enum  
**Purpose:** Define user roles in the system

**Values:**
- `STUDENT`

**Usage:** Used in `student` entity for role-based access control

---

### 27. NotificationType (Enum)
**Entity:** `NotificationType`  
**Type:** Java Enum  
**Purpose:** Define types of notifications

**Values:**
- `ASSIGNMENT`
- `QUIZ`

**Usage:** Used in `notification` entity to categorize notifications

---

## Relationships and Constraints

### Primary Relationships

#### 1. Student ↔ Course (Many-to-Many)
- **Join Table:** `student_course`
- **Student Side:** 
  - `@ManyToMany` with `@JoinTable`
  - Join columns: Student_id, CId
- **Course Side:** 
  - `@ManyToMany(mappedBy = "courses")`
- **Cascade:** Not specified (manual enrollment management)
- **Fetch:** Default (LAZY)

**Business Logic:**
- A student can enroll in multiple courses
- A course can have multiple students
- Enrollment is managed through the join table

#### 2. Student → StudentNotification (One-to-Many)
- **Foreign Key:** `student_notification.student_id` → `student.Student_id`
- **Mapping:** `@ManyToOne` on StudentNotification side
- **Cascade:** Not specified
- **Fetch:** Default (EAGER for @ManyToOne)
- **Nullable:** NOT NULL

**Business Logic:**
- A student can receive multiple notifications
- Each notification belongs to exactly one student

#### 3. Assignment → StudentNotification (One-to-Many)
- **Foreign Key:** `student_notification.assignment_id` → `assignment.id`
- **Mapping:** `@ManyToOne` on StudentNotification side
- **Cascade:** Not specified
- **Fetch:** Default (EAGER)
- **Nullable:** NOT NULL

**Business Logic:**
- An assignment can trigger multiple notifications (one per enrolled student)
- Each notification relates to exactly one assignment

#### 4. Student → StudentAssignment (One-to-Many)
- **Implied Foreign Key:** `student_assignment.studentId` → `student.Student_id`
- **No explicit JPA relationship defined**
- **Managed programmatically**

**Business Logic:**
- A student can submit multiple assignments
- Each submission belongs to exactly one student

#### 5. Assignment → StudentAssignment (One-to-Many)
- **Implied Foreign Key:** `student_assignment.assignmentId` → `assignment.id`
- **No explicit JPA relationship defined**
- **Managed programmatically**

**Business Logic:**
- An assignment can have multiple submissions (one per student)
- Each submission is for exactly one assignment

#### 6. Course → Assignment (One-to-Many)
- **Implied Foreign Key:** `assignment.courseId` → `course.Course_id`
- **No explicit JPA relationship defined**

**Business Logic:**
- A course can have multiple assignments
- Each assignment belongs to exactly one course

#### 7. Course → Quiz (One-to-Many)
- **Implied Foreign Key:** `quiz.Course_id` → `course.Course_id`
- **No explicit JPA relationship defined**

**Business Logic:**
- A course can have multiple quizzes
- Each quiz belongs to exactly one course

#### 8. Course → LectureNote (One-to-Many)
- **Implied Foreign Key:** `lecture_note.courseId` → `course.Course_id`
- **No explicit JPA relationship defined**

**Business Logic:**
- A course can have multiple lecture notes
- Each lecture note belongs to exactly one course

#### 9. Student + Course → S_Result (Composite Key)
- **Composite Primary Key:** (Student_id, Course_id)
- **ID Class:** `SResultId`
- **No explicit foreign key relationships defined**

**Business Logic:**
- Each student-course pair has exactly one result record
- Used for final grades

#### 10. Course → CourseWeight (One-to-One)
- **Foreign Key:** `course_weights.courseId` → `course.Course_id`
- **No explicit JPA relationship defined**

**Business Logic:**
- Each course has exactly one weight configuration
- Defines how final grades are calculated

### Foreign Key Constraints

Most relationships in this schema are **implicitly managed** rather than explicitly declared with JPA annotations. This provides flexibility but requires careful programmatic constraint enforcement:

**Explicitly Declared:**
1. `student_notification.student_id` → `student.Student_id`
2. `student_notification.assignment_id` → `assignment.id`
3. `student_course.Student_id` → `student.Student_id`
4. `student_course.CId` → `course.Course_id`

**Implicitly Managed (programmatically enforced):**
1. All `courseId` references → `course.Course_id`
2. All `studentId` references → `student.Student_id`
3. All `adminId` references → `admin.Admin_id`
4. `assignmentId` references → `assignment.id`

### Cascade Operations

**Current Implementation:**
- **No CASCADE DELETE** - Prevents accidental data loss
- **No CASCADE UPDATE** - Manual updates required
- **Manual orphan removal** - Service layer handles cleanup

**Recommended for Production:**
```sql
-- Example: When a student is deleted, handle related records
ON DELETE CASCADE for student_course (allow unenrollment)
ON DELETE SET NULL for student_assignment (keep submissions for records)
ON DELETE CASCADE for student_notification (remove notifications)
```

### Referential Integrity

**Maintained By:**
1. **Database Level:** Foreign key constraints on join tables
2. **JPA Level:** `@JoinColumn` annotations where specified
3. **Application Level:** Service layer validation

**Integrity Rules:**
- Cannot delete a course with enrolled students (unless join table entries removed first)
- Cannot delete a student with submitted assignments (orphan prevention)
- Cannot create assignment without valid course and admin IDs

---

## Indexes and Performance

### Existing Indexes (Auto-generated by JPA)

#### Primary Key Indexes
All tables have primary key indexes automatically created:
- `student(Student_id)`
- `course(Course_id)`
- `assignment(id)`
- `student_assignment(id)`
- `quiz(id)`
- `lecture_note(id)`
- `notification(id)`
- `student_notification(id)`
- `s_result(Student_id, Course_id)` - Composite
- `s_attend(id)`
- `s_rank(Student_id)`
- `admin(Admin_id)`
- `event(eventId)`
- `event_admin(eventId)`
- `department(Department_id)`
- `instructor(Instructor_id)`
- `user(reg_no)`
- `admin_profile(adminID)`
- `examsheet(id)`
- `course_weights(courseId)`
- `combined_marks(id)`
- `assignment_mark_total(id)`
- `quiz_mark_total(id)`
- `exam_mark_total(id)`

#### Foreign Key Indexes
Automatically created for `@JoinColumn` annotations:
- `student_course(Student_id)`
- `student_course(CId)`
- `student_notification(student_id)`
- `student_notification(assignment_id)`

### Recommended Additional Indexes

For optimal query performance, the following indexes should be added:

```sql
-- Student login optimization
CREATE INDEX idx_student_email ON student(Student_email);

-- Course lookups
CREATE INDEX idx_assignment_course ON assignment(courseId);
CREATE INDEX idx_quiz_course ON quiz(Course_id);
CREATE INDEX idx_lecture_note_course ON lecture_note(courseId);
CREATE INDEX idx_examsheet_course ON examsheet(courseId);

-- Student assignment lookups
CREATE INDEX idx_student_assignment_student ON student_assignment(studentId);
CREATE INDEX idx_student_assignment_assignment ON student_assignment(assignmentId);
CREATE UNIQUE INDEX idx_student_assignment_unique ON student_assignment(studentId, assignmentId);

-- Attendance tracking
CREATE UNIQUE INDEX idx_attendance_student_course ON s_attend(studentId, courseId);

-- Grade aggregation queries
CREATE INDEX idx_combined_marks_student ON combined_marks(studentId, courseId);
CREATE UNIQUE INDEX idx_assignment_total_unique ON assignment_mark_total(studentId, courseId);
CREATE UNIQUE INDEX idx_quiz_total_unique ON quiz_mark_total(studentId, courseId);
CREATE UNIQUE INDEX idx_exam_total_unique ON exam_mark_total(studentId, courseId);

-- Ranking queries
CREATE INDEX idx_rank_average ON s_rank(Average_result DESC);

-- Notification queries
CREATE INDEX idx_notification_course ON notification(course_id);
CREATE INDEX idx_notification_created ON notification(createdAt DESC);
CREATE INDEX idx_notification_unread ON notification(isRead, createdAt DESC);

-- Event queries
CREATE INDEX idx_event_student ON event(studentId);
CREATE INDEX idx_event_admin ON event_admin(adminId);
```

### Query Optimization Strategies

#### 1. N+1 Query Prevention
**Problem:** Loading students with their courses causes N+1 queries

**Solution:** Use fetch joins in queries
```java
@Query("SELECT DISTINCT s FROM Student s LEFT JOIN FETCH s.courses WHERE s.Student_id = :id")
Optional<Student> findByIdWithCourses(@Param("id") String id);
```

#### 2. Pagination for Large Result Sets
**Implementation:**
```java
Page<Student> findAll(Pageable pageable);
```

**Usage:**
```java
Pageable pageable = PageRequest.of(0, 20, Sort.by("Student_name"));
Page<Student> students = studentRepository.findAll(pageable);
```

#### 3. Projection for Selective Column Retrieval
**Instead of loading entire entities:**
```java
@Query("SELECT s.Student_id, s.Student_name, s.Student_email FROM Student s")
List<Object[]> findStudentBasicInfo();
```

#### 4. Caching Strategy
**Recommended cache configuration:**
```java
@Cacheable("courses")
public List<Course> findAll() { ... }

@CacheEvict(value = "courses", allEntries = true)
public Course save(Course course) { ... }
```

---

## Data Integrity Rules

### Business Rules Enforced by Database

#### 1. Grade Calculation Weights
**Rule:** Course weights must sum to 100%
```
examPercentage + assignmentPercentage + quizPercentage = 100
```

**Enforcement:** Application-level validation before saving `course_weights`

#### 2. Attendance Calculation
**Rule:** Total days = Present days + Absent days
```
totalDays = presentDays + absentDays
```

**Enforcement:** Application-level calculation in service layer

#### 3. Unique Enrollment
**Rule:** A student cannot enroll in the same course twice
**Enforcement:** Composite primary key on `student_course(Student_id, CId)`

#### 4. Unique Submission
**Rule:** A student can submit each assignment only once
**Enforcement:** Recommended unique index on `student_assignment(studentId, assignmentId)`

#### 5. Grade Value Ranges
**Rule:** Grades should be between 0 and 100
**Enforcement:** Application-level validation

#### 6. Role-Based Access
**Rule:** Only students with role='STUDENT' can access student features
**Enforcement:** Spring Security with Role enum

### Data Validation Rules

#### Email Format
```java
@Email
@Column(unique = true)
private String Student_email;
```

#### Password Strength
- Minimum length: 8 characters
- Must contain: uppercase, lowercase, digit
- Stored as BCrypt hash

#### Date Formats
- Deadlines: ISO 8601 format (YYYY-MM-DD)
- Timestamps: LocalDateTime for `createdAt`

#### File Upload Constraints
```properties
spring.servlet.multipart.max-file-size=50MB
spring.servlet.multipart.max-request-size=50MB
```

**Allowed file types:**
- Documents: PDF, DOC, DOCX
- Spreadsheets: XLS, XLSX
- Presentations: PPT, PPTX
- Images: JPG, PNG, GIF
- Archives: ZIP, RAR

---

## Database Conventions

### Naming Conventions

#### Table Names
- Entity class name in **snake_case** (auto-generated)
- Example: `Student` → `student`, `CourseWeight` → `course_weight`
- Explicit table names: `@Table(name = "course_weights")`

#### Column Names
- Java field name preserved or mapped explicitly
- Primary keys: Entity name + "_id" (e.g., `Student_id`, `Course_id`)
- Foreign keys: Referenced entity + "Id" (e.g., `courseId`, `studentId`)
- Boolean flags: "is" prefix (e.g., `isRead`)
- Timestamps: Past tense (e.g., `createdAt`)

#### Join Tables
- Format: `{entity1}_{entity2}` (e.g., `student_course`)
- Alphabetical order preferred

### Data Type Conventions

| Java Type | MySQL Type | Usage |
|-----------|------------|-------|
| String | VARCHAR(255) | Short text (IDs, names) |
| String | TEXT | Long text (descriptions, messages) |
| Long | BIGINT | Auto-increment IDs |
| Integer | INT | Counts, ranks |
| Double | DOUBLE | Percentages, grades |
| Boolean | TINYINT(1) | Flags (isRead, Status) |
| LocalDateTime | DATETIME | Timestamps |
| byte[] | BLOB | File content |
| Enum | VARCHAR(50) | Role, NotificationType |

### ID Generation Strategies

#### String-based IDs (Manual)
```java
@Id
private String Student_id; // e.g., "E/20/055"
```
**Used for:** Students, Courses, Admins, Departments
**Reason:** Business-meaningful identifiers

#### Auto-increment IDs
```java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```
**Used for:** Assignments, Quizzes, Submissions, Events, Notifications
**Reason:** No business meaning required

#### Composite Keys
```java
@Id
@IdClass(SResultId.class)
public class S_result {
    @Id
    private String Student_id;
    @Id
    private String Course_id;
}
```
**Used for:** S_result (unique per student-course pair)

### Timestamp Management

```java
@Column(nullable = false, updatable = false)
private LocalDateTime createdAt = LocalDateTime.now();

@Column(nullable = false)
private LocalDateTime updatedAt = LocalDateTime.now();
```

**Recommendation:** Add `createdAt` and `updatedAt` to all tables for audit trail

### Soft Delete Pattern

**Not currently implemented**, but recommended:
```java
@Column(nullable = false)
private Boolean deleted = false;

@Column
private LocalDateTime deletedAt;
```

**Benefits:**
- Maintain referential integrity
- Allow data recovery
- Audit trail preservation

---

## Database Initialization

### Auto DDL (Development)
```properties
spring.jpa.hibernate.ddl-auto=update
```
**Options:**
- `create` - Drop and recreate tables (data loss!)
- `create-drop` - Create on startup, drop on shutdown
- `update` - Update schema without data loss (recommended for dev)
- `validate` - Validate schema without changes (recommended for prod)
- `none` - No schema management

### Production Schema Management

**Recommended approach:** Use migration tools like Flyway or Liquibase

**Flyway Example:**
```sql
-- V1__Initial_schema.sql
CREATE TABLE student (
    Student_id VARCHAR(255) PRIMARY KEY,
    Student_email VARCHAR(255) NOT NULL UNIQUE,
    Student_name VARCHAR(255) NOT NULL,
    Password VARCHAR(255) NOT NULL,
    Student_Calendar VARCHAR(255),
    Student_Result VARCHAR(255),
    Student_Rank INT DEFAULT 0,
    role VARCHAR(50) NOT NULL
);

-- V2__Add_created_at.sql
ALTER TABLE student ADD COLUMN created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP;
```

---

## Performance Benchmarks

### Expected Query Performance

| Operation | Expected Time | Optimization |
|-----------|--------------|--------------|
| Student login | < 100ms | Index on email |
| Load student courses | < 200ms | Fetch join |
| Load assignment list | < 150ms | Index on courseId |
| Submit assignment | < 300ms | Transaction isolation |
| Calculate grades | < 500ms | Cached weights |
| Load notifications | < 100ms | Index on createdAt |
| Rank students | < 1s | Index on Average_result |

### Connection Pool Configuration

```properties
spring.datasource.hikari.maximum-pool-size=10
spring.datasource.hikari.minimum-idle=5
spring.datasource.hikari.connection-timeout=30000
spring.datasource.hikari.idle-timeout=600000
spring.datasource.hikari.max-lifetime=1800000
```

---

## Security Considerations

### Password Storage
- **Algorithm:** BCrypt with strength 10
- **Salt:** Automatically generated per password
- **Never stored in plain text**

### SQL Injection Prevention
- **Method:** JPA parameterized queries
- **All queries use:** Prepared statements
- **No string concatenation** in queries

### Data Access Security
- **Row-level security:** Enforced by service layer
- **Students:** Can only access their own data
- **Admins:** Can access all student data
- **Query filtering:** By Student_id in WHERE clauses

### Sensitive Data
**Never include in logs:**
- Passwords
- File content (BLOB)
- Personal contact information

---

## Backup and Recovery

### Recommended Backup Strategy

#### Daily Backups
```bash
mysqldump -u root -p student_tracker > backup_$(date +%Y%m%d).sql
```

#### Critical Tables (Hourly)
- `student`
- `s_result`
- `student_assignment`

#### Recovery Procedure
```bash
mysql -u root -p student_tracker < backup_20260119.sql
```

### Data Retention Policy
- **Student records:** Keep indefinitely
- **Assignments:** Keep for 5 years after course end
- **Notifications:** Archive after 1 year
- **Events:** Archive after completion

---

## Future Enhancements

### Recommended Improvements

1. **Add Audit Tables**
   - Track all data modifications
   - Store user, timestamp, operation type

2. **Implement Soft Deletes**
   - Add `deleted` and `deletedAt` columns
   - Filter out deleted records in queries

3. **Add Full-Text Search**
   - Index course descriptions
   - Index lecture note content
   - Enable fuzzy search

4. **Optimize File Storage**
   - Move BLOB storage to cloud (S3, Google Cloud Storage)
   - Store only file paths in database
   - Reduce database size

5. **Add Database Triggers**
   - Auto-update `updatedAt` on modifications
   - Cascade grade calculations
   - Maintain denormalized counts

6. **Implement Database Partitioning**
   - Partition `s_result` by year
   - Partition `notification` by date
   - Improve query performance

7. **Add Materialized Views**
   - Student rankings
   - Course statistics
   - Performance dashboards

---

## Appendix

### Sample Data

#### Student Record
```json
{
  "Student_id": "E/20/055",
  "Student_email": "e20055@eng.pdn.ac.lk",
  "Student_name": "De Silva H.D.S.",
  "Password": "$2a$10$...", // BCrypt hash
  "Student_Calendar": "https://calendar.google.com/...",
  "Student_Result": "3.75 GPA",
  "Student_Rank": 15,
  "role": "STUDENT"
}
```

#### Course Record
```json
{
  "Course_id": "CS101",
  "Course_Name": "Introduction to Computer Science",
  "Course_Description": "Fundamental concepts of programming...",
  "Course_offered_by": "Department of Computer Engineering"
}
```

#### Course Weight Configuration
```json
{
  "courseId": "CS101",
  "examPercentage": 50.0,
  "assignmentPercentage": 30.0,
  "quizPercentage": 20.0
}
```

### Database Size Estimates

| Table | Rows (Estimated) | Size (MB) |
|-------|-----------------|-----------|
| student | 1,000 | 2 |
| course | 100 | 0.5 |
| student_course | 5,000 | 1 |
| assignment | 500 | 100 (with files) |
| student_assignment | 10,000 | 500 (with files) |
| quiz | 300 | 0.5 |
| lecture_note | 1,000 | 200 (with files) |
| s_result | 5,000 | 1 |
| notification | 50,000 | 10 |
| **Total** | | **~815 MB** |

### ER Diagram Legend

```
Symbols Used:
━━━━━  One-to-One relationship
────▶  One-to-Many relationship
◀────▶  Many-to-Many relationship
PK:    Primary Key
FK:    Foreign Key
```

---

**Document Maintained By:** Development Team  
**Review Frequency:** Quarterly  
**Next Review:** April 2026
