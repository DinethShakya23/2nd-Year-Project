# Student Academic Tracker - Comprehensive Project Overview

## Table of Contents
1. [Project Summary](#project-summary)
2. [Team Members](#team-members)
3. [Technology Stack](#technology-stack)
4. [Architecture Overview](#architecture-overview)
5. [Key Features](#key-features)
6. [Database Schema](#database-schema)
7. [Backend Structure](#backend-structure)
8. [Frontend Structure](#frontend-structure)
9. [API Endpoints](#api-endpoints)
10. [Security Implementation](#security-implementation)
11. [Setup and Installation](#setup-and-installation)
12. [Directory Structure](#directory-structure)
13. [External Integrations](#external-integrations)

---

## Project Summary

The **Student Academic Tracker** is a comprehensive web-based Learning Management System (LMS) designed to track and analyze student academic performance while providing essential LMS functionalities. The system aims to mitigate academic setbacks by monitoring grades, attendance, assignments, and course activities, ensuring students stay on track toward academic success.

### Project Purpose
- Track and analyze students' academic progress by monitoring grades, attendance, assignments, and course activities
- Provide LMS features such as course management, assignment submission, notifications, and student collaboration
- Offer real-time insights into student performance to help educators identify struggling students and provide timely support
- Implement a GPA-based ranking system to encourage healthy competition and motivate students

---

## Team Members

| Name | Email | Student ID |
|------|-------|-----------|
| Kavindu Gajendra (Amarakeerthi H.K.K.G.) | e20016@eng.pdn.ac.lk | E/20/016 |
| Dineth Shakya (De Silva H.D.S.) | e20055@eng.pdn.ac.lk | E/20/055 |
| Janith Madhura (Madhura T.W.K.J.) | e20231@eng.pdn.ac.lk | E/20/231 |
| Nethmin Tharusha (Ukwaththa U.A.N.T.) | e20404@eng.pdn.ac.lk | E/20/404 |

**University:** University of Peradeniya  
**Department:** Computer Engineering  
**Course:** CO227 - 2nd Year Project

---

## Technology Stack

### Backend
- **Framework:** Spring Boot 3.2.5
- **Language:** Java 21
- **Build Tool:** Maven
- **Database:** MySQL
- **ORM:** Spring Data JPA (Hibernate)
- **Security:** Spring Security with JWT (JSON Web Tokens)
- **Authentication:** Token-based authentication using JJWT (v0.11.5)

### Frontend
- **Technologies:** HTML5, CSS3, JavaScript (Vanilla JS)
- **UI Framework:** Bootstrap 4.5.2
- **Icons:** Font Awesome, Boxicons
- **Server:** JSP-based web application
- **Build Tool:** Maven (WAR packaging)

### Additional Libraries & APIs
- **Lombok:** For reducing boilerplate code
- **ModelMapper:** For DTO-Entity mapping
- **Google APIs:** 
  - Google Sheets API (v4)
  - Google Drive API (v3)
  - OAuth Client for authentication
- **Email Service:** Spring Boot Starter Mail (Gmail SMTP)
- **Testing:** JUnit, Spring Security Test

---

## Architecture Overview

The project follows a **3-tier architecture** with clear separation of concerns:

```
┌─────────────────────────────────────────────────┐
│              Frontend Layer                      │
│  (HTML/CSS/JavaScript - User Interface)         │
└────────────────┬────────────────────────────────┘
                 │ HTTP/REST API
┌────────────────▼────────────────────────────────┐
│              Backend Layer                       │
│         (Spring Boot REST API)                   │
│                                                   │
│  ┌──────────────────────────────────────────┐  │
│  │         Controller Layer                  │  │
│  │    (REST API Endpoints)                   │  │
│  └──────────────┬───────────────────────────┘  │
│                 │                                │
│  ┌──────────────▼───────────────────────────┐  │
│  │         Service Layer                     │  │
│  │    (Business Logic)                       │  │
│  └──────────────┬───────────────────────────┘  │
│                 │                                │
│  ┌──────────────▼───────────────────────────┐  │
│  │       Repository Layer                    │  │
│  │    (Data Access - Spring Data JPA)       │  │
│  └──────────────┬───────────────────────────┘  │
└─────────────────┼────────────────────────────────┘
                  │
┌─────────────────▼────────────────────────────────┐
│            Database Layer                         │
│              (MySQL)                              │
└───────────────────────────────────────────────────┘
```

### Design Patterns Used
- **MVC Pattern:** Model-View-Controller architecture
- **DTO Pattern:** Data Transfer Objects for clean API contracts
- **Repository Pattern:** Spring Data JPA repositories for data access
- **Dependency Injection:** Spring's IoC container
- **Builder Pattern:** Used with Lombok for entity creation

---

## Key Features

### 1. **Student Performance Tracking**
- Detailed insights into academic performance
- Grade tracking and GPA calculations
- Performance analytics and reporting
- Historical data analysis

### 2. **Course Management**
- Create and manage courses
- Upload course materials and lecture notes
- Track course enrollments
- Monitor course-wise student performance
- Course weight configuration for grade calculations

### 3. **Assignment Management**
- Create and publish assignments
- Digital assignment submission by students
- Automated tracking and grading
- Assignment mark sheets and totals
- Deadline management and notifications

### 4. **Quiz Management**
- Create and manage quizzes
- Quiz submission and grading
- Quiz mark tracking and reporting
- Integration with overall grade calculation

### 5. **Attendance Tracking**
- Mark student attendance for each course
- View attendance reports by student and course
- Attendance percentage calculations
- Enable active participation monitoring

### 6. **Real-time Notifications**
- Alert students about new assignments
- Course material upload notifications
- Deadline reminders
- Grade posting notifications
- System-wide announcements

### 7. **Event Management**
- Integrated event calendar
- Schedule academic events and deadlines
- Meeting coordination
- Both student and admin event views

### 8. **GPA-based Ranking System**
- Transparent student performance ranking
- Automatic GPA calculations
- Combined marks (assignments, quizzes, exams)
- Encourages healthy academic competition

### 9. **Security and Authentication**
- Token-based JWT authentication
- Role-based access control (Student, Admin/Lecturer)
- Secure password storage
- Protected API endpoints

### 10. **Google Sheets Integration**
- Export grades and marks to Google Sheets
- Automated grade sheet generation
- Cloud-based data backup

### 11. **Email Notifications**
- Contact form with email integration
- Automated notification emails
- Gmail SMTP integration

### 12. **Document Management**
- Upload and manage lecture notes
- File storage and retrieval
- Support for multiple file formats
- Secure file access control

---

## Database Schema

The system uses **MySQL** with the following main entities:

### Core Entities (24 entities total)

#### 1. **Student**
- `Student_id` (PK)
- `Student_email`
- `Student_name`
- `Password`
- `Student_Calendar`
- `Student_Result`
- `Student_Rank`
- `role` (enum)
- Many-to-Many relationship with Course

#### 2. **Course**
- `Course_id` (PK)
- `Course_Name`
- `Course_Description`
- `Course_offered_by`
- Many-to-Many relationship with Student

#### 3. **Admin**
- Admin credentials and profile information
- Role-based permissions

#### 4. **AdminProfile**
- Extended admin information
- Profile settings

#### 5. **Assignment**
- Assignment details
- Deadlines and submission requirements
- Associated with courses

#### 6. **StudentAssignment**
- Student-specific assignment submissions
- Grades and feedback

#### 7. **Quiz**
- Quiz definitions
- Course-specific quizzes

#### 8. **Examsheet**
- Exam information and schedules

#### 9. **S_result (Student Results)**
- Student grade records
- Composite key structure

#### 10. **S_attend (Student Attendance)**
- Attendance records by student and course

#### 11. **S_rank (Student Ranking)**
- GPA-based ranking information

#### 12. **CombinedMarks**
- Aggregated marks from all sources
- Used for GPA calculations

#### 13. **AssignmentMarkTotal**
- Total assignment marks per student/course

#### 14. **QuizMarkTotal**
- Total quiz marks per student/course

#### 15. **ExamMarkTotal**
- Total exam marks per student/course

#### 16. **CourseWeight**
- Configuration for grade calculation weights
- Assignment, quiz, and exam percentages

#### 17. **Event**
- Student calendar events

#### 18. **EventAdmin**
- Admin/lecturer calendar events

#### 19. **Notification**
- System-wide notifications

#### 20. **StudentNotification**
- Student-specific notifications
- Read/unread status

#### 21. **LectureNote**
- Course lecture materials
- File references

#### 22. **Department**
- Department information

#### 23. **Instructor**
- Instructor/lecturer details

#### 24. **User**
- General user authentication
- User credentials

### Key Relationships
- **Student ↔ Course:** Many-to-Many (enrollment)
- **Course → Assignment:** One-to-Many
- **Course → Quiz:** One-to-Many
- **Student → StudentAssignment:** One-to-Many
- **Student → Attendance:** One-to-Many
- **Student → Results:** One-to-Many

---

## Backend Structure

### Package Organization
```
com.project.co/
├── CoApplication.java          # Main Spring Boot application
├── config/                     # Configuration classes
│   ├── ApplicationConfigStudent.java
│   ├── CorsConfig.java
│   ├── JwtAuthenticationFilter.java
│   ├── JwtService.java
│   ├── SecurityConfigStudent.java
│   └── Webconfig.java
├── controller/                 # REST API Controllers
│   └── controller.java         # Main controller (71 endpoints)
├── dto/                        # Data Transfer Objects (30+ DTOs)
│   ├── StudentDto.java
│   ├── CourseDto.java
│   ├── AssignmentDto.java
│   ├── NotificationDTO.java
│   └── ... (27+ more DTOs)
├── entity/                     # JPA Entities (24 entities)
│   ├── Student.java
│   ├── Course.java
│   ├── Assignment.java
│   ├── Admin.java
│   └── ... (20+ more entities)
├── repo/                       # Spring Data JPA Repositories
│   ├── Studentrepo.java
│   ├── Courserepo.java
│   ├── Assignmentrepo.java
│   └── ... (20+ repositories)
├── service/                    # Business Logic Layer
│   ├── Studentservice.java
│   ├── Courseservice.java
│   ├── Assignmentservice.java
│   ├── UserService.java
│   ├── GoogleAPIService.java
│   ├── EmailSenderService.java
│   └── ... (20+ services)
└── quickstart/                 # Google API integration utilities
```

### Configuration Details

**Application Properties:**
- Server runs on port **8090**
- MySQL database: `project` (auto-created)
- Hibernate DDL: update mode
- File upload limits: max 50MB
- Gmail SMTP configured for email notifications

---

## Frontend Structure

### Page Categories

#### **Public Pages**
- `index.html` - Landing page with image slider
- `About.html` - About the system
- `Contact.html` - Contact form
- `Library.html` - Library/resources section

#### **Authentication Pages**
- `Stdlogin.html` - Student login
- `Adlogin.html` - Admin/lecturer login
- `Signup.html` - User registration

#### **Student Dashboard Pages**
- `home.html` - Student home dashboard
- `profile.html` - Student profile management
- `my_courses.html` - Enrolled courses view
- `course_reg.html` - Course registration
- `calendar.html` - Student calendar/events
- `Ranking.html` - GPA rankings view

#### **Student Assignment & Quiz Pages**
- `assignments.html` - View assignments
- `assignmentDetail.html` - Assignment details and submission
- `saveStdAssignment.html` - Submit assignments
- `AssignmentMarksStudent.html` - View assignment marks
- `quizzes.html` - View quizzes
- `QuizMarksStudent.html` - View quiz marks
- `ExamMarksStudent.html` - View exam marks
- `Grades.html` - Overall grades view

#### **Student Learning Materials**
- `lecture_notes.html` - View lecture notes
- `catagories.html` - Course categories
- `catagoriesstudent.html` - Student course categories

#### **Student Attendance**
- `student_attendance.html` - View attendance records

#### **Admin/Lecturer Dashboard Pages**
- `homelec.html` - Lecturer home dashboard
- `AdminProfile.html` - Admin profile management
- `AdminProfileView.html` - View admin profile
- `Admin_profile.html` - Edit admin profile

#### **Admin Course Management**
- `Courselist.html` - List all courses
- `manageCourses.html` - Create/edit courses
- `selectCourse.html` - Select course for operations

#### **Admin Assignment Management**
- `manageAssigment.html` / `manageAssignment.html` - Manage assignments
- `AssignmentCreateSheet.html` - Create new assignments
- `AssignmentMarks.html` - Enter assignment marks

#### **Admin Quiz Management**
- `manageQuiz.html` - Manage quizzes
- `QuizMarkSheet.html` - Enter quiz marks
- `QuizesMarks.html` - View quiz marks

#### **Admin Exam Management**
- `ExamMarkSheet.html` - Enter exam marks
- `ExamGrades.html` - View exam grades

#### **Admin Attendance**
- `Attendance.html` - Mark attendance
- `Attendancelist.html` - View attendance lists

#### **Admin Lecture Notes**
- `manageLectureNote.html` - Upload/manage lecture notes

#### **Admin Calendar**
- `calnder_admin.html` - Admin calendar and events

#### **Utility Pages**
- `calculator.html` - GPA calculator
- `view.html` - General view page
- `More.html` - Additional features
- `test.html` - Testing page

### JavaScript Files (37 files)
Key frontend functionality includes:
- `Session.js` / `Session_admin.js` - Session management
- `script_login.js` / `script_login_lec.js` - Login handlers
- `script_Signup.js` - Registration handling
- `adminProfile.js` - Admin profile operations
- `course_reg.js` - Course registration logic
- `assienments.js` - Assignment operations
- `Attendance.js` - Attendance marking
- `calendar.js` / `calnder_admin.js` - Calendar functionality
- `grade.js` - Grade calculations
- `script_rank.js` - Ranking system
- `contact.js` - Contact form submission
- And 25+ more specialized JavaScript files

### CSS Files
- `index.css`, `log.css`, `allcss.css` - General styling
- Component-specific CSS files for various pages

---

## API Endpoints

The backend exposes **71+ REST API endpoints** under the base path: `/api/v1/user`

### Student Authentication & Profile
- `GET /login_student/{Student_id}/{Student_name}` - Student login
- `GET /getUserByUserId/{userID}` - Get user by ID
- `PUT /updateUser` - Update user information
- `DELETE /deleteUser/{userID}` - Delete user

### Admin/Lecturer Authentication
- `GET /login_lecturer/{lecturer_id}/{password}` - Lecturer login
- `GET /getAdminByUserId/{adminID}` - Get admin by ID
- `PUT /updateAdminProfile` - Update admin profile
- `DELETE /deleteAdmin/{adminID}` - Delete admin

### Course Management
- `POST /savecourse` - Create new course
- `GET /courses/{Student_id}` - Get student's enrolled courses
- `GET /coursesAdmin/{Admin_id}` - Get admin's courses
- `GET /course/{AdminId}/{CourseId}` - Get specific course
- `GET /regCourse` - Get available courses for registration
- `POST /enroll` - Enroll student in course
- `GET /isEnrolled/{studentId}/{courseId}` - Check enrollment status

### Assignment Management
- `POST /saveAssignment` - Create assignment
- `GET /getfile/{adminId}/{courseId}` - Get assignment files
- `POST /edit` - Edit assignment
- `POST /saveedit` - Save assignment edits
- `DELETE /delete` - Delete assignment

### Student Assignment Submission
- `POST /saveStdAssignment` - Submit student assignment
- `GET /getStdfile/{studentId}/{courseId}` - Get student submission
- `POST /editStudentAssignment` - Edit student submission
- `POST /saveStdEdit` - Save student submission edits
- `DELETE /studentDelete` - Delete student submission

### Quiz Management
- `POST /saveQuiz` - Create quiz
- `GET /getquiz/{AdminId}/{CourseId}` - Get quizzes

### Lecture Notes
- `POST /saveLectureNote` - Upload lecture note
- `GET /getLectureNoteFile/{adminId}/{courseId}` - Download lecture note
- `GET /allLectureNote/{adminId}/{courseId}` - List all lecture notes
- `GET /lectureNotes/{courseId}` - Get course lecture notes
- `POST /editLectureNote` - Edit lecture note
- `POST /saveLectureNoteEdit` - Save lecture note edits
- `DELETE /deleteLectureNote` - Delete lecture note

### Results & Grades
- `POST /saves_result` - Save student result
- `GET /print_result/{Student_id}` - Get student results
- `GET /average_result/{Student_id}` - Get average grade

### Attendance
- `POST /saves_attend` - Record attendance
- `PUT /attend/{Student_id}/{Course_id}` - Update attendance

### Ranking
- `GET /print_rank` - Get student rankings

### Events
- `POST /saveevents` - Create student event
- `POST /saveeventsadmin` - Create admin event
- `GET /events/{Student_id}` - Get student events
- `GET /eventsAdmin/{Admin_id}` - Get admin events

### Department
- `POST /savedepartment` - Create department

### User Management
- `POST /saveUser` - Create user
- `POST /saveAdminProfile` - Create admin profile
- `POST /savestudent` - Create student
- `GET /getUser` - Get all users

### Additional Endpoints
- Various mark total endpoints (assignments, quizzes, exams)
- Combined marks calculations
- Notification management
- Google Sheets integration endpoints

---

## Security Implementation

### Authentication & Authorization
- **JWT (JSON Web Tokens):** Stateless authentication
- **Token-based Security:** Secure API access
- **Password Encryption:** Spring Security password encoding
- **Role-based Access Control:** 
  - `STUDENT` role
  - `ADMIN`/`LECTURER` role

### Security Features
- CORS configuration for cross-origin requests
- JWT authentication filter for all protected endpoints
- Custom UserDetails implementation
- Secure session management
- Protected file uploads and downloads

### Configuration Classes
- `SecurityConfigStudent.java` - Main security configuration
- `JwtAuthenticationFilter.java` - JWT token validation
- `JwtService.java` - Token generation and validation
- `ApplicationConfigStudent.java` - User details service configuration

---

## Setup and Installation

### Prerequisites
- **Java 21** or higher
- **Maven 3.6+**
- **MySQL 8.0+**
- **Git**

### Backend Setup

1. **Clone the repository:**
```bash
git clone https://github.com/DinethShakya23/2nd-Year-Project.git
cd 2nd-Year-Project
```

2. **Configure MySQL database:**
```sql
-- MySQL will auto-create the database, but ensure MySQL is running
-- Default database name: 'project'
-- Default port: 3306
```

3. **Update application.properties:**
Edit `Backend/Co original/src/main/resources/application.properties`:
```properties
spring.datasource.username=your_mysql_username
spring.datasource.password=your_mysql_password
spring.mail.username=your_email@gmail.com
spring.mail.password=your_app_password
```

4. **Build and run the backend:**
```bash
cd "Backend/Co original"
mvn clean install
mvn spring-boot:run
```

The backend will start on `http://localhost:8090`

### Frontend Setup

1. **Build the frontend:**
```bash
cd Frontend
mvn clean package
```

2. **Deploy the WAR file:**
- Deploy `target/student_acca.war` to a servlet container (Tomcat, etc.)
- Or access the webapp directly from the IDE

3. **Access the application:**
- Open `http://localhost:<port>/student_acca/index.html`

### Google API Setup (Optional)

1. **Enable Google Sheets and Drive APIs** in Google Cloud Console
2. **Create OAuth 2.0 credentials**
3. **Download credentials.json** and place in `Backend/Co original/src/main/resources/`

---

## Directory Structure

```
2nd-Year-Project/
├── Backend/
│   ├── .vscode/                    # VSCode configuration
│   └── Co original/                # Main backend application
│       ├── src/
│       │   ├── main/
│       │   │   ├── java/com/project/co/
│       │   │   │   ├── config/        # Security & configuration
│       │   │   │   ├── controller/    # REST API controllers
│       │   │   │   ├── dto/           # Data Transfer Objects
│       │   │   │   ├── entity/        # JPA entities
│       │   │   │   ├── repo/          # Repositories
│       │   │   │   ├── service/       # Business logic
│       │   │   │   └── quickstart/    # Google API utilities
│       │   │   └── resources/
│       │   │       ├── application.properties
│       │   │       └── credentials.json
│       │   └── test/               # Test classes
│       ├── pom.xml                 # Maven configuration
│       └── target/                 # Compiled classes
├── Frontend/
│   ├── src/main/webapp/           # Web application files
│   │   ├── *.html                 # 48 HTML pages
│   │   ├── *.js                   # 37 JavaScript files
│   │   ├── *.css                  # CSS stylesheets
│   │   ├── img/                   # Images and assets
│   │   └── WEB-INF/               # Web app configuration
│   ├── pom.xml                    # Maven configuration
│   └── target/                    # Compiled WAR file
├── data/
│   └── index.json                 # Project metadata
├── README.md                      # Project documentation
└── PROJECT_OVERVIEW.md           # This file
```

---

## External Integrations

### 1. Google APIs
- **Google Sheets API:** Export grades and marks
- **Google Drive API:** File storage and sharing
- **OAuth 2.0:** Secure authentication

### 2. Email Service
- **Gmail SMTP:** For sending notifications
- **Spring Mail:** Email templating and sending

### 3. Frontend Libraries
- **Bootstrap 4.5.2:** Responsive UI framework
- **Font Awesome 6.1.1:** Icon library
- **Boxicons 2.1.4:** Additional icons
- **jQuery 3.7.1:** DOM manipulation and AJAX

---

## Key Achievements

✅ **Comprehensive LMS:** Full-featured learning management system  
✅ **Secure Authentication:** JWT-based security with role management  
✅ **Real-time Tracking:** Live attendance and grade tracking  
✅ **Automated Calculations:** GPA and ranking calculations  
✅ **Cloud Integration:** Google Sheets and Drive integration  
✅ **Email Notifications:** Automated email alerts  
✅ **Responsive Design:** Mobile-friendly interface  
✅ **File Management:** Upload and manage lecture notes and assignments  
✅ **Event Calendar:** Integrated calendar for deadlines and events  
✅ **Performance Analytics:** Detailed student performance reports  

---

## Future Enhancements

- Mobile application (Android/iOS)
- Advanced analytics and data visualization
- AI-powered performance predictions
- Video lecture integration
- Discussion forums
- Parent portal
- SMS notifications
- Multi-language support
- Dark mode theme
- Automated plagiarism detection

---

## References

- **Project Repository:** https://github.com/cepdnaclk/e20-co227-Student-Academic-Tracker
- **Project Page:** https://projects.ce.pdn.ac.lk/co227/e20/Student-Academic-Tracker/
- **University:** https://eng.pdn.ac.lk/
- **Department:** http://www.ce.pdn.ac.lk/

---

## License

This project is part of academic coursework at the University of Peradeniya, Department of Computer Engineering.

---

**Last Updated:** January 2026  
**Version:** 1.0  
**Status:** Active Development

