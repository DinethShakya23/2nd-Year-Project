# Student Academic Tracker - Technical Architecture Document

## Table of Contents
1. [System Architecture](#system-architecture)
2. [Backend Architecture](#backend-architecture)
3. [Frontend Architecture](#frontend-architecture)
4. [Data Flow](#data-flow)
5. [Security Architecture](#security-architecture)
6. [Database Design](#database-design)
7. [API Design](#api-design)
8. [File Upload System](#file-upload-system)
9. [External Integrations](#external-integrations)
10. [Deployment Architecture](#deployment-architecture)

---

## System Architecture

### High-Level Architecture

The Student Academic Tracker follows a **client-server architecture** with clear separation between frontend and backend:

```
┌─────────────────────────────────────────────────────────────┐
│                        Client Layer                          │
│              (Browser - HTML/CSS/JavaScript)                 │
└────────────────────────────┬────────────────────────────────┘
                             │
                             │ HTTPS/REST API
                             │ (JSON)
                             │
┌────────────────────────────▼────────────────────────────────┐
│                     Application Layer                        │
│                  (Spring Boot REST API)                      │
│                                                              │
│  ┌──────────────────────────────────────────────────────┐  │
│  │             Presentation Layer                        │  │
│  │          @RestController (controller.java)            │  │
│  │        - 71 REST API endpoints                        │  │
│  │        - Request/Response handling                    │  │
│  │        - DTO transformations                          │  │
│  └───────────────────┬──────────────────────────────────┘  │
│                      │                                       │
│  ┌───────────────────▼──────────────────────────────────┐  │
│  │             Business Logic Layer                      │  │
│  │               @Service classes                        │  │
│  │        - Business rules                               │  │
│  │        - Data transformations                         │  │
│  │        - File operations                              │  │
│  │        - External API integration                     │  │
│  └───────────────────┬──────────────────────────────────┘  │
│                      │                                       │
│  ┌───────────────────▼──────────────────────────────────┐  │
│  │           Data Access Layer                           │  │
│  │        @Repository interfaces                         │  │
│  │        - Spring Data JPA                              │  │
│  │        - Custom queries                               │  │
│  │        - Transaction management                       │  │
│  └───────────────────┬──────────────────────────────────┘  │
│                      │                                       │
└──────────────────────┼───────────────────────────────────────┘
                       │ JDBC
┌──────────────────────▼───────────────────────────────────────┐
│                    Database Layer                             │
│                      MySQL 8.0+                              │
│              - 24 tables (entities)                          │
│              - Relationships and constraints                 │
└───────────────────────────────────────────────────────────────┘
```

---

## Backend Architecture

### Component Breakdown

#### 1. Entry Point
```java
@SpringBootApplication
public class CoApplication {
    public static void main(String[] args) {
        SpringApplication.run(CoApplication.class, args);
    }
    
    @Bean
    public ModelMapper modelMapper() {
        return new ModelMapper();
    }
}
```
- **Port:** 8090
- **Context Path:** `/api/v1/user`
- **Main Class:** `com.project.co.CoApplication`

#### 2. Configuration Layer (`config/`)

##### Security Configuration
```
SecurityConfigStudent.java
├── HTTP Security configuration
├── CORS policy
├── JWT filter integration
└── Public/protected endpoints
```

##### JWT Configuration
```
JwtService.java
├── Token generation (createToken)
├── Token validation (isValid)
├── Extract claims (extractClaim, extractUsername)
└── Token expiration handling
```

##### Filter Chain
```
JwtAuthenticationFilter.java
├── Extract JWT from Authorization header
├── Validate token
├── Set authentication context
└── Continue filter chain
```

##### Application Configuration
```
ApplicationConfigStudent.java
├── UserDetailsService implementation
├── AuthenticationProvider bean
├── AuthenticationManager bean
└── PasswordEncoder bean
```

##### CORS Configuration
```
CorsConfig.java
└── Cross-origin resource sharing settings
```

#### 3. Controller Layer (`controller/`)

**Single Main Controller:** `controller.java`
- **71+ REST endpoints**
- **RESTful design**
- **DTO-based communication**
- **Exception handling**

**Request Flow:**
```
Client Request
    ↓
@RestController
    ↓
@RequestMapping("/api/v1/user")
    ↓
Endpoint Method (@GetMapping, @PostMapping, etc.)
    ↓
@Autowired Service
    ↓
Return ResponseEntity<T>
```

#### 4. Service Layer (`service/`)

**Key Services:**
- `Courseservice` - Course operations
- `Studentservice` - Student management
- `Assignmentservice` - Assignment handling
- `UserService` - User authentication
- `GoogleAPIService` - Google Sheets/Drive integration
- `EmailSenderService` - Email notifications
- `NotificationService` - System notifications
- `LectureNoteServices` - Lecture note management
- `StudentAssignmentService` - Student submissions
- And 15+ more specialized services

**Service Pattern:**
```java
@Service
public class Courseservice {
    @Autowired
    private Courserepo courserepo;
    
    @Autowired
    private ModelMapper modelMapper;
    
    // Business logic methods
    public CourseDto saveCourse(CourseDto dto) {
        Course entity = modelMapper.map(dto, Course.class);
        Course saved = courserepo.save(entity);
        return modelMapper.map(saved, CourseDto.class);
    }
}
```

#### 5. Repository Layer (`repo/`)

**Spring Data JPA Repositories:**
- Extend `JpaRepository<Entity, ID>`
- Custom query methods using method naming
- `@Query` annotations for complex queries
- Transaction management

**Example:**
```java
@Repository
public interface Studentrepo extends JpaRepository<Student, String> {
    Optional<Student> findByStudentEmail(String email);
    List<Student> findByRole(Role role);
    
    @Query("SELECT s FROM Student s WHERE s.Student_Rank > :rank")
    List<Student> findStudentsAboveRank(@Param("rank") int rank);
}
```

#### 6. Entity Layer (`entity/`)

**24 JPA Entities with relationships:**

Key entity patterns:
```java
@Entity
@NoArgsConstructor
@AllArgsConstructor
@Data
@Builder
public class Student implements UserDetails {
    @Id
    private String Student_id;
    
    @ManyToMany
    @JoinTable(
        name = "student_course",
        joinColumns = @JoinColumn(name = "Student_id"),
        inverseJoinColumns = @JoinColumn(name = "CId")
    )
    private Set<Course> courses;
    
    @Enumerated(EnumType.STRING)
    private Role role;
    
    // UserDetails implementation for Spring Security
}
```

#### 7. DTO Layer (`dto/`)

**30+ Data Transfer Objects:**
- Clean API contracts
- Validation annotations
- No business logic
- Mapped to/from entities using ModelMapper

**Example:**
```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class StudentDto {
    private String studentId;
    private String studentEmail;
    private String studentName;
    // No password in DTO for security
    private String studentCalendar;
    private Integer studentRank;
}
```

---

## Frontend Architecture

### Structure

```
Frontend (JSP Web Application)
├── HTML Pages (48 files)
│   ├── Public pages (index, about, contact, library)
│   ├── Auth pages (login, signup)
│   ├── Student pages (home, courses, assignments, grades)
│   └── Admin pages (manage courses, assignments, attendance)
│
├── JavaScript Modules (37 files)
│   ├── Session management (Session.js, Session_admin.js)
│   ├── Authentication (script_login.js, script_login_lec.js)
│   ├── Course operations (course_reg.js, my_courses.js)
│   ├── Assignment handling (assienments.js, assignmentDetail.js)
│   └── Feature-specific modules
│
├── CSS Stylesheets
│   ├── Global styles (allcss.css, style.css)
│   └── Component styles (per-page CSS)
│
└── Assets
    └── img/ (images and media)
```

### Frontend Communication Pattern

```javascript
// Typical AJAX call pattern
fetch('http://localhost:8090/api/v1/user/courses/' + studentId, {
    method: 'GET',
    headers: {
        'Authorization': 'Bearer ' + sessionStorage.getItem('token'),
        'Content-Type': 'application/json'
    }
})
.then(response => response.json())
.then(data => {
    // Update UI
    displayCourses(data);
})
.catch(error => {
    console.error('Error:', error);
    alert('Failed to load courses');
});
```

### Session Management

**Student Session:**
```javascript
// Session.js
sessionStorage.setItem('studentId', studentId);
sessionStorage.setItem('token', jwtToken);
sessionStorage.setItem('studentName', studentName);
```

**Admin Session:**
```javascript
// Session_admin.js
sessionStorage.setItem('adminId', adminId);
sessionStorage.setItem('token', jwtToken);
sessionStorage.setItem('role', 'ADMIN');
```

---

## Data Flow

### Complete Request-Response Cycle

#### Example: Student Viewing Grades

```
1. USER ACTION
   Student clicks "View Grades" button
   ↓

2. FRONTEND (grades.js)
   - Get studentId from sessionStorage
   - Get JWT token from sessionStorage
   - Construct API URL
   ↓

3. HTTP REQUEST
   GET /api/v1/user/print_result/{studentId}
   Headers: {
     Authorization: Bearer {token}
     Content-Type: application/json
   }
   ↓

4. BACKEND - JWT FILTER (JwtAuthenticationFilter)
   - Extract token from Authorization header
   - Validate token signature
   - Extract username/studentId from token
   - Set SecurityContext
   ↓

5. BACKEND - CONTROLLER (controller.java)
   @GetMapping("/print_result/{Student_id}")
   public ResponseEntity<List<S_resultrdto>> printResult(@PathVariable String Student_id)
   - Validate path variable
   - Call service layer
   ↓

6. BACKEND - SERVICE (S_resultservice)
   - Business logic
   - Call repository
   ↓

7. BACKEND - REPOSITORY (S_resultrepo)
   - Execute JPA query
   - Fetch from database
   ↓

8. DATABASE
   SELECT * FROM s_result WHERE student_id = ?
   ↓

9. BACKEND - MAPPING
   - Map entities to DTOs (ModelMapper)
   - Prepare response
   ↓

10. HTTP RESPONSE
    Status: 200 OK
    Body: [
      {
        "courseId": "CS101",
        "courseName": "Intro to CS",
        "grade": "A",
        "marks": 85
      },
      ...
    ]
    ↓

11. FRONTEND (grades.js)
    - Parse JSON response
    - Update DOM with grades
    - Display to user
    ↓

12. USER INTERFACE
    Grades displayed in table format
```

---

## Security Architecture

### Multi-Layer Security

```
Layer 1: Network Security
├── HTTPS (production)
└── CORS configuration

Layer 2: Authentication
├── JWT tokens
├── Token expiration (configurable)
└── Refresh token mechanism

Layer 3: Authorization
├── Role-based access (STUDENT, ADMIN)
├── Method-level security
└── Endpoint protection

Layer 4: Data Security
├── Password encryption (BCrypt)
├── SQL injection prevention (JPA)
└── XSS protection (Spring Security)

Layer 5: Session Security
├── Stateless sessions (JWT)
└── Token storage in sessionStorage (client-side)
```

### Authentication Flow

```
1. LOGIN REQUEST
   POST /api/v1/user/login_student/{id}/{name}
   ↓

2. UserService.loadUserByUsername(id)
   - Fetch user from database
   - Verify credentials
   ↓

3. AuthenticationManager.authenticate()
   - Validate user details
   - Check password (BCrypt)
   ↓

4. JwtService.createToken(user)
   - Generate JWT token
   - Set expiration
   - Sign with secret key
   ↓

5. RESPONSE
   {
     "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6...",
     "studentId": "E/20/055",
     "role": "STUDENT"
   }
   ↓

6. CLIENT
   - Store token in sessionStorage
   - Include in all subsequent requests
```

### Token Structure

```
JWT Token:
{
  "header": {
    "alg": "HS256",
    "typ": "JWT"
  },
  "payload": {
    "sub": "E/20/055",
    "role": "STUDENT",
    "iat": 1705459200,
    "exp": 1705545600
  },
  "signature": "..."
}
```

---

## Database Design

### Entity Relationships

```
Student ═══════════════════╗
║ Student_id (PK)          ║
║ Student_email            ║
║ Student_name             ║
║ Password                 ║
║ role                     ║
╚══════════════════════════╝
        ║
        ║ Many-to-Many
        ║
        ╠═══════════════════════╗
        ║                       ║
        ▼                       ▼
╔═══════════════════╗   ╔═══════════════════╗
║ student_course    ║   ║ S_result          ║
║ (Join Table)      ║   ║ student_id (FK)   ║
║ Student_id (FK)   ║   ║ course_id (FK)    ║
║ CId (FK)          ║   ║ marks             ║
╚═══════════════════╝   ╚═══════════════════╝
        ║
        ▼
╔═══════════════════════╗
║ Course                ║
║ Course_id (PK)        ║
║ Course_Name           ║
║ Course_Description    ║
║ Course_offered_by     ║
╚═══════════════════════╝
        ║
        ║ One-to-Many
        ║
        ╠═════════════════════════════════╗
        ║                                 ║
        ▼                                 ▼
╔═══════════════════╗           ╔═══════════════════╗
║ Assignment        ║           ║ LectureNote       ║
║ assignment_id(PK) ║           ║ note_id (PK)      ║
║ course_id (FK)    ║           ║ course_id (FK)    ║
║ title             ║           ║ title             ║
║ deadline          ║           ║ file_path         ║
╚═══════════════════╝           ╚═══════════════════╝
        ║
        ║ One-to-Many
        ▼
╔══════════════════════════╗
║ StudentAssignment        ║
║ id (PK)                  ║
║ assignment_id (FK)       ║
║ student_id (FK)          ║
║ submission_file          ║
║ marks                    ║
║ feedback                 ║
╚══════════════════════════╝
```

### Key Tables

**Students & Courses:**
- `student` - Student information
- `course` - Course catalog
- `student_course` - Enrollment (many-to-many)

**Academic Records:**
- `s_result` - Grades and marks
- `s_rank` - GPA rankings
- `combined_marks` - Aggregated scores

**Assignments & Quizzes:**
- `assignment` - Assignment definitions
- `student_assignment` - Submissions
- `quiz` - Quiz definitions
- `assignment_mark_total` - Total marks
- `quiz_mark_total` - Total quiz marks
- `exam_mark_total` - Total exam marks

**Attendance:**
- `s_attend` - Attendance records

**Content:**
- `lecture_note` - Lecture materials
- `examsheet` - Exam schedules

**System:**
- `notification` - System notifications
- `student_notification` - User notifications
- `event` - Student events
- `event_admin` - Admin events

**Configuration:**
- `course_weight` - Grade calculation weights
- `department` - Departments
- `instructor` - Instructors

**Users:**
- `user` - General users
- `admin` - Admins
- `admin_profile` - Admin profiles

---

## API Design

### RESTful Principles

**Resource-based URLs:**
```
✅ GET  /api/v1/user/courses/{studentId}
✅ POST /api/v1/user/savecourse
✅ PUT  /api/v1/user/updateUser
✅ DELETE /api/v1/user/deleteUser/{userId}
```

**HTTP Methods:**
- `GET` - Retrieve resources
- `POST` - Create resources
- `PUT` - Update resources
- `DELETE` - Remove resources

**Response Format:**
```json
{
  "status": "success",
  "data": {
    "courseId": "CS101",
    "courseName": "Intro to CS",
    "courseDescription": "..."
  },
  "message": "Course retrieved successfully"
}
```

**Error Handling:**
```json
{
  "status": "error",
  "error": {
    "code": 404,
    "message": "Course not found"
  },
  "timestamp": "2026-01-17T03:36:51.444Z"
}
```

---

## File Upload System

### File Storage Architecture

```
File Upload Flow:
1. Client selects file
2. FormData with multipart/form-data
3. Backend receives MultipartFile
4. Validate file (type, size)
5. Generate unique filename
6. Save to file system
7. Store path in database
8. Return success response
```

**Configuration:**
```properties
spring.servlet.multipart.enabled=true
spring.servlet.multipart.file-size-threshold=2KB
spring.servlet.multipart.max-file-size=50MB
spring.servlet.multipart.max-request-size=50MB
```

**Controller Example:**
```java
@PostMapping("/saveLectureNote")
public ResponseEntity<?> uploadLectureNote(
    @RequestParam("file") MultipartFile file,
    @RequestParam("courseId") String courseId,
    @RequestParam("title") String title
) {
    // Save file and metadata
}
```

---

## External Integrations

### Google APIs Integration

**Google Sheets:**
- Export grades to spreadsheets
- Automatic grade sheet creation
- OAuth 2.0 authentication

**Google Drive:**
- File storage backup
- Share lecture notes

**Implementation:**
```java
@Service
public class GoogleAPIService {
    private Sheets sheetsService;
    private Drive driveService;
    
    // Initialize services with credentials
    // Create/update spreadsheets
    // Upload/download files
}
```

### Email Service

**Gmail SMTP:**
```properties
spring.mail.host=smtp.gmail.com
spring.mail.port=587
spring.mail.username=your-email@gmail.com
spring.mail.password=app-password
```

**Service:**
```java
@Service
public class EmailSenderService {
    @Autowired
    private JavaMailSender mailSender;
    
    public void sendEmail(String to, String subject, String body) {
        SimpleMailMessage message = new SimpleMailMessage();
        message.setTo(to);
        message.setSubject(subject);
        message.setText(body);
        mailSender.send(message);
    }
}
```

---

## Deployment Architecture

### Development Environment

```
Developer Machine
├── Backend (Spring Boot)
│   └── Runs on localhost:8090
├── Frontend (Tomcat/Servlet Container)
│   └── Runs on localhost:8080
└── MySQL Database
    └── Runs on localhost:3306
```

### Production Environment (Recommended)

```
┌─────────────────────────────────────────────┐
│           Load Balancer / Nginx             │
│         (SSL Termination, Routing)          │
└──────────────┬──────────────────────────────┘
               │
      ┌────────┴────────┐
      │                 │
      ▼                 ▼
┌──────────────┐  ┌──────────────┐
│  Frontend    │  │  Backend     │
│  (Tomcat)    │  │  (Spring)    │
│  :8080       │  │  :8090       │
└──────────────┘  └──────┬───────┘
                         │
                         ▼
                  ┌──────────────┐
                  │   MySQL      │
                  │   :3306      │
                  └──────────────┘
```

### Build & Package

**Backend:**
```bash
cd "Backend/Co original"
mvn clean package
# Produces: target/co-0.0.1-SNAPSHOT.jar
java -jar target/co-0.0.1-SNAPSHOT.jar
```

**Frontend:**
```bash
cd Frontend
mvn clean package
# Produces: target/student_acca.war
# Deploy to Tomcat webapps/
```

---

## Performance Considerations

### Database Optimization
- Indexed foreign keys
- Query optimization with `@Query`
- Lazy loading for collections
- Connection pooling (HikariCP)

### Caching Strategy
- Spring Cache abstraction
- Cache frequently accessed data
- Cache eviction policies

### File Storage
- File system storage (fast access)
- Future: Move to cloud storage (S3, Google Cloud Storage)

---

## Scalability

### Horizontal Scaling
- Stateless backend (JWT tokens)
- Load balancer distribution
- Database replication

### Vertical Scaling
- Increase server resources
- Database optimization
- Connection pool tuning

---

## Monitoring & Logging

### Logging
```properties
logging.level.root=INFO
logging.level.com.project.co=DEBUG
logging.file.name=logs/application.log
```

### Spring Boot Actuator (Future)
- Health checks
- Metrics
- Performance monitoring

---

**Document Version:** 1.0  
**Last Updated:** January 2026  
**For:** Developers and Technical Staff
